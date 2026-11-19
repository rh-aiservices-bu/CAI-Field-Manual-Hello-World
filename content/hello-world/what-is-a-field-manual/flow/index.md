---
title: Flow
description: "How the repos fit together: Shell brings chrome, each manual owns its content, the Library aggregates."
weight: 60
icon: fa-solid fa-diagram-project
---

The intended topology at a glance — Shell supplying chrome to content-only manuals, with the Library aggregating them:

![Sketch: Shell (colors, features, common variables, chrome) wired to each manual — Hello-World as the template to copy or fork, plus Inference (soon), MaaS, Migration-2-3, Safety (soon) — each with its own rendered site; the Library then renders all manuals in one place (sketch labels this `red.ht/caifm`)](shell-manuals-library.png)

*Whiteboard sketch overview. The red dashed outline groups the content-only manuals sharing Shell. Details the sketch adds beyond the text below — per-manual rendered sites, the Library's schedule and CI checks, the `red.ht/caifm` alias as written in the sketch (the interactive diagram below lists `red.ht/CAIFieldManuals` as the planned canonical — treat either form as proposed until the redirect is live), and the planned Inference/Safety manuals — are as sketched; treat them as proposed until confirmed.*

The interactive diagram below shows the same system with clickable detail:

{{< visualizer file="visualizers/fm-flow.html" label="Field Manual repos flow" embed="true" >}}

Where Shell, manuals, and the Library sit. Click a node or an arrow for detail.

Two things the diagram compresses:

**Mount means Hugo module mount.** The Library does not copy or re-host anything. Its `hugo.yaml` imports each manual repo as a Hugo module, so one Library build renders every manual's content under its own tree. The arrows above are those module imports.

**Adding a manual is two steps, both content-only.** Clone Hello-World, write your manual, deploy it as its own Pages site. Then open a PR in the Library that adds one module import. No new infrastructure, no Library code changes; the manual appears on the next Library deploy.
