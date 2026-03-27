# Phase 1: 讀者入口與章節拓樸 - Research

**Researched:** 2026-03-27
**Domain:** GitHub-first Markdown reader entrypoint, chapter topology, and manifest-driven navigation
**Confidence:** HIGH

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
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

### Deferred Ideas (OUT OF SCOPE)
None — discussion stayed within phase scope.
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| READ-01 | 讀者可以從根目錄 `README.md` 立即理解本書目標、預期成果與開始閱讀的入口 | README landing pattern, first-screen content contract, milestone summary block |
| READ-02 | 讀者可以依照明確章節順序進入各章，不需要自行猜測閱讀路徑 | Single manifest pattern, numeric filenames, README chapter list generated from manifest order |
| READ-03 | 讀者可以在章節內使用一致的上一章、下一章與回首頁連結完成導覽 | Canonical bottom-nav block pattern and manifest-derived prev/next linking |
| READ-04 | 讀者可以在閱讀流程中清楚知道哪一章會完成 FV 計算機實作與 GitHub Pages 發布 | Milestone metadata fields, README milestone map, in-chapter milestone callouts |
| CONT-02 | 維護者可以透過單一章節清單或 manifest 管理章節順序與輸出順序 | JSON manifest + JSON Schema validation + Ajv recommendation |
</phase_requirements>

## Summary

Phase 1 should establish a GitHub-native reading surface, not a build system. The root `README.md` needs to behave like a guided book entrypoint, while a single machine-readable manifest owns chapter order, chapter metadata, milestone placement, and the inputs needed to derive per-chapter navigation. That keeps Phase 1 focused on information architecture and prevents Phase 3’s build work from having to untangle duplicated order and metadata later.

The strongest implementation shape is: numeric chapter filenames under a dedicated content directory, one central JSON manifest validated by JSON Schema, and a standardized chapter footer block that remains visible on GitHub but is explicitly delimited so a later EPUB pipeline can strip it safely. GitHub’s documented support for relative links, section links, and hidden HTML comments makes this approach compatible with repository reading today and downstream transforms later.

**Primary recommendation:** Use a single JSON manifest as the canonical topology source, drive `README.md` chapter order and chapter prev/next links from it, and mark the chapter footer nav with HTML comment boundaries so GitHub keeps it and EPUB can remove it later.

## Standard Stack

### Core
| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| GitHub Flavored Markdown | Current GitHub GFM | Reader-facing source format for `README.md` and chapter files | The repo itself is a reading surface, so source content must stay inside documented GitHub Markdown features. |
| JSON Schema Draft 2020-12 | 2020-12 | Schema contract for chapter manifest structure | Standard schema vocabulary is better than ad hoc validation logic and keeps manifest rules explicit. |
| Ajv | 8.18.0 | Validate the manifest against the schema in local scripts and CI | Ajv is the de facto Node validator for JSON Schema and avoids hand-rolled topology checks. |
| Node.js | 18.18.0+ | Minimal automation runtime for manifest validation and topology generation | The repo already has Node 18.18.0 available, which is sufficient for Phase 1’s scripting surface. |

### Supporting
| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| Prettier | 3.8.1 | Keep JSON and Markdown formatting deterministic | Use once manifest, README, and chapter templates are added. |
| markdownlint-cli2 | 0.22.0 | Catch heading, list, blank-line, and structure regressions in Markdown | Use to enforce consistent chapter footer shape and README structure. |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| `book-manifest.json` + schema | YAML manifest | YAML is readable, but JSON is stricter, easier to validate, and avoids parser ambiguity for a small topology file. |
| Central manifest metadata | Per-file front matter | Front matter duplicates topology fields across chapters and weakens the “single source of order” decision. |
| Plain Node scripts | Quarto / mdBook / site generators | Those tools optimize for rendering pipelines, not Phase 1’s repo-native information architecture. |

