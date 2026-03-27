# Phase 4: Agent 協作教學主線 - Research

**Researched:** 2026-03-27
**Domain:** docs-first 的 Agent 協作教學主線、可複製操作、checkpoint 與回正提示
**Confidence:** MEDIUM

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
### 教學主線與章節骨架
- 教學主線採任務驅動，從一開始就圍著 FV 計算機任務往前推進，而不是先講工具概論。
- 每章以任務成果分章，但章內固定帶一輪協作節奏。
- 每章開頭先交代本章會產出的具體成果，再說明本章會練到的協作能力。
- phase 4 聚焦教學節奏、可複製操作與回正機制，不延伸成完整的 GSD 指令教學。

### 協作 framing 與命名
- 章內協作對象統一稱為 `Agent`，不混用品牌名稱。
- 文案優先用自然語言表達規劃、執行、驗收的節拍，再視需要輕量提到 `antigravity` / `GSD`。
- `antigravity` 不作為章節主體品牌強打，重點是如何和 Agent 協作。

### Prompt / Command / Checkpoint 呈現
- 章內 prompt 與 command 以可直接複製的最短版本為主，不先給抽象模板。
- 較長 prompt / command 要拆段呈現，讓每段都有獨立用途。
- 每章只要有實作就必須有 checkpoint，且結果驗收優先於 repo 狀態驗收。
- checkpoint 後要緊貼可直接救火的回正 prompt，避免讀者在偏掉時繼續往前。

### 解釋深度
- 先讓讀者做出結果，再補最少必要解釋。
- 避免長篇工具理論打斷任務推進節奏。

### Deferred Ideas (OUT OF SCOPE)
- 正式的 GSD workflow / `$gsd-*` 指令教學。
- 更深入的協作理論或 prompt engineering 長篇解析。
- 以品牌導向為主的 antigravity / GSD 產品介紹。
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| TUTO-01 | 讀者可以依照章節理解 antigravity 與 GSD 的基本互動節奏與操作上下文 | 每章固定協作 loop、以成果驅動的章節 framing、最少必要術語 |
| TUTO-02 | 讀者可以依照章節中的可複製 prompt、命令與範例逐步完成 FV 計算機 | Prompt/Command 區塊、分段可複製內容、章與章之間的 handoff |
| TUTO-05 | 每一章都提供 checkpoint，讓讀者確認目前 repo 狀態、頁面結果或下一步條件 | 結果優先的 checkpoint、repo 證據、下一步閘門、回正 prompt |
</phase_requirements>

## Summary

Phase 4 最適合被規劃成「同一個教學 loop 重複七次」，而不是每章各講一種不同寫法。前 3 個 phase 已經鎖定 canonical content tree、README/EPUB 雙輸出與 manifest 單一真相來源，所以這一 phase 不需要重新設計內容架構；真正要補的是教學互動 contract：每章如何定義本章成果、如何給可直接複製的 Prompt/Command、如何做結果優先的 checkpoint，以及偏掉時怎麼回正。

最穩的做法是把教學 loop 本身做成可驗證契約，而不是只靠作者風格自律。每章應固定包含這一組 heading 或區塊概念：`## 本章產出`、`## 你會練到什麼`、`## Prompt`、`## Command`、`## Checkpoint`、`## 卡關先這樣回正`。其中 `Prompt` 必須只放對 Agent 說的話，`Command` 只放終端機指令，不能混在同一個 code block；`Checkpoint` 必須先寫讀者應看到的頁面、檔案或行為結果，再寫 repo 證據；`卡關先這樣回正` 則要緊接 checkpoint，直接提供一段可以複製去拉回 Agent 的 prompt。

內容拆分上，最合理的是三個 plans。Wave 1 先建立 Phase 4 驗證腳本與測試骨架，把 tutorial loop 的最小 contract 鎖死。Wave 2 再完成前言與第 1 到第 4 章，讓讀者先學會讀書方式、工作台、用 antigravity 對齊範圍、以及用 GSD 的 phase 思維拆工作。Wave 3 才把第 5 到第 7 章補齊，讓教學主線從「開始協作」一路銜接到 FV 計算機互動介面、驗證與 GitHub Pages 發布，同時跑一次 fresh-clone walkthrough 驗證 prompt / command / checkpoint 真的能照書走。

