---
name: features-compiling
description: Compiling magefiles to static binaries for distribution or CI use without mage installed.
---

# Compiling Static Binaries

Compile a magefile into a standalone binary that includes the mage runtime and all targets. The resulting binary requires no mage or Go installation to run.

## Compile

```bash
mage -compile ./build-tool
```

The binary supports the same flags as mage:

```bash
./build-tool -l          # list targets
./build-tool build       # run a target
./build-tool -v test     # verbose
./build-tool -t 5m build # with timeout
```

## Cross-Compilation

Use `-goos` and `-goarch` to target a different platform:

```bash
mage -compile ./build-linux -goos linux -goarch amd64
mage -compile ./build-win.exe -goos windows -goarch amd64
```

Valid values: see https://golang.org/doc/install/source#environment

## Notes

- When `-compile` is combined with `-d`, the output path is relative to the magefile directory
- Mage ignores `GOOS`/`GOARCH` env vars when compiling the magefile binary itself — set them for your build outputs inside the targets instead
- The `MAGEFILE_MULTILINE` setting is baked in at compile time

## Zero-Install Alternative

To run mage without installing it (using only `go run`), create a launcher file:

```go
//go:build ignore

package main

import (
    "os"
    "github.com/magefile/mage/mage"
)

func main() { os.Exit(mage.Main()) }
```

```bash
go run mage.go build
go run mage.go -l
```

Note: `go run` exits with code 0 or 1 only — non-zero mage exit codes are printed but mapped to 1.

<!--
Source references:
- sources/mage/site/content/compiling/_index.en.md
- sources/mage/site/content/zeroInstall/_index.en.md
-->
