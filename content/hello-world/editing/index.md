---
title: Editing
description: "Clone, render, and manage the Hello World field manual: tooling, local preview, file layout, and verification"
weight: 50
icon: fa-solid fa-pen-to-square
aliases: [/hello-world/prerequisites/]
---

You edit a field manual the same way you run it: make a change, render it, and pass the publication gate before it ships. This chapter covers the full loop: what to install, how to clone and preview the site locally, where things live, and how to prove the build is clean before you publish.

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

Expected output. The literal version and arch suffix vary by OS and install method, so check the **predicates**, not the full line:

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

> **Failure:** `command not found: hugo` → install Hugo Extended for your OS (e.g. `brew install hugo` on macOS, then confirm with `hugo version | grep extended`; the non-extended build cannot compile SCSS). `go: command not found` → install from https://go.dev/dl/.

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

Expected output. The `baseURL` contains a path segment, so the dev server nests under it:

```text
Watching for changes in ...
Web Server is available at http://localhost:1313/CAI-Field-Manual-Hello-World/
Press Ctrl+C to stop
```

Open `http://localhost:1313/CAI-Field-Manual-Hello-World/` in your browser. A 404 at `http://localhost:1313/` is expected: the site is not served at the domain root locally either. If you see page content at the nested path, the server is correct.

> **Failure:** `port 1313 already in use` → stop the other Hugo instance or run `hugo server --port 1314`.

### Production build (publication gate)

This is the only correctness check; CI runs the same command:

```sh
hugo --cleanDestinationDir --gc --minify --environment production \
  --printPathWarnings 2>&1 | grep -i warn | grep -v "css.Sass: libsass"
```

Expected:

```text
(empty — no output)
```

No output means clean. Any other `WARN` line means the build is not done: fix and re-run. The single allowed warning is `css.Sass: libsass` from the pinned `oink v1.4.0` theme; do not add `--panicOnWarning` (it would fail on that known warning).

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

`Pages` and `Total` will shift as content grows. Match the **shape**, not the exact numbers: non-zero `Pages`, `Aliases` `1` (the `editing/` alias for the old `prerequisites/` URL), `Total` under a few seconds.

## 4. Where things live

```
CAI-Field-Manual-Hello-World/
├── hugo.yaml                  # site config: title, baseURL, variants, customize, Shell import
├── hugo.work                  # gitignored: workspace override for local Shell development
├── data/home.yaml             # landing-page sections (hero, cards, FAQ, CTA)
├── content/
│   ├── _index.md              # home-page front matter (site-wide cascade)
│   └── hello-world/
│       ├── _index.md          # manual landing, links to every chapter
│       ├── editing/           # you are here (this chapter)
│       ├── set-up/            # template placeholder — create when you write this chapter
│       ├── operate/           # template placeholder — create when you write this chapter
│       ├── things-that-broke/ # template placeholder — create when you write this chapter
│       ├── customize/
│       ├── teardown/          # template placeholder — create when you write this chapter
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
| `hugo.yaml` `params.variants` | Display-option axes (`version` `3.4/3.5/3.6` + `mode` `gitops/manual/scripted`); option IDs here must match every `{{</* variant ... */>}}` in content |
| `hugo.yaml` `params.customize` | Customize tokens (`user`, `cluster`); IDs here must match every `{{</* customize ... */>}}` |
| `hugo.yaml` `module.imports` | Pinned `CAI-Field-Manual-Shell` commit (`version: <hash>`) + transitive `oink v1.4.0` theme |
| `hugo.yaml` `params.ui` | Look-and-feel defaults; unset keys come from the Shell module via deep-merge |

Layouts, assets, and static files are **not in this repo**. They live in [`CAI-Field-Manual-Shell`](https://github.com/rh-aiservices-bu/CAI-Field-Manual-Shell), imported as a Hugo module. Propose chrome changes there, not here.

## 5. Making changes

Edit Markdown in `content/hello-world/`. Keep the existing front-matter keys (`title`, `description`, `weight`, `icon`) and add new pages as `content/hello-world/<slug>/index.md` leaf bundles. The default archetype (`archetypes/default.md`) scaffolds the bundle front matter; edit `description` and `weight` to place the page in the left nav. If you ever see `draft: true`, remove it: drafts are excluded from the build, so the publication gate would still pass while the page stays invisible.

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

After any edit, re-run the publication gate from §3. No separate test suite exists.

## 6. Finding your way around the page

Two controls sit on every manual page, and [Showcase]({{< relref "/hello-world/what-is-a-field-manual/showcase" >}}) walks through both with screenshots:

- The **variant selector** above the first heading filters the page to your RHAIE version and ops mode.
- The **command palette** (search box, `/`, or `⌘K` / `Ctrl+K`) searches the site and offers page actions such as *Copy Markdown* and *Open in ChatGPT*.

The left navigation follows the field-work timeline; within a page, the right-hand outline lists the h2 and h3 headings.