**Primary recommendation:** 不新增新的內容系統或 DSL，直接沿用 `book/manifest.json`、既有章節檔與 Phase 3 的 README/EPUB builders，額外加上一個 `scripts/verify-phase4.mjs` 與 `tests/phase-04/*.test.mjs`，把教學 loop、可複製區塊與 checkpoint / recovery contract 變成機器可檢查的規則。

## Standard Stack

### Core
| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| Node.js | 24 LTS | 驗證腳本與 `node:test` 執行環境 | Phase 3 已鎖定 Node 24 作為 repo automation runtime，Phase 4 應沿用。 |
| `node:test` | bundled | tutorial structure / checkpoint / recovery contract tests | 不需要額外 test framework；這些測試主要是文字內容與結構驗證。 |
| GFM Markdown | GitHub-compatible | 正式章節、README、EPUB 共用內容源 | Phase 2 / 3 已鎖定，Phase 4 不應分叉到另一套格式。 |

### Supporting
| Tool | Version | Purpose | When to Use |
|------|---------|---------|-------------|
| existing `scripts/build-readme.mjs` | repo-local | 將 Phase 4 教學內容同步到 root README | 每次調整前言 / manifest summary / README 入口資訊後 |
| existing `scripts/build-epub.mjs` | repo-local | 確認 Prompt/Command/Checkpoint 在 EPUB 也可閱讀 | 每次完成 Wave 3 內容整合後 |
| existing `scripts/verify-drift.mjs` | repo-local | 防止 README / EPUB 與 canonical 章節分叉 | 作為 Phase 4 full gate 的一部分 |
| existing `scripts/validate-authoring.mjs` | repo-local | 確保章節仍符合 GFM / placeholder / 術語 contract | 每次新增或改寫章節內容時 |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| 直接在章節內手寫自由格式教學 | 固定 tutorial loop contract | 自由格式較快，但會立刻犧牲可複製性與跨章一致性。 |
| 在 manifest 再增加大量 prompt / command metadata | 把教學塊直接寫在章節 Markdown 內 | Prompt / command / recovery 需要保留自然語言上下文，硬塞進 manifest 會讓 authoring 體驗更差。 |
| 只靠人工 review 檢查 checkpoint | `verify-phase4` + phase tests | 人工 review 容易漏掉某章忘了寫 recovery 或把 prompt / command 混在一起。 |

## Architecture Patterns

### Pattern 1: Repeating Tutorial Loop Per Chapter
**What:** 每章都走相同節奏：成果、能力、Prompt、Command、Checkpoint、Recovery。  
**When to use:** 只要該章有實作或 repo 狀態變化，就完整套用。  
**Example:**
```markdown
## 本章產出
## 你會練到什麼
## Prompt
## Command
## Checkpoint
## 卡關先這樣回正
```

### Pattern 2: Prompt and Command Must Be Separated
**What:** 對 Agent 說的內容與終端機執行的內容各自獨立標示。  
**When to use:** 一律使用，避免讀者把 prompt 貼進 terminal 或反過來。  
**Example:**
```markdown
## Prompt
```text
請先幫我閱讀 book/manifest.json 與 README，然後只列出接下來 3 個最小步驟。
```

## Command
```bash
npm run build:readme
node scripts/verify-phase4.mjs
```
```

### Pattern 3: Result-First Checkpoints
**What:** checkpoint 先寫讀者應看見的行為或畫面，再寫 repo 證據，最後寫是否可安全進下一步。  
**When to use:** 每章至少一次；如果章內有多個明顯狀態轉折，可多次使用。  
**Example:**
```markdown
## Checkpoint
- 你應該已經能在 README 看到更新後的章節入口與摘要。
- `git diff -- README.md book/chapters/03-用 antigravity 對齊範圍與下一步.md` 只顯示本章預期變更。
- 若兩項都成立，再進入下一章。
```

