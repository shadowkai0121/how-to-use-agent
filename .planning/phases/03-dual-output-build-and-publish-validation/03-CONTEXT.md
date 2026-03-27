# Phase 3: 雙輸出建置與發佈驗證 - Context

**Gathered:** 2026-03-27
**Status:** Ready for planning

<domain>
## Phase Boundary

本 phase 負責把前兩個 phase 已經鎖定的 canonical 章節來源，接成同一條可重建、可驗證、可發布的雙輸出流程：從同一組章節生成根目錄 `README.md` 與 EPUB，並在本機、PR 與 release 階段攔截 build 失敗、檢查失敗、內容分叉、placeholder 殘留、缺圖與連結問題。這一輪釐清的是輸出與驗證流程要交付到什麼程度，不擴張到教學章節內容本身、FV 計算機實作，或更晚期的編輯品質強化工作。

</domain>

<decisions>
## Implementation Decisions

### README 生成範圍
- 生成後的根目錄 `README.md` 必須固定包含：本書目的、適合誰、開始閱讀、章節索引、里程碑提示、取得 EPUB 的方式，以及發布 / 驗證狀態資訊。
- README 中的章節索引展示層級為「章節標題 + 摘要」。
- README 同時扮演讀者入口與發佈入口，但仍不承載整本書正文。
- root `README.md` 是由 canonical 內容與模板生成後提交到 repo 的正式產物，不應直接手工維護。

### README 與 canonical 內容的同步範圍
- README 與章節之間的同步重點是入口資訊一致，而不是要求全文逐字同步。
- 需要被視為核心同步內容的包含：章節順序、章節標題、章節摘要、milestone、取得 EPUB 的資訊，以及 README 中保留的固定入口型摘要段落。
- README 可以保留少量入口導向的摘要段落，但不得擴張成第二套正文。

### EPUB 完整度與書籍面貌
- v1 EPUB 的最低標準是「基本正確且可交付」：可閱讀、metadata 正確、語言正確、目錄可用。
- v1 不要求完整封面設計與完整版次資訊，但不能只有一個勉強打得開的檔案。
- 封面先採文字型封面，只放書名與基本資訊，後續再升級設計。
- EPUB 的章節順序必須和 GitHub 章節閱讀順序完全一致。
- Phase 3 就要把 EPUB 品質做到基本可交付，至少包含一種閱讀器 smoke test。

### 驗證層級與阻擋策略
- 驗證分成三層：本機快速檢查、PR 檢查、release 全檢查。
- 驗證策略偏向嚴格 gate，而不是先放寬再補救。
- build 失敗、check error、內容分叉都是明確硬阻擋條件。
- 除了 error 以外，與輸出品質或一致性有關的 warning 也先視為阻擋，不保留寬鬆 warning 級別。
- 驗證失敗時必須提供可讀摘要，而不是只有 exit code，方便作者直接定位問題類型與影響範圍。

### 內容分叉、placeholder 與資產失效的判定
- 「核心內容分叉」的判定以入口層與輸出層資訊為主，不要求 README 與章節全文逐字相同。
- 只要 README 與 canonical 章節在章節順序、標題、摘要、milestone、EPUB 取得資訊或固定入口摘要段落上不同步，就視為內容分叉。
- placeholder 殘留採嚴格規則：正式輸出只要還出現 placeholder 路徑、placeholder 檔名慣例，或任何明顯待補圖標記，就直接 fail。
- 缺圖、失效相對連結、README 生成過期，也都屬於硬阻擋，而不是可延後處理的提醒。
- 連結與資產檢查範圍同時涵蓋內部與外部：README、章節與生成產物都要檢查，相對連結、本地資產與外部 URL 一起驗證。

