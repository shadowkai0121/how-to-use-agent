# Requirements: Agent 使用電子書專案

**Defined:** 2026-03-27
**Core Value:** 讓台灣繁中讀者能跟著章節內容，實際使用 Agent 完成第一個可公開部署的成果，理解與 antigravity、GSD 協作的核心方法。

## v1 Requirements

### Reading Experience

- [ ] **READ-01**: 讀者可以從根目錄 `README.md` 立即理解本書目標、預期成果與開始閱讀的入口
- [ ] **READ-02**: 讀者可以依照明確章節順序進入各章，不需要自行猜測閱讀路徑
- [ ] **READ-03**: 讀者可以在章節內使用一致的上一章、下一章與回首頁連結完成導覽
- [ ] **READ-04**: 讀者可以在閱讀流程中清楚知道哪一章會完成 FV 計算機實作與 GitHub Pages 發布

### Content Source

- [ ] **CONT-01**: 維護者可以用獨立 Markdown 檔案管理各章內容，且語法符合 GitHub Flavored Markdown
- [ ] **CONT-02**: 維護者可以透過單一章節清單或 manifest 管理章節順序與輸出順序
- [ ] **CONT-03**: 維護者可以依照明確的 GFM 安全寫作規範撰寫標題、連結、程式碼區塊、提示與折疊內容
- [ ] **CONT-04**: 維護者可以用統一格式標記待補圖片段落，讓人類後續補圖時不必重新搜尋上下文

### Publishing Pipeline

- [ ] **PUBL-01**: 維護者可以從同一組 Markdown 章節產生可閱讀的根目錄 `README.md`
- [ ] **PUBL-02**: 維護者可以從同一組 Markdown 章節產生包含書名、語言、目錄與 metadata 的 EPUB
- [ ] **PUBL-03**: 專案可以在建置或發佈流程中執行 EPUB 驗證，避免輸出損壞或不合規
- [ ] **PUBL-04**: 專案可以在檢查流程中偵測章節、README 與資產之間的失效連結或路徑問題

### Tutorial Outcome

- [ ] **TUTO-01**: 讀者可以依照章節理解 antigravity 與 GSD 的基本互動節奏與操作上下文
- [ ] **TUTO-02**: 讀者可以依照章節中的可複製 prompt、命令與範例逐步完成 FV 計算機
- [ ] **TUTO-03**: 讀者可以完成 FV 計算機的 HTML、CSS 與 JavaScript，並得到可手動驗證的計算結果
- [ ] **TUTO-04**: 讀者可以將 FV 計算機部署到 GitHub Pages，並取得可公開存取的網址
- [ ] **TUTO-05**: 每一章都提供 checkpoint，讓讀者確認目前 repo 狀態、頁面結果或下一步條件

### Quality And Localization

- [ ] **QUAL-01**: 所有正式書籍內容都以台灣繁體中文撰寫，並維持一致的術語用法
- [ ] **QUAL-02**: 發布前流程可以檢查未清除的圖片占位標記，避免把待補圖提示直接發佈給讀者
- [ ] **QUAL-03**: 發布前流程可以確認 `README.md`、章節與 EPUB 的核心內容沒有人工分叉

## v2 Requirements

### Reader Support

- **SUPP-01**: 讀者可以查看常見錯誤與修復附錄，例如 GitHub Pages 404、公式錯誤與 prompt 漂移
- **SUPP-02**: 讀者可以查看台灣繁中術語表與中英對照附錄
- **SUPP-03**: 讀者可以比對各章節完成時的 repo 狀態快照或版本標記

### Distribution

- **DIST-01**: 專案可以提供 PDF 或紙本友善版輸出
- **DIST-02**: 專案可以提供多語系版本

### Portal Features

- **PORT-01**: 讀者可以在網頁版文件中使用全文搜尋
- **PORT-02**: 讀者可以在網頁版文件中使用 AI 問答、留言或協作功能
- **PORT-03**: 專案可以維護多版本文件

## Out of Scope

| Feature | Reason |
|---------|--------|
| 非 GFM 相容的重度客製 Markdown 元件 | 會直接破壞 GitHub 可讀性與 EPUB 可攜性 |
| 把 `README.md` 當成整本書正文 | 會造成內容分叉與首頁過長，違反 README 作為入口的角色 |
| 第一階段就加入搜尋、留言、Ask AI 等 portal 功能 | 會分散主線，且對 GitHub 與 EPUB 雙輸出幫助有限 |
| 第一階段就追求 PDF 或紙本級排版 | 目前重點是 GitHub 閱讀與 EPUB 交付 |
| 第一階段就做多語系內容同步 | 會顯著提高術語、截圖與維護成本 |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| READ-01 | Unmapped | Pending |
| READ-02 | Unmapped | Pending |
| READ-03 | Unmapped | Pending |
| READ-04 | Unmapped | Pending |
| CONT-01 | Unmapped | Pending |
| CONT-02 | Unmapped | Pending |
| CONT-03 | Unmapped | Pending |
| CONT-04 | Unmapped | Pending |
| PUBL-01 | Unmapped | Pending |
| PUBL-02 | Unmapped | Pending |
| PUBL-03 | Unmapped | Pending |
| PUBL-04 | Unmapped | Pending |
| TUTO-01 | Unmapped | Pending |
| TUTO-02 | Unmapped | Pending |
| TUTO-03 | Unmapped | Pending |
| TUTO-04 | Unmapped | Pending |
| TUTO-05 | Unmapped | Pending |
| QUAL-01 | Unmapped | Pending |
| QUAL-02 | Unmapped | Pending |
| QUAL-03 | Unmapped | Pending |

**Coverage:**
- v1 requirements: 20 total
- Mapped to phases: 0
- Unmapped: 20 ⚠️

---
*Requirements defined: 2026-03-27*
*Last updated: 2026-03-27 after initial definition*
