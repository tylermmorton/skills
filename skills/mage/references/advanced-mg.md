---
name: advanced-mg
description: Advanced mg package features including mg.Fatal for custom exit codes, mg.F for argument wrapping, and the Fn interface.
---

# Advanced mg Package

## Custom Exit Codes

Return a non-1 exit code from a target using `mg.Fatal` or `mg.Fatalf`:

```go
import "github.com/magefile/mage/mg"

func Deploy() error {
    if os.Getenv("ENV") == "" {
        return mg.Fatal(2, "ENV must be set")
    }
    if err := deploy(); err != nil {
        return mg.Fatalf(3, "deploy failed: %v", err)
    }
    return nil
}
```

`mg.ExitStatus(err)` extracts the exit code:
- `nil` → `0`
- no `ExitStatus()` method → `1`
- otherwise → value from `ExitStatus() int`

## mg.F — Dependencies with Arguments

```go
mg.Deps(mg.F(Compile, "server"), mg.F(Compile, "client"))
```

- Args must be: `int`, `bool`, `string`, or `time.Duration`
- Do **not** pass `context.Context` — it is injected automatically
- Functions with identical name+args are deduplicated: `mg.F(Compile, "server")` called twice runs `Compile("server")` once

## Fn Interface

For custom dependency types implement:

```go
type Fn interface {
    Name() string        // fully qualified function name (for deduplication)
    ID() string          // additional uniqueness qualifier
    Run(ctx context.Context) error
}
```

Pass to `mg.Deps` just like any other function.

## Runtime Checks

```go
if mg.Verbose() {
    log.Println("Verbose mode enabled")
}
```

See [core-environment](core-environment.md) for the full list of runtime accessors.

<!--
Source references:
- sources/mage/mg/errors.go
- sources/mage/mg/fn.go
- sources/mage/mg/deps.go
- sources/mage/mg/runtime.go
-->
