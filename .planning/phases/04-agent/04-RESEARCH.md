# Phase 4: Agent 協作教學主線 - Research

**Researched:** 2026-03-27
**Domain:** docs-first, task-driven tutorial design for Agent collaboration
**Confidence:** HIGH

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
### 教學主線與章節骨架
- 教學主線採任務驅動，從一開始就圍著 FV 計算機任務往前推進，而不是先講工具概論。
- 章節以任務成果分章，但每章內固定帶一輪協作節奏。
- 每章的寫法採最短可執行步驟優先，先讓讀者做出結果，再補最少必要解釋。
- 每章開頭要先寫清楚本章會產出的具體成果，再補這一章會練到的協作能力。

### 協作流程 framing
- phase 4 先照 GSD 的開發流程來教讀者協作節奏，但不正式展開 GSD 指令操作。
- 文案上先不用強打 `GSD` 名稱，優先用自然語言表達規劃、執行、驗收等流程節拍。
- 章內對協作對象統一稱為 `Agent`，不混用品牌名或工具名。
- `antigravity` 不作為章節主體品牌來強調，重點放在「和 Agent 對話協作」本身。

### Prompt 與命令呈現方式
- 章內 prompt / 命令以可直接複製的最短版本作為主呈現。
- 主文先放當下章節可直接使用的具體實例版，再補充如何改寫成自己的版本。
- 較長的 prompt / 命令要分段呈現，讓每段都能單獨理解與複製。
- 章內排版以實際操作順序為主，但每段都要用明顯標籤區分 `Prompt` 與 `Command`。

### Checkpoint 設計
- 只要章內有實作，就要提供 checkpoint，不只在章尾集中驗收。
- checkpoint 的優先順序是先確認畫面或行為結果，再確認 repo 狀態，最後才確認是否可安全進到下一步。
- checkpoint 的主體採結果加 repo 證據的形式，但結果驗收必須放在最前面。
- 若該章只能驗證局部成果，仍要以最小可觀察結果作為正式 checkpoint，而不是延後到下一章再驗。
- checkpoint 語氣偏驗收清單，讓讀者能快速比對自己是否達標。

### 卡關回正方式
- 當 Agent 回應偏掉、做太多或做錯方向時，章內要先給可直接救火的回正 prompt，再補如何判斷偏差與自行修正。
- 當 repo 狀態與章節預期不一致時，優先引導讀者退回最近 checkpoint，而不是直接進行模糊補救。
- 回正內容要緊貼在各章 checkpoint 後面，讓讀者在出錯當下就能修正。
- 補救策略偏保守，寧可回到上一個安全點，也不要帶著錯誤繼續往下做。

### 解釋深度與章內密度
- phase 4 先不追求深入原理解釋，結果做出來後只補最少必要說明。
- 教學密度偏實作導向，避免長篇概念說明打斷任務前進節奏。

### Claude's Discretion
- 各章固定協作節奏的具體小標題命名與版型。
- `Prompt` / `Command` 區塊的視覺樣式與標示格式。
- 每章 checkpoint 的具體條列模板，只要維持成果優先、repo 證據次之即可。
- 回正 prompt 的具體措辭與示例長度。

### Deferred Ideas (OUT OF SCOPE)
- 正式的 GSD 指令操作與 workflow 命令教學 — 留到下一個 milestone。
- 更深入的「為什麼這樣和 Agent 協作」原理解析 — 留到後續 milestone 或補充章節深化。
- 更強調 `antigravity` 或 `GSD` 品牌名稱的產品導向教學寫法 — 不屬於本 phase 的主體。
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| TUTO-01 | 讀者可以依照章節理解 antigravity 與 GSD 的基本互動節奏與操作上下文 | Fixed chapter rhythm, outcome-first openings, natural-language plan/execute/check framing, and chapter-level collaboration capability labels |
| TUTO-02 | 讀者可以依照章節中的可複製 prompt、命令與範例逐步完成 FV 計算機 | Copy-ready prompt/command blocks, segmented long prompts, expected-result notes, and example-first then adaptation-second pattern |
| TUTO-05 | 每一章都提供 checkpoint，讓讀者確認目前 repo 狀態、頁面結果或下一步條件 | In-flow checkpoints, result-first verification order, adjacent recovery prompts, and safe-point rollback guidance |
</phase_requirements>

