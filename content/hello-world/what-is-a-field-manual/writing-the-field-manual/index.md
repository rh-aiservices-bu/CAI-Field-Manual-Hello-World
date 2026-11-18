---
title: Writing the field manual
description: The conventions every manual follows and the walkthrough for writing your first one.
weight: 50
icon: fa-solid fa-pen-ruler
---

Every manual on this site follows six conventions and passes one publication gate. The conventions get one sentence each below; [Definition]({{< relref "/hello-world/what-is-a-field-manual/definition" >}}) explains what each buys the reader. The [Hello World manual]({{< relref "/hello-world" >}}) demonstrates all six. Then a walkthrough takes the blank skeleton to a gated, deployable manual.

## The six conventions

1. **Chronological information architecture.** Order the manual by the field-work timeline: set up → operate → things that broke, not by product release phases.
2. **Status-gate verification.** Every command is followed by a verify command and its literal expected output, so a step is confirmed, not just run.
3. **Inline known issues.** Each known issue lives on the page where the reader hits it, as symptom, diagnosis command, workaround, and tracker id.
4. **Variant selector axes.** Version and ops-mode differences go through the page-top selector's axes, with labels that describe themselves.
5. **Customize tokens.** Environment-specific values (username, cluster) are tokens with shipped defaults, not one reader's values baked into commands.
6. **Dual consumption.** Every page works as rendered HTML for a human and as labeled markdown for an AI agent, from the same source.

The Hello World chapters demonstrate all six.

## Write your first manual

You will take the blank skeleton to a publication-gated field manual: duplicate it, retitle it, write the three timeline pages, and pass the gate. Every command below is immediately followed by a verify command or check and its literal expected output, the status gate the site's manuals use. You need a clone of this repo and [Prerequisites](#prerequisites) below.

### Prerequisites

- The definition you are writing against: [What is a Field Manual?]({{< relref "/hello-world/what-is-a-field-manual/definition" >}}). Read it first.
- Hugo Extended ≥ 0.168. Check with:

```sh
hugo version
```

Expected output: a version line at or above `hugo v0.168.0+extended`.

- Go ≥ 1.27. Check with:

```sh
go version
```

Expected output: a version line at or above `go version go1.27`.

- A clone of this repo. Check with:

```sh
ls content/hello-world/_index.md
```

Expected output:

```sh
content/hello-world/_index.md
```

### Procedure

1. Clone this repo as the starting point for your new manual (one manual per repo):

```sh
git clone https://github.com/rh-aiservices-bu/CAI-Field-Manual-Hello-World.git my-manual
cd my-manual
```

Verify you have the right repo:

```sh
git remote get-url origin
```

Expected:

```sh
https://github.com/rh-aiservices-bu/CAI-Field-Manual-Hello-World.git
```

2. Retitle the manual in `content/hello-world/_index.md`: set `title` to your manual's name and `description` to one sentence. Check the front matter parses and the title took:

```sh
head -4 content/hello-world/_index.md
```

Expected output: the `---` fence, then your new `title:` and `description:` lines.

3. Write the three timeline pages (`set-up/`, `operate/`, `things-that-broke/`) following the six conventions: outcome first, a verify step after every command, known issues inline. Verify each page has the verification subsection:

```sh
grep -l "## Verification" content/hello-world/*/index.md
```

Expected output:

```sh
content/hello-world/operate/index.md
content/hello-world/set-up/index.md
content/hello-world/things-that-broke/index.md
```

> **Adding pages:** create leaves as `content/hello-world/<slug>/index.md`. The archetype (`archetypes/default.md`) provides the front matter; set `description` and `weight` yourself. If you see `draft: true`, remove it: drafts are excluded from the build, so the gate still passes while the page stays invisible.

4. Run the dev server and check the section serves:

```sh
hugo server
```

In a second terminal, verify the section renders:

```sh
curl -s -o /dev/null -w "%{http_code}" http://localhost:1313/CAI-Field-Manual-Hello-World/hello-world/
```

Expected output:

```sh
200
```

A 404 at the root (`http://localhost:1313/`) is normal: `baseURL` contains a path, so the dev server serves under `/CAI-Field-Manual-Hello-World/`.

5. Stop the dev server, then run the publication gate from [Editing]({{< relref "/hello-world/editing" >}}) (§3, Production build): the production build, followed by the warning gate that asserts no WARN lines except the known LibSass one. A clean gate prints no WARN output; the LibSass warning in the direct build's own output is expected, not a failure.

### Verification

The manual is publication-ready when:

- The dev server serves the section at `http://localhost:1313/CAI-Field-Manual-Hello-World/hello-world/` (HTTP 200), and each timeline page renders under it.
- The publication gate completes with no ERROR lines, and the warning gate produces no output.
- Every command in the three timeline pages is followed by a verify step with literal expected output.

### Rollback

- Discard the content you added (the timeline pages), or clone fresh:

```sh
rm -r content/hello-world/set-up content/hello-world/operate content/hello-world/things-that-broke
```

Check the tree is back to the shipped state:

```sh
ls content/hello-world
```

Expected output (only the shipped chapters):

```sh
_index.md
customize
editing
what-is-a-field-manual
```

When the gate is clean, open a PR to `main`. Merges to `main` deploy automatically via GitHub Actions.
