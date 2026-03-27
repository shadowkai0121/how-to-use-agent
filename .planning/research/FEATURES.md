# Feature Research

**Domain:** GitHub-first 技術教學電子書 / 文件專案（章節式 Markdown、EPUB 打包）
**Researched:** 2026-03-27
**Confidence:** HIGH

## Feature Landscape

### Table Stakes (讀者會直接期待)

少了這些，產品不會被認為是「精簡」，而是會被認為「還沒做完」。

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| `README.md` 導讀首頁與章節入口 | GitHub 讀者預期一進 repo 就知道這本書在講什麼、從哪開始、最後能做出什麼 | LOW | `README.md` 必須同時扮演首頁、導覽頁、安裝前說明與第一個 CTA；要直接指向第一章與最終成果 `GitHub Pages` FV calculator。 |
| 明確的章節順序與分部結構 | 現代技術書與文件都以可重排的章節樹管理內容，而不是單一超長文件 | LOW | 維持顯式章節順序、穩定檔名、可插入前言/附錄/草稿章節；這是 GitHub 閱讀與 EPUB 目錄的共同基礎。 |
| GitHub Flavored Markdown 安全子集 | 專案要求 GitHub 可直接閱讀，因此標題、相對連結、程式碼區塊、提示框、`<details>` 等要在 GitHub 可靠呈現 | LOW | 不要把閱讀體驗綁在只存在某個生成器的語法上；需要「在 GitHub 可讀，在 EPUB 可轉」。 |
| 穩定的交叉連結與章節導航 | 技術讀者預期能從 README 跳章節、章節跳章節、章節回導讀，且錨點不會亂掉 | MEDIUM | 使用相對連結、穩定標題層級、每章固定的「上一章 / 下一章 / 回首頁 / 本章產出」。 |
| 單一內容來源的雙輸出 | 現代技術內容通常要求同一份來源同時支援網站閱讀與電子書離線閱讀 | MEDIUM | 最低要求是 GitHub 可讀 Markdown 加可重建 EPUB；需包含標題、作者、摘要、封面、TOC、輸出檔名等 metadata。 |
| 可直接操作的程式碼與命令區塊 | 技術電子書若沒有可複製的 code/CLI 區塊，學習阻力會明顯升高 | LOW | 每段命令要標示語言、環境與預期輸出；以「貼上就能跑」為標準，不靠長篇敘述取代可執行示例。 |
| 圖片占位與資產規範 | 教學內容常先寫文後補圖；若沒有占位規則，後續補圖與 EPUB 打包很容易失控 | LOW | 使用明確的「待補圖」段落提示、建議圖意、alt text 與相對路徑規範，避免 broken asset。 |
| 以成果為導向的學習主線 | 這個專案不是純參考文件，而是承諾讀者會做出成果；沒有主線就無法兌現核心價值 | HIGH | 第一階段必須把閱讀路徑收斂到「完成並發布 GitHub Pages FV calculator」，不是只講概念。 |
| 本地預覽與自動打包流程 | 現代文件專案通常要能快速 preview、在修改後自動重建，並在 CI 產出可發布 artifact | MEDIUM | 至少要支援本地預覽/重建與 CI 產出 EPUB；否則內容維護成本會快速上升。 |

### Differentiators (有價值的升級)

