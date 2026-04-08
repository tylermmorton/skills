---
name: features-sh
description: The sh helper library for running shell commands, capturing output, and file manipulation.
---

# sh — Shell Helper Library

`github.com/magefile/mage/sh` provides ergonomic wrappers around `os/exec`.

## Running Commands

| Function | Stdout | Stderr | Returns |
|----------|--------|--------|---------|
| `sh.Run(cmd, args...)` | Suppressed (verbose: shown) | os.Stderr | `error` |
| `sh.RunV(cmd, args...)` | Always os.Stdout | os.Stderr | `error` |
| `sh.RunWith(env, cmd, args...)` | Suppressed (verbose: shown) | os.Stderr | `error` |
| `sh.RunWithV(env, cmd, args...)` | Always os.Stdout | os.Stderr | `error` |
| `sh.Output(cmd, args...)` | Captured | os.Stderr | `(string, error)` |
| `sh.OutputWith(env, cmd, args...)` | Captured | os.Stderr | `(string, error)` |

```go
import "github.com/magefile/mage/sh"

func Build() error {
    return sh.Run("go", "build", "./...")
}

func Version() error {
    out, err := sh.Output("git", "describe", "--tags")
    if err != nil {
        return err
    }
    fmt.Println(out)
    return nil
}
```

## Environment Variables in Args

Args may reference environment variables using `$FOO` syntax — they are expanded before the command runs:

```go
sh.RunWith(map[string]string{"GOOS": "linux"}, "go", "build", "-o", "$GOOS/app", "./...")
```

## Command Factories

Pre-bake a command with fixed arguments, return a function for repeated use:

```go
var goRun = sh.RunCmd("go", "run")

func Lint() error { return goRun("./cmd/lint/...") }
func Test() error { return goRun("./...") }
```

`sh.OutCmd` is the equivalent for capturing output.

## Low-Level: sh.Exec

Full control over stdout/stderr writers:

```go
ran, err := sh.Exec(
    map[string]string{"CGO_ENABLED": "0"},
    os.Stdout,
    os.Stderr,
    "go", "build", "./...",
)
```

`ran` reports whether the command was found and executed (vs. not found/not executable).

## Checking Exit Status

```go
err := sh.Run("go", "test", "./...")
if sh.CmdRan(err) {
    fmt.Printf("exit code: %d\n", sh.ExitStatus(err))
}
```

## File Utilities

```go
sh.Rm("dist/")           // Remove file or directory (no error if missing)
sh.Copy("dst", "src")    // Copy file, overwriting destination
```

<!--
Source references:
- sources/mage/sh/cmd.go
- sources/mage/sh/helpers.go
- sources/mage/site/content/libraries/_index.en.md
-->
