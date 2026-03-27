# Agent 使用電子書專案

## What This Is

這是一個以「如何使用 Agent」為主題的電子書專案，內容以台灣繁體中文撰寫，讓讀者能直接在 GitHub 上透過 `README.md` 閱讀，也能使用專案打包出的 EPUB 電子書離線閱讀。專案內容以章節化 Markdown 檔案管理，並以可實作、可交付、可發布的教學案例帶領讀者學會如何與 Agent 協作。

第一階段聚焦在帶領讀者透過 antigravity 與 GSD 的互動流程，產出可部署到 GitHub Pages 的 FV（Future Value）計算機網頁，讓讀者從閱讀一路走到實作與發布。

## Core Value

讓台灣繁中讀者能跟著章節內容，實際使用 Agent 完成第一個可公開部署的成果，理解與 antigravity、GSD 協作的核心方法。

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] 建立以 GitHub Flavored Markdown 為單一內容來源的電子書寫作流程
- [ ] 提供 `README.md` 線上閱讀入口與 EPUB 打包輸出
- [ ] 以章節 Markdown 檔案管理內容，支援後續擴寫與重組
- [ ] 第一階段內容可帶領讀者完成 GitHub Pages FV 計算機實作
- [ ] 內容全程採用台灣繁體中文，並在需插圖處保留請人類補圖的段落提示

### Out of Scope

- PDF 或紙本排版最佳化 — 目前先聚焦在 GitHub 閱讀與 EPUB 輸出
- 多語系版本 — 第一階段先專注在台灣繁體中文品質與一致性
- 進階多代理協作案例大全 — 先完成從零到部署 FV 計算機的教學主線

## Context

- 這是一個內容導向、文件優先的專案，最終產物是可閱讀、可維護、可打包的電子書內容庫。
- 使用者希望內容可以直接在 GitHub 倉庫中閱讀，因此章節語法需符合 GitHub Flavored Markdown。
- 由於同一份內容還要輸出為 EPUB，章節拆分、目錄組織、圖片提示與打包流程需要從一開始就一致設計。
- 圖片暫時不由專案自動生成；當內容需要圖解時，應插入清楚的段落提示，請人類後續提供圖片。
- 第一階段不只是寫作，而是要讓讀者真的做出成果：使用 antigravity 與 GSD 協作，完成並發布 GitHub Pages 上的 FV 計算機。

## Constraints

- **Language**: 台灣繁體中文 — 目標讀者與內容語氣必須符合在地中文閱讀習慣
- **Markdown**: GitHub Flavored Markdown — 需確保 GitHub `README.md` 與章節檔案正確呈現
- **Publishing**: 同時支援 GitHub 閱讀與 EPUB 打包 — 內容結構需要能被雙用途重用
- **Image Workflow**: 圖片以人工補件為前提 — 文件中需預留清楚的圖片需求提示
- **Phase 1 Outcome**: 必須產出 GitHub Pages FV 計算機教學主線 — 第一階段成功標準需聚焦在可發布成果

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| 以 Markdown 作為單一內容來源 | 同時服務 GitHub 閱讀與 EPUB 打包，降低內容分岐 | — Pending |
| 各章節拆分為獨立 Markdown 檔案 | 便於維護、審稿、重排章節與增量撰寫 | — Pending |
| `README.md` 作為專案首頁與導讀入口 | 讓讀者進入倉庫即可開始閱讀，不依賴額外網站 | — Pending |
| 第一階段以 FV 計算機作為示範成果 | 題目具體、易驗證，且能完整涵蓋 Agent 協作到 GitHub Pages 發布流程 | — Pending |
| 圖片需求先以段落提示標記 | 在不阻塞寫作的前提下保留後續補圖空間 | — Pending |

---
*Last updated: 2026-03-27 after initialization*
