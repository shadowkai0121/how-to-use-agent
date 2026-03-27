---
phase: 2
slug: authoring-contract-and-localized-content-source
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-27
---

# Phase 2 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | `node:test` plus `markdownlint-cli2` and Vale |
| **Config file** | `none — Wave 0 installs` |
| **Quick run command** | `node --test tests/phase-02/*.test.mjs` |
| **Full suite command** | `node --test tests/phase-0*.test.mjs && npx markdownlint-cli2 "**/*.md" && vale .` |
| **Estimated runtime** | ~15 seconds |

---

## Sampling Rate

- **After every task commit:** Run `node --test tests/phase-02/*.test.mjs`
- **After every plan wave:** Run `node --test tests/phase-0*.test.mjs && npx markdownlint-cli2 "**/*.md"`
- **Before `$gsd-verify-work`:** Full suite must be green
- **Max feedback latency:** 30 seconds

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 02-01-01 | 01 | 1 | CONT-01 | integration | `node --test tests/phase-02/content-tree.test.mjs` | ❌ W0 | ⬜ pending |
| 02-02-01 | 02 | 1 | CONT-03 | integration | `node --test tests/phase-02/gfm-contract.test.mjs && npx markdownlint-cli2 "book/**/*.md"` | ❌ W0 | ⬜ pending |
| 02-03-01 | 03 | 2 | CONT-04 | integration | `node --test tests/phase-02/placeholders.test.mjs` | ❌ W0 | ⬜ pending |
| 02-03-02 | 03 | 2 | QUAL-01 | integration | `node --test tests/phase-02/terms.test.mjs && vale .` | ❌ W0 | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

- [ ] `package.json` — add `test`, `lint:md`, and authoring validation commands if missing
- [ ] `tests/phase-02/content-tree.test.mjs` — stubs for `CONT-01`
- [ ] `tests/phase-02/gfm-contract.test.mjs` — stubs for `CONT-03`
- [ ] `tests/phase-02/placeholders.test.mjs` — stubs for `CONT-04`
- [ ] `tests/phase-02/terms.test.mjs` — stubs for `QUAL-01`
- [ ] `book/terms.zh-TW.yml` — canonical terminology source
- [ ] `scripts/validate-authoring.mjs` — custom checks for placeholder path rules and allowed extended syntax
- [ ] `.vale.ini` or `vale.ini` — terminology and prose lint configuration

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| None | None | All phase behaviors are expected to have automated verification once Wave 0 is in place. | No manual-only checks required for the planned Phase 2 surface. |

---

## Validation Sign-Off

- [ ] All tasks have `<automated>` verify or Wave 0 dependencies
- [ ] Sampling continuity: no 3 consecutive tasks without automated verify
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 30s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
