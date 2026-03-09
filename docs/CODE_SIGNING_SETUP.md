# Code Signing Setup Guide for GitHub Actions

This guide explains how to obtain and configure code signing certificates for macOS and Windows in the GitHub Actions CI/CD pipeline for TeSHub Desktop.

---

## Table of Contents

1. [Overview](#overview)
2. [macOS Code Signing Setup](#macos-code-signing-setup)
3. [Windows Code Signing Setup](#windows-code-signing-setup)
4. [Adding Secrets to GitHub](#adding-secrets-to-github)
5. [Testing Your Configuration](#testing-your-configuration)
6. [Troubleshooting](#troubleshooting)
7. [Development Without Signing](#development-without-signing)

---

## Overview

### Why Code Sign?

| Platform | Without Signing | With Signing |
|----------|----------------|--------------|
| **macOS** | "App can't be opened" warnings, Gatekeeper blocks | Clean installation, notarized apps trusted |
| **Windows** | SmartScreen "Unknown Publisher" warning | Verified publisher, better user trust |
| **Linux** | No signing required | No signing required |

### Certificate Types

| Platform | Certificate Type | File Format | Validity |
|----------|------------------|-------------|----------|
| macOS | Apple Developer ID Application | `.p12` | 1 year |
| Windows | Code Signing Certificate | `.pfx` | 1-3 years |

### Cost

| Certificate | Approximate Cost |
|-------------|------------------|
| Apple Developer Program | $99/year |
| Windows Code Signing (Sectigo/DigiCert) | $300-600/year |
| EV Code Signing (required for Windows SmartScreen) | $500-850/year |

---

## macOS Code Signing Setup

### Prerequisites

- Apple Developer Program membership ($99/year)
- macOS computer with Xcode installed
- Apple ID with two-factor authentication enabled

### Step 1: Enroll in Apple Developer Program

1. Visit [developer.apple.com](https://developer.apple.com)
2. Click "Account" → "Enroll Now"
3. Choose "Individual" or "Organization"
4. Complete enrollment and payment

### Step 2: Create Developer ID Application Certificate

1. Open **Xcode** on your Mac
2. Go to **Xcode** → **Settings** → **Accounts**
3. Select your Apple ID → **Manage Certificates**
4. Click **+** → **Developer ID Application**
5. Wait for certificate to appear

### Step 3: Export Certificate as .p12

1. Open **Keychain Access** (Applications → Utilities)
2. Select "My Certificates" in the sidebar
3. Find your "Developer ID Application: Your Name (TEAM_ID)" certificate
4. Right-click → **Export "Developer ID Application..."**
5. Choose **.p12** format
6. Save to Desktop as `developer-id.p12`
7. **Set a secure password** (remember this for `CSC_KEY_PASSWORD`)

### Step 4: Get Your Team ID

1. Visit [developer.apple.com/account](https://developer.apple.com/account)
2. Your Team ID is displayed under your organization name
3. It's a 10-character string like: `ABCD1234XY`

Alternatively, run in Terminal:
```bash
security find-identity -v -p codesigning
```

### Step 5: Get Apple ID App-Specific Password

1. Visit [appleid.apple.com](https://appleid.apple.com)
2. Sign in with your Apple ID
3. Go to **Sign-In and Security**
4. Click **App-Specific Passwords**
5. Click **Generate an app-specific password**
6. Enter label: "GitHub Actions"
7. Copy the generated password (format: `xxxx-xxxx-xxxx-xxxx`)

### Step 6: Encode Certificate to Base64

Run in Terminal:
```bash
base64 -i ~/Desktop/developer-id.p12 > ~/Desktop/developer-id.b64
```

To copy to clipboard:
```bash
base64 -i ~/Desktop/developer-id.p12 | pbcopy
```

### Summary: macOS Secrets

| Secret | Value Example |
|--------|---------------|
| `CSC_LINK` | Base64 string from `.p12` file |
| `CSC_KEY_PASSWORD` | The password you set when exporting |
| `APPLE_ID` | your-email@example.com |
| `APPLE_APP_SPECIFIC_PASSWORD` | `abcd-efgh-ijkl-mnop` |
| `APPLE_TEAM_ID` | `ABCD1234XY` |

---

## Windows Code Signing Setup

### Prerequisites

- Business registration documents (for EV certificates)
- Two-factor authentication hardware token (for EV certificates)

### Step 1: Purchase Code Signing Certificate

**Recommended Providers:**

| Provider | Product | Price | Link |
|----------|---------|-------|------|
| Sectigo | Standard Code Signing | ~$300/year | [sectigo.com](https://sectigo.com) |
| DigiCert | Code Signing | ~$400/year | [digicert.com](https://digicert.com) |
| Certum | Code Signing | ~$250/year | [certum.eu](https://certum.eu) |

**For Windows SmartScreen (Recommended):**
- Purchase **EV (Extended Validation) Code Signing**
- Required for immediate SmartScreen reputation
- Costs ~$500-850/year
- Requires hardware token for key storage

### Step 2: Generate CSR (Certificate Signing Request)

**Using PowerShell (Windows):**

```powershell
# Create certificate request
$req = New-SelfSignedCertificate -DnsName "Your Company" -CertStoreLocation "cert:\CurrentUser\My" -KeyLength 2048 -KeyUsage DigitalSignature -KeyUsage KeyEncipherment

# Export public key for CA
certreq -new request.inf request.csr
```

**Using OpenSSL (Cross-platform):**

```bash
# Generate private key
openssl genrsa -out private.key 2048

# Generate CSR
openssl req -new -key private.key -out certificate.csr \
  -subj "/C=US/ST=State/L=City/O=Company/CN=Your Company"
```

### Step 3: Submit CSR to Certificate Authority

1. Log in to your certificate provider's portal
2. Submit the `.csr` file
3. Complete domain validation (email/phone verification)
4. Wait for certificate issuance (1-5 business days)

### Step 4: Download and Install Certificate

1. Download the issued certificate (`.crt` or `.cer`)
2. Download intermediate certificates (CA bundle)
3. Install both certificates:

**Windows:**
```powershell
# Import certificate with private key
certutil -p "password" -importpfx certificate.pfx
```

**OpenSSL (create .pfx):**
```bash
openssl pkcs12 -export -out certificate.pfx \
  -inkey private.key \
  -in certificate.crt \
  -certfile intermediate.crt
```

### Step 5: Export as .pfx with Password

**Using Windows Certificate Manager:**

1. Press `Win + R`, type `certmgr.msc`
2. Navigate to **Personal** → **Certificates**
3. Right-click your code signing certificate
4. **All Tasks** → **Export**
5. Choose **"Yes, export the private key"**
6. Select **.PFX** format
7. Set a secure password
8. Save as `code-signing.pfx`

### Step 6: Encode Certificate to Base64

**PowerShell:**
```powershell
$bytes = [IO.File]::ReadAllBytes("C:\path\to\code-signing.pfx")
$base64 = [Convert]::ToBase64String($bytes)
$base64 | Set-Clipboard
```

**Using certutil:**
```cmd
certutil -encode code-signing.pfx code-signing.b64
```

Open `code-signing.b64` and remove the header/footer lines:
```
-----BEGIN CERTIFICATE-----
(remove this line)
MIIF...base64 content...
(remove this line)
-----END CERTIFICATE-----
```

### Summary: Windows Secrets

| Secret | Value Example |
|--------|---------------|
| `WIN_CSC_LINK` | Base64 string (no header/footer) |
| `WIN_CSC_KEY_PASSWORD` | The password you set for .pfx |

---

## Adding Secrets to GitHub

### Step-by-Step

1. Navigate to your GitHub repository
2. Click **Settings** (top tab)
3. Click **Secrets and variables** → **Actions** (left sidebar)
4. Click **New repository secret**

### Add Each Secret

| Name | Value | Description |
|------|-------|-------------|
| `CSC_LINK` | Base64 from macOS .p12 | macOS signing certificate |
| `CSC_KEY_PASSWORD` | Your .p12 password | macOS certificate password |
| `APPLE_ID` | your@email.com | Apple ID email |
| `APPLE_APP_SPECIFIC_PASSWORD` | xxxx-xxxx-xxxx-xxxx | App-specific password |
| `APPLE_TEAM_ID` | ABCD1234XY | Apple Team ID |
| `WIN_CSC_LINK` | Base64 from Windows .pfx | Windows signing certificate |
| `WIN_CSC_KEY_PASSWORD` | Your .pfx password | Windows certificate password |

5. For each secret:
   - Enter the **Name** exactly as shown
   - Paste the **Value**
   - Click **Add secret**

### Verify Secrets Added

You should see 7 secrets in the list:

```
✓ CSC_LINK
✓ CSC_KEY_PASSWORD
✓ APPLE_ID
✓ APPLE_APP_SPECIFIC_PASSWORD
✓ APPLE_TEAM_ID
✓ WIN_CSC_LINK
✓ WIN_CSC_KEY_PASSWORD
```

---

## Testing Your Configuration

### Trigger a Test Build

1. Make a small change to any file:
```bash
echo "# Test build" >> README.md
git add README.md
git commit -m "ci: test code signing configuration"
git push
```

2. Go to GitHub → **Actions** tab
3. Click on the **"Build & Release"** workflow
4. Wait for builds to complete

### Verify Signing Status

**macOS:**
1. Download the `.dmg` from artifacts
2. Install the app
3. Open Terminal and run:
```bash
codesign -dv --verbose=4 /Applications/ElevenLabs\ Desktop.app
spctl -a -v /Applications/ElevenLabs\ Desktop.app
```
4. Expected output: `accepted` and `source=Notarized`

**Windows:**
1. Download the `.exe` from artifacts
2. Right-click → **Properties** → **Digital Signatures**
3. You should see your company name as signer
4. Run in PowerShell:
```powershell
Get-AuthenticodeSignature -FilePath "ElevenLabs-Setup.exe"
```
5. Expected: `Status: Valid`

---

## Troubleshooting

### macOS Issues

#### "No valid signing identity found"

**Cause:** Certificate not properly configured

**Fix:**
1. Verify `CSC_LINK` contains valid base64
2. Check certificate hasn't expired
3. Ensure `CSC_KEY_PASSWORD` is correct

#### "Notarization failed"

**Cause:** Apple credentials incorrect or app has issues

**Fix:**
1. Verify `APPLE_ID` and `APPLE_APP_SPECIFIC_PASSWORD`
2. Check Team ID matches your developer account
3. Ensure app doesn't use private APIs

#### "User canceled the operation"

**Cause:** Keychain access issues in CI

**Fix:**
```yaml
# Add to build.yml macOS job
- name: Setup keychain
  run: |
    security create-keychain -p "" build.keychain
    security default-keychain -s build.keychain
    security unlock-keychain -p "" build.keychain
```

### Windows Issues

#### "SignerSign() failed"

**Cause:** Invalid certificate or password

**Fix:**
1. Verify `WIN_CSC_LINK` base64 is correct (no header/footer)
2. Check `WIN_CSC_KEY_PASSWORD` is correct
3. Ensure .pfx includes private key

#### "Certificate chain not found"

**Cause:** Missing intermediate certificates

**Fix:**
1. Re-export .pfx including certificate chain
2. Include intermediate certificates in the .pfx

### General Issues

#### Secrets Not Available

**Cause:** Secrets don't work in PRs from forks

**Fix:** Push to main branch or use a non-forked branch

#### Build Fails After Adding Secrets

**Cause:** Base64 encoding issues

**Fix:**
1. Ensure no newlines in base64 string
2. Remove `-----BEGIN-----` and `-----END-----` lines
3. Re-encode if necessary

---

## Development Without Signing

### Option 1: Disable Signing in CI

Edit `.github/workflows/build.yml`:

```yaml
# macOS job
- name: Build macOS apps
  env:
    CSC_IDENTITY_AUTO_DISCOVERY: 'false'
    # Remove APPLE_* secrets
  run: bun run electron:build:mac
```

```yaml
# Windows job
- name: Build Windows installer
  # Remove WIN_CSC_* env vars
  run: bun run electron:build:win
```

### Option 2: Conditional Signing

Sign only for release builds:

```yaml
- name: Build macOS apps
  env:
    CSC_IDENTITY_AUTO_DISCOVERY: ${{ startsWith(github.ref, 'refs/tags/v') && 'true' || 'false' }}
    CSC_KEY_PASSWORD: ${{ secrets.CSC_KEY_PASSWORD }}
    CSC_LINK: ${{ secrets.CSC_LINK }}
    # ... only for tagged releases
  run: bun run electron:build:mac
```

### Expected Warnings (Unsigned Builds)

**macOS:**
- "App can't be opened because it's from an unidentified developer"
- Users must right-click → Open to bypass

**Windows:**
- "Windows protected your PC" SmartScreen warning
- Users must click "More info" → "Run anyway"

---

## Certificate Renewal

### macOS

- Certificates expire after 1 year
- Apple sends renewal reminder email
- Repeat export process and update `CSC_LINK` secret

### Windows

- Certificates expire after 1-3 years
- Set calendar reminder 30 days before expiry
- Purchase renewal and update `WIN_CSC_LINK` secret

### Best Practices

1. **Document expiry dates** in your team wiki
2. **Set calendar reminders** 30 days before expiry
3. **Test new certificates** in a separate branch first
4. **Rotate passwords** annually

---

## Quick Reference Commands

### macOS

```bash
# View certificate info
security find-identity -v -p codesigning

# Export certificate
security export -k ~/Library/Keychains/login.keychain-db \
  -t certs -f x509 -p codesigning -o certificate.crt

# Encode to base64
base64 -i certificate.p12 | pbcopy
```

### Windows

```powershell
# View certificates
Get-ChildItem -Path Cert:\CurrentUser\My -CodeSigningCert

# Export to .pfx
Export-PfxCertificate -Cert Cert:\CurrentUser\My\THUMBPRINT \
  -FilePath certificate.pfx -Password (ConvertTo-SecureString -String "password" -Force -AsPlainText)

# Encode to base64
[Convert]::ToBase64String([IO.File]::ReadAllBytes("certificate.pfx")) | Set-Clipboard
```

---

## Resources

- [electron-builder Code Signing Docs](https://www.electron.build/code-signing)
- [Apple Developer Program](https://developer.apple.com/programs/)
- [GitHub Actions Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
- [electron-builder NSIS Options](https://www.electron.build/configuration/nsis)
- [Apple Notarization Guide](https://developer.apple.com/documentation/security/notarizing_macos_software_before_distribution)

---

## Support

If you encounter issues:

1. Check the [Troubleshooting](#troubleshooting) section
2. Review GitHub Actions workflow logs
3. Test locally with `bun run electron:build:[platform]`
4. Consult electron-builder documentation
