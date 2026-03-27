# Phase 2: 作者寫作契約與在地化內容源 - Context

**Gathered:** 2026-03-27
**Status:** Ready for planning

<domain>
## Phase Boundary

本 phase 負責建立作者持續撰寫本書時要遵守的 canonical 內容契約：內容樹如何分區、每章 Markdown 檔的顆粒度、manifest 承載哪些章節控制資訊、哪些 GitHub Flavored Markdown 寫法可安全使用、圖片待補如何以統一格式預留，以及 zh-TW 正式內容的術語一致性。這一輪只鎖定作者內容來源與寫作規範，不擴張到 README/EPUB 建置流程、CI 驗證或發佈自動化本身。

</domain>

<decisions>
## Implementation Decisions

### 內容樹與章節來源結構
- canonical 內容樹要明確分區，至少拆成前言、章節、附錄與資產，不採全部內容平鋪在同一層的做法。
- 一章固定對應一個 Markdown 檔，不使用多檔拼成單一章節的模式。
- 章節檔名可直接使用中文。
- 若章節順序調整，章節檔名也要跟著更新，讓檔名順序與實際閱讀順序保持一致。

### Manifest 承載範圍
- manifest 是章節層級控制資訊的單一來源，至少承載標題、摘要、milestone、checkpoint、作者備註與輸出控制。
- 章節 Markdown 主要承載正文內容，不負責再重複維護這些章節層級控制資訊。
- 後續 README 導覽、章節順序與輸出行為應以 manifest 為真相來源，避免 metadata 分散在不同檔案中。

### GFM 安全寫作契約
- 每章文件都從 `#` 標題開始，且標題層級禁止跳階。
- 連結策略以相對路徑為主；只有確實需要指向外部資源時才使用完整 URL。
- 作者可使用 GitHub 在 repo 檔案中可可靠處理的正式支援語法作為安全子集，包括一般 GFM 語法與 GitHub 支援的進階格式。
- 禁用原則不是限制所有進階語法，而是禁用 GitHub 無法可靠處理、或明顯依賴非 GitHub 特殊後處理才能成立的寫法。

### 圖片占位格式
- 待補圖使用 Markdown 圖片語法預留，讓未來補圖後不需要改寫整段結構。
- 每個待補圖占位都要先填入預期檔名，後續人類只需要把圖片放到正確路徑。
- 圖片占位的必要資訊僅要求 Markdown 圖片語法本身與 alt text，不額外增加複雜欄位。
- 待補圖必須以獨立區塊出現，不嵌在正文句子中。
- 即使當下沒有圖片，也傾向先預留占位。
- placeholder 的機器辨識方式是讓待補圖指向 placeholder 路徑，與正式圖片資產路徑分流。

### zh-TW 術語與英文保留策略
- 專有名稱與程式設計名詞維持英文原文，不強制翻譯為中文。
- 介面操作敘述採「中文（原文）」格式，例如設定（`Settings`）或部署（`Deploy`）。
- 需要建立一份可持續擴充的術語清單，作為後續寫作與審稿的一致性依據。
- 若同一概念存在多種中文譯法，優先直接使用英文，以避免譯名漂移。

### Claude's Discretion
- 實際資料夾名稱、內容根目錄名稱與檔案樹細節，只要符合前言 / 章節 / 附錄 / 資產分區原則即可。
- manifest 的實際欄位命名、資料格式與 schema 細節。
- placeholder 路徑命名慣例與其在 repo 內的具體放置位置。
- 術語清單的檔名、格式與是否拆成主表加補充說明。

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Phase boundary and locked project constraints
- `.planning/ROADMAP.md` — Phase 2 的目標、成功標準與需求邊界。
- `.planning/REQUIREMENTS.md` — `CONT-01`、`CONT-03`、`CONT-04`、`QUAL-01` 的正式需求來源。
- `.planning/PROJECT.md` — 專案層級的 GitHub-first、單一 Markdown 內容來源、繁中優先與圖片補件前提。
- `.planning/STATE.md` — 目前專案狀態，以及已預先鎖定的 Phase 2 / Phase 3 方向摘要。
- `.planning/phases/01-reader-entry-and-chapter-topology/01-CONTEXT.md` — 延續 Phase 1 已鎖定的 README 入口角色、manifest 單一真相來源與閱讀順序原則。

### Research grounding for authoring contract
- `.planning/research/SUMMARY.md` — 單一內容來源、GFM-safe 寫作合約、placeholder 規則與 zh-TW 一致性的整體研究結論。
- `.planning/research/FEATURES.md` — 章節化 Markdown、GFM 安全子集、圖片占位與術語表的產品化需求。
- `.planning/research/PITFALLS.md` — 雙來源分叉、GitHub/EPUB 可攜性落差、placeholder 混用與 zh-TW/CJK 品質風險。
- `.planning/research/ARCHITECTURE.md` — canonical 內容樹、manifest 與共享資產的建議結構。
- `.planning/research/STACK.md` — GitHub repository Markdown 可依賴的能力邊界，以及 GFM authoring 的工具與限制。

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- 目前 repo 尚無現成的章節來源樹、manifest、README 生成器或圖片資產流程可直接重用；phase 2 會先建立這些作者內容契約的基礎。
- `.planning/` 內已有 roadmap、requirements、state、research 與 Phase 1 context，可直接作為 phase 2 規格與命名原則的來源。

### Established Patterns
- 專案目前採 planning-first 流程，先用規劃文件鎖定內容與發佈策略，再進入實作。
- `README.md` 已被鎖定為入口與導讀，而非整本書正文。
- 章節順序與輸出順序已被鎖定要由單一 manifest 管理。
- 正式內容已被鎖定維持台灣繁體中文，且圖片待補格式必須可機器檢查。

### Integration Points
- Phase 2 新增的內容樹與 manifest，會成為後續 README 生成、EPUB 組書與驗證流程的上游真相來源。
- placeholder 路徑慣例必須能與未來的資產目錄結構銜接，避免之後正式圖片與待補圖混在一起。
- 術語清單需要放在後續作者與審稿流程都能穩定引用的位置，作為 zh-TW 內容一致性的共享依據。

</code_context>

<specifics>
## Specific Ideas

- 內容控制資訊應盡量集中在 manifest，讓章節 Markdown 專注於正文。
- 圖片占位應該長得像最終 Markdown 圖片結構，這樣補圖時不需要重寫內容，只需把圖片放到正確路徑。
- 專有名稱、程式設計名詞與有歧義的術語，傾向直接保留英文，減少翻譯漂移。
- 介面操作敘述採「中文（原文）」風格，兼顧在地敘述與實際畫面可對照性。

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 02-authoring-contract-and-localized-content-source*
*Context gathered: 2026-03-27*
