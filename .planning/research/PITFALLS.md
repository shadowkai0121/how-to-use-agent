# Pitfalls Research

**Domain:** GitHub Markdown + EPUB 雙輸出技術教學電子書（台灣繁中、章節式 Markdown、含實作教學）
**Researched:** 2026-03-27
**Confidence:** MEDIUM

GitHub、Pandoc、EPUB 驗證器本身的行為屬於 HIGH confidence，因為主要依據官方文件。哪些問題最該優先防範、以及應該落在哪個 roadmap phase，則是依本專案脈絡做的工程判斷，因此整體標為 MEDIUM。

## Critical Pitfalls

### Pitfall 1: 把 GitHub 版與 EPUB 版當成兩份內容維護

**What goes wrong:**
內容很快分叉。GitHub `README.md`、章節 Markdown、匯出用補丁檔、甚至手改過的 EPUB 各自漂移，最後同一段教學在不同輸出中不一致，修一處壞兩處。

**Why it happens:**
團隊通常先讓 GitHub 版「看起來能讀」，再為 EPUB 額外補 hack；但 GFM、Pandoc、EPUB 閱讀器的能力邊界不同，若沒有先定義單一來源與允許語法子集，雙輸出很容易變成雙維護。

**How to avoid:**
- 明定章節 Markdown 為唯一內容來源，禁止手改匯出產物。
- `README.md` 只做入口、導讀、讀法說明，不承擔完整書稿。
- 先寫一份「可攜語法合約」：允許哪些 GFM 功能、哪些 GitHub 專屬功能禁用、哪些需要明確轉換策略。
- 對無法雙輸出的元素採明確政策：禁用、改圖像化，或在 build 中做專用轉換，不能臨時人工補。

**Warning signs:**
- PR 同時修改 `README.md` 內文與章節正文，內容卻不是純導覽更新。
- 倉庫開始出現手工維護的 `dist/`, `epub/`, `export/`, `book-final.md` 之類產物。
- 同一概念在 GitHub 與 EPUB 版本的術語、步驟或截圖要求不一致。

**Phase to address:**
Phase 1: Content Contract And Repository Layout

---

### Pitfall 2: 以為 GitHub 能渲染的 Markdown，EPUB 就也能成立

**What goes wrong:**
GitHub 上看起來正常的內容，進 EPUB 後變成普通程式碼、失去互動、或呈現錯亂。常見受害者包括 GitHub 專屬 autolink 行為、Mermaid 圖、raw HTML hack、依賴 GitHub 後處理的片段。

**Why it happens:**
GFM 不是所有 Markdown 轉換器的共同真相。GitHub 自己還會做額外 post-processing 與 sanitization；Pandoc 也明確說不同格式間的轉換不應期待完美無損。

**How to avoid:**
- 以 `gfm` 輸入搭配明確 Pandoc 設定建立 export pipeline，不要靠預設值猜。
- 建立「GitHub-only feature blacklist」：Mermaid、conversation autolinks、任意 raw HTML、依賴 GitHub UI 的提示框等，沒有轉換器就不要進書稿主線。
- 在 CI 同時做 GitHub render smoke check 與 EPUB build，讓 portability 問題在合併前暴露。
- 每新增一種新語法，先做雙輸出驗證範例，再允許進章節。

**Warning signs:**
- 作者開始用 ` ```mermaid `、GitHub alert、issue shorthand、HTML `<details>` 當主要教學結構。
- 匯出的 EPUB 裡出現「看得見原始 fenced block，看不見實際圖表」。
- 教學內容需要寫「這段只在 GitHub 上看得到效果」卻沒有替代表現。

**Phase to address:**
Phase 1: Content Contract And Repository Layout

---

### Pitfall 3: 章節拓樸不穩，導致 TOC、錨點與跨章連結持續腐壞

**What goes wrong:**
章節順序、標題層級、內部連結與 TOC 反覆壞掉。GitHub 錨點會因標題文字或重名順序改變而變；Pandoc 多檔案預設是先串起來再 parse，若沒有清楚的 build 規則，跨檔行為會很脆弱。

**Why it happens:**
文件專案常一開始只顧「先拆章」，沒有先定義章節命名、排序 manifest、標題唯一性與跨章連結規範。後面一重排章節，整本書的 anchor 與導覽就一起漂。

**How to avoid:**
- 用單一 manifest 定義章節順序，禁止靠檔名自然排序猜書本結構。
- 制定標題規範：每章只能有一個 H1，H2/H3 文案需在該檔內唯一。
- 對常被引用的小節，使用穩定的顯式 anchor 策略或固定 slug 規則，不要只靠標題自動生成。
- 在 CI 跑 internal link check，並保留 GitHub render snapshot 或至少 DOM smoke test。
- 若需要逐檔解析語意，明確使用 `--file-scope` 或等價策略，不要默認依賴 Pandoc 串接行為。

**Warning signs:**
- 改一個標題就連帶修大量 `#fragment` 連結。
- 同一檔案出現多個相同 H2/H3。
- PR 中頻繁出現「修 broken link」「TOC 不見了」「連到第一個同名標題」。