### Pattern 4: Recovery Prompt Lives Next To The Checkpoint
**What:** 章內一出現 checkpoint，就要緊跟一段最短可複製的救火 prompt。  
**When to use:** 一律使用；不要把補救策略集中到章尾 FAQ。  
**Example:**
```text
你剛剛做的內容超出這一章範圍了。請退回到上一個 checkpoint，只保留 README 與 book/chapters/03-用 antigravity 對齊範圍與下一步.md 的變更，然後重新列出本章剩下的 2 個最小步驟。
```

### Pattern 5: Fresh-Clone Walkthrough As Final Proof
**What:** 用乾淨 repo 狀態照著章節走一次，檢查 prompt、command、checkpoint 是否真的可重現。  
**When to use:** Phase 4 收尾時；這是 TUTO-02 / TUTO-05 的真實驗收。  
**Example:**
```text
clone -> install -> read chapter -> copy Prompt -> run Command -> compare Checkpoint
```

### Anti-Patterns to Avoid
- **Tool essay before action:** 先講長篇 antigravity / GSD 理論會破壞任務推進感。
- **Prompt 與 command 混寫:** 讀者會不知道哪段是對 Agent 說、哪段是對 shell 說。
- **Checkpoint 只有 repo 指令沒有結果描述:** 這違反「結果優先」的 locked decision。
- **Recovery prompt 太抽象:** `請修好剛剛的問題` 不足以救火，必須指出要退回哪個 checkpoint、保留哪些檔案。
- **Phase boundary creep:** 把完整部署教學或進階 GSD 命令說明塞進前半章，會模糊 Phase 4 / 5 分工。

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| 章節節奏一致性 | 純人工 checklist | `scripts/verify-phase4.mjs` + `tests/phase-04/*.test.mjs` | 這類結構規則很適合機器檢查。 |
| README 同步 | 直接手改 root `README.md` | 更新 canonical 章節 / manifest 後重新跑 `npm run build:readme` | Phase 3 已鎖定 README 是 generated artifact。 |
| EPUB 顯示驗收 | 只看 Markdown 原文 | 重新跑 `npm run build:epub` 再人工閱讀 | Prompt / code block / alert 在 EPUB 中可能出現不同換行與可讀性問題。 |
| 教學 metadata | 再做第二份章節控制檔 | 延用 `book/manifest.json` 的 summary / checkpoint / milestones | 多一份 metadata 只會製造 drift。 |

**Key insight:** Phase 4 的新東西不是新的 build system，而是「教學互動 contract」。把 contract 做成文字結構規則與 phase tests，比再引入一套內容工具更有效。

## Common Pitfalls

### Pitfall 1: 每章都像獨立文章，沒有共同節奏
**What goes wrong:** 讀者在第 2 章後還是不知道每章該先看哪一段、做哪一步。  
**Why it happens:** 作者只顧著各章內容，不先定教學 loop。  
**How to avoid:** 先建 `verify-phase4` 與 Phase 4 tests，再填內容。  
**Warning signs:** 某些章只有敘述，沒有 Prompt 或 Recovery。

### Pitfall 2: Prompt 太長，不能分段複製
**What goes wrong:** 讀者一次貼超長 prompt，Agent 容易超做、偏題，讀者也不會切分意圖。  
**Why it happens:** 想把上下文一次塞滿。  
**How to avoid:** 一章只保留當下最短可執行 prompt，長 prompt 拆成多段並加上用途說明。  
**Warning signs:** 一個 code block 包了目標、限制、驗收、回正所有內容。

