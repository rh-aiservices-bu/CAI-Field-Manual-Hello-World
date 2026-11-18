---
title: Definition
description: What a field manual promises, what it does not, and who it is written for.
weight: 20
icon: fa-solid fa-scale-balanced
---

## What it is

A field manual is:

- **Chronological field notes**: ordered by the work you are doing (set up, operate, fix what broke).
- **Status-gated procedures**: every command is followed by a verify command and the literal expected output, so "ran it" becomes "confirmed it."
- **Inline known issues**: documented where you hit them, as symptom, diagnosis, workaround, and tracker ID.
- **Written for two audiences at once**: a human reading HTML, an AI agent reading the same page as markdown.

These are four of the six conventions every manual on this site follows. [Writing the field manual]({{< relref "/hello-world/what-is-a-field-manual/writing-the-field-manual" >}}) names all six and states each as a rule.

## What it is not

A field manual is not:

- Official product documentation.
- Guaranteed to be accurate.
- Available ahead of a new release.

> **Orientation, not a support statement.** Field notes record what worked when they were written. Check the official documentation for the version you are running before relying on them for production decisions.

## Humans and their AI agents

AI agents now do part of the field work: the logistically important, strategically boring parts. Regression testing, bug reports, status updates, content review. The manual is written for both audiences:

- You read the rendered page, filtered to your version and ops mode, with your cluster and name substituted in.
- The agent reads the same page as markdown (`llms.txt` or per-page markdown), every variant labeled, every token at its shipped default.

One source, two readers.
