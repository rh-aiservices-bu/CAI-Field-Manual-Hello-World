---
title: Showcase
description: A visual tour of the two pieces of chrome you will use most — the variant selector and the command palette's AI actions.
weight: 40
icon: fa-solid fa-wand-magic-sparkles
---

Reading a field manual is not just scrolling. Two controls shape what you see and where you take it — the variant selector that filters content to your version and workflow, and the command palette that lets you search the site and hand the current page to your AI assistant. This page shows both at their default state and explains what happens when you use them.

## Variant selector — pick your version and workflow, or show all

At the top of every manual page, directly above the first heading, the selector offers one row per axis declared in `hugo.yaml`:

![Variant selector with two axes: RHAIE Version (Show all, 3.4, 3.5, 3.6) and Ops Mode (Show all, GitOps, Manual, Scripted) — both on Show all](variant-selector.png)

What you are looking at:

- **RHAIE Version** — `Show all` · `3.4` · `3.5` · `3.6`. Sourced from the `version` axis in `hugo.yaml` (`params.variants[0]`). The neutral option is `Show all` (`all`); the shipped default is `3.6`.
- **Ops Mode** — `Show all` · `GitOps` · `Manual` · `Scripted`. The `mode` axis in `hugo.yaml` (`params.variants[1]`). Its neutral option is also `Show all`; the default is `all` itself, so new visitors see every ops mode side by side.

How it works:

1. **Select, don't navigate.** Clicking an option does not reload the page. A small inline script sets `data-td-variant-<axis>` on `<html>` and CSS hides every `variant` shortcode block whose `data-td-variant-id` does not contain the active value or the axis's neutral value. A single block can carry several options — `{{%/* variant version "3.5 3.6" */%}}` — and stays visible for either.
2. **Show all is the escape hatch.** Choosing `Show all` removes the filter for that axis entirely, so version notes and alternative procedure steps render together. This is also the state that the markdown, `llms.txt`, and `_print` outputs use — they carry every variant labeled (`RHAIE Version: 3.5`, `Ops Mode: GitOps`) and nothing hidden.
3. **It remembers.** The choice is stored as `td-variant:v1:<axis>` in `localStorage` and re-applied on every page, visit, and tab. The [Customize this!]({{< relref "/hello-world/customize" >}}) page renders the same option groups (`data-td-variant-options`) so the selector and the form mirror each other and sync across tabs via the `storage` event.

For authors, the selector is data-driven. Adding an axis or option is one entry in `hugo.yaml` under `params.variants` (with `id`, `label`, `default`, `neutral`, and `options`); wrapping content is one shortcode `{{%/* variant version "3.4" */%}}…{{%/* /variant */%}}`. Removing a variant block to silence a rendering complaint is never correct — markdown outputs intentionally show all variants.

## Command palette — search, then act

The site's search and all quick actions live in one overlay — the command palette. It is the `td-shell-search` dialog bundled whenever `shell/search-enabled.html` is true (it is here — `offline_search: true` in `hugo.yaml`).

![Command palette open on Search with scope All sections vs Hello World and a Page actions group listing Copy Markdown, Open in ChatGPT, Open in Claude, Open Gemini, Feed it to my AI Agent, View markdown, and View edit history](command-palette.png)

What you are looking at:

- The header is a live `Search…` input (placeholder `Search…`, `esc` to close).
- Right under it, **Search in** toggles the search scope: `All sections` (the whole site) vs. the current manual section (`Hello World` on this page). The index is the offline Lunr bundle published as `offline-search-index.en.json`.
- Below that, the **Page actions** group. Every row there is the same registry entry that appears in the caret menu beside the page title — the palette is just the keyboard-friendly way to reach it.

### How to open it
Three equivalent triggers, all of which set `logicalOpen` in `command-palette.js`:

- **Click** the `Search…` box in the top navbar or the magnifying-glass button in the sidebar header (both carry `data-td-shell-search-open` and target `#td-shell-search`). On mobile, the same trigger lives in the navigation drawer.
- **Press `/`** (or `F` when keyboard navigation is on) anywhere outside a text field — the palette opens in full search mode, with `Search…` focused. This is the hint shown as <kbd>/</kbd> on the sidebar search button.
- **Press `\`** (or `C`) outside a text field — the palette opens restricted to commands, the same as if you had typed `>` as the first character. The cheat sheet (question-mark button in the bottom bar, hover/focus to reveal) lists every shortcut available on the current page.

Once open: type to filter pages and actions, <kbd>↑</kbd>/<kbd>↓</kbd> to move, <kbd>↵</kbd> to open, <kbd>esc</kbd> to close and return focus to the control that opened the palette. Starting a query with `>` limits results to commands only.

The same actions are also reachable without the palette — click the disclosure caret beside the page title to open the `td-page-actions` menu. That menu and the palette share one action manifest (`#td-action-manifest`) generated by `layouts/_partials/actions/context.html`, so their rows never diverge.

### Using the AI actions

The rows you see under **Page actions** are the field manual's AI integration — one source, dual consumption made actionable:

| Row | What it does |
|---|---|
| **Copy Markdown** | Copies the current page's markdown output to the clipboard (via `OinkClipboard.writeText`, with a dialog-aware `execCommand` fallback). The title-bar copy button and this palette row share one confirmation flash. |
| **Open in ChatGPT** | Opens `chatgpt.com` in a new tab with a pre-filled prompt `Read from <permalink> so I can ask questions about it.` The registry rewrites the URL at click time so the deployed host, current query, and hash survive. |
| **Open in Claude** | Same, targeting `claude.ai/new`. |
| **Open Gemini** | Same, targeting `gemini.google.com/app`. |
| **Feed it to my AI Agent** | Opens the **Feed this to your AI Agent** modal dialog on the current page (see below) — `href="#agent-prompt"` is the trigger, not a navigation. |
| **View markdown** | Opens the raw `index.md` output for the page in a new tab. |
| **View edit history** | Opens the GitHub commit history for the page's source file (`content/hello-world/what-is-a-field-manual/showcase/index.md`). |

The three assistant links and the *Feed it to my AI Agent* row only render when the site's `page_context_menu.assistant_links` policy allows them and, for the agent row, when `params.github_repo` is set — without a repo there is nothing to clone.

#### Feed it to my AI Agent — the copy-prompt dialog

Choosing **Feed it to my AI Agent** in the palette (or in the title menu) sets the fragment to `#agent-prompt`, which `static/js/agent-setup.js` watches for. The script clears the hash (via `history.replaceState`, without pushing a history entry so the action can re-fire), builds and opens a `<dialog>` titled *Feed this to your AI Agent*, and focuses the `Copy prompt` button:

- The prompt is assembled from `window.__TD_AGENT_SETUP__.repo` (which `layouts/_partials/hooks/body-end.html` injects from `params.github_repo` — `https://github.com/rh-aiservices-bu/CAI-Field-Manual-Hello-World`). That gives the clone URL `https://github.com/rh-aiservices-bu/CAI-Field-Manual-Hello-World.git` and the directory name `CAI-Field-Manual-Hello-World`.
- The prompt tells the agent to clone the repo, read the *Clone and render locally* section of `README.md` (the single source of truth for prerequisites, `hugo --printPathWarnings` publication gate, and `go get` / `hugo mod graph` module pin checks), survey `content/`, `data/home.yaml`, `hugo.yaml`, `.github/workflows/`, and the Shell module for chrome, then report what the manual can do — filtering, `customize` tokens, per-page markdown / `llms.txt` / `_print` bundles, and how to add a new `content/hello-world/` manual — before asking what to do next.
- **Copy prompt** copies the text via the same `OinkClipboard` helper the copy actions use (dialog-aware, so the palette's hidden fallback field is not targeted). The label flips to `Copied` for 1.6 s. Closing the dialog — `×`, clicking the backdrop, or <kbd>esc</kbd> (which `stopPropagation`s so the title menu's own <kbd>esc</kbd> handler does not also fire) — restores focus to the control that opened the palette.

If the page was opened with no palette in the DOM (landing page, `_print` bundle), the hash listener is not installed and the row is not registered — the feature only exists where the palette does.

---

The two screenshots above are page resources of this bundle (`variant-selector.png`, `command-palette.png`). They were taken with both variant axes on `Show all` and the palette freshly opened with an empty query — the state new visitors see first.