## Summary

Phase 4 should not be planned as "write several chapters." It should be planned as a tutorial system with one repeatable collaboration rhythm that gets reused across chapters. The most important planning move is to standardize the reader loop first: chapter outcome, minimal context, `Prompt`, `Command`, expected result, checkpoint, then recovery prompt. Once that loop is fixed, actual chapter writing becomes execution against a known contract instead of ad hoc prose.

The tutorial should teach collaboration by letting the reader make visible progress on the FV calculator, not by front-loading Agent theory. Official documentation guidance converges on short numbered procedures, one best path, action-before-explanation, and samples that are runnable, concise, and explicit about setup and expected results. For this phase, that means every chapter must anchor to a smallest observable outcome and every prompt or command block must tell the reader what to expect next.

The main risk is reproducibility drift. A docs-first tutorial fails when prompts, commands, checkpoints, chapter order, and actual repo state stop matching. Planning therefore needs a dedicated validation layer for chapter block structure, required checkpoint presence, and stale repo references. Do not leave drift control to editorial review alone.

**Primary recommendation:** Split Phase 4 into three plans: define the reusable tutorial block contract, author the chapter content against that contract, then add validation that enforces prompt/command/checkpoint/recovery structure and repo-state reproducibility.

## Standard Stack

### Core
| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| GitHub Flavored Markdown | Current GitHub GFM | Canonical chapter source for tutorial steps, prompts, commands, checkpoints, and recovery prompts | The repo is itself a reading surface, so the tutorial contract must stay inside documented GitHub-readable Markdown. |
| Existing chapter manifest contract | Phase 1/2 canonical contract | Chapter-level outcome, summary, checkpoint summary, milestone, and ordering metadata | Phase 4 should extend the existing single source of truth instead of inventing chapter-local metadata islands. |
| markdownlint-cli2 | 0.22.0 | Enforce consistent heading, list, and block ordering for tutorial chapters | A block-heavy tutorial degrades quickly without machine checks for structure. |
| Prettier | 3.8.1 | Keep Markdown formatting deterministic for generated or templated tutorial blocks | Stable formatting reduces noisy diffs when block templates are reused across chapters. |

### Supporting
| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| HTML comments in GFM | Supported in current GFM | Mark machine-detectable block boundaries such as tutorial-step or recovery sections | Use if validation scripts need stable anchors without changing reader-visible copy. |
| `node:test` | Node built-in in project runtime | Fast content-contract tests for chapter structure and manifest/tutorial consistency | Use for Phase 4 validation because no external test runner exists yet. |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Fixed chapter rhythm template | Freeform chapter prose | Freeform writing feels flexible, but it makes reader load, validation, and recovery behavior inconsistent. |
| Example-first prompt blocks | Abstract prompt-writing theory before action | Theory-first creates cognitive load before the reader has any progress to anchor to. |
| Local recovery after each checkpoint | One large troubleshooting appendix | Appendix-only recovery is cheaper to write, but it arrives too late for in-flow correction. |

**Installation:**
```bash
npm install -D markdownlint-cli2@0.22.0 prettier@3.8.1
```

**Version verification:** Verified from the npm registry on 2026-03-27.
- `markdownlint-cli2` `0.22.0` published 2026-03-22
- `prettier` `3.8.1` published 2026-01-21

## Architecture Patterns

### Recommended Project Structure
```text
.
├── chapters/                         # Canonical tutorial chapter Markdown
├── book/manifest.json                # Chapter order and chapter-level metadata
├── scripts/
│   ├── validate-tutorial-structure.mjs
│   └── validate-tutorial-references.mjs
└── tests/
    └── phase-04/
```

### Pattern 1: Fixed Collaboration Rhythm Per Chapter
**What:** Every practical chapter uses the same teaching loop so the reader learns the collaboration motion by repetition.
**When to use:** In every chapter that advances the FV calculator or repo state.
**Example:**
~~~markdown
## 本章會完成什麼
- 讓畫面出現第一個可見的 FV 計算機骨架

