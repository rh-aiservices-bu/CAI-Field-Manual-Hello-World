---
title: Flow
description: How the repos fit together — Shell brings chrome, each manual owns its content, Library aggregates.
weight: 60
icon: fa-solid fa-diagram-project
---

{{< visualizer file="visualizers/fm-flow.html" label="Field Manual repos flow" embed="true" >}}

Explore the flows above — each tells a slice of the same picture:

- **Where Hugo + chrome come from** — Shell imports Oink and holds the reference Hugo pin.
- **A manual's life (Hello-World)** — one clone, one deploy, one mount.
- **Manuals → Library (5–10 of them)** — same arrow for each.
- **Add a new manual** — clone Hello-World, import Shell, PR to mount.

The Library doesn't own any manual content — it just mounts. How it does that is detail; the diagram keeps it to one arrow per manual.