### Pitfall 3: Checkpoint 只剩 `git status`
**What goes wrong:** 讀者 repo 狀態看起來沒錯，但畫面或輸出其實不對。  
**Why it happens:** 寫 checkpoint 比較容易想到檔案證據，不容易想到使用者視角結果。  
**How to avoid:** 規定 checkpoint 第一條必須是可觀察結果，第二條才是 repo 證據。  
**Warning signs:** checkpoint 沒有 `README`, `頁面`, `輸出`, `你應該看到` 類型語句。

### Pitfall 4: Recovery prompt 離失敗點太遠
**What goes wrong:** 讀者卡住時需要回頭翻頁找補救方法，心智負擔上升。  
**Why it happens:** 把補救集中在 FAQ 或附錄。  
**How to avoid:** 每個 checkpoint 後面緊跟 recovery prompt。  
**Warning signs:** 章內提到「如果錯了請看後面補充」。

### Pitfall 5: Phase 4 內容吃掉 Phase 5
**What goes wrong:** 第 5 到第 7 章已把所有實作細節做完，Phase 5 沒有清楚的執行空間。  
**Why it happens:** 為了滿足 TUTO-02，把完整實作與部署細節全提前。  
**How to avoid:** Phase 4 著重「協作節奏與可複製操作」，Phase 5 再把最終程式碼、計算驗證與公開部署做滿。  
**Warning signs:** 章節開始大量展開與 phase goal 無關的 API 或 CSS 細節。

## Code Examples

Verified repo-local patterns to reuse:

### Phase 4 Gate Command
```bash
node scripts/verify-phase4.mjs
node --test tests/phase-04/*.test.mjs
```

### Copyable Prompt Block
```text
請先閱讀 book/chapters/03-用 antigravity 對齊範圍與下一步.md，然後只幫我列出這一章要做的 3 個最小步驟，不要直接改檔。
```

### Copyable Command Block
```bash
npm run build:readme
npm run build:epub
```

### Recovery Prompt
```text
你剛剛跨到下一章了。請退回上一個 checkpoint，只保留本章檔案的變更，重新列出這一章剩下的步驟與驗收條件。
```

## State of the Art

| Old Approach | Current Approach | Impact |
|--------------|------------------|--------|
| 教學章節自由格式 | per-chapter tutorial loop contract | 提高可預測性與可複製性 |
| 靠人工看內容有沒有 prompt / checkpoint | phase-specific verification script + tests | 可以在 CI / local gate 及早攔下缺漏 |
| README 入口手工寫摘要 | 由 canonical content + builder 同步輸出 | 降低入口頁與章節主線分叉 |

**Deprecated/outdated:**
- 直接在 `README.md` 維護主線教學摘要，繞過 canonical `book/` 內容。
- 把 checkpoint 視為章尾總結，而不是當下是否能安全前進的閘門。

## Open Questions

1. **第 5 到第 7 章的實作深度要畫在哪裡，才不會吃掉 Phase 5？**
   - What we know: Phase 4 必須讓讀者沿章節推進 FV 計算機主線，但不擴張成完整最終交付。
   - Recommendation: Phase 4 先把協作節奏、可複製 prompt / command、checkpoint / recovery 契約完整寫出；Phase 5 再把最終實作品質與部署交付做滿。

2. **是否要把 recovery prompt 也寫進 manifest？**
   - What we know: summary / checkpoint / milestones 已在 manifest。
   - Recommendation: 不要。recovery prompt 與章節上下文高度耦合，留在章節 Markdown 內更自然。

3. **fresh-clone walkthrough 的最低通過條件是什麼？**
   - What we know: 要能照章節複製 prompt / command 前進，並用 checkpoint 發現偏差。
   - Recommendation: 至少確認第 1、3、5、7 章都能在乾淨狀態下完成一輪「複製 prompt -> 執行 command -> 對照 checkpoint」。

## Validation Architecture

### Test Framework
| Property | Value |
|----------|-------|
| Framework | `node:test` bundled with Node 24 LTS |
| Config file | `none — Phase 4 adds tests/phase-04 helpers and a dedicated gate script` |
| Quick run command | `node --test tests/phase-04/chapter-loop-contract.test.mjs tests/phase-04/checkpoint-recovery.test.mjs` |
| Full suite command | `node scripts/verify-phase4.mjs && node --test tests/phase-04/*.test.mjs && npm run build:readme && npm run build:epub && node scripts/verify-drift.mjs` |
| Estimated runtime | ~20 seconds |

