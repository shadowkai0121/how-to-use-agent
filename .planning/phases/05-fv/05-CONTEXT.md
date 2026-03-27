# Phase 5: FV 計算機成果與公開部署 - Context

**Gathered:** 2026-03-27
**Status:** Ready for planning

<domain>
## Phase Boundary

本 phase 負責把前一個 phase 已經建立的 Agent 協作教學節奏，落到真正可交付的最終成果：讀者要能依章節完成 FV 計算機的 HTML、CSS 與 JavaScript，使用固定範例值手動驗證結果正確，並將作品公開部署到 GitHub Pages，讓任意使用者打開網址後都能直接操作。這一輪聚焦在成果頁長什麼樣、章節切分與 checkpoint、輸入輸出與驗算規則、以及公開部署的完成標準，不擴張到新的產品功能、完整 docs portal，或把書本主體搬到 GitHub Pages。

</domain>

<decisions>
## Implementation Decisions

### 作品頁定位與可個人化呈現
- 成品定位為小型作品頁，而不是只有裸露的計算工具。
- 頁面結構採上方短簡介區、下方計算機本體，並在進入計算機前放一段極短使用說明。
- 預設視覺底稿走清爽專業路線，先提供穩定、可交付的基準成品。
- 章節中的 prompt 要引導讀者表達自己想呈現的簡介與風格期待，讓讀者可以在基準版上做個人化調整。
- 不要求所有讀者的成品外觀完全一致，重點是維持作品頁完成感與可用性。

### 教學切分與 checkpoint 節奏
- phase 5 的主節奏固定採骨架、公式、樣式、部署四段推進。
- 每完成一個可觀察成果就要有正式 checkpoint，不只在章尾做總驗收。
- 骨架階段的 checkpoint 應先確認頁面已出現作品頁雛形，再進入公式實作。
- 公式階段的正式過關標準是輸入書中範例值後，畫面顯示正確結果；不要求程式碼必須長得和書上一樣。
- 樣式階段的正式過關標準是頁面已具備完整作品頁的清楚區塊與可讀性，而不是只比未加樣式前稍微好看。

### 驗算案例與輸入輸出規則
- 手動驗算採固定一組書中範例值作為主驗收案例，讓章節、checkpoint 與公開頁面都能對齊同一個正確答案。
- 結果區除了顯示最終 FV 值，還要附上一兩個關鍵中間資訊，幫助讀者對照輸入與輸出關係。
- 結果輸出格式採純數字格式，不特別包裝成金額樣式。
- 遇到空白、非數字或不合理輸入時，要阻止計算並顯示明確提示，而不是默默失敗或先忽略錯誤處理。

### 公開部署與最終驗收
- GitHub Pages 只公開 FV 計算機作品頁，不把閱讀主體或更多書本內容搬到 Pages。
- 公開網址打開後要直接進到可操作的 FV 計算機作品頁，不額外增加 landing page 或二次導覽。
- 部署完成後的正式驗收至少包含兩件事：公開網址可正常開啟，以及使用書中固定範例值能算出正確結果。
- 部署教學以 GitHub 網站操作流程為主，步驟要寫得對初學者友善、可直接照做。

### Claude's Discretion
- 頁面簡介區與極短使用說明的具體文案模板。
- 讀者個人化 prompt 的措辭、長度與放置位置，只要能有效引導讀者說出自己的簡介與風格偏好即可。
- 各 checkpoint 的具體條列格式與視覺呈現方式，只要維持成果優先、repo 證據次之的原則即可。
- 關鍵中間資訊實際顯示哪些欄位，只要能幫助讀者對照輸入與計算結果即可。

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Phase boundary and formal requirements
- `.planning/ROADMAP.md` — Phase 5 的目標、成功標準與需求邊界。
- `.planning/REQUIREMENTS.md` — `TUTO-03`、`TUTO-04`，以及會延續到 phase 5 checkpoint 設計的 `TUTO-05` 正式需求來源。
- `.planning/PROJECT.md` — 專案核心價值、GitHub-first 電子書定位，以及第一階段必須交付可公開部署成果的原則。
- `.planning/STATE.md` — 目前專案狀態與已鎖定的跨 phase 決策摘要。

