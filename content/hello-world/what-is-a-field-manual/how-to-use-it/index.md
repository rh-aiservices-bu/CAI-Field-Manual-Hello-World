---
title: How to use it
description: Reading the manual as a human, as an agent, and what to do when something breaks.
weight: 30
icon: fa-solid fa-person-hiking
---

Every page in this site serves two readers at once — you and your AI agent — with different but complementary information.

## As a human

1. **Follow the timeline.** The left nav is chronological — introduction first, then editing, set up, operate, things that broke, and teardown. Start where your work starts; the chapter order matches the order you do the work in.
2. **Set your context once.** Open [Customize this!]({{< relref "/hello-world/customize" >}}) and pick your RHAI version, ops mode, name, and cluster. The choice persists in `localStorage` across pages, visits, and tabs — every later command substitutes your values automatically.
3. **Filter, don't fork.** The selector at the top of every manual filters version-specific notes and ops-mode blocks. Use *Show all* when you want every variant side by side instead of separate pages.
4. **Verify every step.** Every command is followed by a verify command and the literal output you should see. A step is not done when it runs — it is done when its verify output matches.

## As an AI agent

- **Per-page markdown** — use the page action menu (*Copy markdown* / *View markdown*) or hand the page to Claude, ChatGPT, or Gemini.
- **Whole-site bundle** — the entire site is available as [`llms.txt`]({{< relref "/" >}}) and every manual as a single flattened markdown file with every variant labeled and nothing filtered.

The markdown, `llms.txt`, and print outputs carry every variant labeled (`Axis: Option`) and every customize token at its shipped default — only the interactive HTML filters or substitutes. Nothing is hidden from agents.

## When something breaks

- Known issues are documented **where you hit them** — look for the symptom/diagnosis/workaround block on the step that failed, not on a separate errata page.
- To report a new issue or propose a fix, open a [GitHub Issue](https://github.com/rh-aiservices-bu/CAI-Field-Manual-Hello-World/issues) with the page URL (or `content/` path) and what you saw.

## See it in practice

- The [Hello World manual]({{< relref "/hello-world" >}}) is the blank skeleton — duplicate it and fill in your own field procedures. The timeline, status gates, and inline issues described above are all demonstrated in its chapters.
