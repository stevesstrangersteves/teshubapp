# Qwen3 TTS Production Build Fix

## Problem

When running the production build, the Qwen3 TTS feature shows "not ready" state because the Python worker script (`qwen3-worker.py`) cannot be found.

## Root Cause

1. During development, the worker is copied to `dist-electron/` by the Vite plugin
2. In production builds with `electron-builder`, files are packaged into `app.asar`
3. The path resolution in `qwen3-bridge.ts` was looking in the wrong locations
4. The worker wasn't being copied to `process.resourcesPath` properly

## Solution

### 1. Updated `vite.config.ts`

Modified the copy plugin to copy the worker to BOTH `dist-electron/` (dev) and `dist/` (production):

```typescript
{
  name: 'copy-qwen3-worker',
  closeBundle() {
    const src = path.join(process.cwd(), 'electron', 'qwen3-worker.py')
    const destDev = path.join(process.cwd(), 'dist-electron', 'qwen3-worker.py')
    const destProd = path.join(process.cwd(), 'dist', 'qwen3-worker.py')
    copyFileSync(src, destDev)
    copyFileSync(src, destProd)
  }
}
```

### 2. Updated `electron-builder.yml`

Added `extraResources` to copy the worker to the resources folder:

```yaml
files:
  - dist/**/*
  - dist-electron/**/*
  - dist/qwen3-worker.py  # Explicit include

extraResources:
  - from: dist/qwen3-worker.py
    to: qwen3-worker.py
```

### 3. Updated `qwen3-bridge.ts`

Fixed the worker path resolution to check multiple locations:

```typescript
const possibleWorkerPaths = [
  // Production (unpacked from app.asar to resourcesPath)
  path.join(process.resourcesPath, 'qwen3-worker.py'),
  // Development (dist-electron folder)
  path.join(__dirname, 'qwen3-worker.py'),
  // Fallback to electron source directory (for dev with vite)
  path.join(app.getAppPath(), 'electron', 'qwen3-worker.py'),
  // Another dev fallback (running from cwd)
  path.join(process.cwd(), 'electron', 'qwen3-worker.py'),
  // Fallback to dist folder (for production test)
  path.join(process.cwd(), 'dist', 'qwen3-worker.py'),
]
```

Also improved error logging:

```typescript
if (!workerPath) {
  const searchedPaths = possibleWorkerPaths.join(', ')
  console.error('[Qwen3Bridge] Worker not found. Searched:', searchedPaths)
  throw new Error(`qwen3-worker.py not found. Searched: ${searchedPaths}`)
}
```

### 4. Fixed Python Path Resolution

Updated to use system Python paths on macOS:

```typescript
const possiblePaths = [
  // System Python on macOS
  '/usr/bin/python3',
  '/opt/homebrew/bin/python3',
  '/usr/local/bin/python3',
  'python3',
  'python',
]
```

## Files Modified

| File | Change |
|------|--------|
| `vite.config.ts` | Copy worker to both dist-electron and dist |
| `electron-builder.yml` | Add extraResources for production |
| `electron/qwen3-bridge.ts` | Fix path resolution and logging |

## Testing

After rebuilding:

```bash
npm run build
```

The worker file should exist in:
- `dist-electron/qwen3-worker.py` (development)
- `dist/qwen3-worker.py` (production source)

In the packaged app, the worker will be at:
- `Contents/Resources/qwen3-worker.py` (macOS)
- `resources/qwen3-worker.py` (Windows/Linux)

## Debugging

If Qwen3 TTS still shows "not ready", check:

1. **Python installation:**
   ```bash
   which python3
   python3 --version
   ```

2. **Worker file location in packaged app:**
   - macOS: `Right-click app → Show Package Contents → Contents/Resources/`
   - Look for `qwen3-worker.py`

3. **Check logs in Console.app:**
   - Search for "Qwen3Bridge" or "teshub-desktop"
   - Look for path resolution messages

4. **Verify extraResources:**
   ```bash
   # After building, check the release folder
   ls -la release/mac*/TeSHub\ Desktop.app/Contents/Resources/
   ```

## Additional Notes

- The worker requires Python 3.9+ with the required packages installed
- Users need to run the Python environment setup from the app first time
- Model files are downloaded to `~/Library/Application Support/teshub-desktop/models/`
