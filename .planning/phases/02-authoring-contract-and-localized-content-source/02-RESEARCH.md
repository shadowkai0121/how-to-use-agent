# Phase 2: 作者寫作契約與在地化內容源 - Research

**Researched:** 2026-03-27
**Domain:** GitHub-first zh-TW authoring contract, canonical Markdown source tree, and localized terminology governance
**Confidence:** HIGH

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
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

### Deferred Ideas (OUT OF SCOPE)
None — discussion stayed within phase scope.
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| CONT-01 | 維護者可以用獨立 Markdown 檔案管理各章內容，且語法符合 GitHub Flavored Markdown | Canonical content tree with one chapter per file, manifest-owned metadata, and chapter-template rules |
| CONT-03 | 維護者可以依照明確的 GFM 安全寫作規範撰寫標題、連結、程式碼區塊、提示與折疊內容 | Prescriptive safe-subset contract for headings, links, fenced code, alerts, and `<details>` blocks |
| CONT-04 | 維護者可以用統一格式標記待補圖片段落，讓人類後續補圖時不必重新搜尋上下文 | Placeholder-path convention, standalone image-block rule, and machine-checkable detection strategy |
| QUAL-01 | 所有正式書籍內容都以台灣繁體中文撰寫，並維持一致的術語用法 | zh-TW termbase file, English-retention policy, and prose-lint validation rules |
</phase_requirements>

## Summary

Phase 2 should lock the authoring surface, not the publishing pipeline. The implementation needs one canonical source tree with explicit partitions for front matter, chapters, appendices, and assets; a single manifest that owns chapter-level control data; and a narrow, written GFM contract that authors can follow without guessing which syntax is safe on GitHub.

The highest-leverage choices are mechanical: keep chapter prose free of duplicated metadata, standardize one placeholder image path namespace that is visibly different from real assets, and introduce a machine-readable zh-TW terminology file early. This phase should end with source structure, templates, and validation rules that make future writing boring and repeatable.

**Primary recommendation:** Use one manifest plus one chapter template contract, enforce a GitHub-safe Markdown subset with automated checks, and make image placeholders and zh-TW terminology machine-readable from day one.

## Standard Stack

### Core
| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| GitHub Flavored Markdown | Current GitHub GFM | Canonical chapter source format | The repository itself is a reading surface, so authors must write inside documented GitHub Markdown behavior. |
| JSON Schema Draft 2020-12 | 2020-12 | Manifest contract and termbase schema | Schema-based validation is safer than scattered ad hoc checks. |
| Ajv | 8.18.0 | Validate manifest and authoring metadata files | Reuses the Node validation direction already established in Phase 1 planning. |
| markdownlint-cli2 | 0.22.0 | Enforce heading, spacing, and Markdown structure rules | The fastest way to catch heading jumps and malformed authoring patterns. |
| Node.js | 18.18.0+ | Local validation runtime | Node `v18.18.0` is already available in this workspace and is enough for Phase 2 automation. |

### Supporting
| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| Prettier | 3.8.1 | Normalize Markdown, JSON, and YAML formatting | Use on every content and schema change to keep diffs readable. |
| `yaml` | 2.8.3 | Parse a zh-TW terminology file if it is stored as YAML | Use if the planner chooses `.yml` for the expandable termbase. |
| Vale | External binary | Prose and terminology linting | Use to enforce zh-TW glossary terms and banned drift phrases once the first chapters exist. |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Manifest-owned chapter metadata | Per-file front matter | Front matter spreads control data across chapter files and weakens the “single source” decision. |
| One Markdown file per chapter | Multiple partials per chapter | Partials make reordering and GitHub reading harder and directly conflict with the locked authoring decision. |
| Placeholder path namespace | HTML comments or inline TODO text | Those are harder to render consistently and easier to leak into prose unnoticed. |

**Installation:**
```bash
npm install -D ajv@8.18.0 markdownlint-cli2@0.22.0 prettier@3.8.1 yaml@2.8.3
```

**Version verification:** Verified from the npm registry on 2026-03-27.
- `ajv` `8.18.0` published 2026-02-14
- `markdownlint-cli2` `0.22.0` published 2026-03-22
- `prettier` `3.8.1` published 2026-01-21
- `yaml` `2.8.3` published 2026-03-21

## Architecture Patterns

