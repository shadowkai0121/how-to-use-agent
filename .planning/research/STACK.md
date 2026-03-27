# Stack Research

**Domain:** Docs-first Markdown ebook project for Agent 教學內容
**Researched:** 2026-03-27
**Confidence:** HIGH

## Recommended Stack

### Core Technologies

| Technology | Version | Purpose | Why Recommended | Confidence |
|------------|---------|---------|-----------------|------------|
| GitHub Flavored Markdown | Current GitHub GFM | Single source of truth for all readable content | This project must be readable directly on GitHub, so the source format has to match GitHub’s renderer, not a site generator’s private dialect. GitHub officially supports GFM, relative links, section anchors, math, alerts, and footnotes. | HIGH |
| Node.js | 24 LTS | Automation runtime for linting, packaging, and CI scripts | In March 2026, Node 24 is Active LTS and the correct default for production tooling. It gives you a stable base for formatting, linting, and release automation without tying the book itself to a web framework. | HIGH |
| pnpm | 10.x | Package manager for the repo tooling layer | pnpm is the standard 2026 choice for new JS tooling repos because it is fast, workspace-friendly, and has stronger supply-chain hygiene features than npm’s default workflow. Use it even for a small docs repo so CI and local installs stay deterministic. | MEDIUM |
| Pandoc | 3.9.x | Canonical Markdown-to-EPUB build engine | Pandoc is still the most practical universal converter for Markdown books. It natively supports `gfm` input and `epub` output, accepts multiple chapter files in order, supports CSS, embedded fonts, metadata, and Lua filters when the EPUB needs small post-processing. | HIGH |
| EPUBCheck | 5.3.x | EPUB validation gate before release | EPUB generation is not done when the file opens. EPUBCheck is the W3C/Daisy conformance checker and should be the release gate for every generated `.epub`. This prevents shipping files that work in one reader but fail store or reader validation later. | HIGH |
| Java Runtime | 21 LTS | Runtime for EPUBCheck in CI and local builds | EPUBCheck is distributed as a Java tool. Use a current LTS JRE in CI instead of depending on whatever system Java happens to exist on the machine. | MEDIUM |

### Supporting Libraries

| Library | Version | Purpose | When to Use | Confidence |
|---------|---------|---------|-------------|------------|
| Prettier | 3.7.x | Canonical formatting for Markdown, YAML, JSON, and workflow files | Use on every commit. It removes editorial noise from diffs and keeps tables, lists, code fences, and config files consistently formatted. | HIGH |
| markdownlint-cli2 | 0.18.x | Markdown structure linting tuned for GFM authoring | Use to catch heading jumps, duplicate headings, malformed lists, missing blank lines, and other issues Prettier will not flag. Prefer `markdownlint-cli2` over the older CLI because it is now the actively favored config-first interface. | MEDIUM |
| Vale | 3.14.x | Prose linting for zh-TW editorial consistency and terminology | Use once the first few chapters exist. It is the right layer for enforcing book voice, banned terms, glossary consistency, and “human must add image here” wording conventions. | HIGH |
| lychee | 0.23.x | Broken-link checking for Markdown and generated artifacts | Use in CI for relative links, external links, and release docs. It is faster and more CI-friendly than older Node-only link checkers. | MEDIUM |
| YAML parser (`yaml`) | 2.x | Read book metadata and build manifest in Node scripts | Use if you keep `book.yml` for title, language, cover, and chapter order. Keep metadata outside chapter bodies so GitHub rendering stays clean. | LOW |

### Development Tools