**Phase to address:**
Phase 1: Content Contract And Repository Layout

---

### Pitfall 4: 章節資產路徑失控，GitHub 可見但 EPUB 缺圖

**What goes wrong:**
章節圖片、範例檔、相對連結在 GitHub 上能開，但匯出 EPUB 時遺失、指到錯誤位置、或只在某個工作目錄下才會成功。尤其多章節分資料夾時最常出事。

**Why it happens:**
GitHub 對相對路徑有自己的 repo-aware 轉換；Pandoc 對多檔輸入、reference-style links、resource lookup 也有自己的規則。作者若一邊用相對路徑、一邊跨檔定義 reference links，很容易得到「局部看起來對，全書 build 才爆」。

**How to avoid:**
- 先決定一種資產模型，不要混用：要嘛集中在 `/assets`, 要嘛每章 `chapter-x/assets/`，但只能選一套。
- Pandoc build 明確配置 `rebase_relative_paths` 與 `--resource-path`，不要靠目前 shell working directory。
- 圖片與插圖需求提示分流：正式資產與 placeholder 標記不要共用同一資料夾命名慣例。
- 把「每個 Markdown 檔引用的所有本地資產都存在」做成自動檢查。

**Warning signs:**
- 同一張圖在 GitHub 顯示正常，EPUB 缺圖。
- build 只能在特定目錄執行，換 CI 或換機器就找不到資源。
- reference link 定義集中在共享檔後，跨章圖片開始解析錯位。

**Phase to address:**
Phase 2: Build Pipeline And Dual-Output Validation

---

### Pitfall 5: 教學主線與真正可執行的 FV 計算機專案脫鉤

**What goes wrong:**
讀者照著書做，做不出與章節描述一致的 GitHub Pages FV 計算機。書稿說的檔案結構、命令、畫面、部署步驟，已經和 repo 裡真正能跑的 sample 不同。

**Why it happens:**
教學內容常先寫 prose，sample app 後補；或 sample app 在別處演化，書稿沒同步。這種 drift 在「教學型電子書 + 實作專案」特別致命，因為讀者的成功標準不是看懂，而是做出可部署成果。

**How to avoid:**
- 把 `/examples/fv-calculator`（或等價目錄）設為唯一可執行真相來源。
- 每章只描述當前 sample 中真實存在的狀態，不能先寫「理想版」。
- 每個 milestone 都要有「從乾淨 clone 走完整條教學路徑」的驗證。
- 把教學步驟切成可驗證節點：建立專案、完成計算、完成樣式、部署到 GitHub Pages，每節都有預期檔案與預期畫面。

**Warning signs:**
- 作者需要在文字裡補「如果你目前看到的程式碼和 repo 不同，以 repo 為準」。
- 教學章節引用的檔名、路徑、命令，在 sample app 內已不存在。
- 新讀者第一次試跑就卡在「這步和畫面不一樣」。

**Phase to address:**
Phase 3: Tutorial Source Of Truth And Runnable Example

---

### Pitfall 6: 內文程式碼區塊不可驗證，最後整本書 code rot

**What goes wrong:**
章節中的 code block 看似完整，但其實不能貼上執行、缺上下文、版本已過時，或只要 sample code 一重構，書中的片段就悄悄失真。這是 tutorial 類內容最常見、也最昂貴的後期維護問題。

