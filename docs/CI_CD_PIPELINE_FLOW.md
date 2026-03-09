# GitHub Actions CI/CD Pipeline Flow

This document explains the complete flow of the GitHub Actions pipelines for the TeSHub Desktop app.

---

## Pipeline Overview

```
┌──────────────────────────────────────────────────────────────────────────┐
│                         GITHUB ACTIONS PIPELINE                           │
└──────────────────────────────────────────────────────────────────────────┘

                              ┌─────────────┐
                              │  Developer  │
                              │   pushes    │
                              │    code     │
                              └──────┬──────┘
                                     │
                     ┌───────────────┴───────────────┐
                     │                               │
              ┌──────▼──────┐                 ┌──────▼──────┐
              │ Pull Request│                 │Push to main │
              │   created   │                 │  or tag v*  │
              └──────┬──────┘                 └──────┬──────┘
                     │                               │
          ┌──────────▼──────────┐         ┌─────────▼─────────┐
          │   CI WORKFLOW       │         │  BUILD WORKFLOW   │
          │   (ci.yml)          │         │   (build.yml)     │
          │                     │         │                   │
          │  ┌───────────────┐  │         │  ┌─────────────┐  │
          │  │ Type Check    │  │         │  │ Build macOS │  │
          │  │ & Lint        │  │         │  │ (parallel)  │  │
          │  └───────────────┘  │         │  └─────────────┘  │
          │                     │         │                   │
          │  ┌───────────────┐  │         │  ┌─────────────┐  │
          │  │ Build         │  │         │  │ Build       │  │
          │  │ Verification  │  │         │  │ Windows     │  │
          │  └───────────────┘  │         │  │ (parallel)  │  │
          │                     │         │  └─────────────┘  │
          │  ┌───────────────┐  │         │                   │
          │  │ Run Tests     │  │         │  ┌─────────────┐  │
          │  └───────────────┘  │         │  │ Build Linux │  │
          │                     │         │  │ (parallel)  │  │
          └──────────┬──────────┘         │  └─────────────┘  │
                     │                    │                   │
                     │                    │  ┌─────────────┐  │
                     │                    │  │ Upload      │  │
                     │                    │  │ Artifacts   │  │
                     │                    │  └─────────────┘  │
                     │                    │                   │
                     │                    │         │         │
                     │                    │         ▼         │
                     │                    │  ┌─────────────┐  │
                     │                    │  │ Tag v*?     │  │
                     │                    │  └──────┬──────┘  │
                     │                    │         │         │
                     │                    │    Yes  │  No     │
                     │                    │         ▼         │
                     │                    │  ┌─────────────┐  │
                     │                    │  │  Create     │  │
                     │                    │  │   GitHub    │  │
                     │                    │  │   Release   │  │
                     │                    │  └─────────────┘  │
                     ▼                    └───────────────────┘
            ┌────────────────┐
            │  All checks    │
            │    must pass   │
            └───────┬────────┘
                    │
                    ▼
            ┌────────────────┐
            │  Merge to main │
            └───────┬────────┘
                    │
                    └─────────────────────────────┐
                                                  │
                                                  ▼
                                         (triggers Build Workflow)
```

---

## Workflow 1: CI Pipeline (`ci.yml`)

**Trigger:** Pull Request to `main` or `master`

**Purpose:** Fast feedback on code quality before merging

### Flow Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                      CI WORKFLOW (ci.yml)                            │
│                   Trigger: pull_request                              │
└─────────────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
        ▼                     ▼                     ▼