### Phase Requirements → Test Map
| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| TUTO-01 | 每章有固定協作 loop，讀者看得出規劃 / 執行 / 驗收節奏 | structure | `node --test tests/phase-04/chapter-loop-contract.test.mjs` | ❌ Wave 0 |
| TUTO-02 | 每章提供可直接複製的 prompt / command / example，且順序能串成主線 | integration/content | `node --test tests/phase-04/copyable-steps.test.mjs` | ❌ Wave 0 |
| TUTO-05 | 每章有結果優先 checkpoint 與緊鄰的 recovery prompt | structure/content | `node --test tests/phase-04/checkpoint-recovery.test.mjs` | ❌ Wave 0 |

### Sampling Rate
- **Per task commit:** `node --test tests/phase-04/chapter-loop-contract.test.mjs tests/phase-04/checkpoint-recovery.test.mjs`
- **Per wave merge:** `node scripts/verify-phase4.mjs && node --test tests/phase-04/*.test.mjs`
- **Phase gate:** full suite command green + fresh-clone walkthrough checkpoint completed

### Wave 0 Gaps
- [ ] `package.json` — declare `test:phase-04`, `verify:phase4`, `check:phase-04`
- [ ] `scripts/verify-phase4.mjs` — enforce chapter loop / copyable block / checkpoint contract
- [ ] `tests/phase-04/helpers.mjs` — shared content readers and assertions
- [ ] `tests/phase-04/chapter-loop-contract.test.mjs` — verifies fixed tutorial headings
- [ ] `tests/phase-04/copyable-steps.test.mjs` — verifies Prompt / Command / example contract
- [ ] `tests/phase-04/checkpoint-recovery.test.mjs` — verifies result-first checkpoints and recovery prompts

### Manual-Only Verifications
| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| 在乾淨 repo 狀態照章節完成至少一輪 copy/paste walkthrough | TUTO-02, TUTO-05 | 真正的可複製性要看讀者是否能按章節順序完成 prompt / command / checkpoint | fresh clone 後依序照第 1、3、5、7 章執行，確認每章的 checkpoint 與 recovery 都可用 |
| EPUB 中的 Prompt / Command / Checkpoint 區塊可讀性正常 | TUTO-01, TUTO-02 | code block、alert 與段落換行在 EPUB 可能與 GitHub 呈現不同 | 重建 EPUB，打開檢查至少三章的教學區塊可視覺辨識 |

## Sources

### Primary (HIGH confidence)
- `.planning/phases/04-agent/04-CONTEXT.md`
- `.planning/ROADMAP.md`
- `.planning/REQUIREMENTS.md`
- `.planning/PROJECT.md`
- `.planning/STATE.md`
- `.planning/phases/01-reader-entry-and-chapter-topology/01-CONTEXT.md`
- `.planning/phases/02-authoring-contract-and-localized-content-source/02-CONTEXT.md`
- `.planning/phases/03-dual-output-build-and-publish-validation/03-CONTEXT.md`
- `.planning/research/SUMMARY.md`
- `.planning/research/FEATURES.md`
- `.planning/research/PITFALLS.md`

### Secondary (MEDIUM confidence)
- Existing Phase 1-3 plan artifacts as repo pattern references

### Tertiary (LOW confidence)
- None

## Metadata

**Confidence breakdown:**
- Tutorial structure and constraints: HIGH - directly grounded in CONTEXT.md and prior phase decisions.
- Plan decomposition: MEDIUM - repo patterns are clear, but actual chapter prose depth will still need execution judgment.
- Validation strategy: MEDIUM - strong fit to repo structure, but final fresh-clone workflow details depend on executed content.

**Research date:** 2026-03-27
**Valid until:** 2026-04-26