**Why it happens:**
直接在 Markdown 內手打一大段範例碼最快，但也是最難測的做法。官方 docs 寫作實務也明說：inline code block 比以程式檔為來源更難測、更難保持最新；用 line number 取 snippet 也很脆弱。

**How to avoid:**
- 長程式碼一律以 sample app 原始檔為真相來源，透過 snippet extraction 或生成流程進章節。
- 若必須在 Markdown 內放短片段，限制用途為指令、設定、極短示例，不放主流程程式。
- snippet 選取用命名區段，不用硬編行號。
- CI 至少要驗證 sample app 可 build、可 deploy；能做到的話，再驗證 Markdown 中提到的命令與關鍵輸出。

**Warning signs:**
- PR 只改 sample code，卻沒有人檢查書稿片段是否同步。
- 章節裡出現大量 30 行以上的 fenced code block，而且來源不是實際專案檔。
- 維護者常靠肉眼比對 code block 與 sample app。

**Phase to address:**
Phase 3: Tutorial Source Of Truth And Runnable Example

---

### Pitfall 7: EPUB 只是「打得出來」，但不是可交付的電子書

**What goes wrong:**
匯出命令成功，檔案也能在某些閱讀器打開，但 metadata 缺漏、封面不對、語言標記錯、TOC 壞掉、不同閱讀器顯示不一致，甚至根本不符合 EPUB 規範。

**Why it happens:**
許多團隊把「Pandoc 有輸出檔」誤當成完成。Pandoc 自己就提醒轉換不保證完美；EPUB 又有額外的 metadata、封面、CSS 與 conformance 要求。沒有驗證器，問題通常要到臨發布才看見。

**How to avoid:**
- 把 EPUB metadata 獨立成明確檔案管理，至少包含 `title`, `creator`, `identifier`, `lang`, `cover-image`, `css`。
- 每次 release build 都跑 EPUBCheck，並把 warning 視為要處理的訊號，不只看 error。
- 用至少兩種閱讀器做 smoke test，不要只看單一預覽器。
- 版本控制 Pandoc defaults 與 CSS，不要用一次性的命令列魔法。

**Warning signs:**
- 書檔只有檔名，沒有正式書名、作者、語言或封面。
- EPUB 能在一個閱讀器打開，但目錄、圖片或字型在另一個閱讀器出問題。
- 團隊從未看過 EPUBCheck 報告。

**Phase to address:**
Phase 2: Build Pipeline And Dual-Output Validation

---

### Pitfall 8: `README.md` 變成第二本書，入口與導覽一起失效

**What goes wrong:**
`README.md` 越寫越長，既想當首頁又想當正文，最後 GitHub 入口失焦、內容重複、章節連結混亂，甚至踩到 GitHub README 截斷上限。讀者進 repo 反而不知道從哪裡開始。

**Why it happens:**
README 是最容易被編輯與最先被看到的檔案，所以團隊自然會把更多內容塞進去。但 GitHub 其實把 README 定位為專案入口，不是長篇書稿本體。

**How to avoid:**
- README 只保留 5 件事：本書目的、適合誰、怎麼開始、章節入口、如何取得 EPUB。
- 正文一律放章節檔，README 只做摘要與導覽。
- README 與章節導覽共用單一章節 manifest 生成，避免重複維護。
- 為 GitHub 閱讀者設計最短路徑：一進 repo 就知道先讀哪章、做哪個成果。

**Warning signs:**
- README 內開始出現大量教學正文、長 code block、與章節同內容段落。
- 每次章節調整都要手改 README 內多處章節清單與摘要。
- README 越來越像「整本書的縮寫版」而不是入口。

**Phase to address:**
Phase 1: Content Contract And Repository Layout

---

### Pitfall 9: 繁中/CJK 排版與圖片 placeholder 流程沒有被產品化

**What goes wrong:**
台灣繁中文章在 EPUB 或轉換後出現不自然斷行、標點孤行、內文與 code 混排難讀；同時「請人類補圖」這類 placeholder 若沒有固定語法與發版檢查，最終會直接流入正式版本。

**Why it happens:**
大多數 Markdown/ebook pipeline 先優化英文。Pandoc 也明確區分一般換行處理與 East Asian line break 行為。若繁中排版、placeholder 標記、alt text 規則沒有在 early phase 定義，最後只能靠人工巡檢補洞。

