---
title: How to use it
description: Reading the manual as a human, as an agent, and what to do when something breaks.
weight: 30
icon: fa-solid fa-person-hiking
---

The two-reader split (human HTML, agent markdown) is defined in [Definition]({{< relref "/hello-world/what-is-a-field-manual/definition" >}}). This page is the steps.

## As a human

1. **Start where your work starts.** The left nav follows the field-work timeline, so the chapter order matches the order you do the job in.
2. **Set your context once.** Open [Customize this!]({{< relref "/hello-world/customize" >}}) and pick your version, ops mode, name, and cluster. The choice persists in `localStorage`, and every later command substitutes your values automatically.
3. **Filter, don't fork.** The selector at the top of every page filters version-specific notes and ops-mode blocks. *Show all* puts every variant side by side.
4. **Verify every step.** Every command comes with a verify command and its literal expected output. A step is done when the verify output matches.

## As an AI agent

- **Per-page markdown**: use the page action menu (*Copy markdown* / *View markdown*), or hand the page URL to Claude, ChatGPT, or Gemini.
- **Whole-site bundle**: [`llms.txt`]({{< relref "/" >}}), plus one flattened markdown file per manual.

## When something breaks

- Find the symptom/diagnosis/workaround block on the page where the procedure failed. There is no separate errata page.
- To report a new issue or propose a fix, open a [GitHub Issue](https://github.com/rh-aiservices-bu/CAI-Field-Manual-Hello-World/issues) with the page URL (or `content/` path) and what you saw.

## See it in practice

- You are reading the demonstration: the [Hello World manual]({{< relref "/hello-world" >}}) uses every step above across its chapters.
