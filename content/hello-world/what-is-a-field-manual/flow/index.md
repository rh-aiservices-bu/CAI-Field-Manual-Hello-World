---
title: Flow
description: "How the repos fit together: Shell brings chrome, each manual owns its content, the Library aggregates."
weight: 60
icon: fa-solid fa-diagram-project
---

{{< visualizer file="visualizers/fm-flow.html" label="Field Manual repos flow" embed="true" >}}

Where Shell, manuals, and the Library sit. Click a node or an arrow for detail.

Two things the diagram compresses:

**Mount means Hugo module mount.** The Library does not copy or re-host anything. Its `hugo.yaml` imports each manual repo as a Hugo module, so one Library build renders every manual's content under its own tree. The arrows above are those module imports.

**Adding a manual is two steps, both content-only.** Clone Hello-World, write your manual, deploy it as its own Pages site. Then open a PR in the Library that adds one module import. No new infrastructure, no Library code changes; the manual appears on the next Library deploy.
