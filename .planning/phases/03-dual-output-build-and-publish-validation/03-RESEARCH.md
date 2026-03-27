# Phase 3: 雙輸出建置與發佈驗證 - Research

**Researched:** 2026-03-27
**Domain:** 單一 canonical Markdown 來源的 README + EPUB 雙輸出與發布前驗證
**Confidence:** MEDIUM

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
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

### Deferred Ideas (OUT OF SCOPE)
- 更高標準的多閱讀器 zh-TW / CJK 排版 QA 與更完整的閱讀器裝置矩陣 — 留到後續編輯品質 / 發布就緒 phase 深化。
- 更成熟的封面設計與完整版次資訊 — 後續可升級，但不是 phase 3 的最低交付門檻。
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| PUBL-01 | 維護者可以從同一組 Markdown 章節產生可閱讀的根目錄 `README.md` | manifest-driven README generator, committed generated artifact, stale README gate |
| PUBL-02 | 維護者可以從同一組 Markdown 章節產生包含書名、語言、目錄與 metadata 的 EPUB | Pandoc `gfm -> epub3`, metadata file/defaults, cover + TOC + lang handling |
| PUBL-03 | 專案可以在建置或發佈流程中執行 EPUB 驗證，避免輸出損壞或不合規 | EPUBCheck hard gate, release smoke-reader check, CI artifact validation |
| PUBL-04 | 專案可以在檢查流程中偵測章節、README 與資產之間的失效連結或路徑問題 | lychee + local asset existence checks across canonical, README, and build outputs |
| QUAL-02 | 發布前流程可以檢查未清除的圖片占位標記，避免把待補圖提示直接發佈給讀者 | explicit placeholder token/path scanner in local, PR, and release gates |
| QUAL-03 | 發布前流程可以確認 `README.md`、章節與 EPUB 的核心內容沒有人工分叉 | canonical snapshot/fingerprint comparison for title/order/summary/milestone/EPUB info |
</phase_requirements>

## Summary

Phase 3 應該被規劃成「單一來源、兩個生成器、三層 gate」，而不是單純多加一個 EPUB 指令。前兩個 phase 已經鎖定 manifest 與 canonical 章節樹是唯一真相來源，因此本 phase 的重點是把這個來源接到兩個可重建輸出：一個是提交到 repo 根目的 `README.md`，另一個是可交付的 EPUB；再用同一套 canonical metadata 去檢查 drift、placeholder、資產與連結失效。

技術路徑已經很收斂。README 不需要引入 site generator，直接用 Node 腳本依 manifest + 模板生成即可。EPUB 用 Pandoc 3.9.0.2 的 `gfm` 輸入與 `epub3` 輸出最標準，metadata、封面、CSS、resource path 都放在 versioned defaults/metadata 檔案，不塞進章節正文。EPUB 合規應由 EPUBCheck 5.3.0 當硬 gate，而不是人工打開檔案看起來沒壞就算過。

最大的規劃風險不是 build command，而是驗證模型設計錯。若直接比對 README、章節與 EPUB 全文，會把入口頁設計與 ebook 正文設計混為一談；若只做 build 成功檢查，又會漏掉 README 過期、placeholder 殘留與核心入口資訊分叉。正確做法是只比較「核心同步欄位」的 canonical snapshot，並把外部連結檢查做成 tiered execution：本機 smoke、PR 完整、release 最嚴格，但三層都維持 fail-fast 與可讀報表。

**Primary recommendation:** 用 `Node 24 LTS + pnpm + Pandoc defaults + EPUBCheck + lychee` 建 manifest-driven dual-output pipeline，並以 canonical snapshot 驗證 README/EPUB 核心資訊同步，而不是做全文字串比對。

## Standard Stack

### Core
| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| Node.js | 24 LTS | build/verify scripts runtime | 本 phase 主要是 repo automation，不需要額外 app framework；Node LTS 是最穩定的腳本層基礎。 |
| pnpm | 10.33.0 | package manager and script runner | 版本已於 npm registry 驗證；適合保持 CI 與本機指令一致。 |
| Pandoc | 3.9.0.2 | canonical `gfm` to `epub3` generator | 官方仍是最穩定的 Markdown-to-EPUB 路徑，且支援 defaults、metadata、resource-path。 |
| EPUBCheck | 5.3.0 | EPUB conformance validation | W3C 官方 checker；release 前必須硬阻擋。 |
| Java | 21 LTS | runtime for EPUBCheck | EPUBCheck 官方要求 Java 7+；規劃上固定 modern LTS 較合理。 |