**How to avoid:**
- 明確設定 `lang: zh-Hant`，並針對 CJK 文本試驗 `east_asian_line_breaks` 與實際閱讀器效果。
- 規定 prose 不做任意硬換行，避免 build 後插入非預期空白。
- 定義固定 placeholder 語法，例如 `[[IMAGE_REQUEST: 說明...]]`，並在 release 前自動掃描殘留。
- 圖片一旦補上，要求 alt text 與檔名規範一併完成，不接受「先放圖，晚點補說明」。

**Warning signs:**
- EPUB 裡中文段落出現奇怪空白或斷行。
- 倉庫裡同時存在多種 placeholder 寫法，像是 `TODO 圖`, `待補圖`, `插圖?`, HTML 註解混用。
- 發版候選仍搜尋得到 placeholder 標記或缺 alt text 的圖片。

**Phase to address:**
Phase 4: Editorial QA And Release Readiness

## Technical Debt Patterns

Shortcuts that seem reasonable but create long-term problems.

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| 直接手改匯出的 EPUB 或中間產物 | 很快修掉單一版面問題 | 下一次 build 立刻覆蓋，雙來源分叉 | Never |
| 在 Markdown 直接維護長篇程式碼範例 | 寫作速度快 | code rot、無法測試、sample app 與書稿分離 | 只限極短指令或設定片段 |
| 用行號範圍抽 snippet | 初期實作簡單 | 原始碼一改，章節抽到錯段卻不易察覺 | 只有暫時性 spike，可接受很短時間 |
| README 同時放導覽與完整章節內容 | 進 repo 馬上看得到很多內容 | GitHub 入口失焦、重複維護、導航混亂 | Never |
| 允許作者自由發明 placeholder 語法 | 不阻塞寫作 | 後續難掃描、難追蹤、易漏進正式版 | Never |
| build 指令只存在 maintainer 腦中或 shell history | 建起來很快 | 新維護者無法重現、CI 難落地 | Never |

## Integration Gotchas

Common mistakes when connecting to external services.

| Integration | Common Mistake | Correct Approach |
|-------------|----------------|------------------|
| GitHub Markdown renderer | 以為 issue/PR shorthand、部分 GitHub UI 特性在 repo 檔案內也會自動成立 | 用 GitHub 官方 `/markdown` render API 做 `gfm` 模式檢查；repo 檔案內容只依賴 repo-file 支援的 GFM |
| GitHub README | 把 README 當完整書稿，忽略其入口角色與大小限制 | README 僅保留導覽與起步資訊，正文進章節檔 |
| Pandoc multi-file build | 以為多個章節檔會天然保持各自語意邊界 | 用 manifest 定義章節順序，必要時使用 `--file-scope`，並把組書規則版本化 |
| Pandoc resource loading | 依賴當前工作目錄找圖或找 CSS | 固定 `--resource-path`，統一資產目錄規則，必要時開 `rebase_relative_paths` |
| EPUB output | 生成成功就當作可發布 | release pipeline 必跑 EPUBCheck，再加至少兩種閱讀器 smoke test |
| GitHub Pages tutorial sample | 把書稿檔案、產物檔案、示範網站同層混放 | sample app 獨立目錄，部署路徑與書稿來源分離，讓讀者可在乾淨 clone 中照做 |

## Performance Traps

Patterns that work at small scale but fail as usage grows.

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| 每個 PR 都做全書完整渲染、全閱讀器人工檢查 | CI 變慢，作者開始跳過驗證 | PR 做 smoke build；nightly 或 release 做 full validation | 約 10 章以上、圖片開始增多時就會明顯痛 |
| README 持續膨脹 | GitHub 首頁讀不動、找不到入口、維護成本高 | README 固定成導覽頁，正文外移 | 在內容量成長後很早就會壞；GitHub 500 KiB 後還會被截斷 |
| 圖片全部直接丟原始大圖 | repo clone 變慢、EPUB 體積膨脹 | 建立圖片規格與壓縮流程，保留原稿在外部設計來源 | 有十幾張以上截圖後就會開始拖慢 review 與 export |
| 用人工比對檢查 snippet 是否同步 | 維護者疲勞、漏錯頻率上升 | 自動化 snippet extraction 與 sample build 驗證 | 一旦 sample app 進入持續迭代，立刻不可持續 |