**Installation:**
```bash
npm install -D ajv@8.18.0 prettier@3.8.1 markdownlint-cli2@0.22.0
```

**Version verification:** Verified from the npm registry on 2026-03-27.
- `ajv` `8.18.0` published 2026-02-14
- `prettier` `3.8.1` published 2026-01-21
- `markdownlint-cli2` `0.22.0` published 2026-03-22

## Architecture Patterns

### Recommended Project Structure
```text
.
├── README.md                       # Guided repo entrypoint
├── book/
│   ├── manifest.json               # Canonical chapter order and metadata
│   └── manifest.schema.json        # Schema contract for manifest validation
├── chapters/
│   ├── 01-...md
│   ├── 02-...md
│   └── 03-...md
├── scripts/
│   ├── validate-manifest.mjs
│   └── sync-topology.mjs
└── tests/
    └── phase-01/
```

### Pattern 1: README As Guided Landing Page
**What:** Keep root `README.md` short, directional, and clearly book-like.
**When to use:** Always for this project. `README.md` is the first reader touchpoint, not a secondary artifact.
**Example:**
```markdown
# Agent 使用電子書

這本書會帶你用 Agent 完成第一個可公開部署的成果：FV 計算機。

## 你會做到什麼
- 完成可運作的 FV 計算機
- 將成果發布到 GitHub Pages

## 開始閱讀
- 從 [第 1 章：建立你的閱讀與實作起點](./chapters/01-reader-entry.md) 開始

## 章節順序
- [第 1 章：建立你的閱讀與實作起點](./chapters/01-reader-entry.md)
- [第 2 章：...](./chapters/02-....md)

## Milestones
- FV 計算機完成：第 X 章
- GitHub Pages 發布：第 Y 章
```

### Pattern 2: Manifest-Driven Topology
**What:** One manifest owns order, paths, milestone labels, checkpoint summaries, and whether a chapter belongs to the FV mainline.
**When to use:** Always. This is the direct implementation of `CONT-02`.
**Example:**
```json
{
  "$schema": "./manifest.schema.json",
  "bookTitle": "Agent 使用電子書",
  "chapters": [
    {
      "id": "reader-entry",
      "order": 1,
      "file": "chapters/01-reader-entry.md",
      "title": "建立你的閱讀與實作起點",
      "summary": "理解本書目標、成果與固定閱讀方式。",
      "milestone": null,
      "checkpoint": "你已知道閱讀順序與最終成果位置。",
      "isFvMainline": true
    },
    {
      "id": "fv-calculator",
      "order": 6,
      "file": "chapters/06-fv-calculator.md",
      "title": "完成你的 FV 計算機",
      "summary": "把教學主線收斂成可運作成果。",
      "milestone": "fv-calculator-complete",
      "checkpoint": "你已完成可驗證的 FV 計算機。",
      "isFvMainline": true
    }
  ]
}
```

### Pattern 3: Delimited Footer Navigation Block
**What:** Put the per-chapter footer nav in Markdown, but wrap it with HTML comments so a later EPUB step can remove the block by marker instead of guessing.
**When to use:** In every chapter footer.
**Example:**
```markdown
<!-- CHAPTER_NAV_START -->

## 繼續閱讀

- 上一章：[第 1 章：建立你的閱讀與實作起點](./01-reader-entry.md)
- 下一章：[第 3 章：整理你的章節節奏](./03-chapter-rhythm.md)
- 回首頁：[README](../README.md)

<!-- CHAPTER_NAV_END -->
```

### Pattern 4: Milestones As Metadata, Not Editorial Guesswork
**What:** Represent milestone stages in the manifest and render them into both README and chapter-level copy.
**When to use:** For FV calculator completion and GitHub Pages publishing, plus any future phase gate that affects reading expectations.
**Example:**
```json
{
  "milestones": {
    "fv-calculator-complete": {
      "label": "完成 FV 計算機",
      "chapterId": "fv-calculator"
    },
    "github-pages-published": {
      "label": "發布到 GitHub Pages",
      "chapterId": "github-pages"
    }
  }
}
```