### Claude's Discretion
- 三層驗證各自實際包含哪些具體指令與工具，以及哪些檢查在本機只做 smoke version、哪些留到 PR 或 release。
- README 與 EPUB 的具體模板、樣式與生成步驟。
- 發布 / 驗證狀態資訊在 README 中的具體呈現方式。
- 外部連結檢查的重試策略、逾時處理與報表格式，只要不弱化硬阻擋原則即可。

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Phase boundary and project constraints
- `.planning/ROADMAP.md` — Phase 3 的目標、成功標準與需求邊界。
- `.planning/REQUIREMENTS.md` — `PUBL-01`、`PUBL-02`、`PUBL-03`、`PUBL-04`、`QUAL-02`、`QUAL-03` 的正式需求來源。
- `.planning/PROJECT.md` — GitHub-first、單一 Markdown 內容來源、zh-TW 正式內容與人工補圖前提等專案層級原則。
- `.planning/STATE.md` — 目前規劃狀態與已鎖定的跨 phase 決策摘要。

### Prior phase decisions that carry forward
- `.planning/phases/01-reader-entry-and-chapter-topology/01-CONTEXT.md` — README 作為入口、manifest 作為單一章節真相來源、章節順序與里程碑導覽的既有決策。
- `.planning/phases/02-authoring-contract-and-localized-content-source/02-CONTEXT.md` — canonical 內容樹、manifest metadata 集中、GFM 安全合約、placeholder 路徑與術語策略。

### Research grounding for build and validation
- `.planning/research/SUMMARY.md` — 單一來源雙輸出、README 生成、Pandoc / EPUBCheck / CI 驗證與 drift 風險的整體結論。
- `.planning/research/ARCHITECTURE.md` — `README.md` 作為 generated artifact、canonical content tree、`pandoc/` / `scripts/` / `dist/` 分工與 build adapter 模式。
- `.planning/research/STACK.md` — Pandoc 3.9.x、EPUBCheck 5.3.x、Node 24 LTS、pnpm 10.x、markdownlint-cli2、Vale、lychee 與 GitHub Actions 的建議角色。
- `.planning/research/PITFALLS.md` — README/EPUB 雙來源分叉、GitHub 與 EPUB 可攜性落差、資產路徑失控、placeholder 漏出與 release gate 風險。
- `.planning/research/FEATURES.md` — 讀者入口、雙輸出、圖片占位、章節導覽與 EPUB 取得方式的產品層需求。

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- 目前 repo 仍幾乎只有 `.planning/` 文件，尚無 build script、content tree、manifest parser、README generator、Pandoc defaults、EPUB metadata 檔或 CI workflow 可直接重用。
- 已存在的 planning 文件、research 文件與前兩個 phase context，可直接作為 phase 3 的規格來源。

### Established Patterns
- `README.md` 已被鎖定為入口與導讀，而非整本書正文。
- manifest 已被鎖定為章節順序、摘要、milestone、checkpoint、作者備註與輸出控制的集中來源。
- 正式內容已被鎖定為同一組 canonical Markdown 章節，不允許為 GitHub 與 EPUB 分別維護兩份正文。
- placeholder 已被鎖定為 Markdown 圖片語法加 placeholder 路徑慣例，方便後續自動掃描與補圖。

### Integration Points
- Phase 3 需要把未來的 canonical 章節樹與 manifest 接上 README 生成流程與 EPUB build 流程。
- README drift 檢查需要直接對接 root `README.md` 這個提交到 repo 的正式產物。
- EPUB build 需要和 metadata、封面、資產路徑與 placeholder 掃描規則協同。
- 驗證層級需要能在本機、PR 與 release 三個時點重用同一套規則，只是嚴格度與覆蓋範圍不同。

</code_context>

<specifics>
## Specific Ideas

- README 應維持入口導向，但允許保留少量固定摘要段落，而不是只有純連結清單。
- EPUB 先追求「基本可交付」，不要等完整美術封面或完整版次資訊才算完成。
- 發布品質策略偏保守，warning 也先當成阻擋，以免把壞輸出拖到 later cleanup。
- 內容分叉的真相重點在入口資訊與輸出一致性，不是要求 README 變成第二份正文。

</specifics>

<deferred>
## Deferred Ideas

- 更高標準的多閱讀器 zh-TW / CJK 排版 QA 與更完整的閱讀器裝置矩陣 — 留到後續編輯品質 / 發布就緒 phase 深化。
- 更成熟的封面設計與完整版次資訊 — 後續可升級，但不是 phase 3 的最低交付門檻。

</deferred>

---

*Phase: 03-dual-output-build-and-publish-validation*
*Context gathered: 2026-03-27*
