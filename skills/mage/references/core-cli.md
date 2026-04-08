---
name: core-cli
description: Mage CLI commands, flags, and invocation patterns.
---

# Mage CLI

```
mage [options] [target [args...]]
```

## Commands

| Flag | Description |
|------|-------------|
| `-l` | List available targets with their short descriptions |
| `-h [target]` | Show help; with a target name, shows full description |
| `-init` | Create a starter magefile in the current directory |
| `-clean` | Remove cached compiled binaries from `CACHE_DIR` |
| `-compile <path>` | Compile magefile to a static binary at the given path |
| `-version` | Show mage version info |

## Options

| Flag | Description |
|------|-------------|
| `-d <dir>` | Directory to read magefiles from (default: `.`) |
| `-w <dir>` | Working directory where targets run (default: same as `-d`) |
| `-v` | Verbose: show target stdout output |
| `-debug` | Enable debug messages |
| `-f` | Force recompilation even if magefiles haven't changed |
| `-keep` | Keep intermediate generated files after running |
| `-t <duration>` | Timeout (e.g. `5m30s`); cancels context at expiry |
| `-gocmd <binary>` | Go binary for compilation (default: `go`) |
| `-goos <os>` | Target OS for `-compile` |
| `-goarch <arch>` | Target arch for `-compile` |

## Common Invocations

```bash
mage                    # Run default target or list targets
mage -l                 # List all targets
mage build              # Run the Build target
mage deploy production  # Run Deploy with argument "production"
mage fmt lint test      # Run multiple targets serially
mage -v test            # Run test with verbose output
mage -t 10m build       # Run build with 10-minute timeout
mage -d magefiles build # Read magefiles from ./magefiles/
```

## Compiled Binary Usage

After `mage -compile ./magefile`:

```bash
./magefile -l           # List targets
./magefile build        # Run a target
./magefile -v test      # Verbose
```

<!--
Source references:
- sources/mage/site/content/index.md
- sources/mage/site/content/compiling/_index.en.md
-->
