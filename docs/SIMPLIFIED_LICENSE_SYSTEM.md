# Simplified License System

**Last Updated:** 2026-03-07
**Version:** v2 (Simplified Random Key Format)

## Overview

The license system has been simplified to use random alphanumeric keys with server-side KV database lookup. No cryptographic signatures are used.

## Architecture

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  Electron App   │────▶│  Cloudflare      │────▶│  Cloudflare KV  │
│  (License Mgr)  │     │  Worker          │     │  (License DB)   │
└─────────────────┘     └──────────────────┘     └─────────────────┘
       │                        │                        │
       │  1. Enter key          │                        │
       │───────────────────────▶│                        │
       │                        │  2. Lookup key         │
       │                        │───────────────────────▶│
       │                        │                        │
       │                        │  3. Return license     │
       │                        │◀───────────────────────│
       │  4. Valid/Invalid      │                        │
       │◀───────────────────────│                        │
       │                        │                        │
       │  5. Save to encrypted  │                        │
       │     storage + 24h TTL  │                        │
```

## License Key Format

**Format:** `PLAN-XXXX-XXXX-XXXX`

Examples:
- `PRO-7A3F-9B2C-4D8E`
- `FREE-2K9L-M4N7-P8Q1`
- `TRIAL-5X6Y-7Z8A-9B2C`

**Character Set:** `ABCDEFGHJKLMNPQRSTUVWXYZ23456789` (32 characters, excludes I, O, 0, 1 for readability)

**Total Combinations:** 32^12 = ~1.2 septillion (practically unguessable)

## Server-Side (Cloudflare Worker)

### Endpoints

#### `POST /api/license/verify`

Verify a license key.

**Request:**
```json
{
  "licenseKey": "PRO-7A3F-9B2C-4D8E"
}
```

**Response (Valid):**
```json
{
  "valid": true,
  "license": {
    "version": "v2",
    "plan": "PRO",
    "features": ["basic-tts", "basic-stt", "kimi-ai", "unlimited-keys"],
    "maxKeys": 999999,
    "issuedAt": 1741392000000
  },
  "validationTTL": 86400000
}
```

**Response (Invalid):**
```json
{
  "valid": false,
  "error": "Invalid license key"
}
```

#### `POST /api/license/validate`

Re-validate an existing license (used for 24h renewal).

**Request:**
```json
{
  "licenseKey": "PRO-7A3F-9B2C-4D8E"
}
```

**Response:** Same as `/api/license/verify`

#### `POST /api/license/revoke`

Revoke a license key (admin only).

**Request:**
```json
{
  "licenseKey": "PRO-7A3F-9B2C-4D8E"
}
```

**Response:**
```json
{
  "success": true,
  "message": "License revoked successfully"
}
```

#### `GET /health`

Health check endpoint.

**Response:**
```json
{
  "status": "ok",
  "time": 1741392000000
}
```

### KV Storage Structure

**Key Format:** `license:{licenseKey}`

**Value (JSON):**
```json
{
  "plan": "PRO",
  "features": ["basic-tts", "basic-stt", "kimi-ai", "unlimited-keys"],
  "maxKeys": 999999,
  "createdAt": 1741392000000,
  "expiresAt": null
}
```

## Client-Side (Electron)

### License Manager Flow

1. **User enters license key** in Settings page
2. **Call server** `/api/license/verify`
3. **If valid:**
   - Save to encrypted storage (`license.dat`)
   - Set `nextValidation = Date.now() + 24h`
4. **On app restart:**
   - Check if `Date.now() < nextValidation`
   - If yes → use cached license (no server call)
   - If no → re-verify with server, update `nextValidation`

### File Locations

| File | Purpose |
|------|---------|
| `electron/license-manager.ts` | Main license management logic |
| `electron/license-api.ts` | HTTP client for server communication |
| `electron/settings-store.ts` | Settings storage (includes license server URL) |

### Encrypted Storage

- **File:** `license.dat` (in user data directory)
- **Encryption:** AES-256-GCM
- **Key Derivation:** scrypt(machine_id, 'license-salt')
- **Machine Binding:** License tied to machine's user data path

## Generating License Keys

### Prerequisites

Set environment variables in `server/.env`:

```env
CLOUDFLARE_API_TOKEN=your_cloudflare_api_token
CLOUDFLARE_ACCOUNT_ID=your_account_id
CLOUDFLARE_KV_NAMESPACE_ID=your_kv_namespace_id
```

### Generate Key

```bash
cd server

