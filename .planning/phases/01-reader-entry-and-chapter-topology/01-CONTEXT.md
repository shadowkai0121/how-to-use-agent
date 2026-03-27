# Phase 1: 讀者入口與章節拓樸 - Context

**Gathered:** 2026-03-27
**Status:** Ready for planning

<domain>
## Phase Boundary

本 phase 負責建立 repo 內的閱讀入口與固定章節拓樸：根目錄 `README.md` 作為導讀入口、單一 manifest 作為章節與輸出順序的來源、各章一致的章內導覽，以及清楚標示 FV 計算機完成與 GitHub Pages 發布所在章節的里程碑提示。這個 phase 釐清閱讀路徑與資訊架構，不擴張到 EPUB 建置流程本身或後續完整教學內容實作。

</domain>

<decisions>
## Implementation Decisions

### README 入口與導讀結構
- `README.md` 的第一屏必須先說明這本書在做什麼，再預告最終成果，並提供明確的「開始閱讀」入口。
- `README.md` 必須直接列出完整章節清單，讓讀者一眼看出固定閱讀順序。
- `README.md` 應採導讀語氣，而不是一般 repo 專案說明文件的語氣。
- `README.md` 必須有明顯的 milestone 區塊，清楚標示哪一章完成 FV 計算機、哪一章完成 GitHub Pages 發布。
- 每一章都應該往最終成果推進並有具體產出，但整體成果以最後整合發布為主。

### Manifest 與章節 metadata
- 單一 manifest 不只管理章節順序與檔案路徑，也要集中管理章節標題、摘要、milestone 標記、checkpoint 資訊，以及是否屬於 FV 主線。
- 章節檔名使用有順序的命名方式，例如 `01-...md`、`02-...md`，讓 repo 內的檔案順序與閱讀順序一致。
- 章節標題採任務導向命名，讓讀者能從標題直接理解這一章做完會得到什麼。
- manifest 必須能支援後續同時驅動閱讀順序、章內導覽與里程碑提示，避免資訊重複維護。

### 章內導覽策略
- 每章底部都要使用一致的導覽模板，提供上一章、下一章與回首頁連結。
- `回首頁` 一律指向根目錄 `README.md`。
- 不顯示整體閱讀進度，例如「第幾章 / 共幾章」。
- 章內導覽要保留在來源 Markdown 中供 GitHub 閱讀使用，但在 EPUB 打包輸出時必須移除。

### 里程碑與章節成果提示
- 每章開頭都要明寫「本章會完成什麼」，章尾都要有 checkpoint。
- 重大里程碑分成兩段：先完成 FV 計算機作品，再完成 GitHub Pages 發布。
- 里程碑提示同時出現在 `README.md` 的全局導覽與各章內容中，讓讀者知道自己正位於哪個里程碑階段。
- 非重大 milestone 章節不需要額外標示「小成果」徽章或獨立成果提示，但章節內容本身仍應有明確產出。

### Claude's Discretion
- `README.md` 導讀區塊的具體文案與版面細節。
- manifest 的實際欄位名稱、檔案格式與資料結構。
- 章尾導覽在來源 Markdown 中的具體表示方式，以及 EPUB 打包時移除該區塊的實作策略。
- 里程碑提示與 checkpoint 的具體文案模板。

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Phase boundary and requirements
- `.planning/ROADMAP.md` — Phase 1 的目標、成功標準與需求邊界。
- `.planning/REQUIREMENTS.md` — `READ-01`、`READ-02`、`READ-03`、`READ-04`、`CONT-02` 的正式需求來源。
- `.planning/PROJECT.md` — 專案層級的核心價值、Markdown 單一內容來源、`README.md` 作為入口，以及第一階段以 FV 計算機為教學主線的原則。
- `.planning/STATE.md` — 目前專案狀態與已鎖定的 Phase 1 決策摘要。

### Additional specs
- No external specs or ADRs exist for this phase beyond the planning documents above.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- 目前 repo 只有 `LICENSE` 與 `.planning/` 規劃文件，尚無可直接重用的章節檔案、manifest、README 結構或建置腳本。

### Established Patterns
- 專案目前採規劃先行流程，Phase 1 的實作需要從零建立讀者入口與章節拓樸。
- 已在專案層級鎖定 `README.md` 僅作入口與導讀、不承載整本正文。
- 已在專案層級鎖定章節順序與輸出順序由單一 manifest 管理。

### Integration Points
- 新增的閱讀入口會落在 repo 根目錄 `README.md`。
- Phase 1 需要建立新的章節來源檔案命名規則與 manifest 檔案，作為後續 phase 的內容與輸出基礎。
- EPUB 相關處理在後續 phase 才會正式實作，但本 phase 的章內導覽必須先為 EPUB 移除需求預留結構。

</code_context>

<specifics>
## Specific Ideas

- 讀者體驗應更像被導讀進一本可實作的電子書，而不是進入一個普通的 GitHub repo。
- milestone 要讓讀者提前知道這條學習路徑最後會做出 FV 計算機並發布到 GitHub Pages。
- 每章都應往最終成果推進，但不要對每個非 milestone 章節額外加上獨立的小成果標示。

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 01-reader-entry-and-chapter-topology*
*Context gathered: 2026-03-27*
