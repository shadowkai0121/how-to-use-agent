# Project Research Summary

**Project:** Agent 使用電子書專案
**Domain:** GitHub-first docs-first technical ebook with a runnable tutorial example
**Researched:** 2026-03-27
**Confidence:** MEDIUM

## Executive Summary

This project is a docs-first teaching product, not a docs portal and not a web app with docs attached. The core artifact should be a set of GitHub-readable GitHub Flavored Markdown chapters in Taiwan Traditional Chinese, with `README.md` acting as a short landing page and Pandoc generating the EPUB artifact. The research is consistent that experts solve this kind of problem by keeping prose in one canonical source tree, keeping output-specific behavior in build config, and treating the FV calculator as a companion example app rather than mixing app code into chapter folders.

The recommended approach is to lock down the content contract first: manifest-driven chapter order, a narrow GFM-safe syntax subset, stable asset paths, explicit image placeholder rules, and a generated `README.md` that stays short. Once that contract exists, add the automation layer around it: Node 24 LTS plus pnpm 10 for repo tooling, Pandoc 3.9.x for EPUB generation, EPUBCheck 5.3.x as a hard release gate, and GitHub Actions for validation and publishing. Only after those foundations are stable should the roadmap invest in the runnable tutorial path and GitHub Pages deployment flow.

The main risks are source drift and false confidence. Drift happens when the README, chapter files, EPUB output, and runnable FV calculator stop matching each other. False confidence happens when GitHub rendering is assumed to equal EPUB compatibility, or when code blocks look plausible but are no longer runnable. The mitigation is explicit: one canonical content tree, generated outputs, CI link and asset checks, traceable snippets from the real sample app, EPUBCheck, and a fresh-clone walkthrough before release.

## Key Findings

Detailed research: [STACK.md](./STACK.md), [FEATURES.md](./FEATURES.md), [ARCHITECTURE.md](./ARCHITECTURE.md), [PITFALLS.md](./PITFALLS.md)

### Recommended Stack

The stack should stay deliberately conservative: plain GFM as source, Pandoc as the only book builder, and GitHub plus GitHub Actions as the distribution surface. Quarto, mdBook, MkDocs, and similar tools only become better choices if the primary artifact becomes a generated site. For this project, the primary artifact is readable repository content plus a validated EPUB.

**Core technologies:**
- GitHub Flavored Markdown: canonical source format, because it matches the GitHub reading surface and minimizes renderer drift.
- Node.js 24 LTS: automation runtime, because it is the stable 2026 default for scripts, linting, and CI tasks.
- pnpm 10.x: package manager, because it keeps tooling installs deterministic and lightweight.
- Pandoc 3.9.x: EPUB build engine, because it is the strongest documented path from GFM chapters to reproducible EPUB3.
- EPUBCheck 5.3.x plus Java 21 LTS: release validation, because EPUB validity has to be a gate, not a best effort.
- Prettier 3.7.x, markdownlint-cli2 0.18.x, Vale 3.14.x, and lychee 0.23.x: content QA, because formatting, structure, prose consistency, and links all need automated checks.

Critical version requirements are straightforward: use `--from=gfm`, not deprecated `markdown_github`; standardize on Node 24 LTS, Pandoc 3.9.x, EPUBCheck 5.3.x, and Java 21 LTS in CI. Do not make Quarto or mdBook the canonical authoring layer.

### Expected Features

The MVP is a guided, outcome-driven learning path that starts on GitHub and ends with a deployed GitHub Pages FV calculator. Readers expect a root README that explains the promise, clear chapter order, stable cross-links, GFM-safe formatting, copy-pasteable prompts and commands, and a rebuildable EPUB with metadata, TOC, cover, and stable naming.

**Must have (table stakes):**
- Short `README.md` landing page with chapter index and a clear start path.
- Explicit chapter ordering and stable file naming managed from a single manifest.
- GFM-safe writing contract with reliable cross-links, code blocks, callouts, and asset conventions.
- Single-source dual output: GitHub-readable chapters plus generated EPUB.
- Outcome-driven tutorial path that gets the reader to a public GitHub Pages FV calculator.
- Per-chapter checkpoints and clear image placeholder rules.