### Supporting
| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| markdownlint-cli2 | 0.22.0 | Markdown structural lint | 在 build 前先攔 heading/list/spacing 問題，避免 README 與章節帶病生成。 |
| Prettier | 3.8.1 | formatting for Markdown/YAML/JSON/workflows | 維持模板、manifest、generated README 周邊檔案 diff 穩定。 |
| Vale | 3.14.1 | prose/terminology lint | Phase 2 的 zh-TW contract 延續到 Phase 3，避免輸出時夾帶不一致文案。 |
| lychee | 0.23.0 | internal/external link checking | README、章節與生成產物的 URL 檢查主力。 |
| yaml | 2.8.3 | parse manifest/metadata in Node | README 生成與 drift snapshot 讀 manifest 時使用。 |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Node script + template 生成 README | 直接用 Pandoc 再輸出 Markdown | 這會把 repo-entry 的版型控制綁進 Pandoc 轉換邏輯；對 README 這種固定入口頁反而比較笨重。 |
| Pandoc | Quarto | Quarto 適合 book/site-first；本專案是 GitHub-first README + EPUB，不該把 canonical source 推向 `.qmd`。 |
| node:test | Vitest | Vitest 功能更全，但 Phase 3 主要測 scripts 與檔案輸出，Node 內建 test runner 已足夠，Wave 0 成本更低。 |
| 全文 diff README/EPUB | canonical snapshot diff | 全文比對會誤判入口文案與 ebook 正文的合法差異；snapshot diff 才符合 locked decisions。 |

**Installation:**
```bash
pnpm add -D prettier@3.8.1 markdownlint-cli2@0.22.0 yaml@2.8.3
# install separately:
# - Node.js 24 LTS
# - Pandoc 3.9.0.2
# - Java 21 LTS
# - EPUBCheck 5.3.0
# - Vale 3.14.1
# - lychee 0.23.0
```

**Version verification:** npm-registry verified on 2026-03-27:
- `pnpm@10.33.0` — published 2026-03-24
- `markdownlint-cli2@0.22.0` — published 2026-03-22
- `yaml@2.8.3` — published 2026-03-21
- `prettier@3.8.1` — published 2026-01-21

GitHub/official release verified on 2026-03-27:
- Pandoc `3.9.0.2` — published 2026-03-19
- EPUBCheck `v5.3.0` — published 2025-09-01
- Vale `v3.14.1` — published 2026-03-20
- lychee `v0.23.0` — published 2026-02-13

## Architecture Patterns

### Recommended Project Structure
```text
.
├── README.md                         # generated and committed artifact
├── content/
│   ├── manifest.yaml                 # canonical chapter order + summaries + milestones
│   ├── frontmatter/
│   ├── chapters/
│   └── backmatter/
├── assets/
│   ├── images/
│   ├── placeholder/                  # machine-detectable placeholder path space
│   └── styles/
│       └── epub.css
├── pandoc/
│   ├── defaults/
│   │   └── epub.yaml
│   └── metadata/
│       └── epub.yaml
├── scripts/
│   ├── build-readme.mjs
│   ├── build-epub.mjs
│   ├── verify-links.mjs
│   ├── verify-placeholders.mjs
│   ├── verify-drift.mjs
│   └── verify-phase3.mjs
├── tests/
│   └── phase3/
└── dist/
    └── epub/
```

### Pattern 1: Manifest-Driven Output Selection
**What:** README 與 EPUB 都只從 manifest 選取章節順序與入口 metadata，不再從檔名排序或人工章節清單推導。  
**When to use:** 一律使用；這是避免 README、章節、EPUB 順序分叉的唯一可維護做法。  
**Example:**
```yaml
# Source pattern: project manifest contract
book:
  title: Agent 使用電子書專案
  lang: zh-Hant-TW
  epub:
    filename: agent-book.epub
chapters:
  - file: chapters/01-開始使用.md
    title: 開始使用 Agent
    summary: 建立閱讀與操作前的共同上下文。
    milestone: null
    outputs: [readme, epub]
```

### Pattern 2: Generated README As A Committed Artifact
**What:** `README.md` 由 canonical manifest + 固定模板生成，並提交到 repo；CI 檢查是否 stale。  
**When to use:** GitHub-first 專案，且 root README 是正式入口。  
**Example:**
```ts
// Source pattern: GitHub README is the surfaced entrypoint, not authoring truth.
import { readFile, writeFile } from 'node:fs/promises';
import YAML from 'yaml';

const manifest = YAML.parse(await readFile('content/manifest.yaml', 'utf8'));
const readme = renderReadmeTemplate({
  title: manifest.book.title,
  chapters: manifest.chapters
    .filter((chapter) => chapter.outputs.includes('readme'))
    .map(({ file, title, summary, milestone }) => ({ file, title, summary, milestone })),
});

await writeFile('README.md', readme);
```