## 這章會練到的協作能力
- 把需求說成 Agent 可執行的最小任務

## Step 1: 先請 Agent 規劃最小變更
### Prompt
```text
請先不要一次做完整功能。
先幫我規劃這一章的最小可見成果：
- 只建立 HTML 結構
- 暫時不用 CSS 細節
- 說明你會改哪些檔案
```

### 預期結果
- Agent 先列出要改的檔案與最小範圍，而不是直接展開完整實作

### Command
```bash
git status --short
```

### Checkpoint
- 畫面：你知道這一章只追求「有骨架可見」
- Repo：工作樹仍是你預期的狀態
- 下一步：可以接受 Agent 開始修改

### 回正 Prompt
```text
先停在規劃，不要直接實作全部功能。
請只列出這一步的最小變更、會動到的檔案、以及完成後我應該看到什麼。
```
~~~

### Pattern 2: Prompt and Command Are Separate Content Types
**What:** `Prompt` is for the Agent conversation, `Command` is for the terminal. They should never be blended into one undifferentiated code block.
**When to use:** Always. This is a core readability and reproducibility requirement.
**Example:**
~~~markdown
### Prompt
```text
請根據目前 repo 狀態，只補上最小可用的輸入欄位與按鈕。
完成後告訴我你改了哪些檔案，以及我應該怎麼驗收。
```

### Command
```bash
git diff --stat
```
~~~

### Pattern 3: Result-First Checkpoints
**What:** Checkpoints verify the visible or behavioral result first, then repo evidence, then whether it is safe to continue.
**When to use:** After every step that changes files, behavior, or reader expectations.
**Example:**
~~~markdown
### Checkpoint
- 結果：頁面上已經看得到本金、利率、期數欄位與「計算」按鈕
- Repo 證據：`git diff --stat` 只顯示本章預期的檔案
- 可繼續條件：你知道下一章會接著補計算邏輯，而不是重做結構
~~~

### Pattern 4: Recovery Is Attached to the Failing Step
**What:** Put the recovery prompt immediately after the checkpoint for the same step.
**When to use:** After every checkpoint where prompt drift, over-building, or repo mismatch is plausible.
**Example:**
~~~markdown
### 回正 Prompt
```text
你剛剛做超過這一章的範圍了。
請回到目前已完成的畫面結果，只保留本章需要的最小變更。
列出應該撤回的檔案或段落，再重新給我最小版本。
```
~~~

### Pattern 5: Chapter-Level Metadata, Step-Level Body Content
**What:** Keep chapter outcomes, summary checkpoints, and milestone flags in manifest metadata; keep detailed prompts and commands in the chapter body.
**When to use:** Always. This preserves the Phase 1/2 single-source metadata rule.
**Example:**
```json
{
  "id": "agent-rhythm-intro",
  "title": "先讓 Agent 跟你對齊最小任務",
  "summary": "用最小 prompt 帶出規劃、實作與驗收節奏。",
  "checkpoint": "你已能分辨何時該先請 Agent 規劃，何時才進入實作。",
  "milestone": null,
  "tutorialCapability": "把任務縮成 Agent 可執行的最小步驟"
}
```

### Anti-Patterns to Avoid
- **Theory-first chapter openings:** Do not start chapters with long tool explanations before the reader sees the chapter outcome.
- **Mixed prompt/command blocks:** Do not force the reader to infer whether a block goes to the Agent or the terminal.
- **Checkpoint only at chapter end:** Do not let multiple irreversible steps accumulate before the first verification point.
- **Recovery in a separate appendix only:** Do not make readers search elsewhere during failure.
- **Chapter-local metadata drift:** Do not duplicate outcome or checkpoint summary in front matter when manifest already owns chapter-level control data.

