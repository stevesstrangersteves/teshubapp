# GitHub Actions Quick Start Guide

Get your TeSHub Desktop app building and releasing in minutes with this quick start guide.

---

## Table of Contents

1. [5-Minute Setup (No Signing)](#5-minute-setup-no-signing)
2. [Full Setup (With Code Signing)](#full-setup-with-code-signing)
3. [First Release](#first-release)
4. [Common Issues](#common-issues)

---

## 5-Minute Setup (No Signing)

Get builds running immediately without code signing certificates.

### Step 1: Verify Workflow Files

Ensure these files exist:

```bash
ls -la .github/workflows/
# Should show:
# - build.yml
# - ci.yml
```

### Step 2: Disable Code Signing

Edit `.github/workflows/build.yml`:

```yaml
# macOS job - change this line
- name: Build macOS apps
  env:
    CSC_IDENTITY_AUTO_DISCOVERY: 'false'  # ← Set to 'false'
    # Comment out or remove these:
    # CSC_KEY_PASSWORD: ${{ secrets.CSC_KEY_PASSWORD }}
    # CSC_LINK: ${{ secrets.CSC_LINK }}
    # APPLE_ID: ${{ secrets.APPLE_ID }}
    # APPLE_APP_SPECIFIC_PASSWORD: ${{ secrets.APPLE_APP_SPECIFIC_PASSWORD }}
    # APPLE_TEAM_ID: ${{ secrets.APPLE_TEAM_ID }}
  run: bun run electron:build:mac

# Windows job - remove these env vars
- name: Build Windows installer
  # Comment out or remove:
  # WIN_CSC_LINK: ${{ secrets.WIN_CSC_LINK }}
  # WIN_CSC_KEY_PASSWORD: ${{ secrets.WIN_CSC_KEY_PASSWORD }}
  run: bun run electron:build:win
```

### Step 3: Push to GitHub

```bash
git add .
git commit -m "ci: add GitHub Actions workflows"
git push origin main
```

### Step 4: Watch the Build

1. Go to your repository on GitHub
2. Click **Actions** tab
3. Watch the "Build & Release" workflow run
4. Wait ~25 minutes for all platforms

### Step 5: Download Artifacts

1. Click on the completed workflow run
2. Scroll to **Artifacts** section
3. Download your platform:
   - `macos-build` → `.dmg` file
   - `windows-build` → `.exe` installer
   - `linux-build` → `.AppImage` or `.deb`

### Expected Warnings (Unsigned Builds)

**macOS:**
```
"TeSHub Desktop" can't be opened because
Apple cannot check it for malicious software.
```

**Fix:** Right-click app → Open → Click "Open" anyway

**Windows:**
```
Windows protected your PC
Microsoft Defender SmartScreen recognized this as an unrecognized app
```

**Fix:** Click "More info" → "Run anyway"

---

## Full Setup (With Code Signing)

For production-ready builds with proper signing.

### Prerequisites

- Apple Developer Program membership ($99/year) - for macOS
- Code Signing Certificate ($300-600/year) - for Windows
- 30-60 minutes for setup

### Step 1: Obtain macOS Certificate

See [CODE_SIGNING_SETUP.md](./CODE_SIGNING_SETUP.md#macos-code-signing-setup) for detailed instructions.

**Quick summary:**
1. Enroll in [Apple Developer Program](https://developer.apple.com/programs/)
2. Create Developer ID Application certificate in Xcode
3. Export as `.p12` from Keychain Access
4. Get App-Specific Password from appleid.apple.com
5. Encode to base64: `base64 -i certificate.p12 | pbcopy`

### Step 2: Obtain Windows Certificate

See [CODE_SIGNING_SETUP.md](./CODE_SIGNING_SETUP.md#windows-code-signing-setup) for detailed instructions.

**Quick summary:**
1. Purchase from [Sectigo](https://sectigo.com) or [DigiCert](https://digicert.com)
2. Generate CSR and submit to CA
3. Receive and install certificate
4. Export as `.pfx` with password
5. Encode to base64 using PowerShell

### Step 3: Add Secrets to GitHub

1. Go to repository **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Add these 7 secrets:

| Name | Value | Example |
|------|-------|---------|
| `CSC_LINK` | Base64 from macOS .p12 | `MII...base64 content...` |
| `CSC_KEY_PASSWORD` | Your .p12 password | `MySecurePassword123` |
| `APPLE_ID` | Apple ID email | `dev@company.com` |
| `APPLE_APP_SPECIFIC_PASSWORD` | App-specific password | `abcd-efgh-ijkl-mnop` |
| `APPLE_TEAM_ID` | 10-char Team ID | `ABCD1234XY` |
| `WIN_CSC_LINK` | Base64 from Windows .pfx | `MII...base64 content...` |
| `WIN_CSC_KEY_PASSWORD` | Your .pfx password | `MySecurePassword456` |

### Step 4: Verify Configuration

```bash
# Make a small change to trigger build
echo "# Build test" >> README.md
git add README.md
git commit -m "ci: test code signing configuration"
git push origin main
```

### Step 5: Verify Signed Builds

**macOS:**
```bash
# After downloading and installing
codesign -dv --verbose=4 /Applications/ElevenLabs\ Desktop.app
# Should show: "Team ID: ABCD1234XY"

spctl -a -v /Applications/ElevenLabs\ Desktop.app
# Should show: "accepted"
```

**Windows:**
```powershell
# Right-click .exe → Properties → Digital Signatures
# Or run:
Get-AuthenticodeSignature -FilePath "ElevenLabs-Setup.exe"
# Should show: "Status: Valid"
```

---

## First Release

### Step 1: Update Version

Edit `package.json`:

```json
{
  "name": "teshub-desktop",
  "version": "1.0.0",  ← Update this
  ...
}
```

### Step 2: Commit and Tag

```bash
git add package.json
git commit -m "release: v1.0.0"
git tag v1.0.0
git push origin main
git push origin v1.0.0  ← This triggers the release!
```

### Step 3: Watch Release Process

1. Go to **Actions** tab
2. Click on "Build & Release" workflow
3. Wait for all builds (~25 minutes)
4. Release job automatically creates GitHub Release

### Step 4: Verify Release

1. Go to **Releases** section (right sidebar)
2. Click on `v1.0.0`
3. Verify all assets are attached:
   - `.dmg` (macOS Intel + Apple Silicon)
   - `.exe` (Windows)
   - `.AppImage` (Linux)
   - `.deb` (Linux Debian/Ubuntu)

### Release Notes

GitHub automatically generates release notes from commits. To customize:

1. Go to the draft release
2. Edit the release notes
3. Add installation instructions
4. Publish release

---

## Common Issues

### Issue: "No matching signing identity"

**Platform:** macOS

**Cause:** Certificate not properly configured

**Fix:**
```bash
# Verify secret is set correctly
# Re-export certificate and update CSC_LINK secret
base64 -i ~/Desktop/developer-id.p12 | pbcopy
```

### Issue: "SmartScreen warning"

**Platform:** Windows

**Cause:** No code signing certificate

**Fix:**
1. Purchase code signing certificate
2. Add `WIN_CSC_LINK` and `WIN_CSC_KEY_PASSWORD` secrets
3. Rebuild

**Temporary workaround:** Users click "More info" → "Run anyway"

### Issue: "Artifact not found"

**Platform:** Any

**Cause:** Build failed or retention expired

**Fix:**
1. Check workflow logs for build errors
2. Re-run workflow if retention expired (14 days)
3. For releases, artifacts are permanently attached

### Issue: "Secrets available only on main branch"

**Platform:** Any

**Cause:** Secrets don't work in PRs from forks

**Fix:** Push to main branch or use a non-forked branch

### Issue: Build takes too long

**Cause:** Cache miss or slow runners

**Fix:**
```bash
# Ensure bun.lock is committed
git add bun.lock
git commit -m "chore: commit lockfile"
git push

# Future builds will use cache
```

### Issue: "Concurrent job limit reached"

**Cause:** Too many parallel workflows

**Fix:**
1. Cancel unnecessary runs in Actions tab
2. Wait for other runs to complete
3. Upgrade to GitHub Teams for more concurrency

---

## Checklist

### Initial Setup
- [ ] Workflow files in `.github/workflows/`
- [ ] `bun.lock` committed
- [ ] Build scripts work locally

### For Unsigned Builds (Development)
- [ ] `CSC_IDENTITY_AUTO_DISCOVERY: 'false'` in build.yml
- [ ] No secrets required

### For Signed Builds (Production)
- [ ] Apple Developer Program membership
- [ ] macOS `.p12` certificate exported
- [ ] Windows `.pfx` certificate purchased
- [ ] All 7 secrets added to GitHub
- [ ] Test build completed successfully

### For First Release
- [ ] Version updated in `package.json`
- [ ] Git tag created and pushed
- [ ] GitHub Release created automatically
- [ ] All platform artifacts attached
- [ ] Release notes reviewed

---

## Next Steps

After successful setup:

1. **Set up automatic releases:**
   - Use tools like `release-please` or `semantic-release`
   - Automate version bumps and changelog

2. **Add testing:**
   - Write unit tests
   - Add E2E tests with Playwright
   - Run tests in CI before builds

3. **Configure notifications:**
   - Set up Slack/Discord webhooks
   - Email notifications for failures

4. **Optimize build times:**
   - Use self-hosted runners for frequent builds
   - Enable dependency caching

5. **Distribute beyond GitHub:**
   - Publish to Mac App Store
   - Submit to Microsoft Store
   - Publish to Snap Store (Linux)

---

## Resources

### Documentation
- [Code Signing Setup](./CODE_SIGNING_SETUP.md) - Detailed certificate guide
- [Pipeline Flow](./CI_CD_PIPELINE_FLOW.md) - Visual workflow diagrams
- [Workflow README](../.github/workflows/README.md) - Configuration reference

### External Links
- [GitHub Actions Docs](https://docs.github.com/en/actions)
- [electron-builder Code Signing](https://www.electron.build/code-signing)
- [Apple Developer Program](https://developer.apple.com/programs/)
- [Sectigo Code Signing](https://sectigo.com/code-signing-certificates)

### Support
- Check workflow logs in GitHub Actions
- Review troubleshooting sections
- Open an issue for workflow bugs

---

## Quick Commands Reference

```bash
# Trigger test build
git commit --allow-empty -m "ci: test build"
git push

# Create release
git tag v1.0.0
git push origin v1.0.0

# View workflow runs (GitHub CLI)
gh run list
gh run view --log

# Cancel running workflow
gh run cancel <run-id>

# Re-run failed workflow
gh run rerun <run-id>
```

---

## Summary

| Task | Time | Complexity |
|------|------|------------|
| Basic setup (no signing) | 5 minutes | ⭐ Easy |
| Full setup (with signing) | 30-60 minutes | ⭐⭐⭐ Medium |
| First release | 5 minutes | ⭐ Easy |
| Ongoing maintenance | Minimal | ⭐ Easy |

You're all set! Your app will now build automatically for all platforms on every push and create releases on version tags.