### Anti-Patterns to Avoid
- **Manual chapter lists in multiple places:** Do not hand-maintain chapter order in `README.md`, chapter footers, and future build scripts separately.
- **README as partial book manuscript:** Do not put long-form chapter prose in root `README.md`.
- **Prev/next links derived from filenames alone:** Numeric filenames help humans, but manifest order must remain canonical.
- **Topology metadata inside every chapter:** Do not distribute milestone/checkpoint/order fields into front matter across chapter files.
- **Undelimited nav stripping plan:** Do not assume EPUB cleanup can safely remove nav later without explicit markers now.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Manifest validation | Custom `if/else` checks scattered through scripts | JSON Schema + Ajv | Schema gives one explicit contract for required fields, uniqueness, and allowed milestone values. |
| Chapter order syncing | Separate hard-coded arrays in README, nav, and output logic | One manifest file | Duplication is the main drift risk this phase is supposed to eliminate. |
| EPUB nav cleanup strategy | Regexes that guess where the footer starts | Explicit `CHAPTER_NAV_START/END` markers | Delimited blocks are safer and make later removal deterministic. |
| Reader milestones | Handwritten milestone reminders copied across files | Manifest milestone metadata rendered into README and chapters | Copy-paste milestone text will drift once chapter names change. |
| Repo navigation | Issue-style shorthand or heading-fragment-only navigation | Explicit relative file links | GitHub does not create issue/PR autolinks in repository files, so file links must stay explicit. |

**Key insight:** The main engineering risk in this phase is topology drift, not rendering complexity. Prefer declarative metadata and explicit markers over “smart” script logic.

## Common Pitfalls

### Pitfall 1: `README.md` grows into a second book
**What goes wrong:** The entrypoint becomes long, repetitive, and stops functioning as a start-here guide.
**Why it happens:** README is easy to edit, so teams keep adding content instead of preserving a landing-page boundary.
**How to avoid:** Lock README to four jobs only: book promise, end goal, start link, ordered chapter list with milestones.
**Warning signs:** Long code blocks, duplicated chapter prose, or repeated checkpoint content in root `README.md`.

### Pitfall 2: Manifest exists but is not truly canonical
**What goes wrong:** Order still leaks into filenames, chapter footers, and README edits, so changing a chapter requires multiple manual fixes.
**Why it happens:** Teams introduce a manifest but continue treating it as documentation instead of executable truth.
**How to avoid:** All prev/next links, README chapter listings, and milestone chapter references must resolve from manifest IDs.
**Warning signs:** A chapter reorder requires editing more than the manifest and generated output.

### Pitfall 3: Chapter nav is written for GitHub but not structured for removal
**What goes wrong:** Later EPUB work has to use brittle text matching to strip footer links.
**Why it happens:** Footer nav looks harmless in Markdown, so teams skip boundary markers.
**How to avoid:** Add explicit HTML comment delimiters around every nav block from the start.
**Warning signs:** Footer patterns differ between chapters or include ad hoc wording.

### Pitfall 4: Milestones are implied, not visible
**What goes wrong:** Readers cannot tell where the FV calculator is completed or where publishing happens until they reach those chapters.
**Why it happens:** Milestones stay in roadmap docs instead of the reading surface.
**How to avoid:** Put milestone summaries in README and in chapter opening/closing copy for milestone chapters.
**Warning signs:** The roadmap knows the milestone chapter, but README and chapters do not surface it.

### Pitfall 5: Navigation relies on GitHub behaviors that do not apply to repo files
**What goes wrong:** Shorthand references or unstable heading fragments break navigation expectations.
**Why it happens:** GitHub conversation features are mistaken for repository-file features.
**How to avoid:** Use explicit relative file links for chapter navigation and keep heading fragments optional, not critical.
**Warning signs:** Repo Markdown contains `#123` or similar shorthand as if it were a stable file link.