這些不是基本配備，但對這本 Agent 教學書會直接放大價值。

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| 每章附可直接複製的 Agent prompt / 指令腳本 | 把「閱讀」變成「跟著做」，降低初學者不知道要怎麼跟 Agent 開口的摩擦 | MEDIUM | 每章都應有「你現在要對 Agent 說什麼」與「預期會產生什麼檔案/結果」。 |
| 常見失敗情境與修復分支 | Agent 教學真正的痛點不是 happy path，而是偏題、壞掉、部署失敗時怎麼拉回來 | MEDIUM | 針對 prompt 漂移、檔案結構跑掉、GitHub Pages 404、計算結果錯誤，提供 rescue path。 |
| 每章 checkpoint 與驗收條件 | 讓讀者知道自己目前是否還在正確軌道上，降低中途放棄率 | MEDIUM | 每章尾端固定列出「完成後應看到的 repo 狀態 / 頁面行為 / 截圖需求」。 |
| Repo 狀態快照與里程碑對照 | 技術讀者很常需要「我現在應該長怎樣」的比對基準 | MEDIUM | 可以用 tag、branch、章節完成快照或附錄對照表，不必一開始就做複雜腳手架。 |
| 台灣繁中術語表與中英對照 | Agent、CLI、deploy、workflow 等術語若只直譯，會讓學習節奏斷裂 | LOW | 保留原始命令與英文名詞，同時提供一致的繁中術語說法，放在附錄或側欄。 |
| Phase-end 發布驗證閉環 | 不是「寫完程式」就算結束，而是要真的看到公開網址與可操作成果 | MEDIUM | 在 phase 1 結尾加入 GitHub Pages 網址、功能檢查清單、發布後自我驗證步驟。 |

### Anti-Features (現在刻意不要做)

這些功能常被直覺認為「更完整」，但現在做只會拖慢主線或破壞核心約束。

| Feature | Why Requested | Why Problematic | Alternative |
|---------|---------------|-----------------|-------------|
| 非 GFM 相容的重度 Markdown 客製元件 | 看起來更像完整 docs portal，更炫 | GitHub 可讀性會先壞掉，EPUB 轉換也更脆弱；專案的單一內容來源目標會被破壞 | 維持 GFM-first；若要增強，只允許可優雅退化的 build-time enhancement |
| v1 就做全文搜尋 / Ask AI / 註解系統 | 現代 docs 平台常有搜尋與 AI 助理，看起來很先進 | GitHub 與 EPUB 都不會同等受益，還會引入外部服務、索引與維運成本 | 先靠短章節、清楚 README 索引、FAQ/疑難排解附錄支撐 |
| v1 就做版本化文件 | 看起來很專業，像成熟框架文件 | 版本化會複製檔案、增加維護分岔；對內容仍在快速成形的教學書是錯誤時機 | 先維護單一最新版，加上 release note / CHANGELOG 即可 |
| 把互動式 embed 或線上沙盒塞進書本正文 | 互動很吸睛，示範也更炫 | GitHub README 與 EPUB 都不是理想載體，容易造成相容性與離線閱讀問題 | 把互動留給最終 GitHub Pages FV calculator 與獨立 demo 頁 |
| 同步追求 PDF / 紙本級排版 | 很多人會希望「順便有 PDF」 | 排版工作量與調校成本高，且目前需求明確聚焦 GitHub 閱讀與 EPUB | 先把 GitHub + EPUB 做穩，PDF 留到第二階段之後再評估 |
| 第一階段就做多語系版本 | 更容易擴大受眾 | 會讓術語治理、同步修訂、截圖維護成本倍增，直接稀釋首版品質 | 先把台灣繁中版做完整，再視驗證結果決定是否擴語系 |

## Feature Dependencies

```text
[README 導讀首頁]
    └──requires──> [明確章節順序與穩定檔名]
                       └──requires──> [相對連結與穩定標題錨點]
                                              └──requires──> [GitHub / EPUB 雙輸出一致性]

[Phase 1 Agent 教學主線]
    └──requires──> [可直接複製的 prompt / code 區塊]
                       └──requires──> [每章 checkpoint / 驗收條件]
                                              └──requires──> [GitHub Pages FV calculator 發布章節]

[EPUB 打包]
    └──requires──> [書籍 metadata / cover / TOC]
                       └──requires──> [圖片占位與資產路徑規範]

[全文搜尋 / Ask AI / 版本化文件]
    └──conflicts──> [GitHub-first MVP 的低複雜度目標]
```

