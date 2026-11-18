---
title: Writing the field manual
description: The field manual for field manuals — the conventions, the intent, and the walkthrough for a CAI author's first manual.
weight: 50
icon: fa-solid fa-pen-ruler
---

The field manual for field manuals — the six conventions every manual on this site follows, why each exists, and the process for shipping one.

## The six conventions

Field manuals here are written against six conventions. Each one states why it exists, what a reader loses when a manual skips it, and the rule itself. The [Hello World manual]({{< relref "/hello-world" >}}) is the working exemplar. Every rule below is something it does.

### 1. Chronological information architecture

The work happens in a fixed order: set the system up once, operate it day to day, and fix what breaks. A manual organized by order-of-work puts the stage a reader is standing in one click away, and the timeline never goes stale the way an install route does. Phases get renumbered by every release, but field work is still set up, operate, breakage. Order it by deployment phases instead and the nav stops matching the reader's moment; a day-two check hides inside whatever phase list came from the installer's point of view.

**Rule: order the manual by the field-work timeline (set up → operate → things that broke).**

In source form, the nav order is each page's `weight` in front matter:

```markdown
---
title: Set up
weight: 20
---
```

`operate/` carries `weight: 30` and `things-that-broke/` carries `weight: 40`, so the left nav reads set up → operate → things that broke.

**Wrong:**

```markdown
---
title: Validate the deployment
weight: 30
---
```

A phase page named for the install route — the reader loses the operator's timeline and cannot tell where a day-two check lives.

**Right:**

```markdown
---
title: Operate
weight: 30
---
```

Named for the stage of field work the page covers, so the nav matches the moment the reader is in.

### 2. Status-gate verification

A command whose output nobody checks is a guess about the system's state. The status gate turns each step from "ran it" into "confirmed it": the command, a verify command, and the literal expected output to compare against. Without the gate, a half-applied change looks exactly like a successful one, and the failure surfaces steps later, far from the command that caused it and past the point where the reader knows what to undo.

**Rule: follow every command with a verify command and its literal expected output.**

The gate: a verify command and its literal expected output, right after the command it checks.

````markdown
Confirm the resource reports Ready:

```sh
oc wait --for=condition=Ready hello/hello-world --timeout=120s
```

Expected output:

```sh
hello.hello.example.com/hello-world condition met
```
````

**Wrong:**

```sh
oc apply -k manifests/hello-world/
```

Applied with no gate — a half-applied change reads as success, and the failure surfaces two steps later, away from its cause.

**Right:**

````markdown
```sh
oc apply -k manifests/hello-world/ && \
oc wait --for=condition=Ready hello/hello-world --timeout=120s
# expect: hello.hello.example.com/hello-world condition met
```
````

The step cannot pass silently — the expected output is part of the step.

### 3. Inline known issues

Breakage arrives in the middle of following a manual, not after finishing it, so the fix belongs on the page where the reader already is. A known issue is documented as symptom, diagnosis command, workaround, and tracker id, inline. Banished to a separate known-issues page, the same information costs the reader their place: they leave mid-procedure, scan a list that is not about their step, and usually give up before finding the workaround. The tracker id is what keeps the entry honest once the workaround rots.

**Rule: document each known issue where the reader hits it (symptom, diagnosis command, workaround, tracker id).**

The block, placed on the page where the reader hits it:

```markdown
## Known issue: hello-world pod OOMKill

**Symptom.** The pod restarts with reason `OOMKilled`.

**Diagnosis.** `oc describe pod hello-world | grep -A3 "Last State"`

**Workaround.** Raise the memory limit in `manifests/hello-world/` and re-apply. (RHOAIENG-0000)
```

**Wrong:**

```markdown
Known issues are collected on the [known-issues](../known-issues/) page.
```

A pointer out of the page — the reader mid-procedure loses their place and, usually, the workaround.

**Right:**

```markdown
**Symptom.** The pod restarts with reason `OOMKilled`.
**Diagnosis.** `oc describe pod hello-world | grep -A3 "Last State"`
**Workaround.** Raise the memory limit and re-apply. (RHOAIENG-0000)
```

Symptom, diagnosis, workaround, tracker id — all on the page where the pod restarts.

### 4. Variant selector axes

Version and ops-mode differences are permanent features of the product, not noise to edit away. The page-top selector holds both axes, filters the page for the current reader, and persists the choice: one manual instead of one fork per version, and no mode buried behind tabs. The labels have to describe themselves, because most consumption is not interactive: agents read the markdown output, where nothing is filtered and the label is the only thing saying which variant a block belongs to.

**Rule: route version and ops-mode divergence through the page-top selector's axes, and write labels that describe themselves.**

A variant block — filtered in the HTML by the page-top selector, labeled in the markdown output:

````markdown
{{</* variant mode gitops */>}}
Apply via GitOps (manifests under `manifests/hello-world/`):

```sh
oc apply -k manifests/hello-world/
```
{{</* /variant */>}}
````

**Wrong:**

```sh
oc apply -k manifests/hello-world/   # GitOps only
```

One mode's commands shipped to every reader — the other modes lose their path, and per-mode copies of the page drift apart instead.

**Right:**

