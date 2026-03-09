# Pro Plan Licensing System

Complete documentation for setting up, testing, and managing the Pro Plan licensing system in the TeSHub Desktop App.

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Setup Guide](#setup-guide)
- [Generating License Keys](#generating-license-keys)
- [Testing the License System](#testing-the-license-system)
- [Troubleshooting](#troubleshooting)

---

## Overview

The Pro Plan Licensing System enables feature gating and license management for the TeSHub Desktop App. It uses ECDSA P-256 cryptographic signatures for secure license key verification.

### Features

- **License Tiers**: FREE, PRO, TRIAL
- **Feature Gating**: Control access to premium features (Kimi AI, unlimited keys, etc.)
- **Key Limits**: FREE plan limited to 10 API keys, PRO has unlimited
- **Encrypted Storage**: License data stored in encrypted local storage
- **Client-Side Verification**: Public key embedded for offline verification

### Plan Comparison

| Feature | FREE | PRO | TRIAL |
|---------|------|-----|-------|
| Max API Keys | 10 | Unlimited | 10 |
| Kimi AI Access | ❌ | ✅ | Limited |
| Parallel TTS | ❌ | ✅ | ❌ |
| Advanced Optimization | ❌ | ✅ | ❌ |
| Priority Support | ❌ | ✅ | ❌ |

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                     TeSHub Desktop App                       │
├─────────────────────────────────────────────────────────────────┤
│  Renderer Process (React)                                        │
│  ┌─────────────────┐  ┌──────────────────┐  ┌────────────────┐ │
│  │ LicenseContext  │  │  useLicense()    │  │ ProPlanDialog  │ │
│  └────────┬────────┘  └────────┬─────────┘  └────────────────┘ │
│           │                    │                                 │
│           └──────────┬─────────┘                                 │
│                      │                                            │
│              window.elevenlabs.license.*                         │
├──────────────────────┼─────────────────────────────────────────┤
│                      │  IPC Handlers                            │
│  Main Process        │                                          │
│  ┌──────────────────▼─────────────────────────────────────────┐│
│  │                    licenseManager                           ││
│  │  - getState()     - activate()     - hasFeature()          ││
│  │  - deactivate()   - verify()       - canAddKeys()          ││
│  └──────────────────┬─────────────────────────────────────────┘│
│                      │                                          │
│              ┌───────▼────────┐                                │
│              │ license.dat    │ (encrypted storage)            │
│              └────────────────┘                                │
├─────────────────────────────────────────────────────────────────┤
│  CLI Tool (Development)                                         │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │  license-keygen.ts  - Generate signed license keys          ││
│  └─────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────┘
```

---

## Setup Guide

### Prerequisites

- Node.js 18+ and npm
- TypeScript installed globally (`npm install -g typescript`)
- ts-node installed globally (`npm install -g ts-node`)

### 1. Install Dependencies

```bash
cd elevenlabs-app
npm install
```

### 2. Build the Application

```bash
# Build TypeScript
npm run build

# Or for development with hot reload
npm run dev
```

### 3. Verify License Components

Ensure all license-related files exist:

```bash
# Backend files
ls -la electron/license-manager.ts
ls -la electron/license-keygen.ts
ls -la electron/vieneu-tts-bridge.ts

# Frontend files
ls -la src/hooks/useLicense.ts
ls -la src/context/LicenseContext.tsx
ls -la src/components/ProPlanDialog.tsx
ls -la src/components/ProOnlyWrapper.tsx

# Type definitions
ls -la src/types/electron.d.ts
```

### 4. Check IPC Handlers

Verify license IPC handlers are registered in `electron/main.ts`:

```typescript
// Should include these handlers:
ipcMain.handle('license:get-state', ...)
ipcMain.handle('license:activate', ...)
ipcMain.handle('license:deactivate', ...)
ipcMain.handle('license:verify', ...)
ipcMain.handle('license:has-feature', ...)
ipcMain.handle('license:can-add-keys', ...)
ipcMain.handle('license:has-kimi-access', ...)
ipcMain.handle('license:get-upgrade-info', ...)
ipcMain.handle('license:generate', ...) // Dev only
```

---

## Generating License Keys

### Using the CLI Tool (Development)

The license key generator is located at `electron/license-keygen.ts`.

#### Generate a PRO License Key

```bash
npx ts-node electron/license-keygen.ts PRO user123
```

Output:
```
License Key: EP-v1-PRO-xyz123-abc456def789...
```

#### Generate a TRIAL License Key (Expires)

```bash
# Expires in 7 days (Unix timestamp)
npx ts-node electron/license-keygen.ts TRIAL user456 --expires $(node -e "console.log(Math.floor((Date.now() + 7*24*60*60*1000) / 1000))")
```

#### Generate a FREE License Key

```bash
npx ts-node electron/license-keygen.ts FREE user789
```

### CLI Options

| Option | Description | Required |
|--------|-------------|----------|
| `plan` | License tier: FREE, PRO, TRIAL | Yes |
| `userId` | User identifier | No |
| `--expires` | Expiration Unix timestamp | No (TRIAL only) |

### Example: Generate Keys for Testing

```bash
#!/bin/bash

# Generate test keys
echo "=== Generating Test License Keys ==="

# PRO key (full access)
echo "PRO Key:"
npx ts-node electron/license-keygen.ts PRO test-user-pro

# TRIAL key (expires in 24 hours)
echo "TRIAL Key (24h):"
EXPIRES=$(node -e "console.log(Math.floor((Date.now() + 24*60*60*1000) / 1000))")
npx ts-node electron/license-keygen.ts TRIAL test-user-trial --expires $EXPIRES

# FREE key (baseline)
echo "FREE Key:"
npx ts-node electron/license-keygen.ts FREE test-user-free
```

### Understanding License Key Format

```
EP-v1-PLAN-TIMESTAMP-SIGNATURE

Example:
EP-v1-PRO-rg5x2k-aB3cD4eF5gH6iJ7kL8mN9oP0qR1sT2uV3wX4yZ5...

Components:
- EP: License prefix (ElevenLabs Pro)
- v1: Key version
- PLAN: Plan type (FREE, PRO, TRIAL)
- TIMESTAMP: Issue time in Base36
- SIGNATURE: ECDSA P-256 signature in Base64URL
```

---

## Testing the License System

### 1. Test License Activation

Open the app and use the browser DevTools console:

```javascript
// Check current license state
window.elevenlabs.license.getState()
// Returns: { isActive: false, plan: 'FREE', features: [...], maxKeys: 10 }

// Activate a license
window.elevenlabs.license.activate('EP-v1-PRO-xyz123-abc...')
// Returns: { success: true }

// Verify activation
window.elevenlabs.license.getState()
// Returns: { isActive: true, plan: 'PRO', features: [...], maxKeys: 999999 }
```

### 2. Test Feature Gating

```javascript
// Check if feature is available
window.elevenlabs.license.hasFeature('kimi-ai')
// PRO: true, FREE: false

// Check Kimi AI access
window.elevenlabs.license.hasKimiAccess()
// PRO: true, FREE: false

// Check key limit
window.elevenlabs.license.canAddKeys(5)
// FREE with 5 keys: true
// FREE with 10 keys: false
```

### 3. Test UI Components

#### Key Manager (10 Key Limit)

1. Navigate to **API Keys** page
2. Try to add 11 API keys as FREE user
3. Should show upgrade dialog on 11th key attempt

#### Kimi Key Manager

1. Navigate to **Kimi AI Keys** page
2. Try to add a Kimi token as FREE user
3. Should show Pro Plan upgrade dialog

### 4. Test License Persistence

```javascript
// Activate license
await window.elevenlabs.license.activate('EP-v1-PRO-xyz...')

// Verify it persists after reload
location.reload()

// After reload, check state
window.elevenlabs.license.getState()
// Should still show active PRO license
```

### 5. Test License Deactivation

```javascript
// Deactivate current license
await window.elevenlabs.license.deactivate()

// Verify state reset
window.elevenlabs.license.getState()
// Returns: { isActive: false, plan: 'FREE', ... }
```

### 6. Test License Verification

```javascript
// Verify a license key without activating
window.elevenlabs.license.verify('EP-v1-PRO-xyz...')
// Returns: { valid: true } or { valid: false, error: '...' }
```

---

## Automated Testing Script

Create a test script at `electron/test-license.js`:

```javascript
const { licenseManager, generateLicenseKey, verifyLicenseKey } = require('./license-manager')

console.log('=== License System Test Suite ===\n')

// Test 1: Generate and verify PRO license
console.log('Test 1: Generate PRO License')
const proKey = generateLicenseKey('PRO', 'test-user')
console.log('Generated:', proKey)
const proResult = verifyLicenseKey(proKey)
console.log('Verified:', proResult.valid ? 'PASS' : 'FAIL')
console.log('Plan:', proResult.license?.plan)

// Test 2: Generate and verify TRIAL license
console.log('\nTest 2: Generate TRIAL License (expires)')
const expiresAt = Math.floor((Date.now() + 24*60*60*1000) / 1000)
const trialKey = generateLicenseKey('TRIAL', 'test-user', expiresAt)
const trialResult = verifyLicenseKey(trialKey)
console.log('Verified:', trialResult.valid ? 'PASS' : 'FAIL')

// Test 3: Test expired license
console.log('\nTest 3: Expired License Detection')
const expiredAt = Math.floor((Date.now() - 1000) / 1000) // 1 second ago
const expiredKey = generateLicenseKey('TRIAL', 'test-user', expiredAt)
const expiredResult = verifyLicenseKey(expiredKey)
console.log('Expired detected:', !expiredResult.valid ? 'PASS' : 'FAIL')
console.log('Error:', expiredResult.error)

// Test 4: Test invalid signature
console.log('\nTest 4: Invalid Signature Detection')
const tamperedKey = proKey.slice(0, -5) + 'XXXXX'
const tamperedResult = verifyLicenseKey(tamperedKey)
console.log('Tamper detected:', !tamperedResult.valid ? 'PASS' : 'FAIL')

// Test 5: License activation flow
console.log('\nTest 5: Activation Flow')
const activateResult = licenseManager.activate(proKey)
console.log('Activated:', activateResult.success ? 'PASS' : 'FAIL')
const state = licenseManager.getState()
console.log('State:', JSON.stringify(state, null, 2))

// Test 6: Feature gating
console.log('\nTest 6: Feature Gating')
console.log('Has kimi-ai:', licenseManager.hasFeature('kimi-ai') ? 'PASS' : 'FAIL')
console.log('Has unlimited-keys:', licenseManager.hasFeature('unlimited-keys') ? 'PASS' : 'FAIL')

// Test 7: Key limits
console.log('\nTest 7: Key Limits')
console.log('Can add 5 keys:', licenseManager.canAddKeys(5) ? 'PASS' : 'FAIL')
console.log('Can add 999 keys:', !licenseManager.canAddKeys(999) ? 'PASS (FREE limited)' : 'N/A')

// Test 8: Deactivation
console.log('\nTest 8: Deactivation')
licenseManager.deactivate()
const deactivatedState = licenseManager.getState()
console.log('Deactivated:', !deactivatedState.isActive ? 'PASS' : 'FAIL')
console.log('Plan reset to FREE:', deactivatedState.plan === 'FREE' ? 'PASS' : 'FAIL')

console.log('\n=== All Tests Complete ===')
```

Run the test:

```bash
cd elevenlabs-app/electron
node test-license.js
```

---

## Troubleshooting

### License Key Fails Verification

**Error**: `Invalid license signature`

**Causes**:
- Key was tampered with
- Key generated with different private key
- Key format corrupted

**Solution**: Regenerate the license key using the CLI tool.

### License Not Persisting After Restart

**Error**: License resets to FREE after app restart

**Causes**:
- Encrypted storage file corrupted
- Machine ID changed (storage is machine-bound)

**Solution**:
```bash
# Delete corrupted license file
rm ~/Library/Application\ Support/elevenlabs-app/license.dat
# Reactivate license in app
```

### Feature Not Available After Activation

**Error**: `hasKimiAccess()` returns false for PRO user

**Causes**:
- License not properly activated
- App needs restart to refresh state

**Solution**:
```javascript
// Force refresh license state
await window.elevenlabs.license.deactivate()
await window.elevenlabs.license.activate('YOUR_KEY')
```

### TypeScript Compilation Errors

**Error**: `Property 'license' does not exist on type 'ElevenLabsAPI'`

**Solution**: Ensure `src/types/electron.d.ts` includes the `LicenseAPI` interface:

```typescript
// In src/types/electron.d.ts
export interface LicenseAPI {
  getState: () => Promise<LicenseState>
  activate: (licenseKey: string) => Promise<{ success: boolean; error?: string }>
  deactivate: () => Promise<void>
  verify: (licenseKey: string) => Promise<{ valid: boolean; error?: string }>
  hasFeature: (feature: string) => Promise<boolean>
  canAddKeys: (currentCount: number) => Promise<boolean>
  hasKimiAccess: () => Promise<boolean>
  getUpgradeInfo: () => Promise<{ type: 'telegram' | 'url'; contact: string }>
  generate: (plan: 'FREE' | 'PRO' | 'TRIAL', userId?: string, expiresAt?: number) => Promise<string>
}

export interface ElevenLabsAPI {
  // ... other APIs
  license: LicenseAPI
}
```

### IPC Handler Not Found

**Error**: `No IPC handler registered for 'license:activate'`

**Solution**: Check `electron/main.ts` for handler registration:

```typescript
import { licenseManager } from './license-manager'

ipcMain.handle('license:activate', async (_, { licenseKey }) => {
  return licenseManager.activate(licenseKey)
})
```

---

## Security Notes

### Production Deployment

For production, implement these additional security measures:

1. **Obfuscate Public Key**: Use code obfuscation to hide the embedded public key
2. **Server-Side Verification**: Move license verification to a secure backend
3. **License Revocation**: Implement online revocation checking
4. **Runtime Integrity**: Add checksums for critical files
5. **Rate Limiting**: Limit license activation attempts

### Safe Development Practices

- **Never commit private keys** to version control
- **Use separate keys** for development and production
- **Rotate keys** periodically
- **Audit license usage** for anomalies

---

## Contact

For license distribution and Pro Plan purchases, contact us on Telegram.
