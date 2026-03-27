---
phase: 03
slug: dual-output-build-and-publish-validation
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-27
---

# Phase 03 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | `node:test` bundled with Node 24 LTS |
| **Config file** | `none — Wave 0 installs` |
| **Quick run command** | `node --test tests/phase3/*.test.mjs` |
| **Full suite command** | `node --test tests/**/*.test.mjs` |
| **Estimated runtime** | ~45 seconds |

---

## Sampling Rate

- **After every task commit:** Run `node --test tests/phase3/*.test.mjs`
- **After every plan wave:** Run `node --test tests/**/*.test.mjs`
- **Before `$gsd-verify-work`:** Full suite must be green
- **Max feedback latency:** 45 seconds

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 03-01-01 | 01 | 1 | PUBL-01 | unit/integration | `node --test tests/phase3/readme-generation.test.mjs` | ❌ W0 | ⬜ pending |
| 03-01-02 | 01 | 1 | PUBL-02 | integration | `node --test tests/phase3/epub-build.test.mjs` | ❌ W0 | ⬜ pending |
| 03-01-03 | 01 | 1 | PUBL-04 | integration | `node --test tests/phase3/link-and-asset-validation.test.mjs` | ❌ W0 | ⬜ pending |
| 03-02-01 | 02 | 2 | PUBL-01 | integration | `node --test tests/phase3/readme-generation.test.mjs` | ❌ W0 | ⬜ pending |
| 03-02-02 | 02 | 2 | PUBL-02 | integration | `node --test tests/phase3/epub-build.test.mjs` | ❌ W0 | ⬜ pending |
| 03-02-03 | 02 | 2 | QUAL-03 | integration | `node --test tests/phase3/drift-detection.test.mjs` | ❌ W0 | ⬜ pending |
| 03-03-01 | 03 | 3 | PUBL-03 | integration/smoke | `node --test tests/phase3/epub-validation.test.mjs` | ❌ W0 | ⬜ pending |
| 03-03-02 | 03 | 3 | QUAL-02 | unit/integration | `node --test tests/phase3/placeholder-gate.test.mjs` | ❌ W0 | ⬜ pending |
| 03-03-03 | 03 | 3 | PUBL-04 | integration | `node --test tests/phase3/link-and-asset-validation.test.mjs` | ❌ W0 | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

- [ ] `package.json` — declare Node 24+/pnpm scripts for build and verification
- [ ] `tests/phase3/readme-generation.test.mjs` — stubs for PUBL-01
- [ ] `tests/phase3/epub-build.test.mjs` — stubs for PUBL-02
- [ ] `tests/phase3/epub-validation.test.mjs` — stubs for PUBL-03
- [ ] `tests/phase3/link-and-asset-validation.test.mjs` — stubs for PUBL-04
- [ ] `tests/phase3/placeholder-gate.test.mjs` — stubs for QUAL-02
- [ ] `tests/phase3/drift-detection.test.mjs` — stubs for QUAL-03
- [ ] `tools/` or documented bootstrap path for Pandoc / EPUBCheck / Java
- [ ] local runtime upgrade path from Node `v18.18.0` to Node 24 LTS

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| EPUB opens in at least one target reader and shows readable zh-TW content, TOC, and text cover | PUBL-02, PUBL-03 | Research requires at least one reader smoke test, but the exact reader/tool is still a project choice | Build the EPUB, open it in the chosen baseline reader, confirm title, language, TOC order, and visible content match the generated artifact |
| External-link policy feels usable at local tier while still hard-failing on broken targets | PUBL-04 | Timeout and retry budget are intentionally left to implementation discretion | Run local link verification with the chosen smoke profile, inspect the report output, and confirm the command still blocks on verified broken external links |

---

## Validation Sign-Off

- [ ] All tasks have `<automated>` verify or Wave 0 dependencies
- [ ] Sampling continuity: no 3 consecutive tasks without automated verify
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 45s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
