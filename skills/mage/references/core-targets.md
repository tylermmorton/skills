---
name: core-targets
description: Target function signatures, required vs optional arguments, flags, aliases, namespaces, and running multiple targets.
---

# Targets

A target is any exported function matching this signature:

```
func [Name]([ctx context.Context,] [args...]) [error]
```

**Allowed argument types:** `string`, `int`, `float64`, `bool`, `time.Duration`
**Optional (flag) argument types:** `*string`, `*int`, `*float64`, `*bool`, `*time.Duration`

## Valid Target Examples

```go
func Build() {}
func Install(ctx context.Context) error { return nil }
func Run(what string) error { return nil }
func Exec(ctx context.Context, name string, count int, debug bool, timeout time.Duration) error { return nil }
```

## Required Arguments

Passed positionally from CLI after the target name:

```go
func Deploy(env string) error { ... }
```

```bash
mage deploy production
```

## Optional Arguments (Flags) — v1.16.0+

Use pointer types for optional parameters; pass as `-name=value`:

```go
func Deploy(env string, dryrun *bool) error {
    if dryrun != nil && *dryrun {
        fmt.Println("Dry run only")
        return nil
    }
    return sh.Run("kubectl", "apply", "-f", env)
}
```

```bash
mage deploy production          # dryrun is nil
mage deploy production -dryrun  # *bool shorthand for true
mage deploy production -dryrun=true
```

## Comments as Help Text

```go
// Build compiles the project.
//
// Runs go generate, then go build. Use GOFLAGS to pass extra flags.
func Build() error { ... }
```

- First sentence → shown with `mage -l`
- Full comment → shown with `mage -h build`

## Multiline Comments

Add `//mage:multiline` to preserve line breaks in help output:

```go
//mage:multiline

// Build compiles the project.
//
// Step 1: generate
// Step 2: build
func Build() error { ... }
```

Or set `MAGEFILE_MULTILINE=1`.

## Aliases

```go
var Aliases = map[string]interface{}{
    "b":     Build,
    "i":     Install,
    "build": Install, // alias can differ from target name
}
```

## Namespaces

Group related targets using a named type of `mg.Namespace`:

```go
type Docker mg.Namespace

// Builds the Docker image.
func (Docker) Build() error { return nil }

// Pushes the image to the registry.
func (Docker) Push() error { return nil }
```

```bash
mage docker:build
mage docker:push
```

## Running Multiple Targets

```bash
mage fmt lint test
```

Targets run serially left-to-right. If any fails, subsequent targets are skipped. Shared dependencies run only once across all targets.

## Context and Timeout

```go
func Build(ctx context.Context) error {
    mg.CtxDeps(ctx, Generate)
    return sh.Run("go", "build", "./...")
}
```

Pass `-t 5m30s` to set a timeout; the context is cancelled at that time.

<!--
Source references:
- sources/mage/site/content/targets/_index.en.md
- sources/mage/site/content/magefiles/_index.en.md
-->
