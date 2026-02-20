# Publishing @nauticalstream/proto

This repository uses **semantic-release** for fully automated versioning and publishing.

## 🤖 How It Works

Every push to `main` branch automatically:

1. Analyzes commit messages since last release
2. Determines version bump (major/minor/patch)
3. Generates proto files with Buf
4. Updates `package.json` version
5. Creates `CHANGELOG.md`
6. Creates Git tag
7. Publishes to npm with provenance
8. Creates GitHub release

**No manual versioning needed!** Just use proper commit messages.

## 📝 Commit Message Format

Use [Conventional Commits](https://www.conventionalcommits.org/):

```bash
# Patch release (0.0.1 → 0.0.2)
fix: correct error code mapping
fix(auth): handle empty token gracefully

# Minor release (0.0.1 → 0.1.0)
feat: add new payment event types
feat(platform): introduce device linking proto

# Major release (0.0.1 → 1.0.0) - BREAKING CHANGE
feat!: redesign error response structure

# Or with footer:
feat: add new field to User message

BREAKING CHANGE: removed deprecated `legacy_id` field

# No release (documentation, chores, etc.)
docs: update README
chore: update dependencies
ci: fix workflow permissions
```

## 🚀 Publishing Workflow

1. **Make changes to proto files**

   ```bash
   vim proto/error/v1/error.proto
   ```

2. **Commit with conventional message**

   ```bash
   git add .
   git commit -m "feat: add retry error codes"
   ```

3. **Push to main**

   ```bash
   git push origin main
   ```

4. **Wait for automatic release**
   - GitHub Action runs
   - Version is bumped automatically
   - Package published to npm
   - GitHub release created

## 📋 Version Bump Rules

| Commit Type                    | Example                          | Version Impact |
| ------------------------------ | -------------------------------- | -------------- |
| `fix:`                         | `fix: correct enum value`        | Patch (0.0.X)  |
| `feat:`                        | `feat: add new message type`     | Minor (0.X.0)  |
| `feat!:` or `BREAKING CHANGE:` | `feat!: remove deprecated field` | Major (X.0.0)  |
| `docs:`, `chore:`, `ci:`       | `docs: update examples`          | No release     |

## 📦 Manual Publishing (if needed)

If you need to publish manually:

```bash
# Generate proto files
npm run buf:generate

# Publish
npm publish
```

## 📋 Prerequisites

Install tools:

```bash
# Python publishing
pip install build twine

# npm (already installed)
npm whoami  # Check npm login

# Go (no tools needed, uses Git tags)
```

## 🤖 Automated Publishing (GitHub Actions)

### Setup npm CI/CD with Granular Access Token

**Why Granular Tokens?** npm recommends granular access tokens for CI/CD instead of classic automation tokens because they:

- Can be scoped to specific packages only
- Have configurable expiration dates
- Support IP address restrictions
- Enable package provenance verification

**Setup Steps:**

1. **Create Granular Access Token**

   Visit: https://www.npmjs.com/settings/[your-username]/tokens
   - Click "Generate New Token" → "Granular Access Token"
   - **Token name**: `nauticalstream-proto-ci`
   - **Expiration**: 1 year (or preferred duration)
   - **Packages and scopes**:
     - Permissions: "Read and write"
     - Packages: Select `@nauticalstream/proto` (or "All packages" if not yet published)
   - **Organizations**: Select organizations if applicable
   - (Optional) **IP Allowlist**: Add GitHub Actions IPs for extra security

   Copy the generated token (won't be shown again)

2. **Add Token to GitHub Secrets**

   ```bash
   gh secret set NPM_TOKEN --repo nauticalstream/proto
   # Paste your granular access token when prompted
   ```

3. **Verify Setup**
   ```bash
   gh secret list --repo nauticalstream/proto
   ```

**Publishing**: Push a Git tag to trigger automated publishing:

```bash
npm version patch && git push --follow-tags
```

The GitHub Action will automatically:

- Generate proto files with Buf
- Publish to npm with provenance
- Verify package integrity with OIDC
