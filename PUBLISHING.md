# Publishing @nauticalstream/proto

This repository publishes to three package registries from a single Git repo.

## 📝 Before Publishing

1. Update proto definitions in `proto/*/`
2. Run `npm run buf:generate` to regenerate code
3. Update version in all three configs:
   - `package.json` (npm)
   - Git tag (Go)
   - `pyproject.toml` (Python)
4. Commit all changes including generated code

## 📦 Publishing Process

### 1. JavaScript/TypeScript → npm

```bash
# Bump version
npm version patch  # or minor, major

# Publish to npm
npm publish
```

Consumers install:
```bash
npm install @nauticalstream/proto
```

### 2. Go → GitHub (Go Modules)

```bash
# Tag the version
git tag v0.0.2
git push origin v0.0.2
```

Consumers install:
```bash
go get github.com/nauticalstream/proto@v0.0.2
```

### 3. Python → PyPI

```bash
# Update version in pyproject.toml manually first

# Build package
python -m build

# Publish to PyPI
python -m twine upload dist/*
```

Consumers install:
```bash
pip install nauticalstream-proto
```

## 🔄 Complete Release Workflow

```bash
# 1. Update proto files
vim proto/error/v1/error.proto

# 2. Regenerate code
npm run buf:generate

# 3. Update all versions
npm version patch
# Manually update pyproject.toml version to match

# 4. Commit
git add .
git commit -m "chore: release v0.0.2"

# 5. Tag for Go
git tag v0.0.2

# 6. Push
git push origin main
git push origin v0.0.2

# 7. Publish npm
npm publish

# 8. Publish Python
python -m build
python -m twine upload dist/*
```

## 🎯 Version Management

Keep versions in sync across all three packages:
- `package.json` → `"version": "0.0.2"`
- Git tag → `v0.0.2`
- `pyproject.toml` → `version = "0.0.2"`

## 📋 Prerequisites

Install tools:
```bash
# Python publishing
pip install build twine

# npm (already installed)
npm whoami  # Check npm login

# Go (no tools needed, uses Git tags)
```
