# Phase 4: Agent 協作教學主線 - Context

**Gathered:** 2026-03-27
**Status:** Ready for planning

<domain>
## Phase Boundary

本 phase 負責把讀者帶進以 FV 計算機為主線的 Agent 協作教學流程：讀者要能沿著章節，用可直接複製的 prompt、命令與範例推進任務，並在每個實作節點用 checkpoint 確認自己沒有走偏。這一輪聚焦在教學節奏、章節寫法、可複製操作與回正機制，不擴張到正式 GSD 指令教學、FV 計算機最終完整交付，或 GitHub Pages 公開部署本身。

</domain>

<decisions>
## Implementation Decisions

### 教學主線與章節骨架
- 教學主線採任務驅動，從一開始就圍著 FV 計算機任務往前推進，而不是先講工具概論。
- 章節以任務成果分章，但每章內固定帶一輪協作節奏。
- 每章的寫法採最短可執行步驟優先，先讓讀者做出結果，再補最少必要解釋。
- 每章開頭要先寫清楚本章會產出的具體成果，再補這一章會練到的協作能力。

### 協作流程 framing
- phase 4 先照 GSD 的開發流程來教讀者協作節奏，但不正式展開 GSD 指令操作。
- 文案上先不用強打 `GSD` 名稱，優先用自然語言表達規劃、執行、驗收等流程節拍。
- 章內對協作對象統一稱為 `Agent`，不混用品牌名或工具名。
- `antigravity` 不作為章節主體品牌來強調，重點放在「和 Agent 對話協作」本身。

### Prompt 與命令呈現方式
- 章內 prompt / 命令以可直接複製的最短版本作為主呈現。
- 主文先放當下章節可直接使用的具體實例版，再補充如何改寫成自己的版本。
- 較長的 prompt / 命令要分段呈現，讓每段都能單獨理解與複製。
- 章內排版以實際操作順序為主，但每段都要用明顯標籤區分 `Prompt` 與 `Command`。

### Checkpoint 設計
- 只要章內有實作，就要提供 checkpoint，不只在章尾集中驗收。
- checkpoint 的優先順序是先確認畫面或行為結果，再確認 repo 狀態，最後才確認是否可安全進到下一步。
- checkpoint 的主體採結果加 repo 證據的形式，但結果驗收必須放在最前面。
- 若該章只能驗證局部成果，仍要以最小可觀察結果作為正式 checkpoint，而不是延後到下一章再驗。
- checkpoint 語氣偏驗收清單，讓讀者能快速比對自己是否達標。

### 卡關回正方式
- 當 Agent 回應偏掉、做太多或做錯方向時，章內要先給可直接救火的回正 prompt，再補如何判斷偏差與自行修正。
- 當 repo 狀態與章節預期不一致時，優先引導讀者退回最近 checkpoint，而不是直接進行模糊補救。
- 回正內容要緊貼在各章 checkpoint 後面，讓讀者在出錯當下就能修正。
- 補救策略偏保守，寧可回到上一個安全點，也不要帶著錯誤繼續往下做。

### 解釋深度與章內密度
- phase 4 先不追求深入原理解釋，結果做出來後只補最少必要說明。
- 教學密度偏實作導向，避免長篇概念說明打斷任務前進節奏。

### Claude's Discretion
- 各章固定協作節奏的具體小標題命名與版型。
- `Prompt` / `Command` 區塊的視覺樣式與標示格式。
- 每章 checkpoint 的具體條列模板，只要維持成果優先、repo 證據次之即可。
- 回正 prompt 的具體措辭與示例長度。

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Phase boundary and formal requirements
- `.planning/ROADMAP.md` — Phase 4 的目標、成功標準與需求邊界。
- `.planning/REQUIREMENTS.md` — `TUTO-01`、`TUTO-02`、`TUTO-05` 的正式需求來源。
- `.planning/PROJECT.md` — 專案核心價值、GitHub-first 電子書定位，以及 FV 計算機作為教學成果主線的原則。
- `.planning/STATE.md` — 目前專案狀態與已鎖定的跨 phase 決策摘要。

### Prior phase decisions that carry forward
- `.planning/phases/01-reader-entry-and-chapter-topology/01-CONTEXT.md` — `README.md` 作為入口、manifest 單一真相來源、章節 milestone 與 checkpoint metadata 的上游決策。
- `.planning/phases/02-authoring-contract-and-localized-content-source/02-CONTEXT.md` — canonical 內容樹、章節 metadata 集中管理、GFM 安全寫作契約與 zh-TW 內容策略。
- `.planning/phases/03-dual-output-build-and-publish-validation/03-CONTEXT.md` — `README.md` / EPUB 同源輸出、README 入口定位，以及內容分叉與 placeholder 驗證的下游限制。

### Research grounding for tutorial structure
- `.planning/research/SUMMARY.md` — docs-first 教學產品定位、單一內容來源、FV 計算機作為 companion example、以及 fresh-clone walkthrough 的重要性。
- `.planning/research/FEATURES.md` — 可複製 prompts / commands、章節 checkpoint、結果導向教學路徑等產品需求。
- `.planning/research/PITFALLS.md` — prompt 漂移、README / 章節 / 實作分叉與「看起來可行但實際不可重現」的主要風險。

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- 目前 repo 尚無實際章節稿、prompt 區塊模板、checkpoint 模板或 FV 計算機範例程式可直接重用。
- `.planning/` 內既有 roadmap、requirements、state、research 與前 3 個 phase context，可直接作為 phase 4 的規格與教學約束來源。

### Established Patterns
- 專案目前採 planning-first 流程，先用 context 鎖定內容決策，再交給後續 research / planning / implementation。
- `README.md` 已被鎖定為入口與導讀，不承載整本正文。
- 章節順序、摘要、milestone 與 checkpoint 控制資訊已被鎖定應由單一 manifest 集中管理。
- 正式內容維持台灣繁體中文，且所有正式輸出都受雙輸出一致性與 placeholder 驗證約束。

### Integration Points
- phase 4 會定義實際教學章節如何使用 manifest metadata 呈現 prompt、command、checkpoint 與 milestone 資訊。
- 這個 phase 產出的教學節奏，將直接決定 phase 5 FV 計算機實作與部署章節要如何承接。
- phase 4 的內容設計必須和 phase 3 已鎖定的 README / EPUB 生成流程相容，避免引入只適用單一輸出的教學結構。

</code_context>

<specifics>
## Specific Ideas

- 教學應該讓讀者先「做出東西」，再理解自己剛剛如何和 Agent 協作，而不是先上工具理論。
- phase 4 要讓讀者先習慣 GSD 形狀的開發節奏，但先不把注意力拉到 `$gsd-*` 指令本身。
- 只要章內有實作，就要有可立即比對的 checkpoint，避免讀者做了幾章才發現早就走偏。
- prompt / command 應該貼近真實操作順序，讀者看到什麼就做什麼，但必須清楚知道自己是在對 Agent 說話還是在終端機執行命令。

</specifics>

<deferred>
## Deferred Ideas

- 正式的 GSD 指令操作與 workflow 命令教學 — 留到下一個 milestone。
- 更深入的「為什麼這樣和 Agent 協作」原理解析 — 留到後續 milestone 或補充章節深化。
- 更強調 `antigravity` 或 `GSD` 品牌名稱的產品導向教學寫法 — 不屬於本 phase 的主體。

</deferred>

---

*Phase: 04-agent*
*Context gathered: 2026-03-27*
