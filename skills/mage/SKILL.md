---
name: mage
description: Mage is a Make/Rake-like build tool using Go functions as targets. Use when writing magefiles, declaring dependencies, running shell commands in build scripts, or compiling static build binaries.
metadata:
  author: Anthony Fu
  version: "2026.04.07"
  source: Generated from https://github.com/magefile/mage, scripts located at https://github.com/antfu/skills
---

> The skill is based on Mage v1.17.1, generated at 2026-04-07.

Mage is a Go-based build tool. Write plain Go functions as targets — no DSL, no Makefile syntax. Mage compiles them and runs them from the CLI. Dependencies between targets are declared explicitly and run in parallel by default.

## Core References

| Topic | Description | Reference |
|-------|-------------|-----------|
| Magefiles | Build tags, package structure, magefiles/ directory, Default target | [core-magefiles](references/core-magefiles.md) |
| Targets | Function signatures, required/optional args, comments as help, aliases, namespaces | [core-targets](references/core-targets.md) |
| Dependencies | mg.Deps, parallel/serial, mg.F for args, context propagation | [core-dependencies](references/core-dependencies.md) |
| CLI | Commands, flags, invocation patterns | [core-cli](references/core-cli.md) |
| Environment | Environment variables controlling mage behavior | [core-environment](references/core-environment.md) |

## Features

### Helper Libraries

| Topic | Description | Reference |
|-------|-------------|-----------|
| sh | Running shell commands, capturing output, file utilities | [features-sh](references/features-sh.md) |
| target | File timestamp comparisons to skip rebuilds | [features-target-lib](references/features-target-lib.md) |
| Importing | Import targets from external packages into a magefile | [features-importing](references/features-importing.md) |
| Compiling | Compile magefiles to static binaries for distribution | [features-compiling](references/features-compiling.md) |

## Advanced

| Topic | Description | Reference |
|-------|-------------|-----------|
| mg package | mg.Fatal for custom exit codes, mg.F, Fn interface | [advanced-mg](references/advanced-mg.md) |