### Dependency Notes

- **`README 導讀首頁` requires `明確章節順序與穩定檔名`:** 沒有顯式章節順序，README 只能列散亂連結，讀者無法知道正確閱讀路徑。
- **`明確章節順序與穩定檔名` requires `相對連結與穩定標題錨點`:** GitHub 導覽、章節互鏈與 EPUB 目錄都依賴可預期的 heading 與 path。
- **`Phase 1 Agent 教學主線` requires `可直接複製的 prompt / code 區塊`:** 這本書承諾的是「跟著做出成果」，不是只解釋概念。
- **`可直接複製的 prompt / code 區塊` requires `每章 checkpoint / 驗收條件`:** 沒有中途驗收點，讀者很難定位自己偏離在哪一步。
- **`每章 checkpoint / 驗收條件` requires `GitHub Pages FV calculator 發布章節`:** phase 1 的學習閉環是公開部署，不是本機跑起來就結束。
- **`EPUB 打包` requires `書籍 metadata / cover / TOC`:** 缺 metadata 的 EPUB 在閱讀器與書庫中的可用性很差。
- **`全文搜尋 / Ask AI / 版本化文件` conflicts with `GitHub-first MVP 的低複雜度目標`:** 這些功能更像成熟 docs portal 的第二階段投資，不該先吃掉內容成書與教學主線的資源。

## MVP Definition

### Launch With (v1)

- [ ] `README.md` 導讀首頁與章節索引，清楚說明「這本書會帶你做到什麼」。
- [ ] 章節化 Markdown 主線，至少覆蓋環境準備、Agent 協作基本節奏、FV calculator 實作、GitHub Pages 發布。
- [ ] GFM-safe 的寫作規範：標題層級、相對連結、code block、callout、`<details>`、圖片占位。
- [ ] 單一來源的 EPUB 打包輸出，包含書籍 metadata、TOC、封面與穩定檔名。
- [ ] 每章 checkpoint 與 phase 1 最終驗收：公開可用的 GitHub Pages FV calculator。

### First-Phase Reader Journey

1. 讀者進入 `README.md`，立即理解最終成果是「完成並發布 FV calculator」而不是只讀完一本書。
2. 讀者依章節完成環境準備，學會如何向 Agent 提需求、怎麼拆步驟、怎麼驗收輸出。
3. 讀者跟著章節逐步建立 FV calculator 的 HTML、CSS、JavaScript 與 repo 結構。
4. 讀者將成果推上 GitHub，完成 GitHub Pages 設定與部署。
5. 讀者打開公開網址，依書中驗收條件檢查功能、版面與計算結果，形成完整的學習閉環。

### Add After Validation (v1.x)

- [ ] 常見錯誤與修復附錄，例如 prompt 漂移、部署失敗、公式錯誤。
- [ ] 台灣繁中術語表與中英對照附錄。
- [ ] 里程碑快照或章節對照 tag，讓讀者可以跳回正確進度。
- [ ] 如果 GitHub 內閱讀體驗證實不足，再補一個輕量 HTML 發布層與搜尋。

### Future Consideration (v2+)

- [ ] 全文搜尋、Ask AI、讀者留言或註解層。
- [ ] 文件版本化與多版本維護策略。
- [ ] 多語系版本。
- [ ] PDF / 紙本級版面優化。

## Feature Prioritization Matrix