| Tool | Purpose | Notes | Confidence |
|------|---------|-------|------------|
| GitHub Actions | Lint, build EPUB, validate EPUB, publish release artifacts | Use Actions as the default automation layer. GitHub now recommends Actions for Pages automation, and the same CI stack cleanly handles EPUB packaging too. | HIGH |
| `actions/checkout@v5` | Checkout repo in CI | Standard baseline action. | MEDIUM |
| `actions/configure-pages@v5` | Optional future GitHub Pages setup | Only needed if you later add a generated companion site. Not required for GitHub-native reading. | HIGH |
| `actions/upload-pages-artifact@v4` and `actions/deploy-pages@v4` | Optional future Pages deploy | Keep these in reserve for a later HTML site. Do not make Pages the primary content source. | HIGH |
| GitHub Releases | Ship the generated `.epub` | The clean distribution model is: repository for reading, release asset for downloading. Do not commit built EPUB files into the main branch history. | MEDIUM |

## Installation

```bash
# Core repo tooling
pnpm add -D prettier@^3.7 markdownlint-cli2@^0.18 vale@^3.14 yaml@^2

# Build prerequisites installed outside package.json
# 1. Install Pandoc 3.9.x
# 2. Install Java 21 LTS
# 3. Install EPUBCheck 5.3.x
# 4. Install lychee 0.23.x via its binary package or GitHub Action
```

## Recommended Repository Layout

```text
.
├── README.md
├── book.yml
├── chapters/
│   ├── 00-preface.md
│   ├── 01-start-here.md
│   ├── 02-agent-workflow.md
│   ├── 03-antigravity-gsd.md
│   └── 04-fv-calculator.md
├── assets/
│   ├── cover.png
│   └── images/
├── scripts/
│   ├── build-epub.mjs
│   └── check-links.mjs
├── dist/
│   └── agent-book.epub
└── .github/
    └── workflows/
        └── book.yml
```

## README and Chapter Organization

### README.md

Use `README.md` as the GitHub entry page, not as the whole book.

Why:
- GitHub surfaces the root README automatically.
- GitHub truncates README rendering beyond 500 KiB.
- A short landing page is easier to scan and keeps repository navigation usable.

Structure it like this:
1. Book promise and target reader
2. What the reader will build
3. Reading order with relative links to chapter files
4. Link to latest EPUB release
5. Contribution/editorial notes

### Chapters

Use one chapter per file in `chapters/` with numeric prefixes.

Why:
- Numeric prefixes give deterministic reading and build order.
- Relative links work on GitHub without special tooling.
- Pandoc can consume files in explicit order without needing source rewrites.

Recommended rules:
- Keep chapter files pure GFM `.md`
- No per-file YAML front matter unless absolutely necessary
- Put book-level metadata in `book.yml`
- Put reusable assets in `assets/images/`
- Insert image placeholders as explicit blockquotes or callouts, not TODO comments hidden from readers
- Keep filenames ASCII and stable, even though chapter content is zh-TW

Recommended `book.yml` fields:
- `title`
- `subtitle`
- `author`
- `lang: zh-Hant-TW`
- `rights`
- `cover-image`
- `identifier`
- `chapters` array in final reading order

## EPUB Generation Recommendation

Use Pandoc as the only generator and make EPUB a derived artifact:

```bash
pandoc \
  --from=gfm \
  --to=epub3 \
  --standalone \
  --metadata-file=book.yml \
  --css=assets/epub.css \
  --epub-cover-image=assets/cover.png \
  --output=dist/agent-book.epub \
  chapters/00-preface.md \
  chapters/01-start-here.md \
  chapters/02-agent-workflow.md \
  chapters/03-antigravity-gsd.md \
  chapters/04-fv-calculator.md
```

Then validate:

```bash
epubcheck dist/agent-book.epub
```

Why this is the right default:
- GFM stays canonical at authoring time.
- EPUB stays reproducible in CI.
- Metadata, cover, CSS, and chapter order are explicit.
- You avoid maintaining separate GitHub docs and ebook source trees.

## Alternatives Considered