# Generate PRO license
npm run generate-license PRO

# Generate FREE license
npm run generate-license FREE

# Generate TRIAL license (expires)
npm run generate-license TRIAL 1735689600000
```

### Output Example

```
═══════════════════════════════════════════════════════════
                LICENSE KEY GENERATED
═══════════════════════════════════════════════════════════

Plan: PRO
Expires: Never

License Key:
PRO-7A3F-9B2C-4D8E

═══════════════════════════════════════════════════════════
```

## Testing

### 1. Generate Test License

```bash
cd server
npm run generate-license PRO
```

### 2. Test in Electron App

1. Open Electron app
2. Go to Settings → License tab
3. Enter the generated license key
4. Click "Activate"
5. Verify PRO features are unlocked

### 3. Test Caching

1. Close and reopen app
2. License should still be valid (cached)
3. No server call should be made
4. Check console: `[License] Loaded license from storage`

### 4. Test Re-validation

1. Wait 24 hours OR manually set `nextValidation` to past
2. Reopen app
3. License should re-verify with server
4. Check console: `[License] Re-validating license...`

## Migration from v1 (ECDSA Keys)

### For Existing Users

Old v1 keys (`EP-v1-pro-xxx-xxx`) will no longer work with the new system.

**Options:**
1. **Issue new keys** to existing users manually
2. **Add backward compatibility** in `worker.ts`:
   ```typescript
   if (licenseKey.startsWith('EP-v1-')) {
     // Old ECDSA verification path
   } else {
     // New random key lookup
   }
   ```

### Key Format Detection

| Prefix | Version | Action |
|--------|---------|--------|
| `EP-v1-` | v1 | Legacy ECDSA (deprecated) |
| `PRO-`, `FREE-`, `TRIAL-` | v2 | New random key |

## Security Considerations

### Trade-offs

| Aspect | v1 (ECDSA) | v2 (Random) |
|--------|------------|-------------|
| Client verification | ✅ Yes | ❌ No |
| Key guessing difficulty | ✅ High | ✅ Very High (32^12) |
| Complexity | ❌ High | ✅ Low |
| Revocation | ✅ Yes | ✅ Yes |
| Offline support | ✅ Complex | ✅ Simple (24h cache) |

### Mitigations

1. **Key guessing:** 32^12 combinations = ~1.2 septillion (safer than UUIDs)
2. **Rate limiting:** Implement on `/api/license/verify` endpoint
3. **Audit logging:** Log all verification attempts
4. **Revocation:** Delete from KV to revoke

## Troubleshooting

### "Invalid license key"

1. Verify key format: `PRO-XXXX-XXXX-XXXX`
2. Check KV storage: `wrangler kv:key get "license:PRO-XXX"`
3. Ensure key was generated and stored in KV

### License not persisting

1. Check encrypted storage file exists
2. Verify machine ID hasn't changed
3. Check console logs for errors

### Re-validation failing

1. Check network connectivity
2. Verify server is running: `GET /health`
3. Check KV storage for key

## Configuration

### Environment Variables (Client)

| Variable | Default | Description |
|----------|---------|-------------|
| `LICENSE_SERVER_URL` | `http://localhost:8787` | License server URL |
| `LICENSE_USER_DATA_PATH` | App's user data | Custom data directory |

### Environment Variables (Server)

| Variable | Required | Description |
|----------|----------|-------------|
| `CLOUDFLARE_API_TOKEN` | ✅ | Cloudflare API token |
| `CLOUDFLARE_ACCOUNT_ID` | ✅ | Cloudflare account ID |
| `CLOUDFLARE_KV_NAMESPACE_ID` | ✅ | KV namespace ID |

## Related Files

- `server/src/worker.ts` - Cloudflare Worker entry point
- `server/src/generate-license.ts` - License key generator
- `electron/license-manager.ts` - Electron license management
- `electron/license-api.ts` - HTTP client for server
