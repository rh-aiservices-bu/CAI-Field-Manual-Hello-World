---
title: Customizing
description: "Set the choices this manual remembers: RHAIE version, ops mode, your name, your cluster."
weight: 60
icon: fa-solid fa-sliders
---

Every choice on this form is saved in your browser and reused on every page, so you set each value once.

{{< customize-form >}}

## What each choice does

- **RHAIE Version**: filters version-specific notes. Pick 3.4, 3.5, or 3.6; *Show all* reads every version's notes together.
- **Ops Mode**: filters procedure blocks, since the GitOps, Manual, and Scripted variants of a step differ; *Show all* keeps every mode visible.
- **Your name**: substitutes into login commands, e.g. `oc login --username={{< customize user >}} …`
- **Cluster**: substitutes into every cluster-scoped command, e.g. `oc login --server=api{{< customize cluster >}} …`

## Resetting

Clear this site's storage to return to the shipped defaults, or just set each control above again. The defaults ship on purpose: they are example values (guest, `.ocp.abcd.sandbox1234.opentlc.com`, Show all, 3.6), not real ones, so commands still read correctly before you set anything.
