---
name: core-environment
description: Environment variables that control mage behavior at runtime.
---

# Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `MAGEFILE_CACHE` | `$HOME/.magefile` | Directory for compiled magefile binaries |
| `MAGEFILE_VERBOSE` | `""` | Set to `1` or `true` for verbose mode (like `-v`) |
| `MAGEFILE_DEBUG` | `""` | Set to `1` or `true` for debug mode (like `-debug`) |
| `MAGEFILE_GOCMD` | `go` | Go binary used for compilation |
| `MAGEFILE_HASHFAST` | `""` | Set to `1` or `true` to use fast (non-dependency-aware) hashing |
| `MAGEFILE_IGNOREDEFAULT` | `""` | Set to `1` or `true` to ignore the default target and always list targets |
| `MAGEFILE_MULTILINE` | `""` | Set to `1` or `true` to preserve line breaks in help text |
| `MAGEFILE_ENABLE_COLOR` | `""` | Set to `1` or `true` to enable ANSI color output in target list |
| `MAGEFILE_TARGET_COLOR` | `Cyan` | ANSI color name for target names (requires `MAGEFILE_ENABLE_COLOR=1`) |

## Notes

**`MAGEFILE_HASHFAST`:** Speeds up startup (especially on Windows) by hashing only magefiles, not dependencies. Use `-f` to force a rebuild when a dependency changes.

**`MAGEFILE_MULTILINE`:** Can also be set per-magefile with `//mage:multiline` comment. This is evaluated at generation time, so compiled binaries have it baked in.

**`MAGEFILE_TARGET_COLOR` values:** `Black`, `Red`, `Green`, `Yellow`, `Blue`, `Magenta`, `Cyan`, `White`, and `Bright*` variants. Case-insensitive.

## Runtime API (mg package)

These environment variables are accessible in magefiles via the `mg` package:

```go
import "github.com/magefile/mage/mg"

mg.Verbose()       // bool — is -v set?
mg.Debug()         // bool
mg.GoCmd()         // string — go binary path
mg.CacheDir()      // string — cache directory path
mg.HashFast()      // bool
mg.IgnoreDefault() // bool
mg.EnableColor()   // bool
mg.Multiline()     // bool
```

<!--
Source references:
- sources/mage/site/content/environment/_index.en.md
- sources/mage/mg/runtime.go
-->