````markdown
{{</* variant mode manual */>}}
Apply from the web console: **Ecosystem → Installed Operators → Hello World → Create instance**.
{{</* /variant */>}}
````

Every mode gets its own block on the same page; the selector filters, the label describes.

### 5. Customize tokens

No two readers share a cluster name, a username, or a project. Customize tokens mark those values and substitute the reader's own: set once, then applied in every command, with a shipped default so the manual still reads before any value is set. Without them, every command ships with one environment's values baked in; readers hand-edit, and hand-edits are where wrong clusters, stale usernames, and copy-paste mistakes come from.

**Rule: substitute environment-specific values with customize tokens, each with a sensible shipped default.**

Tokens in a command, with the shipped default stated where the reader sets it:

````markdown
- Your cluster's API endpoint, e.g. `api{{</* customize cluster */>}}`, and a
  username (default: {{</* customize user */>}}).
````

**Wrong:**

```sh
oc login --username=guest --server=api.ocp.abcd.sandbox1234.opentlc.com
```

One environment's values baked into the command — every other reader hand-edits, and hand-edits are where wrong clusters and typos come from.

**Right:**

````markdown
```sh
oc login --username={{</* customize user */>}} --server=api{{</* customize cluster */>}}
```
````

The reader's own values substitute everywhere once they set them; the default carries the reader who has not set anything.

### 6. Dual consumption

A field manual is read by a human in a browser and consumed by an agent as markdown: same page, both outputs. The HTML filters variants and substitutes tokens interactively; the markdown output carries every variant labeled and every token at its shipped default, so nothing is lost, only flattened. Written for just one consumer, the page serves the other one ambiguity: an unlabeled variant or an unexplained value in the markdown is a sentence the agent cannot act on. The audience split this convention serves (humans and their AI agents getting different but complementary information) is the definition in [What is a Field Manual?]({{< relref "/hello-world/what-is-a-field-manual/definition" >}}).

**Rule: write every page so the markdown-only reader gets every variant labeled and every token at its shipped default.**

The block that survives flattening: the prose restates the axis and the value, so the markdown-only reader — who gets the block labeled but every page merged — still knows what applies when.

````markdown
{{</* variant version 3.4 */>}}
On **3.4**, the health endpoint is `/healthz` instead of `/readyz`.
{{</* /variant */>}}
````

**Wrong:**

```markdown
The health endpoint is `/healthz`.
```

Shipped unlabeled into the merged markdown — the reader, and any agent, cannot tell which version the sentence describes.

**Right:**

````markdown
{{</* variant version 3.4 */>}}
On **3.4**, the health endpoint is `/healthz` instead of `/readyz`.
{{</* /variant */>}}
````

The sentence carries its own condition; the markdown output labels the block, the prose does the rest.

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

1. Clone this repo for your new manual (one manual per repo):

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

2. Retitle the manual in `content/hello-world/_index.md`: set `title` to your manual's name and `description` to one sentence. Verify the front matter parses and the title took:

```sh
head -4 content/hello-world/_index.md
```

Expected output: the `---` fence, then your new `title:` and `description:` lines.

3. Write the three timeline pages — `set-up/`, `operate/`, `things-that-broke/` — following the conventions above: outcome first, status-gate verification on every command, known issues inline. Verify each page has the verification subsection:

```sh
grep -l "## Verification" content/hello-world/*/index.md
```

Expected output:

```sh
content/hello-world/operate/index.md
content/hello-world/set-up/index.md
content/hello-world/things-that-broke/index.md
```

> **Authoring new pages:** Add leaves as `content/hello-world/<slug>/index.md` leaf bundles. The default archetype (`archetypes/default.md`) scaffolds the front matter — set `description` and `weight` to place it in the left nav. Remove any `draft: true` if it appears (drafts are excluded from the build, so the gate would still pass while the page stays invisible).

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

A 404 at the root (`http://localhost:1313/`) is expected: with a path in `baseURL`, the dev server serves under `/CAI-Field-Manual-Hello-World/`.

5. Stop the dev server, then run the publication gate:

```sh
hugo --cleanDestinationDir --gc --minify --environment production --printPathWarnings
```

Expected output: a build summary ending in a line like `Total in N ms`, with no ERROR lines. The known LibSass WARNING from the pinned theme (v1.4.0) is expected in this direct build output. It is not a failure.

6. Run the warning gate, which asserts no WARN lines other than the known LibSass one:

```sh
hugo --cleanDestinationDir --gc --minify --environment production --printPathWarnings 2>&1 | grep -i warn | grep -v "css.Sass: libsass"
```

Expected output: none. No output is clean.

### Verification

The manual is publication-ready when:
- The dev server serves the section at `http://localhost:1313/CAI-Field-Manual-Hello-World/hello-world/` (HTTP 200), and each timeline page renders under it.
- The publication gate completes with no ERROR lines, and the warning gate produces no output.
- Every command in the three timeline pages is followed by a verify step with literal expected output.

### Rollback

- Discard the new content you added (e.g. the timeline pages you wrote) — or clone fresh:

```sh
rm -r content/hello-world/set-up content/hello-world/operate content/hello-world/things-that-broke
```

Verify the tree is back to the shipped state:

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