### Recommended Project Structure
```text
.
├── book/
│   ├── manifest.json               # Canonical chapter control data
│   ├── manifest.schema.json        # Manifest contract
│   ├── frontmatter/
│   │   ├── 00-前言.md
│   │   └── 01-如何閱讀本書.md
│   ├── chapters/
│   │   ├── 02-建立你的閱讀與第一個實作起點.md
│   │   └── ...
│   ├── appendices/
│   │   └── 附錄-a-術語表.md
│   └── terms.zh-TW.yml             # Canonical terminology list
├── assets/
│   ├── images/                     # Real published images
│   └── placeholders/               # Placeholder image targets only
├── scripts/
│   ├── validate-manifest.mjs
│   ├── validate-authoring.mjs
│   └── validate-terms.mjs
└── tests/
    └── phase-02/
```

### Pattern 1: Manifest Owns Chapter Control Data
**What:** Keep chapter title, summary, milestone, checkpoint, author notes, and output flags only in the manifest.
**When to use:** Always. Chapter Markdown should hold prose only.
**Example:**
```json
{
  "id": "finish-fv-calculator",
  "file": "book/chapters/06-完成可驗證的-fv-計算機.md",
  "title": "完成可驗證的 FV 計算機",
  "summary": "把前幾章的實作收斂成可驗證成果。",
  "milestone": "fv-calculator-complete",
  "checkpoint": "你已完成可驗證的 FV 計算機。",
  "authorNotes": [
    "保留 GitHub Pages 發布前的狀態描述"
  ],
  "outputs": ["readme", "epub"]
}
```

### Pattern 2: Chapter Files Start Clean and Predictable
**What:** Every chapter starts with one H1, then body headings without jumps.
**When to use:** In every frontmatter, chapter, and appendix Markdown file.
**Example:**
```markdown
# 第 6 章：完成可驗證的 FV 計算機

## 本章會完成什麼

## 你會產出的東西

## 操作步驟
```

### Pattern 3: GitHub-Safe Extended Blocks Only
**What:** Allow GitHub-supported alerts and `<details>` blocks, but only in a narrow template shape.
**When to use:** For callouts, tips, warnings, and optional deep dives.
**Example:**
```markdown
> [!NOTE]
> 這一節只補充背景，主線操作仍以前文步驟為準。

<details>
<summary>展開看完整說明</summary>

這裡可以放補充文字、圖片或程式碼區塊。

</details>
```

### Pattern 4: Placeholder Images Are Real Markdown Images
**What:** Use standard Markdown image syntax that already looks like the final shape.
**When to use:** Whenever a figure is expected but not yet available.
**Example:**
```markdown
![FV 計算機完成畫面的預期截圖](../assets/placeholders/ch06-fv-calculator-finished.png)
```

### Pattern 5: Terminology Lives in One Expandable Termbase
**What:** Store canonical zh-TW wording, English term, and usage notes in one machine-readable file.
**When to use:** For UI nouns, workflow verbs, and domain terms likely to drift.
**Example:**
```yaml
- key: github-pages
  preferred_zh_tw: GitHub Pages
  english: GitHub Pages
  rule: keep_english
  notes: 專有服務名，正文直接保留英文

- key: deploy
  preferred_zh_tw: 部署
  english: Deploy
  rule: chinese_with_original
  notes: 介面操作敘述寫成 部署（Deploy）
```

### Anti-Patterns to Avoid
- **Chapter metadata in prose files:** Do not duplicate milestone, checkpoint, or summary inside front matter or hidden comments.
- **Inline image placeholders inside paragraphs:** Placeholders must be standalone blocks so humans can replace them without text surgery.
- **Free-form callout styles:** Do not mix raw HTML cards, custom shortcodes, and GitHub alerts.
- **Term decisions in reviewer comments only:** Terminology must move into a tracked file, not stay in PR discussion memory.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Heading-level enforcement | Custom regexes over Markdown text | `markdownlint-cli2` | It already catches heading jumps and structural mistakes reliably. |
| Manifest consistency | Manual review for required metadata fields | JSON Schema + Ajv | This keeps the content contract explicit and machine-checkable. |
| Terminology drift tracking | Ad hoc spreadsheet or memory | Versioned `terms.zh-TW.yml` + Vale vocabulary/style rules | The glossary must live with the source and be enforceable. |
| Placeholder detection | Searching for random `TODO` strings | Dedicated `assets/placeholders/` path namespace | A path-based contract is simpler to render and easier to scan. |
| Collapsed content styling | Custom HTML blocks or build-only widgets | Standard `<details><summary>` blocks | GitHub documents this pattern directly and it degrades predictably. |

