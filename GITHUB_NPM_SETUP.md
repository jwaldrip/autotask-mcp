# GitHub Actions npm Publishing Setup

## Overview

This repository is configured to automatically publish to npm via GitHub Actions when you push to the `main` branch.

## One-Time Setup

### 1. Generate npm Access Token

Run this command locally:

```bash
npm token create --read-and-write
```

**Important**: Save this token securely! You'll need it for the next step.

### 2. Add NPM_TOKEN to GitHub Secrets

1. Go to repository settings: https://github.com/jwaldrip/autotask-mcp/settings/secrets/actions
2. Click **"New repository secret"**
3. Name: `NPM_TOKEN`
4. Value: Paste the token from step 1
5. Click **"Add secret"**

### 3. Verify Workflow Permissions

Ensure the GitHub token has write permissions:

1. Go to: https://github.com/jwaldrip/autotask-mcp/settings/actions
2. Under "Workflow permissions", select **"Read and write permissions"**
3. Check **"Allow GitHub Actions to create and approve pull requests"**
4. Click **"Save"**

## How It Works

### Automated Release Process

When you push to `main`:

1. **Tests Run**: Lints, builds, and runs all tests
2. **Semantic Release**: Analyzes commit messages to determine version bump
   - `fix:` → Patch version (1.0.1 → 1.0.2)
   - `feat:` → Minor version (1.0.0 → 1.1.0)
   - `BREAKING CHANGE:` → Major version (1.0.0 → 2.0.0)
3. **Version Bump**: Updates package.json and package-lock.json
4. **npm Publish**: Publishes the new version to npm
5. **GitHub Release**: Creates a GitHub release with changelog
6. **Update Changelog**: Commits updated CHANGELOG.md back to repo

### Commit Message Format

Use conventional commits for automatic versioning:

```bash
# Patch release (1.0.1 → 1.0.2)
git commit -m "fix: corrected log output issue"

# Minor release (1.0.0 → 1.1.0)
git commit -m "feat: added new search functionality"

# Major release (1.0.0 → 2.0.0)
git commit -m "feat: redesigned API

BREAKING CHANGE: removed legacy endpoints"
```

### Manual Workflow Trigger

You can also manually trigger a release:

1. Go to: https://github.com/jwaldrip/autotask-mcp/actions/workflows/release.yml
2. Click **"Run workflow"**
3. Select branch: `main`
4. Click **"Run workflow"**

## Development Workflow

### Standard Flow

```bash
# Make changes
git add .
git commit -m "fix: your bug fix description"
git push origin main

# GitHub Actions will automatically:
# 1. Run tests
# 2. Determine version bump from commit message
# 3. Publish to npm
# 4. Create GitHub release
```

### Pre-release Flow

For alpha/beta releases:

```bash
# Create a beta branch
git checkout -b beta

# Make changes and push
git commit -m "feat: experimental feature"
git push origin beta

# This publishes as: autotask-mcp@1.1.0-beta.1
```

## Troubleshooting

### "NPM_TOKEN not found" Error

**Problem**: GitHub Action fails with authentication error

**Solution**:
1. Verify NPM_TOKEN is set in repository secrets
2. Generate a new token if the old one expired
3. Update the secret with the new token

### "Permission denied" Error

**Problem**: GitHub Action can't push commits

**Solution**:
1. Check workflow permissions in repository settings
2. Ensure "Read and write permissions" is enabled

### Release Not Triggered

**Problem**: Pushed to main but no release created

**Possible Causes**:
1. Commit message doesn't follow conventional format (fix:, feat:, etc.)
2. Changes only in files ignored by semantic-release
3. Tests failed (check Actions tab)

**Solution**:
```bash
# Check the workflow run
https://github.com/jwaldrip/autotask-mcp/actions

# Force a release with a proper commit
git commit --allow-empty -m "chore: trigger release"
git push origin main
```

## Skipping CI/CD

To push without triggering a release:

```bash
git commit -m "docs: update README [skip ci]"
git push origin main
```

## Security Notes

- Never commit npm tokens to the repository
- Rotate npm tokens periodically
- Use "Automation" tokens for CI/CD (not user tokens)
- Keep tokens in GitHub Secrets only

## References

- [Semantic Release Documentation](https://semantic-release.gitbook.io/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [GitHub Actions Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
- [npm Tokens](https://docs.npmjs.com/about-access-tokens)
