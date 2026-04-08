---
name: core-magefiles
description: Magefile structure, build tags, package conventions, and the magefiles directory feature.
---

# Magefiles

A magefile is any Go file with the `mage` build tag in `package main`.

```go
//go:build mage

package main
```

## Key Rules

- Any number of magefiles may exist in the same directory — name them anything
- They are excluded from normal Go builds (build tag ensures this)
- May use Go's standard build constraints (OS, arch, etc.)
- Generate a starter magefile: `mage -init`

## Magefiles Directory

Place magefiles in a `magefiles/` subdirectory to keep them separate from your Go source. If no magefile exists in the current directory, mage automatically uses `magefiles/` as the source while keeping the current directory as the working directory.

```
project/
├── magefiles/
│   └── build.go   # //go:build mage
├── main.go
└── go.mod
```

This is equivalent to: `mage -d magefiles -w .`

Useful when IDEs behave erratically with magefiles sharing a directory with other `package main` files.

## Default Target

```go
// Default runs when `mage` is invoked with no target
var Default = Build
```

Without a default, running `mage` prints the target list.

<!--
Source references:
- sources/mage/site/content/magefiles/_index.en.md
- sources/mage/site/content/index.md
-->
