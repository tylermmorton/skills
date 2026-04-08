---
name: features-target-lib
description: The target helper library for make-style file timestamp comparisons to skip unnecessary rebuilds.
---

# target — File Timestamp Library

`github.com/magefile/mage/target` provides make-style file freshness checks to avoid redundant work.

## Functions

### `target.Path(dst string, sources ...string) (bool, error)`

Returns `true` if any source file is **newer** than the destination file. Does not recurse into directories.

```go
import "github.com/magefile/mage/target"

func Build() error {
    changed, err := target.Path("bin/app", "go.mod", "go.sum", "main.go")
    if err != nil {
        return err
    }
    if !changed {
        fmt.Println("Nothing to build")
        return nil
    }
    return sh.Run("go", "build", "-o", "bin/app", ".")
}
```

### `target.Dir(dst string, sources ...string) (bool, error)`

Like `target.Path` but **recursively** checks all files and subdirectories under any directory sources.

```go
func Bundle() error {
    changed, err := target.Dir("dist/app.js", "src/")
    if err != nil {
        return err
    }
    if !changed {
        return nil
    }
    return sh.Run("esbuild", "src/index.ts", "--outfile=dist/app.js")
}
```

## Pattern

```go
func Target() error {
    rebuild, err := target.Dir("output/", "source/")
    if err != nil { return err }
    if !rebuild { return nil }
    // ... do build work
}
```

<!--
Source references:
- sources/mage/site/content/filesources/_index.en.md
- sources/mage/site/content/libraries/_index.en.md
-->
