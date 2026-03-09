# Release Management Guide

## Overview

This guide explains how releases are created and managed in the TeSHub Desktop project.

## Release Strategy

### What Gets Included in Releases?

**✅ Included:**
- Application binaries (macOS, Windows, Linux)
- Checksums (SHA256)
- Release notes

**❌ NOT Included:**
- Source code archives
- Development files
- Dependencies

## Automatic Source Code Removal

GitHub automatically creates `Source code (zip)` and `Source code (tar.gz)` assets for every release.

**A GitHub Actions workflow automatically removes these** to keep releases clean and focused on application binaries only.

### How It Works

1. **Release is published** → GitHub creates default source code archives
2. **Cleanup workflow triggers** → Detects source code assets
3. **Assets are deleted** → Only app builds remain
4. **Clean release** → Users see only what they need

**Workflow Location**: `.github/workflows/cleanup-release.yml`

## Creating a Release

### Method 1: GitHub Web UI (Automatic Cleanup)

1. Go to **Releases** page
2. Click **"Draft a new release"**
3. Set **Tag version** (e.g., `v1.0.0`)
4. Add **Release title** and **description**
5. Upload application binaries
6. Click **"Publish release"**
7. ✅ Workflow automatically removes source code assets

### Method 2: GitHub CLI

```bash
# Create and publish release
gh release create v1.0.0 \
  --title "TeSHub Desktop v1.0.0" \
  --notes "Release notes here" \
  TeSHub.Desktop-*.zip
```

### Method 3: Git Tags + GitHub Actions

```bash
# Create and push tag
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0

# GitHub Actions will:
# 1. Build application binaries
# 2. Create release
# 3. Cleanup workflow removes source code
```

## Release Naming Convention

Use semantic versioning:
- `v1.0.0` - Major release
- `v1.0.1` - Patch/bug fix
- `v1.1.0` - Minor release (features)

## Checklist Before Release

- [ ] All tests pass
- [ ] Code is merged to main
- [ ] Documentation is updated
- [ ] Version number is updated in code
- [ ] Changelog is updated
- [ ] Application builds successfully
- [ ] No sensitive data in commit history

## After Release

1. **GitHub creates release** with app binaries
2. **Cleanup workflow runs** (removes source code)
3. **Users can download** application binaries
4. **Verify** that only app builds appear

## Troubleshooting

### Source Code Not Being Removed?

If source code assets remain after 1 hour:

1. Check workflow status in **Actions** tab
2. Verify workflow has `contents: write` permission
3. Check release is `published` (not draft)
4. Manually delete if needed:

```bash
# Get asset ID
gh api repos/OWNER/REPO/releases/tags/v1.0.0 \
  --jq '.assets[] | {name, id}'

# Delete specific asset
gh api -X DELETE repos/OWNER/REPO/releases/assets/ASSET_ID
```

### Files Not Uploading?

1. Verify file size is under GitHub's limit (2GB)
2. Check network connection
3. Try uploading via GitHub CLI:

```bash
gh release upload v1.0.0 ./app.zip
```

## FAQ

**Q: Why remove source code from releases?**
A: Reduces release bloat and confusion. Source code is already on GitHub.

**Q: Can users get source code?**
A: Yes, from the **Code** tab or by cloning the repository.

**Q: What if I want to include source code?**
A: Disable the cleanup workflow or manually upload it.

**Q: How often can I release?**
A: As often as needed. Each tag creates a new release.

## References

- [GitHub Releases Documentation](https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Semantic Versioning](https://semver.org/)

---

**Last Updated**: March 9, 2026