┌───────────────┐     ┌───────────────┐     ┌───────────────┐
│    Job 1:     │     │    Job 2:     │     │    Job 3:     │
│  Quality      │     │    Build      │     │     Test      │
│   Check       │     │   Verify      │     │               │
│               │     │               │     │               │
│ Runs on:      │     │ Runs on:      │     │ Runs on:      │
│ ubuntu-latest │     │ ubuntu-latest │     │ ubuntu-latest │
│               │     │               │     │               │
│ Steps:        │     │ Steps:        │     │ Steps:        │
│ 1. Checkout   │     │ 1. Checkout   │     │ 1. Checkout   │
│ 2. Setup Bun  │     │ 2. Setup Bun  │     │ 2. Setup Bun  │
│ 3. Cache      │     │ 3. Cache      │     │ 3. Cache      │
│ 4. Install    │     │ 4. Install    │     │ 4. Install    │
│ 5. tsc        │     │ 5. Build      │     │ 5. bun test   │
│ 6. eslint     │     │ 6. Verify     │     │ 6. Coverage   │
└───────────────┘     └───────────────┘     └───────────────┘
        │                     │                     │
        └─────────────────────┼─────────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │ All jobs pass?  │
                    └────────┬────────┘
                             │
              ┌──────────────┴──────────────┐
              │                             │
             Yes                           No
              │                             │
              ▼                             ▼
     ┌─────────────────┐          ┌─────────────────┐
     │ PR status: ✅   │          │ PR status: ❌   │
     │ Ready to merge  │          │ Fix and retry   │
     └─────────────────┘          └─────────────────┘
```

### Job Details

| Job | Duration | Purpose | Critical |
|-----|----------|---------|----------|
| Quality Check | ~3-5 min | Type checking + linting | Yes |
| Build Verify | ~5-8 min | Ensure code compiles | Yes |
| Tests | ~2-5 min | Run test suite | Yes |

---

## Workflow 2: Build Pipeline (`build.yml`)

**Trigger:** Push to `main`/`master` OR version tag (`v*`)

**Purpose:** Build distributable apps for all platforms

### Flow Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                    BUILD WORKFLOW (build.yml)                        │
│              Trigger: push (main/master) OR tag v*                   │
└─────────────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
        ▼                     ▼                     ▼
┌───────────────┐     ┌───────────────┐     ┌───────────────┐
│  Job: Build   │     │  Job: Build   │     │  Job: Build   │
│    macOS      │     │   Windows     │     │    Linux      │
│               │     │               │     │               │
│ Runs on:      │     │ Runs on:      │     │ Runs on:      │
│ macos-latest  │     │ windows-latest│     │ ubuntu-latest │
│               │     │               │     │               │
│ Steps:        │     │ Steps:        │     │ Steps:        │
│ 1. Checkout   │     │ 1. Checkout   │     │ 1. Checkout   │
│ 2. Setup Bun  │     │ 2. Setup Bun  │     │ 2. Setup Bun  │
│ 3. Cache      │     │ 3. Cache      │     │ 3. Cache      │
│ 4. Install    │     │ 4. Install    │     │ 4. Install    │
│ 5. Build      │     │ 5. Build      │     │ 5. Build      │
│ 6. Sign +     │     │ 6. Sign +     │     │ 6. Build      │
│    Notarize   │     │    Sign       │     │    (AppImage) │
│               │     │               │     │ 7. Build      │
│ 7. Upload     │     │ 7. Upload     │     │    (deb)      │
│    artifacts  │     │    artifacts  │     │ 8. Upload     │
│               │     │               │     │    artifacts  │
└───────────────┘     └───────────────┘     └───────────────┘
        │                     │                     │
        └─────────────────────┼─────────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │ All builds      │
                    │ successful?     │
                    └────────┬────────┘
                             │
              ┌──────────────┴──────────────┐
              │                             │
             Yes                           No
              │                             │
              ▼                             ▼
     ┌─────────────────┐          ┌─────────────────┐
     │ Continue to     │          │ Workflow        │
     │ release check   │          │ failed - notify │
     └────────┬────────┘          └─────────────────┘
              │
              ▼
     ┌─────────────────┐
     │ Pushed tag v*?  │
     └────────┬────────┘
              │
     ┌────────┴────────┐
     │                 │
    Yes               No
     │                 │
     ▼                 ▼
┌─────────────┐   ┌─────────────┐
│ Job: Create │   │   Done      │
│  Release    │   │ Artifacts   │
│             │   │ available   │
│ Steps:      │   │ for 14 days │
│ 1. Download │   └─────────────┘
│    artifacts│
│ 2. Create   │
│    Release  │
│ 3. Attach   │
│    files    │
└─────────────┘
```

### Job Details