| Feature | User Value | Implementation Cost | Priority |
|---------|------------|---------------------|----------|
| `README.md` 導讀首頁與章節入口 | HIGH | LOW | P1 |
| 明確章節順序與穩定檔名 | HIGH | LOW | P1 |
| GitHub-safe Markdown 寫作規範 | HIGH | LOW | P1 |
| 單一來源 GitHub + EPUB 雙輸出 | HIGH | MEDIUM | P1 |
| Phase 1 FV calculator 成果主線 | HIGH | HIGH | P1 |
| 每章 checkpoint / 驗收條件 | HIGH | MEDIUM | P1 |
| 常見失敗情境與修復分支 | HIGH | MEDIUM | P2 |
| 台灣繁中術語表 | MEDIUM | LOW | P2 |
| Repo 狀態快照 / 章節對照 tag | MEDIUM | MEDIUM | P2 |
| 搜尋 / Ask AI / 評論層 | LOW | HIGH | P3 |
| 文件版本化 | LOW | HIGH | P3 |
| PDF / 紙本優化 | LOW | HIGH | P3 |

**Priority key:**
- P1: Launch 必備
- P2: 核心驗證後應補上
- P3: 明確延後

## Competitor Feature Analysis

| Feature | Competitor A | Competitor B | Our Approach |
|---------|--------------|--------------|--------------|
| 章節順序與階層 | `mdBook` 用 `SUMMARY.md` 明確定義章節順序、階層與草稿章節 | `Quarto` 用 `_quarto.yml` 的 `book.chapters` 管理章節與附錄 | 直接把章節順序視為核心內容模型，讓 GitHub 閱讀與 EPUB 目錄共用同一套結構 |
| 閱讀導航 | `mdBook` 內建 sidebar、prev/next、搜尋 | `Quarto` 內建 page navigation、sidebar、download links | v1 先把 README 索引、章節互鏈、EPUB TOC 做穩；搜尋不是第一天要解的問題 |
| 雙輸出與封面 / metadata | `Quarto` 支援 HTML/EPUB 與 cover、downloads、metadata | `HonKit` 支援網站與 eBook（PDF/ePub/Mobi），也有 glossary | 我們只鎖定真正需要的兩種輸出：GitHub 可讀 Markdown 與 EPUB，避免過早擴格式 |
| 搜尋 / 版本化 / portal 功能 | `VitePress`、`Quarto`、`GitBook` 都能做搜尋；`Docusaurus` 支援版本化，但官方也提醒版本化會增加複雜度 | 商業 docs 平台可加 AI 問答、註解、協作層 | 對這個專案來說，這些屬於內容穩定後再加的 portal 功能，不應先於教學主線與成書流程 |

## Sources

- [HIGH] GitHub Docs, Basic writing and formatting syntax: https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax
- [HIGH] GitHub Docs, Collapsed sections: https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/organizing-information-with-collapsed-sections
- [HIGH] GitHub Docs, What is GitHub Pages?: https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages
- [HIGH] GitHub Docs, Configuring a publishing source for your GitHub Pages site: https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site
- [HIGH] GitHub Flavored Markdown Spec: https://github.github.com/gfm/
- [HIGH] mdBook Docs, `SUMMARY.md`: https://rust-lang.github.io/mdBook/format/summary.html
- [HIGH] mdBook Docs, Reading books: https://rust-lang.github.io/mdBook/guide/reading.html
- [HIGH] mdBook Docs, `watch` command: https://rust-lang.github.io/mdBook/cli/watch.html
- [HIGH] Quarto Docs, Creating a Book: https://quarto.org/docs/books/index.html
- [HIGH] Quarto Docs, Book Structure: https://quarto.org/docs/books/book-structure.html
- [HIGH] Quarto Docs, Customizing Book Output: https://quarto.org/docs/books/book-output
- [HIGH] Quarto Docs, Book Options: https://quarto.org/docs/reference/projects/books
- [HIGH] Quarto Docs, ePub Options: https://quarto.org/docs/reference/formats/epub.html
- [HIGH] HonKit Docs: https://honkit.netlify.app/
- [HIGH] VitePress Docs, Search: https://vitepress.dev/reference/default-theme-search
- [HIGH] Docusaurus Docs, Versioning: https://docusaurus.io/docs/versioning

---
*Feature research for: Agent 使用教學電子書 / GitHub-first 技術文件型電子書*
*Researched: 2026-03-27*
