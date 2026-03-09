# CI/CD Documentation Index

Complete documentation for the GitHub Actions CI/CD pipeline of the TeSHub Desktop app.

---

## 📖 Documentation Map

```
docs/
├── CI_CD_INDEX.md (this file)           ← You are here
├── GITHUB_ACTIONS_QUICKSTART.md         ← Start here for quick setup
├── CODE_SIGNING_SETUP.md                ← Certificate procurement guide
└── CI_CD_PIPELINE_FLOW.md               ← Visual workflow diagrams

.github/
├── workflows/
│   ├── build.yml                        ← Main build workflow
│   ├── ci.yml                           ← CI quality checks
│   └── README.md                        ← Workflow configuration
├── SECRETS_SETUP.md                     ← Quick secrets reference
└── ...

electron-builder.yml                     ← Build configuration
```

---

## 📋 All Documents

### For Quick Setup

| Document | Purpose | Time |
|----------|---------|------|
| [GITHUB_ACTIONS_QUICKSTART.md](./GITHUB_ACTIONS_QUICKSTART.md) | Complete setup guide | 5-60 min |

**Start here if:** You're new to the project or want to set up CI/CD quickly

---

### For Code Signing

| Document | Purpose | Time |
|----------|---------|------|
| [CODE_SIGNING_SETUP.md](./CODE_SIGNING_SETUP.md) | Obtain certificates | 30-60 min |

**Read this if:** You need production-ready signed builds

**Contents:**
- macOS Developer ID certificate ($99/year)
- Windows Code Signing certificate ($300-600/year)
- Step-by-step export and encoding instructions
- GitHub secrets configuration

---

### For Understanding the Pipeline

| Document | Purpose | Time |
|----------|---------|------|
| [CI_CD_PIPELINE_FLOW.md](./CI_CD_PIPELINE_FLOW.md) | Visual diagrams | 15-20 min |

**Read this if:** You want to understand how the workflows work

**Contents:**
- Workflow flowcharts
- Parallel execution model
- Caching strategy
- Artifact flow
- Failure handling

---

### For Workflow Configuration

| Document | Purpose | Time |
|----------|---------|------|
| [.github/workflows/README.md](../.github/workflows/README.md) | Configuration reference | 10-15 min |

**Read this if:** You need to modify workflow behavior

**Contents:**
- Workflow triggers
- Job definitions
- Optimization features
- Customization options
- Troubleshooting

---

### For Secrets Setup

| Document | Purpose | Time |
|----------|---------|------|
| [.github/SECRETS_SETUP.md](../.github/SECRETS_SETUP.md) | Quick secrets guide | 5-10 min |

**Read this if:** You just need to add secrets quickly

**Contents:**
- Secret names and values
- Base64 encoding commands
- GitHub UI navigation

---

## 🚀 Getting Started Paths

### Path 1: Quick Setup (No Signing)

**Goal:** Get builds running in 5 minutes

