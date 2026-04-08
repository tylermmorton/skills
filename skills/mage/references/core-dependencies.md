---
name: core-dependencies
description: Declaring dependencies with mg.Deps, parallel vs serial execution, passing arguments via mg.F, and context propagation.
---

# Dependencies

Use `mg.Deps` to declare functions that must run before the current target. Dependencies are guaranteed to run **exactly once** per mage invocation, even if declared by multiple targets.

## Basic Usage

```go
import "github.com/magefile/mage/mg"

func Build() error {
    mg.Deps(Generate, Fmt)   // runs Generate and Fmt in parallel
    return sh.Run("go", "build", "./...")
}

func Generate() error { return sh.Run("go", "generate", "./...") }
func Fmt() error      { return sh.Run("gofmt", "-w", ".") }
```

Place `mg.Deps` as the first line so the target waits for all dependencies before proceeding.

## Parallel vs Serial

| Function | Behavior |
|----------|----------|
| `mg.Deps(fns...)` | Run in parallel goroutines |
| `mg.SerialDeps(fns...)` | Run serially |
| `mg.CtxDeps(ctx, fns...)` | Parallel, passes context |
| `mg.SerialCtxDeps(ctx, fns...)` | Serial, passes context |

## Dependencies with Arguments

Use `mg.F` to pass arguments to a dependent function:

```go
func Build() {
    mg.Deps(
        mg.F(Compile, "server"),
        mg.F(Compile, "client"),
    )
}

func Compile(target string) error {
    return sh.Run("go", "build", "-o", target, "./cmd/"+target)
}
```

`mg.F(Compile, "server")` and `mg.F(Compile, "client")` are treated as **distinct** dependencies. Two calls to `mg.F(Compile, "server")` would only run once.

## Context Propagation

```go
func Build(ctx context.Context) error {
    mg.CtxDeps(ctx, Generate)  // passes ctx to Generate
    return sh.Run("go", "build", "./...")
}
```

`mg.Deps` (no ctx variant) uses a background context — dependencies won't be cancelled by `-t` timeout. Use `mg.CtxDeps` to propagate timeout cancellation.

## Execution Order Example

```go
func Build() { mg.Deps(F, G) }
func F()     { mg.Deps(H); fmt.Println("f") }
func G()     { mg.Deps(H); fmt.Println("g") }
func H()     { fmt.Println("h") }
```

Output (h always first; f/g order non-deterministic):
```
h
g   ← or f, order varies
f
Build
```

<!--
Source references:
- sources/mage/site/content/dependencies/_index.en.md
- sources/mage/mg/deps.go
- sources/mage/mg/fn.go
-->
