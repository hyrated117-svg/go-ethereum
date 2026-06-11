# Pre-Commit Verification for go-ethereum

**Domain:** Go/Ethereum Development  
**Scope:** go-ethereum repository  
**Purpose:** Help developers verify code quality before committing changes  
**Type:** Quick verification checklist  

## Overview

This skill guides you through the go-ethereum pre-commit verification process. It ensures your changes are properly formatted, compile successfully, pass all tests, meet style standards, and maintain dependency hygiene.

## When to Use

Run this checklist **before every commit** to catch issues early and maintain code quality standards.

## Steps

### 1. Format Your Code
Format all modified files using Go's standard tools:

```sh
gofmt -w <modified files>
goimports -w <modified files>
```

**Why:** Ensures consistent style and import organization across the codebase.

### 2. Build All Commands
Verify that all tools compile successfully:

```sh
make all
```

**Why:** Catches compilation errors early. This includes `keeper` which has special build requirements.

### 3. Run Tests

**During development** (faster feedback):
```sh
go run ./build/ci.go test -short
```

**Before committing** (full verification):
```sh
go run ./build/ci.go test
```

**Why:** Validates functionality. The full test suite includes Ethereum execution-spec tests and all state/block test permutations.

### 4. Run Linting
```sh
go run ./build/ci.go lint
```

**Why:** Catches style issues and potential code problems beyond basic formatting.

### 5. Check Generated Code
```sh
go run ./build/ci.go check_generate
```

**If this fails:**
- Install required code generators: `make devtools`
- Run appropriate `go generate` commands
- Include updated files in your commit

**Why:** Ensures generated files (e.g., `gen_*.go`) are up to date.

### 6. Check Dependencies
```sh
go run ./build/ci.go check_baddeps
```

**Why:** Prevents forbidden dependencies from being introduced.

## Commit Preparation

Before pushing, ensure:
- ✓ No binaries committed (build outputs or investigation byproducts)
- ✓ Commit message follows format: `<package(s)>: description`
  - Example: `core/vm: fix stack overflow in PUSH instruction`
- ✓ PR title follows format: `<modified paths>: description`
  - Example: `core, eth: add arena allocator support`

## Quick Reference

| Step | Command | Duration |
|------|---------|----------|
| Format | `gofmt -w` + `goimports -w` | ~10s |
| Build | `make all` | ~30s |
| Test (short) | `go run ./build/ci.go test -short` | ~2min |
| Test (full) | `go run ./build/ci.go test` | ~10min+ |
| Lint | `go run ./build/ci.go lint` | ~1min |
| Gen Check | `go run ./build/ci.go check_generate` | ~10s |
| Deps Check | `go run ./build/ci.go check_baddeps` | ~5s |

## Tips

- **Iterative development:** Use `-short` tests frequently while developing
- **Pre-commit:** Run full verification before committing
- **Selective formatting:** You can format individual files instead of all modified files
- **Multiple packages:** Use comma-separated format in commit messages when multiple areas are affected

## Automation Helper

To run the entire pre-commit verification suite in one go, use:

```sh
gofmt -w <files> && goimports -w <files> && make all && go run ./build/ci.go test && go run ./build/ci.go lint && go run ./build/ci.go check_generate && go run ./build/ci.go check_baddeps
```

**Note:** Replace `<files>` with your list of modified files. This runs sequentially and stops on first error, preventing later steps if an earlier one fails.

## Common Issues & Troubleshooting

### Formatting Issues
**Problem:** `goimports` removes imports or reorders them unexpectedly  
**Solution:** 
- Run locally to verify: `goimports -d <file>`
- Check for circular dependencies in your code
- Ensure modified files compile before formatting

### Build Failures
**Problem:** `make all` fails with compiler errors  
**Solution:**
- Check Go version matches project requirements (see `go.mod`)
- Run `go mod tidy` if dependency errors appear
- Review recent changes for syntax errors

### Test Failures
**Problem:** Full test suite fails but `-short` tests pass  
**Solution:**
- Check if integration tests require special setup (e.g., database, network)
- Review test output for `SKIP` flags indicating unmet dependencies
- Run individual test packages: `go test ./core -v` for targeted debugging

### Generated Code Out of Date
**Problem:** `check_generate` fails  
**Solution:**
- Install generators: `make devtools`
- Run: `go generate ./...`
- Verify `gen_*.go` files are updated and include in commit

### Lint Failures
**Problem:** Linting reports style issues not caught by `gofmt`  
**Solution:**
- Review specific lint rule violations in output
- Common issues: unused variables, missing error handling, code complexity
- Fix manually or use linter suggestions

### Dependency Violations
**Problem:** `check_baddeps` fails with forbidden dependencies  
**Solution:**
- Review added imports for disallowed packages
- Check if dependency restriction exists in build rules
- Consult team guidelines for approved alternatives

## Commit Message Validation

Before committing, verify your message format:

### Valid Formats
✓ `core/vm: fix stack overflow in PUSH instruction`  
✓ `eth, rpc: make trace configs optional`  
✓ `cmd/geth: add new flag for sync mode`  
✓ `core, eth: add arena allocator support`  

### Invalid Formats
✗ `fix bug` (missing package prefix)  
✗ `Core/VM: Fix Stack Overflow` (uppercase, incorrect format)  
✗ `core/vm/instruction: fix PUSH` (too specific, use top-level package)  

### Checklist
- [ ] Prefix matches modified package(s) (comma-separated if multiple)
- [ ] Description is lowercase
- [ ] Description is concise (ideally < 50 chars)
- [ ] No period at end of message
- [ ] Explains *what* changed, not *why* (save detailed context for PR description)

## Related Resources

- [AGENTS.md](AGENTS.md) — Full pre-commit guidelines and commit message conventions
- [README.md](README.md) — Project overview