## Security Mistakes

Domain-specific security issues beyond general web security.

| Mistake | Risk | Prevention |
|---------|------|------------|
| 教學中要求讀者使用不必要的長效 PAT 或把 token 直接貼進指令 | 讀者洩漏憑證、把秘密提交進 repo | 優先教 `GITHUB_TOKEN`、最小權限、`.env.example` 與假資料 |
| Pandoc pipeline 對外部內容或 PR 內容使用未受限 include/filter | 在 CI 或本機 build 時暴露不應讀取的檔案 | 對不可信輸入使用 `--sandbox`，避免內容驅動任意 IO，審查自訂 filters |
| 熱連結外部圖片或遠端資產 | 離線閱讀失敗、隱私暴露、對方資源失效 | 正式書稿資產一律本地化並納入版本控制；外部連結只作參考，不作渲染依賴 |

## UX Pitfalls

Common user experience mistakes in this domain.

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| 章節標題是主題名，不是讀者成果 | 讀者不知道該先讀哪章，也無法判斷完成標準 | 用結果導向命名，例如「部署你的第一個 FV 計算機到 GitHub Pages」 |
| code block 沒標示用途 | 讀者不知道哪些要複製、哪些只是說明 | 標示「建立檔案」「貼上此段」「預期輸出」「不要照抄」 |
| GitHub 閱讀路徑與 EPUB TOC 不一致 | 同一本書在兩種載體像兩套產品 | 使用同一章節 manifest 生成 README 導覽與 EPUB TOC |
| placeholder 與正式內容長得太像 | 讀者會把待補圖訊息誤當正文 | 用機器可辨識、視覺上也明顯不同的 placeholder 語法 |

## "Looks Done But Isn't" Checklist

- [ ] **README 入口：** 常缺明確起讀路徑與章節索引，驗證新讀者進 repo 後 30 秒內能找到第一章。
- [ ] **雙輸出：** 常只驗 GitHub，不驗 EPUB，確認每次 release 都有 GitHub render smoke check 與 EPUB build。
- [ ] **EPUB 品質：** 常少 metadata、封面或驗證步驟，確認 EPUBCheck 已跑且結果被保存。
- [ ] **教學可信度：** 常少乾淨環境重跑，確認有人從 fresh clone 走完整條 FV 計算機教學。
- [ ] **Snippet 同步：** 常只改 sample code，沒檢查書稿片段，確認 snippet 來源是可追溯的。
- [ ] **圖片流程：** 常留下 placeholder 或缺 alt text，確認 release 前掃描沒有殘留標記。
- [ ] **繁中品質：** 常忽略 CJK 斷行與語系 metadata，確認 `lang`、段落換行規則與閱讀器顯示已檢查。
- [ ] **內部連結：** 常在重命名章節後腐壞，確認全書 internal links 與 anchor 皆通過檢查。

## Recovery Strategies

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| GitHub/EPUB 雙來源分叉 | HIGH | 凍結新內容；指定單一 canonical source；移除手改產物；回填生成流程；逐章比對差異後再重新發布 |
| 教學主線與 sample app 脫鉤 | HIGH | 先以 sample app 為真相重建教學節點；刪除不再存在的步驟；重新做 fresh-clone walkthrough |
| code rot | MEDIUM | 把長 snippet 回收進 sample app；建立命名 snippet；補上 build/test；重新產生章節片段 |
| EPUB 缺圖或 metadata 錯誤 | MEDIUM | 修正資產路徑與 metadata 檔；重跑 export；以 EPUBCheck 與多閱讀器重驗 |
| anchor/TOC 腐壞 | MEDIUM | 建立章節 manifest 與標題規範；統一 slug；跑 link checker；修正所有 cross-reference |
| placeholder 漏進發布版 | LOW | 全域掃描 placeholder token；補圖或降級為文字敘述；補 alt text；重新 build release artifact |

## Pitfall-to-Phase Mapping

How roadmap phases should address these pitfalls.