| Recommended | Alternative | When to Use Alternative | Confidence |
|-------------|-------------|-------------------------|------------|
| Pandoc | Quarto | Use Quarto only if executable notebooks, code-driven rendering, or a future book website with rich multi-format publishing becomes a core requirement. For this project, Quarto’s `.qmd` layer and Pandoc-style canonicalization add unnecessary distance from raw GitHub-readable Markdown. | HIGH |
| Pandoc | mdBook | Use mdBook only if the primary product is an HTML book site and GitHub raw Markdown is secondary. mdBook is excellent for web books, but its native model is `SUMMARY.md` plus HTML rendering, not README-first GitHub reading plus EPUB release artifacts. | HIGH |
| Raw GFM chapters | MkDocs / Docusaurus / VitePress as source of truth | Use these only when the website is the main product. They are site generators, not ebook-first pipelines, and they push you toward HTML-site conventions instead of clean repository reading. | MEDIUM |
| pnpm | npm | Use npm only if you insist on zero extra package-manager onboarding for contributors. For a 2026 greenfield repo, pnpm is the better default. | MEDIUM |

## What NOT to Use

| Avoid | Why | Use Instead | Confidence |
|-------|-----|-------------|------------|
| Quarto `.qmd` as the canonical source | Quarto is strong, but it rewrites toward Pandoc-flavored markdown conventions and is best when computation or multi-format publishing is the core job. Here, GitHub-readable raw Markdown is the core job. | Raw GFM `.md` chapters + Pandoc build step | HIGH |
| mdBook as the canonical source | mdBook is HTML-book-first and depends on its own `SUMMARY.md` book model. EPUB requires extra backend choices and GitHub README becomes an afterthought. | Raw GFM `.md` chapters + Pandoc | HIGH |
| `markdown_github` in Pandoc | Pandoc documents `markdown_github` as deprecated and less accurate than `gfm`. | `--from=gfm` | HIGH |
| Jekyll front matter in every chapter | This couples content to GitHub Pages/Jekyll behavior and makes raw repository reading noisier. The project is GitHub-readable first, not Jekyll-first. | Central `book.yml` metadata file | MEDIUM |
| Checking built `.epub` into `main` | Binary artifacts pollute diffs, bloat history, and create merge pain. | Build in CI and attach to GitHub Releases | HIGH |
| GitHub wiki as the main book surface | GitHub docs explicitly frame README as the repository entry point and longer docs as separate surfaces. Wikis also complicate single-repo, single-source workflows. | Root `README.md` + chapter files in repo | MEDIUM |

## Stack Patterns by Variant

**If the project stays “GitHub repo + EPUB only”:**
- Use raw GFM chapters, Pandoc, EPUBCheck, and GitHub Releases.
- Because this is the lowest-complexity stack that fully satisfies the stated constraints.

**If the project later adds a companion docs site:**
- Keep the raw GFM chapters as source, add a separate HTML build target in CI, and publish that site with GitHub Pages Actions.
- Because the book source should remain stable while distribution channels multiply.

**If executable examples become core product value:**
- Re-evaluate Quarto for generated examples, but only as a secondary rendering pipeline.
- Because code execution is Quarto’s strength, not this project’s current bottleneck.

## Version Compatibility

| Package A | Compatible With | Notes |
|-----------|-----------------|-------|
| Node.js 24 LTS | pnpm 10.x, Prettier 3.7.x, markdownlint-cli2 0.18.x | Safe default for 2026 CI and local tooling |
| Pandoc 3.9.x | GFM input, EPUB3 output | Use `gfm`, not deprecated `markdown_github` |
| EPUBCheck 5.3.x | Java 7+ officially, Java 21 LTS recommended operationally | Standardize on modern LTS Java in CI even though EPUBCheck supports older runtimes |
| GitHub repository Markdown | Relative links, section anchors, math, alerts, footnotes | Keep authoring within documented GitHub features to avoid dual-render drift |

## Recommendation Summary

