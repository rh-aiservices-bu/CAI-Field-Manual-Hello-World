---
title: Definition
description: What a field manual is, what it is not, and the contract that implies.
weight: 20
icon: fa-solid fa-scale-balanced
---

## What it is

A field manual is:

- **Chronological field notes** — organised by the work you are doing (set up, operate, fix what broke), not by product release phases or feature lists.
- **Status-gated procedures** — every command is followed by a verify command and the literal expected output, so "ran it" becomes "confirmed it."
- **Inline known issues** — documented where you hit them (symptom, diagnosis, workaround, tracker ID), not banished to a separate page.
- **Written for two audiences at once** — a human reading HTML and an AI agent reading markdown, with the same content serving both.

The six conventions this manual follows are: chronological information architecture, status-gate verification, inline known issues, variant selector axes, customize tokens, and dual consumption.

## What it is not

A field manual is **not**:

- Official product documentation.
- Guaranteed to be 100% accurate 100% of the time.
- Available ahead of a new release.
- Always right on everything — there are exceptions to every rule.

That distinction matters. Field notes reflect what worked during field work when the manual was written; verify against the current official product documentation before relying on them for production decisions.

> **Orientation, not a support statement.** Commands and versions reflect what worked in the field when this manual was written. Check the official docs for the version you are running.

## Humans and their AI agents

We no longer make this journey alone. AI agents handle some of the logistically important but strategically boring tasks — regression testing, reporting bugs, updating statuses, reviewing content for clarity and accuracy. Field manuals are written so they speak to both audiences, providing different but complementary information to each:

- The human reads the rendered page, filters by version and ops mode, and substitutes their own cluster and identity.
- The agent reads the same page as markdown (`llms.txt` or per-page markdown) with every variant labeled and every token at its shipped default — nothing filtered, nothing hidden.

That split is the point: the field manual is one source, consumed two ways.