### Pattern 3: Pandoc Defaults File For EPUB
**What:** EPUB build options 版本化在 defaults 檔，不散落在 CI shell line。  
**When to use:** 一律使用；metadata、cover、TOC、resource-path 都是 phase 核心需求。  
**Example:**
```yaml
# Source: https://pandoc.org/MANUAL.html
from: gfm
to: epub3
standalone: true
table-of-contents: true
metadata-files:
  - pandoc/metadata/epub.yaml
css:
  - assets/styles/epub.css
epub-cover-image: assets/covers/text-cover.png
resource-path:
  - .
  - assets
output-file: dist/epub/agent-book.epub
input-files:
  - content/frontmatter/preface.md
  - content/chapters/01-開始使用.md
```

### Pattern 4: Canonical Snapshot Drift Detection
**What:** 從 manifest + 固定入口摘要區塊產出一份 machine-readable snapshot，再比對 README 與 EPUB 抽出的核心資訊。  
**When to use:** 驗證 `README.md`、章節與 EPUB 沒有人工分叉時。  
**Example:**
```ts
// Compare only locked sync fields, not full prose.
const canonical = {
  order: chapters.map((c) => c.file),
  titles: chapters.map((c) => c.title),
  summaries: chapters.map((c) => c.summary),
  milestones: chapters.map((c) => c.milestone ?? null),
  epubAccess: manifest.book.epub.filename,
  entrySections: extractReadmeEntryBlocks(templateSource),
};
```

### Pattern 5: Three-Tier Validation With Same Rules, Different Scope
**What:** local / PR / release 用同一規則集，但外部連結覆蓋與 reader smoke test 深度不同。  
**When to use:** 符合 locked decision 的三層驗證。  
**Example:**
```text
local   -> formatting + manifest/schema + README stale + placeholders + local assets + internal links + epub build smoke
PR      -> local all + full lychee + epubcheck + drift report
release -> PR all + clean rebuild + reader smoke test + release artifact packaging
```

### Anti-Patterns to Avoid
- **Manual README edits:** `README.md` 是正式產物，但不是人工維護來源；任何手改都會造成 stale artifact。
- **全文同步檢查:** 這違反 locked decision，會迫使 README 變第二份正文。
- **cwd-sensitive Pandoc invocations:** 若依賴執行目錄找圖，CI 很容易過不了。
- **One giant CI shell command:** Phase 3 需要可讀摘要與可定位失敗點，應拆成 versioned scripts。
- **Warning-only release gate:** 已被明確否決；與品質或一致性有關的 warning 也要阻擋。

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| EPUB packaging | 自己拼 zip/opf/nav/documents | Pandoc `epub3` | EPUB 結構細節多，自己組書只會製造隱性損壞。 |
| EPUB validation | 自製 zip/open smoke check | EPUBCheck | 能打開不代表合規；官方 checker 才是 release gate。 |
| Link checking | 手寫 regex 掃所有 URL | lychee + local asset existence script | 外部 URL、相對路徑、anchor 與 report formatting 都有現成工具。 |
| Markdown rendering truth | 自己用 markdown parser 模擬 GitHub | GitHub documented GFM contract + local structural checks | GitHub 會做額外 post-processing，自己模擬很容易錯。 |
| README/EPUB drift detection | 全文 diff | canonical snapshot diff | Phase 3 只需要檢查核心同步欄位，全文 diff 不符合產品邊界。 |

**Key insight:** 本 phase 真正應該 hand-roll 的只有 repo-specific orchestration，例如 README 模板拼裝、placeholder 規則掃描、canonical snapshot 比對。通用出版與檢查能力不要重寫。

## Common Pitfalls

### Pitfall 1: 把 README build 與 README authoring 混在一起
**What goes wrong:** 維護者直接改 root `README.md`，之後 manifest 與 README 慢慢分叉。  
**Why it happens:** README 是最顯眼的檔案，容易被當成主要編輯面。  
**How to avoid:** 明確標註 generated artifact；提供 `pnpm build:readme` 與 stale check。  
**Warning signs:** README diff 沒有對應 manifest/template 變更。