### Planning Implications
- **Plan 1 should define the tutorial contract:** lock the chapter skeleton, block labels, manifest metadata additions, and the rules for step ordering.
- **Plan 2 should write the actual Phase 4 content:** populate each tutorial chapter with copy-ready prompt/command/checkpoint/recovery blocks following the contract.
- **Plan 3 should add drift checks:** validate every practical chapter contains the required blocks, uses the labels consistently, and references repo paths or commands that actually exist in the project.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Per-chapter teaching rhythm | Different prose style in every chapter | One reusable tutorial block contract | Readers learn the collaboration motion by repetition; inconsistency increases cognitive load. |
| Prompt adaptation guidance | Open-ended "rewrite this however you want" advice | Concrete example first, short adaptation note second | Novices need a working base prompt before customization. |
| Recovery flow | Vague prose like "fix it and continue" | Explicit rollback-to-checkpoint guidance plus rescue prompt | Conservative recovery is more reproducible than improvisation. |
| Drift detection | Manual editorial review only | Content-structure tests and reference checks | Structural drift is predictable and cheap to catch automatically. |
| Repo-state verification | Custom freeform checklists per chapter | Shared result-first checkpoint template | Shared ordering makes the tutorial easier to scan and test. |

**Key insight:** The expensive failure mode in this phase is not bad prose. It is losing the one-to-one mapping between chapter steps, repo state, and reader-observable results.

## Common Pitfalls

### Pitfall 1: The tutorial teaches tools before it teaches progress
**What goes wrong:** Readers meet terminology and workflow concepts before they have any success signal, so the tutorial feels abstract and heavy.
**Why it happens:** Writers naturally want to explain the system before letting the reader act.
**How to avoid:** Open with the chapter outcome and the smallest task, then explain only the minimum framing needed for that task.
**Warning signs:** The first screen of a chapter has no concrete output, file change, or visible result.

### Pitfall 2: Readers cannot tell what goes to the Agent versus the terminal
**What goes wrong:** Copy-paste errors rise because the tutorial visually blurs prompts and commands together.
**Why it happens:** Both are often rendered as code fences without explicit type labels.
**How to avoid:** Treat `Prompt` and `Command` as separate block types with stable headings and ordering.
**Warning signs:** A chapter contains unlabeled fenced blocks or combines natural-language instructions and shell commands in one block.

### Pitfall 3: Checkpoints prove repo state but not user-visible progress
**What goes wrong:** Readers may have a clean diff but still not see the expected page behavior, so they keep going with a broken mental model.
**Why it happens:** Repository evidence is easier to author than explicit visual or behavioral outcomes.
**How to avoid:** Start every checkpoint with the visible or behavioral result, then add repo evidence, then state the safe-to-continue condition.
**Warning signs:** Checkpoints begin with `git status` and never mention what the reader should see.

### Pitfall 4: Recovery advice is detached from the point of failure
**What goes wrong:** When Agent output drifts or repo state diverges, the reader has to hunt for generic troubleshooting advice and loses momentum.
**Why it happens:** Recovery is deferred to a later appendix to keep the main chapter clean.
**How to avoid:** Put one local rescue prompt immediately after each checkpoint where the likely failure mode is known.
**Warning signs:** The only recovery guidance lives outside the chapter or speaks in generic terms like "ask the Agent to fix it."

### Pitfall 5: The tutorial references a repo state that no longer exists
**What goes wrong:** Prompts mention files, paths, or outputs that drift from the repo and Phase 1/2/3 contracts, making the chapter feel non-reproducible.
**Why it happens:** Tutorial prose evolves separately from manifest data, build constraints, or actual sample files.
**How to avoid:** Validate file-path references, keep chapter-level metadata in manifest, and make Phase 4 tests fail on missing required blocks or stale repo references.
**Warning signs:** The chapter says "you should see file X" but file X does not exist anywhere in the repo or planned example app.

### Pitfall 6: Overlong prompts hide the actual reader decision
**What goes wrong:** The prompt becomes a mini-spec that is hard to scan, hard to adapt, and easy to misuse.
**Why it happens:** Writers try to make one prompt handle planning, implementation, explanation, and edge cases at once.
**How to avoid:** Break long prompts into purpose-specific blocks and keep each one attached to a single step.
**Warning signs:** A prompt block spans many paragraphs, mixes multiple chapter goals, or contains backup plans that belong in recovery instead.

