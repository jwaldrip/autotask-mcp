# Publishing Guide

This guide explains how to publish the `autotask-mcp` package to npm.

## Prerequisites

1. **npm Account**: Create an account at [npmjs.com](https://www.npmjs.com)
2. **npm CLI Authentication**: Log in to npm from your terminal:
   ```bash
   npm login
   ```
3. **Repository Access**: Ensure you have push access to the GitHub repository

## Pre-Publishing Checklist

Before publishing, ensure:

- [ ] All tests pass: `npm test`
- [ ] Code is linted: `npm run lint`
- [ ] Version is updated in `package.json`
- [ ] CHANGELOG.md is updated with new version changes
- [ ] All changes are committed to git
- [ ] Working directory is clean

## Publishing Steps

### 1. Update Version

Update the version in `package.json` using semantic versioning:

```bash
# For bug fixes
npm version patch

# For new features (backward compatible)
npm version minor

# For breaking changes
npm version major
```

This will:
- Update `package.json` version
- Create a git tag
- Commit the change

### 2. Build the Package

```bash
npm run build
```

### 3. Test the Package Locally

Test the package before publishing:

```bash
# Create a tarball
npm pack

# Test with npx (in a different directory)
npx ./autotask-mcp-X.X.X.tgz
```

Or use `npm link`:

```bash
# In the package directory
npm link

# Test the command
autotask-mcp --help

# Unlink when done
npm unlink -g autotask-mcp
```

### 4. Publish to npm

```bash
# Dry run to see what will be published
npm publish --dry-run

# Publish to npm
npm publish
```

The `prepublishOnly` script will automatically:
1. Run the build
2. Run all tests

### 5. Push Git Tags

```bash
git push origin main --tags
```

### 6. Create GitHub Release

1. Go to [GitHub Releases](https://github.com/jwaldrip/autotask-mcp/releases)
2. Click "Draft a new release"
3. Select the version tag
4. Add release notes (copy from CHANGELOG.md)
5. Publish release

## Automated Publishing with GitHub Actions

For automated publishing on new releases:

1. Add npm token to GitHub secrets:
   - Go to repository Settings > Secrets and variables > Actions
   - Add `NPM_TOKEN` with your npm access token

2. Create `.github/workflows/publish.yml`:

```yaml
name: Publish to npm

on:
  release:
    types: [published]

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '20'
          registry-url: 'https://registry.npmjs.org'
      - run: npm ci
      - run: npm test
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

## Version Management

Follow [Semantic Versioning](https://semver.org/):

- **MAJOR** (1.0.0 → 2.0.0): Breaking changes
- **MINOR** (1.0.0 → 1.1.0): New features, backward compatible
- **PATCH** (1.0.0 → 1.0.1): Bug fixes

## Troubleshooting

### Error: Package name already exists

If you get a naming conflict:
1. Check if the package name is available: `npm view autotask-mcp`
2. Consider using a scoped package: `@yourusername/autotask-mcp`
3. Update `name` in `package.json`

### Error: You must be logged in

```bash
npm login
npm whoami  # Verify login
```

### Error: No permission to publish

Ensure your npm account has publishing rights for this package.

### Error: Tests failed

Fix all failing tests before publishing:
```bash
npm test
```

## Post-Publishing

After publishing:

1. Verify package appears on npm: https://www.npmjs.com/package/autotask-mcp
2. Test installation: `npx autotask-mcp@latest`
3. Update documentation if needed
4. Announce the release

## Useful Commands

```bash
# Check what will be included in package
npm pack --dry-run

# View package info
npm view autotask-mcp

# View all versions
npm view autotask-mcp versions

# Deprecate a version
npm deprecate autotask-mcp@1.0.0 "Please use version 1.0.1"

# Unpublish (within 72 hours only)
npm unpublish autotask-mcp@1.0.0
```

## Support

For issues with publishing:
- npm support: https://www.npmjs.com/support
- GitHub issues: https://github.com/jwaldrip/autotask-mcp/issues
