---
phase: 04
slug: agent
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-27
---

# Phase 04 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | `node:test` bundled with Node 24 LTS |
| **Config file** | `none — Wave 0 installs` |
| **Quick run command** | `node --test tests/phase-04/chapter-loop-contract.test.mjs tests/phase-04/checkpoint-recovery.test.mjs` |
| **Full suite command** | `node scripts/verify-phase4.mjs && node --test tests/phase-04/*.test.mjs && npm run build:readme && npm run build:epub && node scripts/verify-drift.mjs` |
| **Estimated runtime** | ~20 seconds |

---

## Sampling Rate

- **After every task commit:** Run `node --test tests/phase-04/chapter-loop-contract.test.mjs tests/phase-04/checkpoint-recovery.test.mjs`
- **After every plan wave:** Run `node scripts/verify-phase4.mjs && node --test tests/phase-04/*.test.mjs`
- **Before `$gsd-verify-work`:** Full suite must be green
- **Max feedback latency:** 20 seconds

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 04-01-01 | 01 | 1 | TUTO-01 | structure | `node --test tests/phase-04/chapter-loop-contract.test.mjs` | ❌ W0 | ⬜ pending |
| 04-01-02 | 01 | 1 | TUTO-02 | content | `node --test tests/phase-04/copyable-steps.test.mjs` | ❌ W0 | ⬜ pending |
| 04-01-03 | 01 | 1 | TUTO-05 | structure/content | `node --test tests/phase-04/checkpoint-recovery.test.mjs` | ❌ W0 | ⬜ pending |
| 04-02-01 | 02 | 2 | TUTO-01 | integration | `node scripts/verify-phase4.mjs` | ❌ W0 | ⬜ pending |
| 04-02-02 | 02 | 2 | TUTO-02 | integration/content | `node --test tests/phase-04/copyable-steps.test.mjs` | ❌ W0 | ⬜ pending |
| 04-02-03 | 02 | 2 | TUTO-05 | integration/content | `node --test tests/phase-04/checkpoint-recovery.test.mjs` | ❌ W0 | ⬜ pending |
| 04-03-01 | 03 | 3 | TUTO-01 | integration | `npm run build:readme && npm run build:epub && node scripts/verify-drift.mjs` | ❌ W0 | ⬜ pending |
| 04-03-02 | 03 | 3 | TUTO-02 | integration/manual | `node scripts/verify-phase4.mjs && node --test tests/phase-04/*.test.mjs` | ❌ W0 | ⬜ pending |
| 04-03-03 | 03 | 3 | TUTO-05 | integration/manual | `node --test tests/phase-04/checkpoint-recovery.test.mjs` | ❌ W0 | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

- [ ] `package.json` — declare `test:phase-04`, `verify:phase4`, `check:phase-04`
- [ ] `scripts/verify-phase4.mjs` — enforce tutorial loop and checkpoint contract
- [ ] `tests/phase-04/helpers.mjs` — shared helpers for chapter assertions
- [ ] `tests/phase-04/chapter-loop-contract.test.mjs` — stubs for TUTO-01
- [ ] `tests/phase-04/copyable-steps.test.mjs` — stubs for TUTO-02
- [ ] `tests/phase-04/checkpoint-recovery.test.mjs` — stubs for TUTO-05

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Fresh-clone walkthrough confirms prompts, commands, and checkpoints really work in reading order | TUTO-02, TUTO-05 | 真實可複製性需要用乾淨狀態照章節實走，而不是只看檔案結構 | 依第 1、3、5、7 章各完成一輪「複製 Prompt -> 執行 Command -> 比對 Checkpoint」；若失敗，使用該章 recovery prompt 確認可回正 |
| EPUB 中的 Prompt / Command / Checkpoint 區塊可讀、可辨識 | TUTO-01, TUTO-02 | EPUB code block、alert、段落換行與 GitHub 呈現可能不同 | 重建 EPUB，打開檢查至少第 1、4、7 章三章，確認六個固定區塊都可辨識且 code block 沒破版 |

---

## Generated Output Coverage

| Artifact | Structure Coverage | Copyability Coverage | Drift / Output Coverage |
|----------|--------------------|----------------------|-------------------------|
| `book/frontmatter/00-前言.md` | `scripts/verify-phase4.mjs` | `tests/phase-04/copyable-steps.test.mjs` | `npm run build:readme`, `npm run build:epub` |
| `book/chapters/*.md` | `scripts/verify-phase4.mjs`, `tests/phase-04/chapter-loop-contract.test.mjs` | `tests/phase-04/copyable-steps.test.mjs` | source of truth for README / EPUB |
| `book/manifest.json` | `scripts/verify-phase4.mjs` chapter summary / checkpoint alignment | n/a | `node scripts/verify-drift.mjs` |
| `README.md` | generated from canonical content | manual spot-check for entry readability | `npm run build:readme`, `node scripts/verify-drift.mjs` |
| `dist/epub/how-to-use-agent.epub` | manual EPUB readability check | manual copyability / readability check | `npm run build:epub` |

---

## Validation Sign-Off

- [ ] All tasks have `<automated>` verify or Wave 0 dependencies
- [ ] Sampling continuity: no 3 consecutive tasks without automated verify
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 30s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
