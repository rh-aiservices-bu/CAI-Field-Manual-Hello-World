---
title: Editing
description: Clone, render, and manage the Hello World field manual — tooling, local preview, file layout, and verification
weight: 50
icon: fa-solid fa-pen-to-square
aliases: [/hello-world/prerequisites/]
---

You edit a field manual the same way you run it — one command, one verify, one status gate. This chapter is the full loop: what to install, how to clone and preview the site locally, where things live, and how to prove the build is clean before you publish.

## 1. Tooling

The site builds with [Hugo Extended](https://gohugo.io/) and [Go](https://go.dev/) modules. No Node.js, no Python, no extra package manager.

| Tool | Minimum | Why |
|---|---|---|
| Hugo Extended | `≥ 0.168` | Renders the site and pulls the Shell + theme via `hugo.yaml` |
| Go | `≥ 1.27` | Resolves Hugo modules (`go.mod` / `go.sum`) |

### Verify

```sh
hugo version
go version
```

Expected — the literal version and arch suffix vary by OS and install method; check the **predicates**, not the full line:

```text
hugo v0.169.0+extended ...    # ← must contain "extended" and "v0.168" or newer
go version go1.27.3 ...       # ← must contain "go1.27" or newer
```

Quick predicate you can script:

```sh
hugo version | grep extended && echo "hugo: ok"
go version | grep -E 'go1\.(2[7-9]|[3-9][0-9])' && echo "go: ok"
```

Expected:

```text
hugo: ok
go: ok
```

> **Failure:** `command not found: hugo` → install Hugo Extended for your OS (e.g. `brew install hugo` on macOS — confirm with `hugo version | grep extended`; the non-extended build cannot compile SCSS). `go: command not found` → install from https://go.dev/dl/.

## 2. Clone

```sh
git clone https://github.com/rh-aiservices-bu/CAI-Field-Manual-Hello-World.git
cd CAI-Field-Manual-Hello-World
```

Expected:

```text
Cloning into 'CAI-Field-Manual-Hello-World'...
remote: Enumerating objects: ...
Receiving objects: 100% (...)
Resolving deltas: 100% (...)
```

Check you have the right repo:

```sh
git remote get-url origin
```

Expected:

```text
https://github.com/rh-aiservices-bu/CAI-Field-Manual-Hello-World.git
```

## 3. Render locally

### Live preview

```sh
hugo server
```

Expected — the `baseURL` contains a path segment, so the dev server nests under it:

```text
Watching for changes in ...
Web Server is available at http://localhost:1313/CAI-Field-Manual-Hello-World/
Press Ctrl+C to stop
```

Open `http://localhost:1313/CAI-Field-Manual-Hello-World/` in your browser. A 404 at `http://localhost:1313/` is **expected** — the site is not served at the domain root locally either. If you see page content at the nested path, the server is correct.

> **Failure:** `port 1313 already in use` → stop the other Hugo instance or run `hugo server --port 1314`.

### Production build (publication gate)

This is the only correctness check — CI runs the same command:

```sh
hugo --cleanDestinationDir --gc --minify --environment production \
  --printPathWarnings 2>&1 | grep -i warn | grep -v "css.Sass: libsass"
```

Expected:

```text
(empty — no output)
```

No output means clean. Any other `WARN` line means the build is not done — fix and re-run. The single allowed warning is `css.Sass: libsass` from the pinned `oink v1.4.0` theme; do not add `--panicOnWarning` (it would fail on that known warning).

The full build also prints a summary:

```text
                  │ EN
──────────────────┼────
 Pages            │ 35
 Paginator pages  │  0
 Non-page files   │  2
 Static files     │ 38
 Processed images │  0
 Aliases          │  1
 Cleaned          │  0
Total in 994 ms
```

`Pages` and `Total` will shift as content grows — match the **shape**, not the exact numbers: non-zero `Pages`, `Aliases` `1` (the `editing/` alias for the old `prerequisites/` URL), `Total` under a few seconds.


## 4. Where things live

```
CAI-Field-Manual-Hello-World/
├── hugo.yaml                  # site config: title, baseURL, variants, customize, Shell import
├── hugo.work                  # gitignored — workspace override for local Shell development
├── data/home.yaml             # landing-page sections (hero, cards, FAQ, CTA)
├── content/
│   ├── _index.md              # home-page front matter (site-wide cascade)
│   └── hello-world/
│       ├── _index.md          # manual landing — links to every chapter
│       ├── editing/           # ← you are here (this chapter)
│       ├── set-up/
│       ├── operate/
│       ├── things-that-broke/
│       ├── customize/
│       ├── teardown/
│       └── what-is-a-field-manual/
│           ├── overview/
│           ├── definition/
│           ├── how-to-use-it/
│           └── showcase/      # variant selector + command palette tour
├── AGENTS.md                  # publication gate + content rules
└── README.md                  # clone/render reference + admin setup
```

| Path | What it controls |
|---|---|
| `hugo.yaml` `params.variants` | Display-option axes (`version` `3.4/3.5/3.6` + `mode` `gitops/manual/scripted`) — option IDs here must match every `{{</* variant ... */>}}` in content |
| `hugo.yaml` `params.customize` | Customize tokens (`user`, `cluster`) — IDs here must match every `{{</* customize ... */>}}` |
| `hugo.yaml` `module.imports` | Pinned `CAI-Field-Manual-Shell` commit (`version: <hash>`) + transitive `oink v1.4.0` theme |
| `hugo.yaml` `params.ui` | Look-and-feel defaults; unset keys come from the Shell module via deep-merge |

Layouts, assets, and static files are **not in this repo** — they live in [`CAI-Field-Manual-Shell`](https://github.com/rh-aiservices-bu/CAI-Field-Manual-Shell) (imported as a Hugo module). Propose chrome changes there, not here.

## 5. Making changes

Edit Markdown in `content/hello-world/`. Keep the existing front-matter keys (`title`, `description`, `weight`, `icon`) and add new pages as `content/hello-world/<slug>/index.md` leaf bundles. The default archetype (`archetypes/default.md`) scaffolds the bundle front matter; edit `description` and `weight` to place it in the left nav, and remove any `draft: true` if you ever see it (drafts are excluded from the build, so the publication gate would still pass while the page is invisible).

Useful commands:

```sh
hugo new content content/hello-world/my-topic/index.md   # scaffold (respects archetype if present)
hugo config | grep -i baseurl                             # confirm active site URL (hugo config lowercases keys)
hugo mod graph                                            # confirm pinned Shell + oink versions
```

Expected for `hugo mod graph` (hash varies with the pinned commit):

```text
github.com/rh-aiservices-bu/CAI-Field-Manual-Hello-World github.com/rh-aiservices-bu/CAI-Field-Manual-Shell@v0.0.0-...
github.com/rh-aiservices-bu/CAI-Field-Manual-Shell@v0.0.0-... github.com/pgsty/oink@v1.4.0
```

After any edit, re-run the publication gate from §3 — no separate test suite exists.

## 6. Finding your way — the two navigations

Every manual page has two independent navigations. They answer different questions.

### Left navigation — where you are in the manual

The sidebar on the left is the **field-work timeline**. It is chronological — chapters are numbered `1` → `N`, children are `N.M` — and it uses two-level fold/unfold.

```
┌─ Sidebar ─────────────────────────────┐
│  Hello World  ▾          [⌕ Search] │
│  ┌─ 1. Introduction  ▸               │
│  │   1.1 What is a field manual      │
│  │   1.2 Definition                  │
│  │   1.3 How to use it               │
│  │   1.4 Showcase                    │
│  ├─ 2. Editing  ◂ you are here       │
│  ├─ 3. Set up                        │
│  ├─ 4. Operate                       │
│  ├─ 5. Things that broke             │
│  ├─ 6. Customize this!               │
│  └─ 7. Teardown                      │
│                                      │
│  Manual selector sits above the tree │
│  (one manual → static link; N        │
│   manuals → dropdown). Chevron ▸/▾   │
│   folds each chapter's children.     │
└──────────────────────────────────────┘

  What it tells you:
  • Ordered chapters — follow 1→N for new work,
    jump directly for day-two tasks.
  • Fold state is in-memory per visit (resets on
    navigation); `sidebar_expanded` in
    content/_index.md keeps the whole tree open.
  • Search (⌕) opens the command palette — same
    as `/` or `⌘K` from anywhere.
```

What to look for on the real page:

- **Manual selector** — the row directly above the tree. With one manual it is a static link to `hello-world/`; with more it becomes a dropdown (`shell/root-menu.html`).
- **Numbered items** — chapters render `1.` … `N.` and children `N.M` via a CSS counter on `ul--0` (single-manual fix in `assets/scss/_styles_project.scss` suppresses the selector row's counter).
- **Active highlight** — the current page's row and its ancestor chapters are emphasised; verify by navigating — the highlight must move and the ancestors must stay expanded.
- **Prev/Next pager** — at the bottom of the article, `← Previous` / `Next →` walk the same `1`→`N` order and respect the per-manual root (`layouts/_partials/pager-state.html`).

### Content navigation — where you are on this page

The column on the right (and the heading stack inside the article) is the **in-page outline**. It is built from the page's `h2`/`h3` headings — the right TOC lists `h2` as top-level entries and `h3` nested beneath them. The page title (`h1`) is the article header above the TOC scope and does not appear in it (Hugo default `markup.tableOfContents.startLevel: 2`). The variant selector sits directly above the first heading.

```
┌─ Article ───────────────────┐ ┌─ On this page ──────┐
│  Variant selector            │ │  1. Tooling          │
│  ┌ RHAIE Version ─────────┐ │ │  2. Clone            │
│  │ Show all  3.4  3.5  3.6 │ │ │  3. Render locally   │
│  └────────────────────────┘ │ │     Live preview     │
│  ┌ Ops Mode ──────────────┐ │ │     Production build │
│  │ Show all GitOps Manual │ │ │  4. Where things live│
│  └────────────────────────┘ │ │  5. Making changes   │
│  Status: Showing 3.6 …      │ │  6. Finding your way │
│                             │ │     Left navigation  │
│  # Editing                  │ │     Content navigation│
│  ## 1. Tooling              │ │                      │
│  ## 2. Clone                │ │  Page actions  ▾     │
│  ## 3. Render locally       │ │  Copy Markdown       │
│  ### Live preview           │ │  View markdown       │
│  ### Production build       │ │  Open in ChatGPT     │
│  ## 4. Where things live    │ │  View edit history   │
│  …                          │ │                      │
└─────────────────────────────┘ └──────────────────────┘

  What it tells you:
  • Variant selector — filters version/mode blocks
    on this page (choice persists in localStorage).
  • Headings — h2→h3 outline; h2 at the top level,
    h3 nested; right TOC tracks scroll position.
  • Page actions (▸ next to title or palette `>`) —
    copy markdown, view source, jump to LLM apps.
```

What to look for on the real page:

- **Variant selector** — one row per axis (`hugo.yaml` `params.variants`); the active pill is highlighted and a status line beneath it restates the view as a sentence (e.g. *Showing RHAIE 3.6, every ops mode*). Your choice persists across pages via `localStorage` (`td-variant:v1:`).
- **Right TOC** — `h2`/`h3` entries only (`h2` at the top level, `h3` nested; `h4` and deeper are not listed). The page title is the `h1` above the outline, not inside it. Verify by scrolling — the active entry highlights as you pass each section.
- **Page-action menu** — the `▾` disclosure next to the article title (and the command palette's `Page actions` group) offers *Copy Markdown*, *View markdown*, *Open in ChatGPT / Claude / Gemini*, *Feed it to my AI Agent*, and *View edit history*. Markdown outputs carry every variant labeled — HTML filtering is view-only.
- **Customize** — the `Customize this!` chapter sets `user`/`cluster` tokens that substitute into every `{{</* customize ... */>}}` across the manual (also `td-customize:v1:` in `localStorage`).

Together: the left nav answers "which step of the job am I on?" — the content navigation answers "which part of this step am I reading?"