| Job | Duration | Platform | Artifacts |
|-----|----------|----------|-----------|
| Build macOS | ~15-25 min | macos-latest | `.dmg`, `.zip` |
| Build Windows | ~15-25 min | windows-latest | `.exe`, `.msi` |
| Build Linux | ~15-25 min | ubuntu-latest | `.AppImage`, `.deb` |
| Create Release | ~2-3 min | ubuntu-latest | GitHub Release |

---

## Parallel Execution Model

All three platform builds run **in parallel**, not sequentially:

```
Time →

macOS     [==== Build ====]
Windows   [==== Build ====]
Linux     [==== Build ====]

Total time: ~25 minutes (not 75 minutes)
```

### Why Parallel?

| Aspect | Sequential | Parallel |
|--------|------------|----------|
| Total time | ~75 minutes | ~25 minutes |
| Cost | 3x runner minutes | 3x runner minutes |
| Feedback | Slow | Fast |
| Resource usage | Low | High |

---

## Caching Strategy

```
┌─────────────────────────────────────────────────────────────┐
│                    BUN CACHE FLOW                            │
└─────────────────────────────────────────────────────────────┘

Job 1 (macOS)              Cache Server              Job 2 (Windows)
     │                          │                          │
     │  1. Check cache key      │                          │
     ├─────────────────────────►│                          │
     │                          │                          │
     │  2. Cache miss           │                          │
     ◄─────────────────────────┤                          │
     │                          │                          │
     │  3. Install dependencies │                          │
     │                          │                          │
     │  4. Save to cache        │                          │
     ├─────────────────────────►│                          │
     │                          │                          │
     │                          │  5. Check cache key      │
     │                          ◄──────────────────────────┤
     │                          │                          │
     │                          │  6. Cache HIT!           │
     │                          ├──────────────────────────►
     │                          │                          │
     │                          │  7. Restore cache        │
     │                          ◄──────────────────────────┤
     │                          │                          │
     │                          │                          │ 8. Skip install
```

### Cache Keys

```
Cache Key Format: {OS}-bun-{bun.lock hash}

Examples:
- macOS:   "macos-bun-a1b2c3d4e5f6"
- Windows: "windows-bun-a1b2c3d4e5f6"
- Linux:   "Linux-bun-a1b2c3d4e5f6"

Restore Keys (fallback):
- "macos-bun-"
- "windows-bun-"
- "Linux-bun-"
```

---

## Artifact Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                        ARTIFACT FLOW                                 │
└─────────────────────────────────────────────────────────────────────┘

Build Jobs                    GitHub                  Release Job
                              Storage
    │                            │                         │
    │  Upload macos-build        │                         │
    ├───────────────────────────►│                         │
    │                            │                         │
    │  Upload windows-build      │                         │
    ├───────────────────────────►│                         │
    │                            │                         │
    │  Upload linux-build        │                         │
    ├───────────────────────────►│                         │
    │                            │                         │
    │                            │  Download all           │
    │                            ◄─────────────────────────┤
    │                            │                         │
    │                            │  Merge artifacts        │
    │                            │                         │
    │                            │  Create Release         │
    │                            │  Attach files           │
    │                            │                         │
    │                            │                         │
```

### Artifact Retention

| Workflow | Retention | Download Location |
|----------|-----------|-------------------|
| CI (pull requests) | 14 days | Actions tab → Workflow run |
| Build (main) | 14 days | Actions tab → Workflow run |
| Build (release) | Forever | GitHub Releases page |

---

## Conditional Execution

### Release Only on Tags

```yaml
Create Release job:
  if: startsWith(github.ref, 'refs/tags/v')
```

```
Push to main       → Builds run, Release SKIPPED
Push tag v1.0.0    → Builds run, Release EXECUTED
```

### Code Signing Only for Releases

```yaml
env:
  CSC_IDENTITY_AUTO_DISCOVERY: ${{ startsWith(github.ref, 'refs/tags/v') && 'true' || 'false' }}
```

```
Pull request       → No signing (fast, no secrets needed)
Push to main       → No signing (fast)
Tag v1.0.0         → Sign + notarize (production ready)
```

---

## Secrets Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                        SECRETS AVAILABILITY                          │
└─────────────────────────────────────────────────────────────────────┘

Workflow Context          Secrets Available?      Reason
─────────────────────────────────────────────────────────────────────
PR from same branch      ✅ YES                   Trusted source
PR from fork             ❌ NO                    Security risk
Push to main             ✅ YES                   Trusted source
Tag push                 ✅ YES                   Trusted source
Scheduled run            ✅ YES                   Trusted source
```