| Pitfall | Prevention Phase | Verification |
|---------|------------------|--------------|
| 把 GitHub 版與 EPUB 版當成兩份內容維護 | Phase 1: Content Contract And Repository Layout | repo 內只有單一內容來源；沒有手改匯出物；README 只做導覽 |
| 以為 GitHub 能渲染的 Markdown，EPUB 就也能成立 | Phase 1: Content Contract And Repository Layout | 存在語法合約與禁用清單；新增語法有雙輸出測試案例 |
| 章節拓樸不穩，導致 TOC、錨點與跨章連結持續腐壞 | Phase 1: Content Contract And Repository Layout | 章節 manifest、標題規範、link checker 已落地 |
| 章節資產路徑失控，GitHub 可見但 EPUB 缺圖 | Phase 2: Build Pipeline And Dual-Output Validation | CI 可在乾淨環境成功抓到所有資產；EPUB 無缺圖 |
| 教學主線與真正可執行的 FV 計算機專案脫鉤 | Phase 3: Tutorial Source Of Truth And Runnable Example | 從 fresh clone 能照章節做出並部署 sample |
| 內文程式碼區塊不可驗證，最後整本書 code rot | Phase 3: Tutorial Source Of Truth And Runnable Example | 長 snippet 可追溯到 sample 原始檔；sample build/test 通過 |
| EPUB 只是「打得出來」，但不是可交付的電子書 | Phase 2: Build Pipeline And Dual-Output Validation | build 產出含完整 metadata 的 EPUB，且 EPUBCheck 通過 |
| `README.md` 變成第二本書，入口與導覽一起失效 | Phase 1: Content Contract And Repository Layout | README 長度與內容範圍受控；新讀者可快速找到起讀點 |
| 繁中/CJK 排版與圖片 placeholder 流程沒有被產品化 | Phase 4: Editorial QA And Release Readiness | release 前無 placeholder 殘留；繁中閱讀器 smoke test 通過 |

## Sources

- GitHub Flavored Markdown Spec: GitHub 會在 GFM 轉 HTML 後做額外 post-processing 與 sanitization；不同 Markdown 實作之間本來就可能產生意外差異。 HIGH  
  https://github.github.com/gfm/
- GitHub Docs, About the repository README file: README 是 repo 入口；支援相對連結；GitHub 會依標題產生 outline；README 超過 500 KiB 會被截斷。 HIGH  
  https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes
- GitHub Docs, Basic writing and formatting syntax: section anchor 會因標題修改或重名順序變動而改變；圖片應有 alt text 且 repo 內資產建議用相對路徑。 HIGH  
  https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax
- GitHub Docs, Autolinked references and URLs: issue/PR shorthand 是 conversation 特性，不應假設 repo 內章節檔可依賴。 HIGH  
  https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/autolinked-references-and-urls
- GitHub Docs, Creating diagrams: Mermaid 等圖表是 GitHub Markdown 檔案上的特定渲染能力。 HIGH  
  https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/creating-diagrams
- GitHub REST API, Markdown endpoints: 可用官方 render API 做 `gfm` 模式 smoke check。 HIGH  
  https://docs.github.com/en/rest/markdown/markdown
- Pandoc User’s Guide, Using pandoc / General writer options / Non-default extensions / EPUB metadata: 多檔預設會先 concatenate；`--resource-path`、`rebase_relative_paths`、`east_asian_line_breaks`、EPUB metadata 都必須明確管理。 HIGH  
  https://pandoc.org/MANUAL.html  
  https://pandoc.org/demo/example33/2.1-using-pandoc.html  
  https://pandoc.org/demo/example33/3.3-general-writer-options.html  
  https://pandoc.org/demo/example33/8.21-non-default-extensions.html  
  https://pandoc.org/demo/example33/11.1-epub-metadata.html
- EPUBCheck official repository: EPUBCheck 是 EPUB 官方 conformance checker。 HIGH  
  https://github.com/w3c/epubcheck
- Microsoft Learn, How to include code in docs: inline snippets 較難測、較易過時；應盡量由程式檔引用，且避免脆弱的行號片段。 MEDIUM  
  https://learn.microsoft.com/en-us/contribute/content/code-in-docs

---
*Pitfalls research for: Agent 教學電子書專案*
*Researched: 2026-03-27*