### Pitfall 2: 用全文比對判定 drift
**What goes wrong:** README 入口文案與 EPUB 正文天然不同，造成大量 false positive。  
**Why it happens:** 想偷懶用字串 diff 解決同步問題。  
**How to avoid:** 只比對 title/order/summary/milestone/EPUB access/fixed entry blocks。  
**Warning signs:** README 合法改版也一直被 drift gate 擋住。

### Pitfall 3: 資產在 GitHub 可見但 EPUB 缺圖
**What goes wrong:** relative path 在 repo 中能開，但 Pandoc 封裝後找不到。  
**Why it happens:** 沒有固定 `resource-path` 或 placeholder 與正式資產混用。  
**How to avoid:** 統一資產根目錄、固定 Pandoc defaults、獨立 placeholder path space。  
**Warning signs:** 本機 build 依賴特定工作目錄；CI 才爆圖。

### Pitfall 4: 外部連結檢查造成不可用的本機體驗
**What goes wrong:** 作者每次本機 build 都被短暫 429/timeout 擋住，最後乾脆跳過檢查。  
**Why it happens:** 沒分 tier，直接把 full external scan 放在所有指令。  
**How to avoid:** local 只做 smoke set 或短逾時版本，PR/release 做完整 retries，但仍 hard-fail。  
**Warning signs:** 維護者習慣用 `--no-links` 類型旗標逃避檢查。

### Pitfall 5: EPUB build 成功就被當成交付完成
**What goes wrong:** 檔案打得開，但 metadata、lang、TOC、cover 或 conformance 仍錯。  
**Why it happens:** 把 build success 當作品質檢查。  
**How to avoid:** EPUBCheck + 至少一種 reader smoke test 進 release gate。  
**Warning signs:** 沒人保存 EPUBCheck 報告，或只看 error 不看 warning。

## Code Examples

Verified patterns from official sources:

### Pandoc EPUB Build
```bash
# Source: https://pandoc.org/MANUAL.html
pandoc --defaults pandoc/defaults/epub.yaml
```

### EPUBCheck Validation
```bash
# Source: https://w3c.github.io/epubcheck/docs/installation/
java -jar tools/epubcheck/epubcheck.jar dist/epub/agent-book.epub
```

### README Stale Check
```bash
pnpm build:readme
git diff --exit-code README.md
```

### Link Verification Split
```bash
pnpm verify:links:internal
pnpm verify:links:external
```