1. Read: [GITHUB_ACTIONS_QUICKSTART.md](./GITHUB_ACTIONS_QUICKSTART.md#5-minute-setup-no-signing)
2. Edit: `.github/workflows/build.yml` (disable signing)
3. Push: To GitHub
4. Done: Builds run automatically

**Time:** 5 minutes
**Result:** Unsigned builds with warnings

---

### Path 2: Full Setup (With Signing)

**Goal:** Production-ready signed builds

1. Read: [GITHUB_ACTIONS_QUICKSTART.md](./GITHUB_ACTIONS_QUICKSTART.md#full-setup-with-code-signing)
2. Read: [CODE_SIGNING_SETUP.md](./CODE_SIGNING_SETUP.md)
3. Purchase: Apple Developer Program + Windows certificate
4. Export: Certificates as .p12 and .pfx
5. Encode: To base64
6. Add: GitHub secrets (7 total)
7. Push: Test build
8. Verify: Signed artifacts

**Time:** 30-60 minutes
**Result:** Production-ready signed builds

---

### Path 3: Understanding the System

**Goal:** Deep dive into CI/CD architecture

1. Read: [CI_CD_PIPELINE_FLOW.md](./CI_CD_PIPELINE_FLOW.md)
2. Read: [.github/workflows/README.md](../.github/workflows/README.md)
3. Review: Workflow YAML files
4. Analyze: Cache keys and artifact flow

**Time:** 1-2 hours
**Result:** Complete understanding of the pipeline

---

## 🔧 Common Tasks

### Adding a New Secret

**Reference:** [CODE_SIGNING_SETUP.md](./CODE_SIGNING_SETUP.md#adding-secrets-to-github)

1. Go to Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Add name and value
4. Click "Add secret"

---

### Triggering a Release

**Reference:** [GITHUB_ACTIONS_QUICKSTART.md](./GITHUB_ACTIONS_QUICKSTART.md#first-release)

```bash
git tag v1.0.0
git push origin v1.0.0
```

---

### Viewing Build Artifacts

**Reference:** [CI_CD_PIPELINE_FLOW.md](./CI_CD_PIPELINE_FLOW.md#artifact-flow)

1. Go to Actions tab
2. Click workflow run
3. Scroll to Artifacts section
4. Download desired platform

---

### Troubleshooting Failed Builds

**Reference:** Multiple documents

| Issue | Document | Section |
|-------|----------|---------|
| Signing errors | [CODE_SIGNING_SETUP.md](./CODE_SIGNING_SETUP.md) | Troubleshooting |
| Cache misses | [.github/workflows/README.md](../.github/workflows/README.md) | Troubleshooting |
| Build failures | [CI_CD_PIPELINE_FLOW.md](./CI_CD_PIPELINE_FLOW.md) | Failure handling |
| General issues | [GITHUB_ACTIONS_QUICKSTART.md](./GITHUB_ACTIONS_QUICKSTART.md) | Common issues |

---

## 📊 Workflow Summary

### CI Workflow (`ci.yml`)

| Property | Value |
|----------|-------|
| **Trigger** | Pull requests |
| **Purpose** | Quality gates before merge |
| **Jobs** | Type check, lint, test, build verify |
| **Runtime** | 5-10 minutes |
| **Artifacts** | Coverage reports (optional) |

---

### Build Workflow (`build.yml`)

| Property | Value |
|----------|-------|
| **Trigger** | Push to main/master, version tags |
| **Purpose** | Cross-platform builds |
| **Jobs** | macOS, Windows, Linux builds + release |
| **Runtime** | 15-25 minutes |
| **Artifacts** | .dmg, .exe, .AppImage, .deb |

---

## 🔐 Secrets Overview

### Required for Full Setup

| Secret | Platform | Purpose |
|--------|----------|---------|
| `CSC_LINK` | macOS | Code signing certificate |
| `CSC_KEY_PASSWORD` | macOS | Certificate password |
| `APPLE_ID` | macOS | Apple ID email |
| `APPLE_APP_SPECIFIC_PASSWORD` | macOS | Notarization |
| `APPLE_TEAM_ID` | macOS | Developer team |
| `WIN_CSC_LINK` | Windows | Code signing certificate |
| `WIN_CSC_KEY_PASSWORD` | Windows | Certificate password |

### Optional (Development)

No secrets required for unsigned builds - just set `CSC_IDENTITY_AUTO_DISCOVERY: 'false'`

---

## 📦 Build Outputs

### macOS

| File | Format | Architectures |
|------|--------|---------------|
| `.dmg` | Disk image | x64, arm64 |
| `.zip` | Archive | x64, arm64 |

---

### Windows

| File | Format | Architectures |
|------|--------|---------------|
| `.exe` | NSIS installer | x64 |
| `.msi` | MSI installer | x64 |

---

### Linux

| File | Format | Distributions |
|------|--------|---------------|
| `.AppImage` | Universal package | All |
| `.deb` | Debian package | Ubuntu, Debian |

---

## 🎯 Decision Tree

```
Need to set up CI/CD?
│
├─→ Want quick setup?
│   └─→ [GITHUB_ACTIONS_QUICKSTART.md](./GITHUB_ACTIONS_QUICKSTART.md#5-minute-setup)
│
├─→ Need production builds?
│   └─→ [CODE_SIGNING_SETUP.md](./CODE_SIGNING_SETUP.md)
│
├─→ Want to understand the pipeline?
│   └─→ [CI_CD_PIPELINE_FLOW.md](./CI_CD_PIPELINE_FLOW.md)
│
└─→ Just need to add secrets?
    └─→ [.github/SECRETS_SETUP.md](../.github/SECRETS_SETUP.md)
```

---

## 📞 Support Resources

### Documentation

- This documentation index
- Workflow README
- Code signing guide
- Pipeline flow diagrams

### External Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [electron-builder Code Signing](https://www.electron.build/code-signing)
- [Apple Developer Program](https://developer.apple.com/programs/)
- [Bun Documentation](https://bun.sh/docs)

### Commands

```bash
# View workflow runs
gh run list

# View specific run
gh run view <run-id>

# Watch live
gh run watch <run-id>

# Re-run failed job
gh run rerun <run-id>

# Cancel running workflow
gh run cancel <run-id>
```

---

## 📝 Document Maintenance

### When to Update

| Change | Documents to Update |
|--------|---------------------|
| Workflow modifications | README.md, CI_CD_PIPELINE_FLOW.md |
| New secrets | CODE_SIGNING_SETUP.md, SECRETS_SETUP.md |
| Build target changes | README.md, GITHUB_ACTIONS_QUICKSTART.md |
| Troubleshooting discoveries | All relevant troubleshooting sections |

### Review Schedule

- **Monthly:** Verify links and commands work
- **Quarterly:** Update screenshots and examples
- **Annually:** Review certificate costs and providers

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────┐
│              CI/CD QUICK REFERENCE                           │
├─────────────────────────────────────────────────────────────┤
│ Workflows:    .github/workflows/                            │
│ Documentation: docs/CI_CD_*.md                              │
│ Secrets:      Settings → Secrets and variables → Actions    │
│ Artifacts:    Actions tab → Workflow run → Artifacts        │
│ Releases:     Releases tab (right sidebar)                  │
├─────────────────────────────────────────────────────────────┤
│ Trigger build:  git push                                    │
│ Create release: git tag v1.0.0 && git push origin v1.0.0   │
│ View logs:      gh run view --log                          │
│ Re-run failed:  gh run rerun <run-id>                      │
├─────────────────────────────────────────────────────────────┤
│ macOS cert:   $99/year (Apple Developer Program)            │
│ Windows cert: $300-600/year (Sectigo/DigiCert)             │
│ Build time:   ~25 minutes (all platforms)                  │
│ Cache saves:  2-5 minutes per build                        │
└─────────────────────────────────────────────────────────────┘
```

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-03-03 | Initial documentation |

---

**Last updated:** 2026-03-03
**Maintained by:** DevOps team
