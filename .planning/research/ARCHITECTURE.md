# Architecture Research

**Domain:** Docs-first Markdown ebook repository for Agent teaching content
**Researched:** 2026-03-27
**Confidence:** MEDIUM

## Standard Architecture

### System Overview

The recommended architecture is a **single-source content pipeline**:

- Human authors edit only canonical chapter files in GitHub Flavored Markdown.
- A manifest defines chapter order, metadata, and which outputs include each chapter.
- Build scripts render three publish channels from the same source set: `README.md`, EPUB, and GitHub Pages artifacts.
- Example apps such as the FV calculator live beside the book, not inside chapter folders, and are copied into the Pages artifact at publish time.

```
┌──────────────────────────────────────────────────────────────┐
│                         Source Layer                         │
├──────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────┐  │
│  │ content/     │  │ assets/      │  │ examples/          │  │
│  │ chapters     │  │ images, css  │  │ fv-calculator      │  │
│  └──────┬───────┘  └──────┬───────┘  └─────────┬──────────┘  │
├─────────┴─────────────────┴────────────────────┴─────────────┤
│                    Build Orchestration Layer                 │
├──────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────┐  │
│  │ content      │  │ pandoc/      │  │ scripts/           │  │
│  │ manifest     │  │ defaults,    │  │ validate + build   │  │
│  │ book.yaml    │  │ templates    │  │                    │  │
│  └──────┬───────┘  └──────┬───────┘  └─────────┬──────────┘  │
├─────────┴─────────────────┴────────────────────┴─────────────┤
│                        Publishing Layer                      │
├──────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────┐  │
│  │ README.md    │  │ dist/epub/   │  │ dist/site/         │  │
│  │ GitHub repo  │  │ agent.epub   │  │ Pages artifact     │  │
│  └──────────────┘  └──────────────┘  └────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

### Component Responsibilities

| Component | Responsibility | Typical Implementation |
|-----------|----------------|------------------------|
| `content/` | Canonical prose, chapter titles, chapter-local notes, image placeholder prompts | GitHub Flavored Markdown files grouped into front matter, chapters, and back matter |
| `content/book.yaml` | Canonical ordering, book metadata, per-output inclusion rules | YAML or JSON manifest consumed by build scripts |
| `assets/` | Shared images, cover art, EPUB CSS, site CSS | Stable file paths referenced relatively from chapters and render configs |
| `examples/` | Runnable companion projects linked from the book | Separate app folders with their own build, then copied into the Pages artifact |
| `pandoc/` | Output-specific adapters so source files stay portable | Pandoc defaults files, templates, filters, metadata files |
| `scripts/` | Validation, composition, rendering, release packaging | PowerShell or Node wrappers that call Pandoc and copy assets |
| `dist/` | Ephemeral outputs only | Generated README staging, EPUB bundle, Pages-ready static files |
| `.github/workflows/` | CI verification and deployment | Build on push/tag, upload Pages artifact, optionally attach EPUB to releases |

## Recommended Project Structure

```text
.
├── README.md                     # Generated GitHub entrypoint; committed
├── content/
│   ├── book.yaml                 # Canonical order and output rules
│   ├── frontmatter/
│   │   ├── preface.md            # Intro shown in README and EPUB
│   │   └── how-to-read.md        # Reader onboarding
│   ├── chapters/
│   │   ├── 01-introduction.md
│   │   ├── 02-agent-basics.md
│   │   ├── 03-gsd-workflow.md
│   │   └── 04-fv-calculator.md
│   └── backmatter/
│       ├── glossary.md
│       └── references.md
├── assets/
│   ├── images/                   # Shared figures and screenshots
│   ├── covers/                   # EPUB cover assets
│   └── styles/
│       ├── epub.css
│       └── site.css
├── examples/
│   └── fv-calculator/
│       ├── src/
│       └── package.json
├── pandoc/
│   ├── defaults/
│   │   ├── readme.yaml
│   │   ├── epub.yaml
│   │   └── site.yaml
│   ├── metadata/
│   │   └── book.yaml
│   ├── templates/
│   └── filters/
├── scripts/
│   ├── validate-content.ps1
│   ├── build-readme.ps1
│   ├── build-epub.ps1
│   ├── build-site.ps1
│   └── publish.ps1
├── dist/
│   ├── epub/
│   ├── site/
│   └── manifests/
└── .github/
    └── workflows/
        ├── verify.yml
        └── publish.yml
