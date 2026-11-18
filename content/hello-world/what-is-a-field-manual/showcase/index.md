---
title: Showcase
description: A visual tour of the two pieces of chrome you will use most — the variant selector and the command palette's AI actions.
weight: 40
icon: fa-solid fa-wand-magic-sparkles
---

Reading a field manual is not just scrolling. Two controls shape what you see and where you take it — the variant selector that filters content to your version and workflow, and the command palette that lets you search the site and hand the current page to your AI assistant. This page shows both at their default state and explains how to use them.

## Variant selector — pick your version and workflow, or show all

At the top of every manual page, directly above the first heading, the selector offers one row per filter:

![Variant selector with two axes: RHAIE Version (Show all, 3.4, 3.5, 3.6) and Ops Mode (Show all, GitOps, Manual, Scripted) — both on Show all](variant-selector.png)

- **RHAIE Version** — `Show all` · `3.4` · `3.5` · `3.6`
- **Ops Mode** — `Show all` · `GitOps` · `Manual` · `Scripted`

How to use it:

1. **Click a pill to filter.** Only the content for that version or ops mode stays visible — for example, picking `3.5` hides notes that only apply to `3.4` or `3.6`.
2. **Pick Show all to compare.** `Show all` removes the filter for that row so every version's notes and every procedure variant render together. Use it when you want to see the differences side by side.
3. **Your choice sticks.** Once you pick a version or mode, it stays selected as you move between pages. Change it anytime by clicking a different pill, or reset it on the [Customize this!]({{< relref "/hello-world/customize" >}}) page where the same options appear.

## Command palette — search, then act

The site's search and all quick actions live in one overlay — the command palette:

![Command palette open on Search with scope All sections vs Hello World and a Page actions group listing Copy Markdown, Open in ChatGPT, Open in Claude, Open Gemini, Feed it to my AI Agent, View markdown, and View edit history](command-palette.png)

What you are looking at:

- The header is a live `Search…` input.
- Right under it, **Search in** toggles where to search: `All sections` (the whole site) vs. the current manual (`Hello World` on this page).
- Below that, the **Page actions** group — quick actions for the page you are on. They are the same actions that appear in the menu next to the page title; the palette is just the keyboard-friendly way to reach them.

### How to open it

- **Click** the `Search…` box in the top bar or the magnifying-glass button in the sidebar.
- **Press ⌘K / Ctrl+K** — opens the palette if closed, closes it if open.
- **Press /** — opens directly into full search.
- **Press \\** — opens directly into the command list (same as typing `>`).
- **Press Esc** — closes the palette and returns focus to where you were.

Once open, type to filter pages and actions, use <kbd>↑</kbd>/<kbd>↓</kbd> to move, <kbd>↵</kbd> to open, and prefix your query with `>` to show only commands.

You can also reach the same actions without the palette: click the disclosure caret `▾` next to the page title to open the page menu.

### Using the AI actions

The rows you see under **Page actions** let you take the current page elsewhere — especially to your AI assistant:

| Row | What it does |
|---|---|
| **Copy Markdown** | Copies the current page as markdown to your clipboard. |
| **Open in ChatGPT** | Opens ChatGPT in a new tab with this page pre-loaded so you can ask questions about it. |
| **Open in Claude** | Same, for Claude. |
| **Open Gemini** | Opens the Gemini app in a new tab. Unlike ChatGPT and Claude, it does not pre-fill a prompt — paste the page URL yourself. |
| **Feed it to my AI Agent** | Opens a dialog with a ready-to-copy setup prompt for Claude Code or Codex (see below). |
| **View markdown** | Opens the raw markdown for this page in a new tab. |
| **View edit history** | Opens the GitHub history for this page. |

#### Feed it to my AI Agent — the copy-prompt dialog

Choosing **Feed it to my AI Agent** opens a dialog titled *Feed this to your AI Agent* with a single `Copy prompt` button:

- The prompt tells your agent to clone this repository, follow the setup steps in the README, survey the manuals, and report what the manual can do — then ask you what to do next.
- Click **Copy prompt** to copy it, then paste it into Claude Code or Codex. The label briefly flips to `Copied` to confirm.
- Close the dialog with `×`, by clicking outside it, or with <kbd>esc</kbd>.

---

The two screenshots above are part of this page. They were taken with both filters on `Show all` and the palette freshly opened with an empty query — the state new visitors see first.