## Code Examples

Verified patterns from official sources:

### Minimal Manifest Schema
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "required": ["bookTitle", "chapters"],
  "properties": {
    "bookTitle": { "type": "string", "minLength": 1 },
    "chapters": {
      "type": "array",
      "minItems": 1,
      "items": {
        "type": "object",
        "required": ["id", "order", "file", "title", "summary", "checkpoint", "isFvMainline"],
        "properties": {
          "id": { "type": "string", "pattern": "^[a-z0-9-]+$" },
          "order": { "type": "integer", "minimum": 1 },
          "file": { "type": "string", "pattern": "^chapters/[0-9]{2}-.*\\.md$" },
          "title": { "type": "string", "minLength": 1 },
          "summary": { "type": "string", "minLength": 1 },
          "milestone": {
            "type": ["string", "null"],
            "enum": [null, "fv-calculator-complete", "github-pages-published"]
          },
          "checkpoint": { "type": "string", "minLength": 1 },
          "isFvMainline": { "type": "boolean" }
        },
        "additionalProperties": false
      }
    }
  },
  "additionalProperties": false
}
```

### Ajv Validation Script
```js
// Source pattern: JSON Schema Draft 2020-12 + Ajv 8.x
import fs from "node:fs";
import Ajv from "ajv";

const schema = JSON.parse(fs.readFileSync("book/manifest.schema.json", "utf8"));
const manifest = JSON.parse(fs.readFileSync("book/manifest.json", "utf8"));

const ajv = new Ajv({ allErrors: true });
const validate = ajv.compile(schema);

if (!validate(manifest)) {
  console.error(validate.errors);
  process.exit(1);
}
```

### Chapter Footer Template
```markdown
<!-- CHAPTER_NAV_START -->

## 繼續閱讀

- 上一章：[{{prevTitle}}](./{{prevFile}})
- 下一章：[{{nextTitle}}](./{{nextFile}})
- 回首頁：[README](../README.md)

<!-- CHAPTER_NAV_END -->
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Manual README TOC plus hand-written chapter links | Manifest-driven topology with generated or checked navigation | Matured as docs repos moved toward single-source pipelines | Phase 1 should start with one topology file, not retrofit it later. |
| Using repo-discussion shorthand like `#123` in docs | Explicit relative links in repository Markdown | Stable GitHub docs guidance | Reader navigation in repo files must use file paths, not conversation shortcuts. |
| Free-form footer content per chapter | Standardized, machine-recognizable nav blocks | Current docs/build practice | Later output-specific stripping and consistency checks become straightforward. |

**Deprecated/outdated:**
- `README.md` as full manuscript: outdated for this repo because it directly conflicts with the locked “README as entrypoint” decision.
- Per-file topology front matter: outdated for this phase because it weakens `CONT-02` and duplicates canonical metadata.

## Open Questions

1. **What should the manifest filename and directory be?**
   - What we know: it must be a single canonical manifest and should be easy for later scripts to consume.
   - What's unclear: whether the project prefers `book/manifest.json`, `content/manifest.json`, or a root-level file.
   - Recommendation: choose `book/manifest.json` in planning to keep topology separate from prose files and future build outputs.

2. **Should README be generated in Phase 1 or only validated against the manifest?**
   - What we know: the README content boundary must be fixed now, and order drift must be prevented.
   - What's unclear: whether implementation should include generation immediately or a stricter manual file plus validation step.
   - Recommendation: plan Phase 1 so README is either generated or programmatically checked against the manifest before merge. Do not rely on manual review alone.

3. **What is the initial chapter count and milestone chapter numbering?**
   - What we know: readers must see a fixed order and clear milestone locations.
   - What's unclear: exact early chapter list and which numbers will map to FV completion and GitHub Pages publishing.
   - Recommendation: finalize the initial manifest chapter set during planning and treat milestone chapter IDs as required fields.

## Validation Architecture