```

### Structure Rationale

- **`README.md`:** Keep this as a generated artifact because GitHub automatically surfaces a README from `.github`, root, or `docs`, and the root README is the cleanest repository entrypoint for readers.
- **`content/`:** This is the only human-authored narrative source. It should remain renderer-neutral and close to GitHub Flavored Markdown.
- **`content/book.yaml`:** A manifest is mandatory once one source tree feeds multiple outputs. Without it, README chapter order, EPUB spine order, and site navigation drift.
- **`assets/`:** Shared binaries must have stable paths across GitHub rendering, EPUB packaging, and Pages deployment.
- **`examples/`:** Tutorial applications are first-class project outputs, but they are not prose. Keeping them separate prevents renderer logic from leaking into app source.
- **`pandoc/`:** Output differences belong in config, templates, and filters, not inside chapter text. This is the cleanest way to support `gfm`, `epub3`, and HTML from one source set.
- **`scripts/`:** A docs-first repo still needs an explicit build surface. Small wrapper scripts are easier to debug than long CI-only shell commands.
- **`dist/`:** Treat all rendered outputs as disposable. The only generated file worth committing is root `README.md` because it is part of the repository UX.

## Architectural Patterns

### Pattern 1: Manifest-Driven Single Source

**What:** One manifest controls chapter order, metadata, and output inclusion.
**When to use:** Always, if `README.md`, EPUB, and Pages must be derived from the same chapter set.
**Trade-offs:** Adds one more maintained file, but removes duplicated order definitions across build scripts.

**Example:**
```yaml
title: Agent 使用教學電子書
lang: zh-TW
chapters:
  - file: frontmatter/preface.md
    outputs: [readme, epub, site]
  - file: chapters/01-introduction.md
    outputs: [readme, epub, site]
  - file: chapters/04-fv-calculator.md
    outputs: [epub, site]
```

### Pattern 2: Output Adapters via Defaults Files

**What:** Each publish channel gets its own render defaults file while sharing the same source tree.
**When to use:** When one output needs GitHub-safe Markdown, another needs EPUB metadata and styling, and another needs HTML navigation.
**Trade-offs:** More config files, but far less conditional markup in chapters.

**Example:**
```yaml
# pandoc/defaults/epub.yaml
from: gfm
to: epub3
standalone: true
table-of-contents: true
css:
  - assets/styles/epub.css
metadata-files:
  - pandoc/metadata/book.yaml
resource-path:
  - .
  - assets
```

### Pattern 3: Generated Entrypoint, Never Source-in-README

**What:** The root README is generated from selected chapters and a template, then committed so GitHub visitors see the latest landing page immediately.
**When to use:** When the repository itself is a reading surface, not just a source archive.
**Trade-offs:** Requires a validation step to detect stale README output, but avoids maintaining two divergent introductions.

**Example:**
```powershell
./scripts/build-readme.ps1
git diff --exit-code README.md
```

### Pattern 4: Companion Apps as Separate Build Targets

**What:** The book and the example app share release cadence, but not source directories or build pipelines.
**When to use:** For tutorial repos where readers build a real artifact such as the FV calculator.
**Trade-offs:** Slightly more CI wiring, but much cleaner ownership boundaries.

**Example:**
```text
examples/fv-calculator/src
  -> app build
  -> dist/site/examples/fv-calculator/
  -> linked from chapter markdown and Pages navigation
