---
name: features-importing
description: Importing targets from external packages into a magefile using the mage:import comment.
---

# Importing Targets

Mage can import targets from external packages, enabling reuse of build logic across repos.

## Requirements

- The imported package must **not** be `package main` (unlike magefiles themselves)
- All files in the package are imported; files with build tags (e.g. `//go:build mage`) are excluded
- Aliases and `Default` vars in imported packages are ignored

## Syntax

Add `//mage:import` as a comment on the import line:

```go
//go:build mage

package main

import (
    //mage:import
    _ "github.com/myorg/build-tools/common"

    //mage:import build
    "github.com/myorg/build-tools/builder"
)
```

## Import Modes

| Mode | Effect | Example call |
|------|--------|-------------|
| No name (blank import) | Targets added to root namespace | `mage deploy` |
| Named import | Targets placed under a namespace | `mage build:all` |

```go
// common package has: func Deploy() error
// builder package has: func All() error

// After import above:
// mage deploy     → common.Deploy
// mage build:all  → builder.All
```

## Using Imported Targets as Dependencies

```go
import (
    //mage:import build
    builder "github.com/myorg/build-tools/builder"
)

func Release() error {
    mg.Deps(builder.All)   // use imported function directly
    return sh.Run("./scripts/publish.sh")
}
```

## Writing an Importable Package

```go
// github.com/myorg/build-tools/common/common.go
// Note: NOT package main, no //go:build mage tag

package common

import "github.com/magefile/mage/sh"

// Runs go vet on all packages.
func Vet() error {
    return sh.Run("go", "vet", "./...")
}

// Runs gofmt.
func Fmt() error {
    return sh.Run("gofmt", "-w", ".")
}
```

<!--
Source references:
- sources/mage/site/content/importing/_index.en.md
-->