### Placeholder Gate
```ts
const placeholderPatterns = [
  /assets\/placeholder\//,
  /placeholder[-_/]/i,
  /\[\[IMAGE_REQUEST:/,
];
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| `markdown_github` input in Pandoc | `gfm` input | Pandoc current manual | 不要用 deprecated reader；否則會把 GitHub-compatible source 建在舊接口上。 |
| 手工維護 README 章節清單 | generated root `README.md` | GitHub-first docs pipelines became standard | 能直接防止 repo entry drift。 |
| ad hoc shell commands in CI | versioned defaults + scripts | current CI best practice | 失敗更可定位，且本機與 CI 對齊。 |
| “file opens” 當 EPUB 驗證 | EPUBCheck hard gate | EPUB3 tooling maturity | 交付品質更可預測。 |

**Deprecated/outdated:**
- `markdown_github`: Pandoc 已明確將其標為 deprecated，應改用 `gfm`。
- 把 `README.md` 當正文來源：與本專案 Phase 1/3 的鎖定決策直接衝突。

## Open Questions

1. **manifest 最終欄位命名是否在 Phase 1/2 plan 中固定為 `manifest.yaml` 或其他名稱？**
   - What we know: chapter order/title/summary/milestone/checkpoint/output controls 已鎖定在 manifest。
   - What's unclear: 欄位實名與 schema 檔位置還未實作。
   - Recommendation: 在 Phase 3 Wave 0 先讀取並沿用前兩 phase 實作；不要在本 phase 重新發明第二套 schema。

2. **release reader smoke test 要選哪一個閱讀器作為最低門檻？**
   - What we know: locked decision 至少要有一種閱讀器 smoke test。
   - What's unclear: 具體 reader/tool 尚未指定。
   - Recommendation: 先定一個可自動或半自動執行的最低門檻 reader，再把更完整裝置矩陣留到後續 phase。

3. **外部連結的 local smoke 範圍要多大？**
   - What we know: 外部連結也必須驗，且 warning 不能放行。
   - What's unclear: local 是否跑全部外部 URL 或只跑小集合。
   - Recommendation: 規劃成可配置 profile；local 用較短 timeout + retry budget，PR/release 跑完整集合。

## Validation Architecture

### Test Framework
| Property | Value |
|----------|-------|
| Framework | `node:test` bundled with Node 24 LTS |
| Config file | none — see Wave 0 |
| Quick run command | `node --test tests/phase3/*.test.mjs` |
| Full suite command | `node --test tests/**/*.test.mjs` |

### Phase Requirements → Test Map
| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| PUBL-01 | canonical content generates committed root README with fixed sections | unit/integration | `node --test tests/phase3/readme-generation.test.mjs` | ❌ Wave 0 |
| PUBL-02 | canonical content generates EPUB with title/lang/TOC/metadata | integration | `node --test tests/phase3/epub-build.test.mjs` | ❌ Wave 0 |
| PUBL-03 | invalid EPUB or epubcheck warnings fail the gate | integration/smoke | `node --test tests/phase3/epub-validation.test.mjs` | ❌ Wave 0 |
| PUBL-04 | broken links/assets across chapters, README, outputs fail clearly | integration | `node --test tests/phase3/link-and-asset-validation.test.mjs` | ❌ Wave 0 |
| QUAL-02 | placeholder paths/tokens in publishable outputs fail | unit/integration | `node --test tests/phase3/placeholder-gate.test.mjs` | ❌ Wave 0 |
| QUAL-03 | README, chapters, and EPUB core sync fields do not drift | integration | `node --test tests/phase3/drift-detection.test.mjs` | ❌ Wave 0 |

### Sampling Rate
- **Per task commit:** `node --test tests/phase3/*.test.mjs`
- **Per wave merge:** `node --test tests/**/*.test.mjs`
- **Phase gate:** full phase tests + `pnpm verify:phase3` green before `/gsd:verify-work`

### Wave 0 Gaps
- [ ] `package.json` — declare Node 24+/pnpm scripts for build and verification
- [ ] `tests/phase3/readme-generation.test.mjs` — covers `PUBL-01`
- [ ] `tests/phase3/epub-build.test.mjs` — covers `PUBL-02`
- [ ] `tests/phase3/epub-validation.test.mjs` — covers `PUBL-03`
- [ ] `tests/phase3/link-and-asset-validation.test.mjs` — covers `PUBL-04`
- [ ] `tests/phase3/placeholder-gate.test.mjs` — covers `QUAL-02`
- [ ] `tests/phase3/drift-detection.test.mjs` — covers `QUAL-03`
- [ ] `tools/` or documented bootstrap path for Pandoc / EPUBCheck / Java
- [ ] Local runtime upgrade: current workspace Node is `v18.18.0`; research assumes Node 24 LTS

## Sources

### Primary (HIGH confidence)
- GitHub Docs, README behavior: https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes
- GitHub Docs, Markdown syntax, relative links, image paths, anchors: https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax
- GitHub Flavored Markdown spec: https://github.github.com/gfm/
- GitHub Docs, Markdown REST API: https://docs.github.com/en/rest/markdown/markdown
- Pandoc manual: https://pandoc.org/MANUAL.html
- Pandoc EPUB docs: https://pandoc.org/epub.html
- Node release policy: https://nodejs.org/en/about/previous-releases
- EPUBCheck install docs: https://w3c.github.io/epubcheck/docs/installation/

### Secondary (MEDIUM confidence)
- Pandoc latest release verified via official GitHub releases API: https://github.com/jgm/pandoc/releases/tag/3.9.0.2
- EPUBCheck latest release verified via official GitHub releases API: https://github.com/w3c/epubcheck/releases/tag/v5.3.0
- Vale latest release verified via official GitHub releases API: https://github.com/vale-cli/vale/releases/tag/v3.14.1
- lychee latest release verified via official GitHub releases API: https://github.com/lycheeverse/lychee/releases/tag/lychee-v0.23.0
- npm registry version checks: `pnpm@10.33.0`, `markdownlint-cli2@0.22.0`, `yaml@2.8.3`, `prettier@3.8.1`

### Tertiary (LOW confidence)
- None

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - 核心工具版本與能力由官方文件、release API、npm registry 交叉驗證。
- Architecture: MEDIUM - 生成器與 gate 模式穩定，但 README template 與 snapshot shape 仍需配合前兩 phase 實作落地。
- Pitfalls: MEDIUM - 失敗模式高度可信，但外部連結 tiering 與 reader smoke-test 深度仍帶專案裁量。

**Research date:** 2026-03-27
**Valid until:** 2026-04-26