```

## Data Flow

### Publishing Flow

```text
Author edits content/*.md + content/book.yaml + assets/*
    ↓
scripts/validate-content.ps1
    ↓
scripts/build-readme.ps1
    ↓
README.md
    ↓
GitHub repository landing experience

Author edits content/*.md + content/book.yaml + assets/*
    ↓
scripts/validate-content.ps1
    ↓
scripts/build-epub.ps1
    ↓
dist/epub/agent-ebook.epub
    ↓
GitHub release asset or manual download

Author edits content/*.md + content/book.yaml + assets/* + examples/fv-calculator/*
    ↓
scripts/validate-content.ps1
    ↓
scripts/build-site.ps1 + example app build
    ↓
dist/site/
    ↓
GitHub Actions upload-pages-artifact / deploy-pages
    ↓
GitHub Pages
```

### Key Data Flows

1. **Chapters to README:** The manifest selects only onboarding-friendly content for the root README. Build logic injects a short intro, TOC, and relative links to deeper chapter files so GitHub remains the primary browse surface.
2. **Chapters to EPUB:** The full chapter sequence, metadata, cover, and CSS are assembled into one standalone EPUB. Asset paths must resolve through a shared resource path so screenshots and covers package correctly.
3. **Chapters to GitHub Pages:** The same chapter set is rendered to HTML pages or chunked HTML, then merged with copied static assets and built example-app files into one deployable Pages artifact.
4. **Examples to Book Navigation:** Chapter markdown links to example source and deployed example URLs, but the app build remains independent. The site build only consumes the app’s compiled output.

## Suggested Build Order

The implementation order should follow dependency depth, not perceived user visibility:

1. **Define the source tree and manifest first.**
   The rest of the pipeline depends on stable chapter paths, naming, and ordering.
2. **Define asset conventions second.**
   Image paths, cover files, and CSS locations must be stable before any renderer config is reliable.
3. **Build validation before publishing.**
   Link checks, heading checks, placeholder checks, and stale README detection prevent downstream churn.
4. **Implement README generation next.**
   It is the fastest visible proof that the single-source pipeline works and validates relative-link strategy on GitHub.
5. **Implement EPUB packaging after README is stable.**
   EPUB depends on manifest quality, metadata completeness, cover assets, and resource-path correctness.
6. **Implement GitHub Pages output after EPUB.**
   Pages adds navigation, artifact upload, and example-app integration. It should not be the first renderer because it has the most moving parts.
7. **Add CI publish and release packaging last.**
   Deployment should automate a working local build, not define the architecture.

### Build Dependencies

```text
content tree
  -> manifest
  -> asset conventions
  -> validation
  -> README build
  -> EPUB build
  -> site build
  -> Pages deploy / release packaging
```

## Scaling Considerations

| Scale | Architecture Adjustments |
|-------|--------------------------|
| 1 author, 10 chapters | One manifest, one asset tree, one build pipeline is enough |
| 2-5 contributors, 10-40 chapters | Add chapter ownership, stricter validation, and review rules for generated README drift |
| Multiple milestones, 40+ chapters, recurring releases | Split front matter/back matter more aggressively, add reusable snippets/partials, and publish tagged EPUB releases |

### Scaling Priorities

1. **First bottleneck:** Output drift between source chapters and `README.md`. Fix it with a mandatory local build command and CI verification.
2. **Second bottleneck:** Asset-path breakage across GitHub, EPUB, and Pages. Fix it with stable shared paths and output-specific defaults files rather than chapter-local hacks.

## Anti-Patterns

### Anti-Pattern 1: Writing the Book in `README.md`

**What people do:** Treat the root README as the primary manuscript, then copy sections into chapter files later.
**Why it's wrong:** GitHub entrypoint concerns and book-authoring concerns diverge almost immediately. The README becomes a forked manuscript.
**Do this instead:** Keep prose in `content/` and generate `README.md` from a curated subset of canonical chapters.

### Anti-Pattern 2: Mixing Example App Source into Chapter Folders

**What people do:** Place the FV calculator source under the same tree as narrative chapters because the chapter talks about the app.
**Why it's wrong:** Static site output, asset copying, and prose linting all become harder. Renderer assumptions leak into app structure.
**Do this instead:** Keep `examples/` as a sibling boundary and copy only built assets into the Pages artifact.

### Anti-Pattern 3: Encoding Output Differences Inside Chapter Markdown

**What people do:** Add renderer-specific raw HTML, manual anchor hacks, or duplicated image blocks directly in the source chapters.
**Why it's wrong:** Source portability collapses, GitHub rendering degrades, and every new output channel increases cleanup cost.
**Do this instead:** Move output differences into Pandoc defaults files, templates, filters, and metadata.

## Integration Points

### External Services

| Service | Integration Pattern | Notes |
|---------|---------------------|-------|
| GitHub Repository | Commit canonical source plus generated root README | GitHub surfaces the README and supports relative links between Markdown files |
| GitHub Actions | Run validation, build artifacts, deploy Pages | Use a custom workflow so build and deploy are explicit and reproducible |
| GitHub Pages | Publish `dist/site/` as artifact output | Prefer Actions artifact deployment over committing built site output |
| GitHub Releases | Attach EPUB on version tags | Optional, but the cleanest distribution channel for offline readers |

### Internal Boundaries

| Boundary | Communication | Notes |
|----------|---------------|-------|
| `content/` ↔ `pandoc/` | Manifest + defaults files | Renderer rules should not leak into chapter source except for semantic metadata |
| `content/` ↔ `examples/` | Relative links and published URLs only | No shared source folders |
| `scripts/` ↔ `dist/` | Write-only artifact generation | Never hand-edit files under `dist/` |
| `README.md` ↔ `content/` | One-way generation | The README is a publish artifact, not an authoring file |

## Sources

- Pandoc User’s Guide: output formats including `gfm`, `epub3`, and `chunkedhtml`, plus defaults files and resource paths: https://pandoc.org/MANUAL.html
- GitHub Docs, About READMEs: README surfacing rules and relative links in Markdown files: https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes
- GitHub Docs, Using custom workflows with GitHub Pages: build/deploy artifact flow with `configure-pages`, `upload-pages-artifact`, and `deploy-pages`: https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages
- Quarto Docs, Creating a Book and GitHub (GFM): useful comparison point for book-first HTML/EPUB pipelines, but less aligned with pure GFM-as-canonical-source requirements: https://quarto.org/docs/books/ and https://quarto.org/docs/output-formats/gfm.html
- mdBook Docs, Creating a book: useful comparison point for chapter-first HTML books with `book.toml` and `src/SUMMARY.md`, but not the best fit when EPUB and root README generation are first-class outputs: https://rust-lang.github.io/mdBook/guide/creating.html

---
*Architecture research for: Agent 使用電子書專案*
*Researched: 2026-03-27*