**Key insight:** Phase 2 is mostly about standardization pressure. The safest plan is to choose boring, explicit shapes that can be linted, parsed, and migrated later.

## Common Pitfalls

### Pitfall 1: Manifest and chapter files both become metadata sources
**What goes wrong:** Title, checkpoint, or milestone data drifts between prose files and the manifest.
**Why it happens:** Teams add “temporary” front matter for convenience.
**How to avoid:** Keep all chapter-level control data in the manifest and treat Markdown files as prose-only.
**Warning signs:** A chapter rename requires edits in both the manifest and local metadata fields.

### Pitfall 2: GitHub-safe syntax is defined too loosely
**What goes wrong:** Authors start using Mermaid, arbitrary raw HTML, or renderer-specific tricks that later block reuse.
**Why it happens:** “GFM-safe” is declared without a concrete allowlist.
**How to avoid:** Write a short authoring spec that explicitly allows headings, relative links, fenced code, GitHub alerts, and `<details>`; everything else requires review.
**Warning signs:** PRs introduce raw HTML blocks other than `<details>` or rely on GitHub-only post-processing behavior.

### Pitfall 3: Placeholder images are visually obvious but not machine-detectable
**What goes wrong:** Reviewers can see placeholders, but release tooling cannot distinguish them from final images.
**Why it happens:** The project uses human-readable notes instead of a stable path contract.
**How to avoid:** Reserve `assets/placeholders/` for placeholder targets only and require every missing figure to point there.
**Warning signs:** Placeholder images and final screenshots share the same folder or naming pattern.

### Pitfall 4: zh-TW consistency is left to editorial taste
**What goes wrong:** The same concept appears as multiple Chinese translations across chapters.
**Why it happens:** The project agrees on “Traditional Chinese” but never codifies preferred terms.
**How to avoid:** Add a small termbase now and let it grow with the manuscript.
**Warning signs:** Review comments repeatedly debate whether to translate the same word.

### Pitfall 5: File renames stop matching reading order
**What goes wrong:** Manifest order changes but filenames still reflect old positions, which confuses repo browsing.
**Why it happens:** Teams rely on manifest order and stop maintaining filename order.
**How to avoid:** Keep numeric or ordered Chinese filenames aligned with manifest order whenever chapters move.
**Warning signs:** The manifest says chapter 4 but the file path still starts with chapter 6 wording.

## Code Examples

Verified patterns from official sources:

### GitHub Alert Block
```markdown
> [!TIP]
> 先完成這一步，再繼續下一章。
```

### GitHub Collapsed Section
```markdown
<details>
<summary>展開看補充說明</summary>

這裡可以放補充文字、圖片或程式碼。

</details>
```

### Standalone Placeholder Image
```markdown
![這裡預計放 GitHub Pages 部署完成畫面的截圖](../assets/placeholders/ch07-github-pages-live.png)
```

### Termbase Rule Entry
```yaml
- key: settings
  preferred_zh_tw: 設定
  english: Settings
  rule: chinese_with_original
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| README or chapter front matter holding duplicated metadata | Central manifest as chapter-control source | Current docs-pipeline practice | Keeps chapter prose portable and prevents metadata drift. |
| Raw HTML callout hacks | GitHub-supported alerts and `<details>` | Current GitHub Docs guidance | Makes authoring rules shorter and repository rendering predictable. |
| Human-only image TODO notes | Real Markdown image placeholders with reserved paths | Current single-source docs practice | Lets future automation detect missing figures without parsing prose. |

**Deprecated/outdated:**
- Free-form placeholder markers such as `TODO 圖` or HTML comments as the main figure contract.
- Multiple Chinese translations for the same UI or platform term without a tracked canonical entry.

## Open Questions

1. **Should the termbase live beside the manifest or as an appendix source?**
   - What we know: it must be expandable and enforceable.
   - What's unclear: whether the canonical file should be `book/terms.zh-TW.yml` only, or also generate a human-facing appendix later.
   - Recommendation: make `book/terms.zh-TW.yml` canonical now; render a glossary appendix from it later if needed.

2. **Should front matter and appendices sit under `book/` or as top-level siblings?**
   - What we know: the content tree must be clearly partitioned.
   - What's unclear: exact root path naming.
   - Recommendation: keep all prose under `book/` so the planner can reason about one canonical content root.

## Validation Architecture

### Test Framework
| Property | Value |
|----------|-------|
| Framework | `node:test` plus `markdownlint-cli2` and Vale |
| Config file | none - see Wave 0 |
| Quick run command | `node --test tests/phase-02/*.test.mjs` |
| Full suite command | `node --test tests/phase-0*.test.mjs && npx markdownlint-cli2 "**/*.md" && vale .` |

