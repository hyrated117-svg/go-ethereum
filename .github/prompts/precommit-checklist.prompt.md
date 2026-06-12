---
description: "Run the pre-commit validation checklist for go-ethereum before committing changes"
argument-hint: "List any modified files or leave blank to check all"
agent: "agent"
---

# Pre-Commit Validation Checklist

I'll help you validate your changes against the go-ethereum pre-commit requirements. This ensures your commits meet the project standards before pushing.

Run the following checks in order. I'll help you execute and verify each one:

## ✓ 1. Formatting
Format all modified files with `gofmt` and `goimports`:
```sh
gofmt -w <modified-files>
goimports -w <modified-files>
```

## ✓ 2. Build All Commands
Verify all tools compile successfully:
```sh
make all
```
This should build all executables under `cmd/`, including `keeper`.

## ✓ 3. Tests (Full Suite)
Before committing, run the complete test suite:
```sh
go run ./build/ci.go test
```
This includes Ethereum execution-spec tests and all state/block test permutations.

## ✓ 4. Linting
Run style and lint checks:
```sh
go run ./build/ci.go lint
```

## ✓ 5. Generated Code
Ensure all generated files are up to date:
```sh
go run ./build/ci.go check_generate
```
If this fails, run `make devtools` first, then the appropriate `go generate` commands.

## ✓ 6. Dependency Hygiene
Verify no forbidden dependencies were introduced:
```sh
go run ./build/ci.go check_baddeps
```

---

## Validation Report
Once all checks pass, report the results:
- [ ] Formatting passed
- [ ] Build succeeded
- [ ] Tests passed
- [ ] Linting passed
- [ ] Generated code up to date
- [ ] Dependencies clean

**Ready to commit!** Ensure your commit message follows the format:
```
<package(s)>: description
```

Example: `core/vm: fix stack overflow in PUSH instruction`