## Code Examples

Verified patterns from official sources and project constraints:

### Chapter Opening Template
~~~markdown
## 本章會完成什麼
- [一個可觀察的成果]

## 這章會練到的協作能力
- [一個明確的 Agent 協作能力]

## 開始之前
- 你目前應該已完成：[上一章 checkpoint 摘要]
~~~

### Step Template With Prompt, Command, Result, Checkpoint, Recovery
~~~markdown
## Step [N]: [單一步驟標題]

### Prompt
```text
[這一步可直接複製給 Agent 的最短 prompt]
```

### 預期結果
- [這一步完成後，Agent 應該先做或先說什麼]

### Command
```bash
[這一步需要的終端命令]
```

### Checkpoint
- 結果：[讀者應看到的畫面、行為或輸出]
- Repo 證據：[工作樹、檔案、路徑或差異]
- 可繼續條件：[下一步是否安全]

### 回正 Prompt
```text
[若 Agent 偏題、做太多或 repo 狀態不對時的最短救火 prompt]
```
~~~

### Segmented Long Prompt Pattern
~~~markdown
### Prompt
```text
先只做這一步，不要一次完成整章。
目標：
- [目標 1]
- [目標 2]

限制：
- 只改 [檔案或範圍]
- 不要先做 [下一章內容]

完成後請回覆：
- 改了哪些檔案
- 我應該怎麼驗收
```
~~~

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Tool overview before first action | Task-first tutorial with an immediate smallest outcome | Established modern docs guidance for procedures and tutorials | Phase 4 should start chapters with outcomes and steps, not concept lectures. |
| One monolithic prompt per chapter | Short, role- and goal-constrained prompts tied to one step | Strengthened by current LLM writing guidance | Readers can copy safely and diagnose drift faster. |
| Chapter-end validation only | In-flow checkpoints after each meaningful action | Standard task-doc practice | Readers detect divergence before it compounds. |
| Generic troubleshooting appendix | Local recovery prompts attached to the failing step | Current task-doc and tutorial support expectations | Recovery becomes actionable in the moment instead of after abandonment. |
| Freeform sample snippets | Explicit setup, expected results, and tested minimal samples | Current sample-code guidance | Tutorial blocks become easier to reproduce and maintain. |

**Deprecated/outdated:**
- Long concept-first chapters that delay the first visible result.
- Unlabeled fenced blocks that force readers to infer prompt versus command.
- Recovery advice that depends on readers improvising with the Agent.

## Open Questions

1. **Which chapter-level metadata should Phase 4 add to the manifest?**
   - What we know: outcome summaries and checkpoint summaries belong at chapter level, not step level.
   - What's unclear: whether a dedicated `tutorialCapability` field is worth formalizing now.
   - Recommendation: add only the minimum metadata that downstream generators or validators need; keep detailed step prose in chapter bodies.

2. **How much repo-state specificity belongs in checkpoints before the FV example app exists in Phase 5?**
   - What we know: `TUTO-05` requires repo evidence, but Phase 4 does not yet deliver the full FV implementation.
   - What's unclear: whether Phase 4 checkpoints should reference placeholder files, planned file names, or only current tutorial artifacts.
   - Recommendation: plan checkpoints around the smallest current artifact that truly exists in the repo at each step; avoid speculative file references.

3. **Should repo snapshots or tags be included now?**
   - What we know: progress snapshots are useful, but they are listed as `SUPP-03` in v2 rather than Phase 4 requirements.
   - What's unclear: whether lightweight snapshot references are needed to make recovery credible.
   - Recommendation: keep snapshots out of scope for Phase 4 plans unless validation shows checkpoints alone are insufficient.

## Validation Architecture

### Test Framework
| Property | Value |
|----------|-------|
| Framework | `node:test` plus Markdown structure checks |
| Config file | none - see Wave 0 |
| Quick run command | `node --test tests/phase-04/*.test.mjs` |
| Full suite command | `node --test && npx markdownlint-cli2 "**/*.md"` |