### Phase Requirements → Test Map
| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| CONT-01 | Every chapter is an independent Markdown file under the canonical content tree and manifest paths resolve to real files | integration | `node --test tests/phase-02/content-tree.test.mjs` | ❌ Wave 0 |
| CONT-03 | Chapter files follow the safe authoring subset: one H1, no heading jumps, relative links by default, fenced code blocks, standard alerts/details only | integration | `node --test tests/phase-02/gfm-contract.test.mjs && npx markdownlint-cli2 "book/**/*.md"` | ❌ Wave 0 |
| CONT-04 | Placeholder figures use standalone Markdown image blocks that point only into `assets/placeholders/` | integration | `node --test tests/phase-02/placeholders.test.mjs` | ❌ Wave 0 |
| QUAL-01 | zh-TW terminology matches the canonical termbase and prohibited drift terms are flagged | integration | `node --test tests/phase-02/terms.test.mjs && vale .` | ❌ Wave 0 |

### Sampling Rate
- **Per task commit:** `node --test tests/phase-02/*.test.mjs`
- **Per wave merge:** `node --test tests/phase-0*.test.mjs && npx markdownlint-cli2 "**/*.md"`
- **Phase gate:** Full suite green before `/gsd:verify-work`

### Wave 0 Gaps
- [ ] `package.json` — if Phase 1 has not created the Node scripts yet, add `test`, `lint:md`, and authoring validation commands
- [ ] `tests/phase-02/content-tree.test.mjs` — covers `CONT-01`
- [ ] `tests/phase-02/gfm-contract.test.mjs` — covers `CONT-03`
- [ ] `tests/phase-02/placeholders.test.mjs` — covers `CONT-04`
- [ ] `tests/phase-02/terms.test.mjs` — covers `QUAL-01`
- [ ] `book/terms.zh-TW.yml` — canonical terminology source
- [ ] `scripts/validate-authoring.mjs` — custom checks for placeholder path rules and allowed extended syntax
- [ ] `.vale.ini` or `vale.ini` — terminology and prose lint configuration

## Sources

### Primary (HIGH confidence)
- GitHub Docs: https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax
  - Checked headings, relative links, images, footnotes, and GitHub alerts.
- GitHub Docs: https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/organizing-information-with-collapsed-sections
  - Verified `<details>` and `<summary>` as supported collapsed-section syntax in GitHub Markdown.
- GitHub Docs: https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/autolinked-references-and-urls
  - Verified repository content should not rely on conversation-style shorthand as a navigation contract.
- GitHub Flavored Markdown Spec: https://github.github.com/gfm/
  - Checked GFM as the baseline source format and HTML comment validity.
- Pandoc User’s Guide: https://pandoc.org/MANUAL.html
  - Verified `gfm` is the preferred GitHub-flavored input and `markdown_github` is deprecated; this supports keeping Phase 2 authoring inside modern GFM.
- JSON Schema Draft 2020-12: https://json-schema.org/draft/2020-12
  - Verified the current schema vocabulary for manifest and termbase validation.
- npm registry checks performed on 2026-03-27
  - `ajv` `8.18.0`
  - `markdownlint-cli2` `0.22.0`
  - `prettier` `3.8.1`
  - `yaml` `2.8.3`

### Secondary (MEDIUM confidence)
- `.planning/research/ARCHITECTURE.md`
  - Used to stay consistent with the project’s previously researched single-source content pipeline.
- `.planning/research/PITFALLS.md`
  - Used to align this phase’s authoring rules with the already-identified placeholder and terminology risks.

### Tertiary (LOW confidence)
- Vale binary/version choice
  - The project should use Vale for terminology enforcement, but exact distribution/version should be finalized during implementation because it is not being installed from npm here.

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - Core recommendations are based on current GitHub docs, JSON Schema docs, npm registry data, and the existing Phase 1 Node validation direction.
- Architecture: HIGH - The recommended structure follows the locked user decisions directly and fits the repo’s current planning-first state.
- Pitfalls: HIGH - The main risks are already visible from the phase boundary and are preventable with explicit contracts.

**Research date:** 2026-03-27
**Valid until:** 2026-04-26