### Required Secrets by Job

| Job | Required Secrets |
|-----|------------------|
| Build macOS | `CSC_LINK`, `CSC_KEY_PASSWORD`, `APPLE_ID`, `APPLE_APP_SPECIFIC_PASSWORD`, `APPLE_TEAM_ID` |
| Build Windows | `WIN_CSC_LINK`, `WIN_CSC_KEY_PASSWORD` |
| Build Linux | None |
| Create Release | None (uses artifacts) |

---

## Failure Handling

```
┌─────────────────────────────────────────────────────────────────────┐
│                      FAILURE SCENARIOS                               │
└─────────────────────────────────────────────────────────────────────┘

Scenario                      Behavior                Recovery
─────────────────────────────────────────────────────────────────────
CI job fails                  PR blocked              Fix code, push again
macOS build fails             Other builds continue   Check macOS logs
Windows build fails           Other builds continue   Check Windows logs
All builds fail               Workflow failed         Check common steps
Release job fails             Artifacts available     Re-run release job
Cache miss                    Slower build            Automatic, no action
Secret missing                Build continues         Unsigned build
```

### Automatic Retry

GitHub Actions does NOT automatically retry failed jobs. Manual intervention required:

```bash
# Option 1: Re-run from GitHub UI
Actions → Select workflow → Click "Re-run jobs"

# Option 2: Push empty commit to trigger new run
git commit --allow-empty -m "ci: retry build"
git push
```

---

## Performance Metrics

### Typical Run Times

| Workflow | Min | Max | Average |
|----------|-----|-----|---------|
| CI (cache hit) | 5 min | 10 min | 7 min |
| CI (cache miss) | 8 min | 15 min | 11 min |
| Build (all platforms) | 20 min | 30 min | 25 min |
| Release creation | 2 min | 5 min | 3 min |

### Cost Estimation (GitHub Actions Minutes)

| Runner Type | Minutes per Run | Monthly Runs | Monthly Cost* |
|-------------|-----------------|--------------|---------------|
| ubuntu-latest | 30 | 100 | Free (included) |
| macos-latest | 25 × 10 = 250 | 100 | ~$40/month |
| windows-latest | 25 × 2 = 50 | 100 | ~$8/month |

*Assumes GitHub Pro plan ($4/month included minutes)

---

## Monitoring and Alerts

### Check Workflow Status

```bash
# Using GitHub CLI
gh run list                    # List recent runs
gh run view <run-id>           # View specific run
gh run watch <run-id>          # Watch live
```

### Webhook Notifications

Configure webhooks to get notified:

1. Repository Settings → Webhooks
2. Add webhook: `https://your-server.com/webhook`
3. Events: Check "Workflows"
4. Get notified on: `workflow_run.completed`

---

## Related Documents

- [CODE_SIGNING_SETUP.md](./CODE_SIGNING_SETUP.md) - How to obtain certificates
- [workflow-README.md](../.github/workflows/README.md) - Workflow configuration guide
- [SECRETS_SETUP.md](../.github/SECRETS_SETUP.md) - Quick secrets reference

---

## Quick Reference

### Trigger a Full Build

```bash
git tag v1.0.0
git push origin v1.0.0
```

### View Build Artifacts

1. Go to repository **Actions** tab
2. Click on the workflow run
3. Scroll to **Artifacts** section
4. Download desired platform

### Re-run Failed Job

1. Go to Actions tab
2. Click failed workflow
3. Click "Re-run jobs" button
4. Select specific job or all jobs

### Cancel Running Workflow

1. Go to Actions tab
2. Click running workflow
3. Click "Cancel workflow" button

---

## Support

For issues with the pipeline:

1. Check workflow logs in GitHub Actions
2. Review this document's troubleshooting section
3. Consult [GitHub Actions documentation](https://docs.github.com/en/actions)
4. Check electron-builder logs for build failures