### Prior phase decisions that carry forward
- `.planning/phases/01-reader-entry-and-chapter-topology/01-CONTEXT.md` — `README.md` 作為入口、重大里程碑分成「完成 FV 計算機」與「完成 GitHub Pages 發布」兩段，以及章節 checkpoint / milestone metadata 的上游決策。
- `.planning/phases/02-authoring-contract-and-localized-content-source/02-CONTEXT.md` — canonical 內容樹、manifest 集中管理章節控制資訊、zh-TW 正式內容策略與 placeholder 契約。
- `.planning/phases/03-dual-output-build-and-publish-validation/03-CONTEXT.md` — `README.md` / EPUB 同源輸出、README 仍是入口而非正文、以及雙輸出驗證與內容分叉限制。
- `.planning/phases/04-agent/04-CONTEXT.md` — 任務驅動教學主線、Prompt/Command 呈現方式、實作即 checkpoint、以及卡關回正策略。

### Research grounding for runnable tutorial and deployment
- `.planning/research/SUMMARY.md` — companion example app、fresh-clone walkthrough、Pages 發布閉環與避免 tutorial drift 的整體研究結論。
- `.planning/research/FEATURES.md` — outcome-driven tutorial path、每章 checkpoint、GitHub Pages 最終驗收與常見失敗情境修復需求。
- `.planning/research/PITFALLS.md` — FV 計算機 sample 與書稿脫鉤、Pages 404、公式錯誤與「看起來完成但實際不可用」的主要風險。
- `.planning/research/ARCHITECTURE.md` — companion app 與書稿邊界、Pages artifact 角色，以及 examples 與 deploy output 的建議分工。
- `.planning/research/STACK.md` — GitHub Pages 相關建議做法與 repository/tooling 邊界，避免把 Pages 反客為主變成主要內容來源。

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- 目前 repo 仍只有 `.planning/` 規劃文件與 `LICENSE`，尚無可直接重用的 FV 計算機 sample app、章節稿、部署腳本或 Pages 設定。
- 已存在的 roadmap、requirements、state、research 與前四個 phase context，可直接作為 phase 5 的規格、風格邊界與教學約束來源。

### Established Patterns
- 專案目前採 planning-first 流程，先用 context 鎖定決策，再交給後續 research / planning / implementation。
- `README.md` 已被鎖定為 repo 的閱讀入口，GitHub Pages 不應反客為主成為書本主體。
- 章節順序、摘要、milestone、checkpoint 與輸出控制資訊已被鎖定由單一 manifest 集中管理。
- phase 4 已鎖定實作教學要以任務驅動方式推進，並在每個實作節點提供成果優先的 checkpoint 與回正方式。

### Integration Points
- phase 5 會首次落地真正可執行的 FV 計算機成果，後續 sample app、章節內容與 checkpoint 必須彼此對齊，避免 tutorial drift。
- 這個 phase 的實作與部署章節，需要直接承接 phase 4 的 prompt / command / checkpoint 模式。
- 公開部署成果必須和前面 phases 已鎖定的 README 導覽、milestone 提示與雙輸出策略相容，不得改變 repo 的閱讀入口定位。

</code_context>

<specifics>
## Specific Ideas

- 教學不只要讓讀者做出計算工具，還要讓成品看起來像一個可以公開展示的小型作品頁。
- 章節 prompt 應該鼓勵讀者說出自己想放在頁首的簡介與偏好的風格方向，但預設底稿仍以清爽專業為基準。
- phase 5 的完成感來自兩個層次一起成立：本機或公開頁面能用固定範例值算對，以及公開網址打開後就能直接操作。
- 結果區保留少量中間資訊，目的不是把數學推導整段展開，而是幫助讀者快速比對自己輸入的條件與得到的結果。

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 05-fv*
*Context gathered: 2026-03-27*