**Should have (competitive):**
- Copy-ready Agent prompts or command scripts in each chapter.
- Failure-recovery branches for prompt drift, Pages 404s, formula bugs, and repo-structure mistakes.
- Repo state snapshots or tags so readers can compare their progress.
- Taiwan Traditional Chinese glossary and terminology guide.
- Publish verification loop so “done” means publicly working, not just locally written.

**Defer (v2+):**
- Full-text search, Ask AI, comments, or portal-like collaboration features.
- Documentation versioning.
- PDF or print-grade layout.
- Multi-language editions.
- Heavy interactive embeds inside book content.

### Architecture Approach

The architecture research converges on a manifest-driven single-source pipeline. Canonical prose lives in a dedicated content tree, shared assets live in stable paths, the FV calculator lives as a separate companion app, and output-specific behavior lives in Pandoc defaults or templates plus small build scripts. `README.md` should be generated and committed as the repository entrypoint; EPUB and any Pages artifacts should be disposable build outputs.

**Major components:**
1. Canonical content tree: human-authored chapters, front matter, and back matter in portable GFM.
2. Manifest and metadata: chapter order, output inclusion, title, language, cover, and other book-level rules.
3. Shared assets: images, cover art, and EPUB or site CSS with stable relative paths.
4. Companion example app: the executable tutorial truth, kept separate from prose.
5. Build adapters: validation, README generation, EPUB packaging, and optional Pages artifact assembly.
6. CI and publishing: lint, build, validate, deploy, and attach release artifacts.

### Critical Pitfalls

1. **Maintaining GitHub and EPUB as two content sources**: keep one canonical content tree, keep README as an entrypoint only, and ban manual edits to generated artifacts.
2. **Assuming GitHub-rendered Markdown will survive EPUB export unchanged**: define a strict portable syntax subset, blacklist GitHub-only features unless transformed, and validate dual-output behavior in CI.
3. **Letting chapter topology drift**: use one manifest for order, enforce heading and anchor rules, and run internal link checks early.
4. **Letting the tutorial drift away from the runnable FV calculator**: treat the companion app as the only executable truth and validate the full walkthrough from a fresh clone.
5. **Shipping an EPUB that merely opens, but is not publication-ready**: keep metadata and CSS explicit, run EPUBCheck on every release, and smoke-test multiple readers.
6. **Ignoring zh-TW or CJK editorial QA and placeholder hygiene**: standardize placeholder syntax, scan for leftovers before release, and validate CJK rendering in actual readers.

## Implications for Roadmap

Based on research, suggested phase structure:

### Phase 1: Content Contract And Repository Layout
**Rationale:** Every later step depends on stable source boundaries, predictable chapter order, and a syntax and asset contract. If this phase is loose, every renderer and chapter rewrite becomes rework.  
**Delivers:** Canonical content tree, manifest, chapter naming rules, README scope, GFM-safe syntax contract, asset and path conventions, placeholder syntax, and zh-TW metadata defaults.  
**Addresses:** README landing page, chapter order, stable navigation, GFM-safe authoring, and image placeholder rules.  
**Avoids:** Dual-source drift, GitHub-only syntax leakage, anchor or TOC decay, and README bloat.

### Phase 2: Build Pipeline And Dual-Output Validation
**Rationale:** The project should automate correctness before scaling content volume. This is the cheapest point to lock in reproducible outputs and catch portability failures.  
**Delivers:** Local build scripts, generated root README, EPUB build, content linting, link checking, asset validation, EPUBCheck gate, and GitHub Actions verification plus release packaging.  
**Uses:** Node 24 LTS, pnpm 10.x, Pandoc 3.9.x, EPUBCheck 5.3.x, Java 21 LTS, Prettier, markdownlint-cli2, Vale, and lychee.  
**Implements:** Manifest-driven rendering and output adapters.  
**Avoids:** Missing assets in EPUB, broken release artifacts, silent README drift, and false confidence from single-surface validation.

### Phase 3: Tutorial Source Of Truth And Runnable Example
**Rationale:** Only after the content and build pipeline are stable should the team bind the prose to the real deliverable readers will build.  
**Delivers:** `examples/fv-calculator/`, chapter-aligned prompts and commands, traceable code inclusion or snippet extraction, per-chapter checkpoints, fresh-clone walkthrough validation, and GitHub Pages deployment verification.  
**Addresses:** Outcome-driven learning path, copy-ready prompts and code, chapter checkpoints, and public deployment closure.  
**Implements:** Companion app boundary and Pages integration without turning the repo into a full docs portal.  
**Avoids:** Tutorial or app drift, code rot, and “works in prose but not in repo” failure.

