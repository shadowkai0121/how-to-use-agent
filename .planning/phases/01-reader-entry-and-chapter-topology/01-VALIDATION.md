---
phase: 1
slug: reader-entry-and-chapter-topology
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-27
---

# Phase 1 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | `node:test` with manifest-validation and Markdown topology checks |
| **Config file** | `none — Wave 0 installs` |
| **Quick run command** | `node --test tests/phase-01/*.test.mjs` |
| **Full suite command** | `node --test && npx markdownlint-cli2 "**/*.md"` |
| **Estimated runtime** | ~15 seconds |

---

## Sampling Rate

- **After every task commit:** Run `node --test tests/phase-01/*.test.mjs`
- **After every plan wave:** Run `node --test && npx markdownlint-cli2 "**/*.md"`
- **Before `$gsd-verify-work`:** Full suite must be green
- **Max feedback latency:** 15 seconds

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 1-01-01 | 01 | 1 | CONT-02 | unit | `node --test tests/phase-01/manifest-schema.test.mjs` | ❌ W0 | ⬜ pending |
| 1-01-02 | 01 | 1 | READ-02 | integration | `node --test tests/phase-01/topology-order.test.mjs` | ❌ W0 | ⬜ pending |
| 1-02-01 | 02 | 2 | READ-01 | integration | `node --test tests/phase-01/readme-entry.test.mjs` | ❌ W0 | ⬜ pending |
| 1-02-02 | 02 | 2 | READ-04 | integration | `node --test tests/phase-01/milestone-signaling.test.mjs` | ❌ W0 | ⬜ pending |
| 1-03-01 | 03 | 2 | READ-03 | integration | `node --test tests/phase-01/chapter-nav.test.mjs` | ❌ W0 | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

- [ ] `package.json` — define `test`, `lint:md`, and topology validation scripts
- [ ] `tests/phase-01/manifest-schema.test.mjs` — covers `CONT-02`
- [ ] `tests/phase-01/readme-entry.test.mjs` — covers `READ-01`
- [ ] `tests/phase-01/topology-order.test.mjs` — covers `READ-02`
- [ ] `tests/phase-01/chapter-nav.test.mjs` — covers `READ-03`
- [ ] `tests/phase-01/milestone-signaling.test.mjs` — covers `READ-04`
- [ ] `book/manifest.schema.json` — machine-verifiable manifest contract

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| README first-screen導讀語氣維持「書籍入口」而非一般 repo 說明 | READ-01 | 語氣與資訊密度需要人工判讀 | 開啟 `README.md`，確認第一屏先說明本書目標、最終成果與開始閱讀入口，且沒有長篇正文內容 |
| 章節 milestone 提示是否對讀者形成清楚學習預期 | READ-04 | 里程碑訊息的可理解性仍需閱讀體驗檢查 | 依 manifest 章節順序閱讀 milestone 章開頭與 `README.md` milestone 區塊，確認能明白哪一章完成 FV 計算機、哪一章完成 GitHub Pages 發布 |

---

## Validation Sign-Off

- [ ] All tasks have `<automated>` verify or Wave 0 dependencies
- [ ] Sampling continuity: no 3 consecutive tasks without automated verify
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 15s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
