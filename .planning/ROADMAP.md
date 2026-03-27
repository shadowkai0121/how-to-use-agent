# Roadmap: Agent 使用電子書專案

## Overview

本路線圖以「單一 Markdown 內容來源、雙輸出、可實作教學成果」為主線，先固定讀者入口與章節拓樸，再鎖定作者寫作契約與建置驗證，之後把 antigravity / GSD 教學流程與 FV 計算機實作綁成同一條可完成、可發布、可驗證的學習路徑。

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: 讀者入口與章節拓樸** - 固定 README 入口、章節順序、章內導覽與單一 manifest 路徑。
- [ ] **Phase 2: 作者寫作契約與在地化內容源** - 建立獨立章節、GFM 安全規範、圖片占位格式與 zh-TW 術語一致性。
- [ ] **Phase 3: 雙輸出建置與發佈驗證** - 從同一內容源生成 README 與 EPUB，並加入連結、資產、EPUB 與內容分叉檢查。
- [ ] **Phase 4: Agent 協作教學主線** - 讓讀者依章節掌握 antigravity / GSD 節奏、可複製 prompt 與 checkpoint。
- [ ] **Phase 5: FV 計算機成果與公開部署** - 完成可驗證的 FV 計算機並發布到 GitHub Pages。

## Phase Details

### Phase 1: 讀者入口與章節拓樸
**Goal**: 讀者可以在 repo 中有明確入口與固定閱讀路徑，並清楚知道成果章節所在位置。  
**Depends on**: Nothing (first phase)  
**Requirements**: READ-01, READ-02, READ-03, READ-04, CONT-02  
**Success Criteria** (what must be TRUE):
  1. 讀者打開根目錄 `README.md` 即可理解本書目標、預期成果與開始閱讀入口。
  2. 讀者可依單一 manifest 驅動的固定章節順序前進，不需要自行猜測下一章或輸出順序。
  3. 讀者可在各章使用一致的上一章、下一章與回首頁連結完成導覽。
  4. 讀者可清楚辨識哪一章會完成 FV 計算機實作與 GitHub Pages 發布。
  5. 維護者調整單一章節清單後，閱讀順序與輸出順序會同步更新。
**Plans**: 3 plans

Plans:
- [ ] 01-01-PLAN.md — 建立 manifest schema、Node 驗證入口與 Phase 1 測試骨架
- [ ] 01-02-PLAN.md — 建立 canonical manifest、七個章節檔與章尾導覽同步
- [ ] 01-03-PLAN.md — 完成 README 導讀入口並跑過 Phase 1 全驗證

### Phase 2: 作者寫作契約與在地化內容源
**Goal**: 維護者可以用一致的 zh-TW GFM 內容源持續撰寫、擴寫與重組本書。  
**Depends on**: Phase 1  
**Requirements**: CONT-01, CONT-03, CONT-04, QUAL-01  
**Success Criteria** (what must be TRUE):
  1. 維護者可用獨立 Markdown 檔案新增、拆分或調整章節，且內容在 GitHub 上正常呈現。
  2. 維護者可依明確 GFM 安全寫作規範撰寫標題、連結、程式碼區塊、提示與折疊內容。
  3. 維護者可用統一格式標記待補圖片段落，並保留足夠上下文給後續人工補圖。
  4. 讀者看到的正式內容維持台灣繁體中文，且術語使用一致。
**Plans**: 3 plans

Plans:
- [ ] 02-01-PLAN.md — 擴充 manifest schema、作者驗證腳本與 Phase 2 測試骨架
- [ ] 02-02-PLAN.md — 建立 canonical zh-TW 內容樹、中文章節檔與 placeholder 資產分區
- [ ] 02-03-PLAN.md — 建立 zh-TW termbase、Vale 規則與 Phase 2 完整驗證

### Phase 3: 雙輸出建置與發佈驗證
**Goal**: 專案可以從同一組 canonical 章節生成同步的 README 與 EPUB，並在發布前攔截內容與輸出問題。  
**Depends on**: Phase 1, Phase 2  
**Requirements**: PUBL-01, PUBL-02, PUBL-03, PUBL-04, QUAL-02, QUAL-03  
**Success Criteria** (what must be TRUE):
  1. 維護者可從同一組 Markdown 章節生成可閱讀的根目錄 `README.md`，作為 GitHub 線上入口。
  2. 維護者可從同一組 Markdown 章節生成包含書名、語言、目錄與 metadata 的 EPUB。
  3. 驗證流程會在章節、`README.md` 或資產路徑失效時明確失敗。
  4. 驗證流程會在 EPUB 損壞或不合規時明確失敗。
  5. 發布前流程會在圖片占位未清除或 `README.md`、章節與 EPUB 核心內容分叉時阻擋輸出。
**Plans**: TBD

### Phase 4: Agent 協作教學主線
**Goal**: 讀者可以沿著章節掌握 antigravity 與 GSD 的協作節奏，並用可複製操作推進實作。  
**Depends on**: Phase 3  
**Requirements**: TUTO-01, TUTO-02, TUTO-05  
**Success Criteria** (what must be TRUE):
  1. 讀者可依章節理解 antigravity 與 GSD 的基本互動節奏與操作上下文。
  2. 讀者可直接複製章節中的 prompt、命令與範例，逐步推進實作。
  3. 每一章都提供 checkpoint，讓讀者能確認目前 repo 狀態、頁面結果或下一步條件。
**Plans**: TBD

### Phase 5: FV 計算機成果與公開部署
**Goal**: 讀者可以完成 FV 計算機並把成果公開部署到 GitHub Pages。  
**Depends on**: Phase 4  
**Requirements**: TUTO-03, TUTO-04  
**Success Criteria** (what must be TRUE):
  1. 讀者可依書中流程完成 FV 計算機的 HTML、CSS 與 JavaScript。
  2. 讀者可輸入範例值並手動驗證 FV 計算結果正確。
  3. 讀者可將 FV 計算機部署到 GitHub Pages 並取得可公開存取的網址。
  4. 任意使用者開啟公開網址後，都能操作可運作的 FV 計算機頁面。
**Plans**: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4 → 5

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. 讀者入口與章節拓樸 | 0/TBD | Not started | - |
| 2. 作者寫作契約與在地化內容源 | 0/TBD | Not started | - |
| 3. 雙輸出建置與發佈驗證 | 0/TBD | Not started | - |
| 4. Agent 協作教學主線 | 0/TBD | Not started | - |
| 5. FV 計算機成果與公開部署 | 0/TBD | Not started | - |