Use this stack:
- Raw GFM `.md` chapters as the canonical source
- `README.md` as a short landing page and table of contents
- `book.yml` for metadata and chapter order
- Pandoc 3.9.x to build `epub3`
- EPUBCheck 5.3.x as a mandatory validation step
- Node 24 LTS + pnpm 10 for automation
- Prettier + markdownlint-cli2 + Vale in CI

Do not start with Quarto, mdBook, or MkDocs. They are all defensible tools, but they optimize for a different primary artifact than this project. For an Agent 教學電子書 that must read cleanly on GitHub and also ship as EPUB, the 2026 standard stack is still “plain GFM source + Pandoc build pipeline + EPUBCheck gate.”

## Sources

- https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes — verified README surfacing order, relative links, auto-generated outline, and 500 KiB truncation. HIGH
- https://github.github.com/gfm/ — verified GitHub Flavored Markdown as GitHub’s supported Markdown dialect. HIGH
- https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax — verified current GitHub Markdown features such as footnotes and alerts. HIGH
- https://docs.github.com/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages — verified GitHub’s recommended Pages workflow via Actions and current official action versions. HIGH
- https://nodejs.org/en/about/previous-releases — verified that Node 24 is Active LTS on 2026-03-27 and production apps should use LTS lines. HIGH
- https://nodejs.org/en/blog/announcements/evolving-the-nodejs-release-schedule — verified the 2026 release-schedule transition and why Node 24 remains the stable default before Node 26 LTS. HIGH
- https://pnpm.io/ — verified pnpm’s current positioning around speed, workspace support, and safer dependency management. MEDIUM
- https://github.com/pnpm/pnpm — verified current pnpm major line (10.x). MEDIUM
- https://pandoc.org/releases.html — verified current Pandoc release line (3.9.x). HIGH
- https://pandoc.org/epub.html — verified native EPUB generation, CSS support, embedded assets, and chapter-file workflow. HIGH
- https://pandoc.org/demo/example2.html — verified `gfm` support and that `markdown_github` is deprecated and less accurate. HIGH
- https://www.pandoc.org/demo/example33/3.4-options-affecting-specific-writers.html — verified EPUB metadata, CSS, title page, and font embedding capabilities. HIGH
- https://w3c.github.io/epubcheck/ — verified EPUBCheck as the W3C EPUB conformance checker. HIGH
- https://w3c.github.io/epubcheck/docs/installation/ — verified EPUBCheck 5.3.x installation model and Java requirement. HIGH
- https://github.com/vale-cli/vale/releases — verified current Vale release line (3.14.x). MEDIUM
- https://vale.sh/ — verified Vale’s markup-aware prose-linting role and integrations. HIGH
- https://github.com/prettier/prettier/releases — verified current Prettier release line (3.7.x). MEDIUM
- https://prettier.io/docs/ — verified Markdown/GFM formatting support. HIGH
- https://github.com/DavidAnson/markdownlint-cli2 — verified current CLI usage model and config behavior. MEDIUM
- https://lychee.cli.rs/ and https://github.com/lycheeverse/lychee — verified lychee’s role as a fast CI-friendly link checker. MEDIUM
- https://quarto.org/docs/output-formats/gfm.html — verified Quarto generates GFM as an output, which is why it is better as a derived pipeline than the canonical source here. HIGH
- https://quarto.org/docs/reference/formats/epub.html — verified Quarto EPUB support. HIGH
- https://rust-lang.github.io/mdBook/format/configuration/renderers.html — verified mdBook’s built-in renderers are HTML and Markdown, with other outputs coming from community backends. HIGH
- https://rust-lang.github.io/mdBook/format/summary.html — verified mdBook’s strict `SUMMARY.md`-driven book model. HIGH
- https://www.mkdocs.org/ — verified MkDocs is a static site generator for Markdown docs, reinforcing that it is site-first rather than README+EPUB-first. HIGH

---
*Stack research for: Agent 使用電子書專案*
*Researched: 2026-03-27*