### Test Framework
| Property | Value |
|----------|-------|
| Framework | `node:test` with manifest-validation and Markdown topology checks |
| Config file | none - see Wave 0 |
| Quick run command | `node --test tests/phase-01/*.test.mjs` |
| Full suite command | `node --test && npx markdownlint-cli2 \"**/*.md\"` |

### Phase Requirements → Test Map
| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| READ-01 | Root `README.md` contains book goal, expected outcome, and one explicit start-reading link near the top | integration | `node --test tests/phase-01/readme-entry.test.mjs` | ❌ Wave 0 |
| READ-02 | README chapter list order matches manifest order and chapter filenames follow numeric prefixes | integration | `node --test tests/phase-01/topology-order.test.mjs` | ❌ Wave 0 |
| READ-03 | Every chapter contains the same footer-nav block with previous, next, and home links | integration | `node --test tests/phase-01/chapter-nav.test.mjs` | ❌ Wave 0 |
| READ-04 | README and milestone chapters expose FV calculator and GitHub Pages milestone signals | integration | `node --test tests/phase-01/milestone-signaling.test.mjs` | ❌ Wave 0 |
| CONT-02 | Manifest conforms to schema and can derive one unambiguous reading/output order | unit | `node --test tests/phase-01/manifest-schema.test.mjs` | ❌ Wave 0 |

### Sampling Rate
- **Per task commit:** `node --test tests/phase-01/*.test.mjs`
- **Per wave merge:** `node --test && npx markdownlint-cli2 "**/*.md"`
- **Phase gate:** Full suite green before `/gsd:verify-work`

### Wave 0 Gaps
- [ ] `package.json` — define scripts for `test`, `lint:md`, and topology checks
- [ ] `tests/phase-01/manifest-schema.test.mjs` — covers `CONT-02`
- [ ] `tests/phase-01/readme-entry.test.mjs` — covers `READ-01`
- [ ] `tests/phase-01/topology-order.test.mjs` — covers `READ-02`
- [ ] `tests/phase-01/chapter-nav.test.mjs` — covers `READ-03`
- [ ] `tests/phase-01/milestone-signaling.test.mjs` — covers `READ-04`
- [ ] `book/manifest.schema.json` — required for machine-verifiable manifest contract

## Sources

### Primary (HIGH confidence)
- GitHub Docs: https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes
  - Checked README’s role as the repository entrypoint and the documented behavior around repository reading.
- GitHub Docs: https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax
  - Checked section links, relative links, custom anchors, alerts, and hiding content with HTML comments.
- GitHub Docs: https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/autolinked-references-and-urls
  - Verified that issue and pull-request autolink shortcuts are not created in repository files.
- GitHub Flavored Markdown Spec: https://github.github.com/gfm/
  - Verified that HTML comments are valid Markdown constructs and usable as explicit delimiters.
- JSON Schema Draft 2020-12: https://json-schema.org/draft/2020-12
  - Verified the current stable schema vocabulary recommended for manifest validation.
- npm registry checks performed on 2026-03-27
  - `ajv` `8.18.0`
  - `prettier` `3.8.1`
  - `markdownlint-cli2` `0.22.0`

### Secondary (MEDIUM confidence)
- Pandoc User’s Guide: https://pandoc.org/MANUAL.html
  - Used only to confirm that later multi-file parsing and path-rebasing concerns are real, which supports adding explicit nav delimiters and a canonical manifest now.

### Tertiary (LOW confidence)
- None.

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - GitHub docs, JSON Schema docs, and npm registry version checks are current and directly relevant to this phase.
- Architecture: HIGH - The central manifest and delimited-nav patterns are direct consequences of locked project decisions plus documented GitHub Markdown behavior.
- Pitfalls: HIGH - The main risks are repo-local drift risks already visible from the phase boundary and supported by GitHub file-rendering rules.

**Research date:** 2026-03-27
**Valid until:** 2026-04-26