### Phase Requirements → Test Map
| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| TUTO-01 | Each practical chapter declares a concrete outcome, a collaboration capability, and uses the fixed collaboration rhythm in order | integration | `node --test tests/phase-04/tutorial-rhythm.test.mjs` | ❌ Wave 0 |
| TUTO-02 | Every actionable step contains copy-ready `Prompt` or `Command` blocks plus an expected-result note that supports step-by-step progress | integration | `node --test tests/phase-04/copy-ready-blocks.test.mjs` | ❌ Wave 0 |
| TUTO-05 | Every practical chapter contains result-first checkpoints and adjacent recovery prompts | integration | `node --test tests/phase-04/checkpoints-and-recovery.test.mjs` | ❌ Wave 0 |

### Sampling Rate
- **Per task commit:** `node --test tests/phase-04/*.test.mjs`
- **Per wave merge:** `node --test && npx markdownlint-cli2 "**/*.md"`
- **Phase gate:** Full suite green before `/gsd:verify-work`

### Wave 0 Gaps
- [ ] `package.json` — define `test` and Markdown validation scripts
- [ ] `tests/phase-04/tutorial-rhythm.test.mjs` — covers `TUTO-01`
- [ ] `tests/phase-04/copy-ready-blocks.test.mjs` — covers `TUTO-02`
- [ ] `tests/phase-04/checkpoints-and-recovery.test.mjs` — covers `TUTO-05`
- [ ] `scripts/validate-tutorial-structure.mjs` — parses chapter Markdown for required block order
- [ ] `scripts/validate-tutorial-references.mjs` — checks file-path and command references against repo reality

## Sources

### Primary (HIGH confidence)
- Project-local canonical refs:
  - `.planning/phases/04-agent/04-CONTEXT.md` - locked tutorial-shape decisions and out-of-scope constraints
  - `.planning/REQUIREMENTS.md` - `TUTO-01`, `TUTO-02`, `TUTO-05`
  - `.planning/ROADMAP.md` - Phase 4 goal and success criteria
  - `.planning/PROJECT.md` - docs-first product positioning and FV calculator teaching goal
  - `.planning/research/SUMMARY.md` - docs-first architecture and fresh-clone walkthrough importance
  - `.planning/research/FEATURES.md` - copy-ready prompts, task-driven path, and checkpoint expectations
  - `.planning/research/PITFALLS.md` - drift and non-reproducibility risks
- Google developer documentation style guide: https://developers.google.com/style/procedures
  - Checked numbered procedures, one best path, context-before-action ordering, step goals, results, optional-step labeling, and minimizing interruptions.
- Google Technical Writing Two, Creating sample code: https://developers.google.com/tech-writing/two/sample-code
  - Checked that sample code should build, perform the claimed task, explain how to run, state expected outputs, and avoid snippet rot.
- Google Technical Writing Two, Using large language models (LLMs) in technical writing: https://developers.google.com/tech-writing/two/llms
  - Checked prompt guidance for role, audience, document type, goal, constraints, context, and iterative refinement.
- GitHub Docs, Basic writing and formatting syntax: https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax
  - Verified the documented GFM features Phase 4 can safely rely on for readable tutorial blocks.

### Secondary (MEDIUM confidence)
- GitHub Docs, About READMEs: https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes
  - Used to reinforce that the repo itself remains a first-class reading surface, so tutorial blocks must stay readable there.
- npm registry checks performed on 2026-03-27
  - `markdownlint-cli2` `0.22.0`
  - `prettier` `3.8.1`

### Tertiary (LOW confidence)
- None.

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - This phase mostly extends already locked Markdown and manifest patterns, and the only package versions used here were verified from the registry.
- Architecture: HIGH - The fixed tutorial rhythm, result-first checkpoints, and attached recovery prompts are directly supported by project constraints and current official procedure guidance.
- Pitfalls: HIGH - The main failure modes are visible from the project's own research and are reinforced by official guidance on procedures, sample code, and prompt constraints.

**Research date:** 2026-03-27
**Valid until:** 2026-04-26