### Phase 4: Editorial QA And Release Readiness
**Rationale:** This phase hardens reader trust and release quality after the main learning loop already works.  
**Delivers:** Troubleshooting appendix, glossary, progress snapshots or tags, placeholder and alt-text audit, zh-TW or CJK typography review, multi-reader EPUB smoke tests, and a release checklist.  
**Addresses:** Failure-recovery guidance, terminology consistency, and post-MVP reader support.  
**Avoids:** Placeholder leakage, poor CJK rendering, fragile release quality, and avoidable support load.

### Phase Ordering Rationale

- Phase 1 comes first because chapter order, syntax portability, and asset conventions are architectural dependencies, not editorial cleanup.
- Phase 2 comes before the runnable tutorial because generated README and EPUB outputs need to be trustworthy before prose volume and sample complexity increase.
- Phase 3 groups the sample app, snippets, checkpoints, and Pages deployment together because they form one user-facing “reader can finish the tutorial” outcome.
- Phase 4 is last because it hardens quality and support after the source model, build pipeline, and primary tutorial loop are already stable.

### Research Flags

Phases likely needing deeper research during planning:
- **Phase 3:** Only if the FV calculator uses a framework heavier than static HTML, CSS, and JavaScript, or if snippet extraction tooling needs nontrivial selection.
- **Phase 4:** zh-TW or CJK EPUB rendering, placeholder governance, and reader-device QA need hands-on validation rather than docs-only confidence.

Phases with standard patterns (skip research-phase):
- **Phase 1:** Manifest-driven content model, generated README scope, and GFM-safe authoring rules are well documented and already converged across the research.
- **Phase 2:** Pandoc plus EPUBCheck plus GitHub Actions is a standard, well-supported build path for this problem.
- **Phase 3:** If the FV calculator remains a simple static Pages app, the baseline architecture is standard and does not need more upfront research.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | Core recommendations are backed by official GitHub, Pandoc, EPUBCheck, and Node sources; uncertainty is mostly around optional tooling choices. |
| Features | HIGH | Strong match between research, competitor patterns, and the project goal of delivering a GitHub-first tutorial ebook with a deployable outcome. |
| Architecture | MEDIUM | The single-source pipeline pattern is solid, but the exact folder layout and generated README strategy are still design choices that should be validated against repo preferences. |
| Pitfalls | MEDIUM | Failure modes are credible and grounded in official tool behavior, but prioritization and phase mapping are still project-specific engineering judgment. |

**Overall confidence:** MEDIUM

### Gaps to Address

- Canonical folder layout: choose early whether the narrative root is `content/` or a simpler `chapters/` root, then keep the roadmap consistent.
- Pages scope: confirm whether GitHub Pages is only for the FV calculator or also for a lightweight companion HTML book.
- Snippet strategy: choose how chapter code stays synced with the runnable example before long technical chapters are written.
- Placeholder contract: define one machine-detectable image placeholder syntax and one release-time check.
- CJK QA baseline: pick the actual EPUB readers or devices used for smoke testing so zh-TW typography and line breaking are validated against real conditions.

## Sources

### Primary (HIGH confidence)
- GitHub Flavored Markdown spec: canonical Markdown behavior and portability boundaries.
- GitHub Docs on READMEs, Markdown syntax, and GitHub Pages custom workflows: repository entrypoint rules, supported formatting, and deployment patterns.
- Pandoc manual and EPUB documentation: `gfm` input, `epub3` output, defaults files, resource paths, metadata, and portability constraints.
- W3C EPUBCheck docs and official repository: EPUB conformance validation expectations.
- Node.js release documentation: Node 24 LTS status and release cadence.

### Secondary (MEDIUM confidence)
- pnpm docs and repository: current package manager baseline and major version.
- Prettier, markdownlint-cli2, Vale, and lychee docs: repo QA tooling choices.
- Quarto and mdBook docs: comparison points used to justify what not to make canonical.
- Microsoft Learn snippet guidance: rationale for keeping long code in real source files rather than hand-maintained Markdown blocks.

### Tertiary (LOW confidence)
- `yaml` package selection for manifest parsing: low-stakes implementation detail that should be validated during setup if a Node manifest reader is needed.

---
*Research completed: 2026-03-27*
*Ready for roadmap: yes*
