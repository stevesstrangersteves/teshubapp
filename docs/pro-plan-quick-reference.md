# Pro Plan Licensing - Quick Reference

## Quick Start

### Generate a Test License Key

```bash
# PRO license (unlimited everything)
npx ts-node electron/license-keygen.ts PRO test-user

# TRIAL license (expires in 24 hours)
npx ts-node electron/license-keygen.ts TRIAL test-user --expires $(node -e "console.log(Math.floor((Date.now() + 86400000) / 1000))")

# FREE license (10 key limit)
npx ts-node electron/license-keygen.ts FREE test-user
```

### Activate License in App

Open DevTools console in the app:

```javascript
await window.elevenlabs.license.activate('EP-v1-PRO-xyz...')
```

### Check License Status

```javascript
const state = await window.elevenlabs.license.getState()
console.log('Plan:', state.plan)
console.log('Features:', state.features)
console.log('Max Keys:', state.maxKeys)
```

---

## Common Commands

| Task | Command |
|------|---------|
| Generate PRO key | `npx ts-node electron/license-keygen.ts PRO <userId>` |
| Generate TRIAL key | `npx ts-node electron/license-keygen.ts TRIAL <userId> --expires <timestamp>` |
| Build app | `npm run build` |
| Run tests | `node electron/test-license.js` |
| Clear license data | `rm ~/Library/Application\ Support/elevenlabs-app/license.dat` |

---

## API Reference

### License State

```typescript
interface LicenseState {
  isActive: boolean
  plan: 'FREE' | 'PRO' | 'TRIAL'
  features: string[]
  maxKeys: number
  expiresAt?: number
}
```

### Available Methods

```javascript
// Get current license state
window.elevenlabs.license.getState()

// Activate a license key
window.elevenlabs.license.activate('EP-v1-PRO-xyz...')

// Deactivate current license
window.elevenlabs.license.deactivate()

// Verify a key without activating
window.elevenlabs.license.verify('EP-v1-PRO-xyz...')

// Check if feature is available
window.elevenlabs.license.hasFeature('kimi-ai')

// Check if can add more API keys
window.elevenlabs.license.canAddKeys(currentCount)

// Check Kimi AI access
window.elevenlabs.license.hasKimiAccess()

// Get upgrade info (Telegram contact)
window.elevenlabs.license.getUpgradeInfo()
```

---

## Feature Flags by Plan

| Feature | FREE | PRO | TRIAL |
|---------|------|-----|-------|
| `basic-tts` | ✅ | ✅ | ✅ |
| `basic-stt` | ✅ | ✅ | ✅ |
| `sfx` | ✅ | ✅ | ❌ |
| `music` | ✅ | ✅ | ❌ |
| `key-manager` | ✅ | ✅ | ❌ |
| `kimi-ai` | ❌ | ✅ | ⚠️ Limited |
| `unlimited-keys` | ❌ | ✅ | ❌ |
| `parallel-tts` | ❌ | ✅ | ❌ |
| `advanced-optimization` | ❌ | ✅ | ❌ |

---

## Testing Checklist

### Before Release

- [ ] Generate test PRO license key
- [ ] Activate license in app
- [ ] Verify PRO features are unlocked
- [ ] Test Kimi AI access
- [ ] Test adding unlimited API keys
- [ ] Deactivate license
- [ ] Verify FREE plan restrictions return
- [ ] Test license persistence (restart app)
- [ ] Test upgrade dialog appears for FREE users

### Common Test Scenarios

**Scenario 1: FREE User Hits Key Limit**
1. Add 10 API keys as FREE user
2. Try to add 11th key
3. Verify upgrade dialog appears

**Scenario 2: FREE User Tries Kimi AI**
1. Navigate to Kimi Keys page as FREE user
2. Try to add a Kimi token
3. Verify Pro Plan dialog appears

**Scenario 3: PRO User Access**
1. Activate PRO license
2. Verify Kimi AI is accessible
3. Verify can add more than 10 API keys
4. Verify all Pro features work

---

## File Locations

| File | Purpose |
|------|---------|
| `electron/license-manager.ts` | Core license logic |
| `electron/license-keygen.ts` | Key generation CLI |
| `src/hooks/useLicense.ts` | React hook |
| `src/context/LicenseContext.tsx` | Global context |
| `src/components/ProPlanDialog.tsx` | Upgrade dialog |
| `src/types/electron.d.ts` | TypeScript types |
| `~/Library/Application Support/elevenlabs-app/license.dat` | Encrypted license storage |

---

## Troubleshooting Quick Fixes

| Problem | Fix |
|---------|-----|
| License not activating | Delete `license.dat` and reactivate |
| TypeScript errors | Check `LicenseAPI` interface in types |
| Features not unlocking | Restart app after activation |
| Key generation fails | Ensure ts-node is installed globally |

---

## Telegram Contact Setup

Update the Telegram contact in `electron/license-manager.ts`:

```typescript
getUpgradeInfo(): { type: 'telegram' | 'url'; contact: string; message?: string } {
  return {
    type: 'telegram',
    contact: 'https://t.me/YOUR_TELEGRAM_HANDLE', // TODO: Replace
    message: 'Contact us on Telegram to upgrade to Pro Plan!'
  }
}
```

Also update in `src/components/ProPlanDialog.tsx` and `src/components/UpgradePopup.tsx`.
