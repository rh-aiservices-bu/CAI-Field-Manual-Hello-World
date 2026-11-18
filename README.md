Rendered site: https://rh-aiservices-bu.github.io/CAI-Field-Manual-Hello-World/

# CAI Field Manual — Hello World

The CAI Field Manual is a working example of what future field manuals should
look like: chronological field-work notes — editing → set up → operate
→ things that broke → customize → teardown — where every command is followed
by a verify step with literal expected output, and known issues are documented
where the reader hits them.
In this Hello World repo the three shipped bundles are `editing`, `customize`,
and `what-is-a-field-manual` (the introduction); `set-up`, `operate`,
`things-that-broke`, and `teardown` are stubs you create by following
[Writing the field manual](https://rh-aiservices-bu.github.io/CAI-Field-Manual-Hello-World/hello-world/what-is-a-field-manual/writing-the-field-manual/).

This repo is the **rendered site + all content**. Look-and-feel, layout
overrides, and build rules come from the
[`CAI-Field-Manual-Shell`](https://github.com/rh-aiservices-bu/CAI-Field-Manual-Shell)
Hugo module (imported via `hugo.yaml` → `module.imports`).

Built with [Hugo](https://gohugo.io/) and the [OINK](https://oink.pgsty.com/)
theme (`github.com/pgsty/oink`, transitively via the Shell module).

## How to consume it

This repo ships the blank skeleton. The full template defines six bundles (editing, set up, operate, things that broke, customize, teardown).

**Humans** — read the rendered HTML:

- The selector at the top of every manual (RHAIE version: 3.4 / 3.5 / 3.6;
  ops mode: GitOps / Manual / Scripted — each with *Show all*) filters
  content; the selected pill is clearly highlighted, and a status line
  beneath the pills restates the current view as a sentence, e.g. *Showing
  RHAIE 3.6, GitOps steps — reading as guest on
  api.ocp.abcd.sandbox1234.opentlc.com*; the choice persists in
  `localStorage` across pages, visits, and tabs.
- The **Customize this!** page (bottom of the manual nav) sets every choice
  in one place: RHAIE version, ops mode, your name (default `guest`), and
  your cluster (default `.ocp.abcd.sandbox1234.opentlc.com`). The name and
  cluster substitute into every `customize` token across the manuals.
- Search with `/` or the navbar search box (`offline_search`).
- Each section also renders a print bundle for offline/print reading.

**AI agents** — consume the markdown:

- Per-page markdown via the page action menu: *Copy markdown*, *View
  markdown*, *Open in Claude / ChatGPT*, *Open Gemini*.
- **Command palette** (the navbar search box or `/`): type `agent` and
  choose **"Feed it to my AI Agent"** — it opens a copy-ready prompt for
  Claude Code or Codex: the agent clones the repo, reads the README and
  the code, reports what the manual can do, and asks what to do next.
- Whole site as [`llms.txt`](https://rh-aiservices-bu.github.io/CAI-Field-Manual-Hello-World/llms.txt).
- The markdown, `llms.txt`, and print outputs carry every display option
  labeled (`Axis: Option`) and every customize token as its shipped
  default — only the interactive HTML filters content or substitutes
  values, so agents always see all variants with concrete values.

## Report issues, propose changes

- **Issues** — file a [GitHub Issue](https://github.com/rh-aiservices-bu/CAI-Field-Manual-Hello-World/issues)
  with the page URL (or the `content/` path) and what you hit.
  *(Labels and triage conventions TBD for now.)*
- **Changes** — PRs to `main` are welcome. Content lives in `content/hello-world/` — one manual per repo, six section bundles (`editing/`, `set-up/`, `operate/`, `things-that-broke/`, `customize/`, `teardown/`, each `index.md` + code). Shipped here: `editing/`, `customize/`, `what-is-a-field-manual/`; template placeholders: `set-up/`, `operate/`, `things-that-broke/`, `teardown/`. New manual = clone this repo and retitle `content/hello-world/_index.md`. Run the publication gate (below) before opening a PR. Merges to `main` deploy automatically via GitHub Actions; repo Settings → Pages → Source must be **GitHub Actions** (one-time admin step).

Where things live:

- `hugo.yaml` — site config: title, baseURL, languages, markup, outputs, module imports, plus the display-option (variant) axes and customize fields (user, cluster). Chrome defaults (`logo`, `offline_search`, `copyright`, `footer_center_info`, `ui` incl. `docs_section`/`docs_sidebar_root`) live in the Shell module's `hugo.yaml` and are deep-merged; site values win on conflict.
- `content/hello-world/` — the manual: six bundles (`editing/`, `set-up/`, `operate/`, `things-that-broke/`, `customize/`, `teardown/`); shipped: `editing/`, `customize/`, `what-is-a-field-manual/`, template placeholders: `set-up/`, `operate/`, `things-that-broke/`, `teardown/`
- `_archive/` — previous content before the hello-world reset (gitignored; not rendered)
- Layouts, assets, and static (chrome): live in the Shell module (`CAI-Field-Manual-Shell`). See that repo's README for what it exports. To pick up shell updates: edit the Shell import's `version:` hash in `hugo.yaml` to the new commit (e.g. `git ls-remote https://github.com/rh-aiservices-bu/CAI-Field-Manual-Shell main | cut -c1-12`), then re-run the publication gate — Hugo resolves the pinned commit directly, so the build passes even if `go.mod`/`go.sum` are stale. `version:` accepts semver, branch names, or commit hashes (Hugo ≥ 0.150); a commit hash is deliberate — commit-precise, no Shell tagging. To reconcile `go.mod`/`go.sum` after a bump, run `go get github.com/rh-aiservices-bu/CAI-Field-Manual-Shell@<hash>` (don't use `hugo mod get` — it rewrites `go.mod`/`go.sum` to Shell `main`, dirtying the lockfiles; don't use `hugo mod tidy` either — it can prune the Shell entry). Verify `hugo mod graph` shows the pinned pseudo-version and still shows `github.com/pgsty/oink v1.4.0` (the pinned theme). Re-run the publication gate.

The project root stays minimal: the Shell module supplies the layouts and
chrome, the theme module (oink) supplies the navbar chrome, and build outputs
(`public/`, `resources/`) are gitignored and regenerate.

## Disclaimers

- **Orientation, not a support statement.** Commands and versions reflect
  what worked during field work when the manual was written; verify against
  the current official product documentation before relying on them.
- **Not official product documentation.** These are field notes from
  prototypes, maintained by project contributors.
- **Prototype.** Structure, pages, and URLs are under development and may
  change or move.

## Clone and render locally

Prerequisites:

- Hugo Extended ≥ 0.168 (`hugo version`)
- Go ≥ 1.27 — no Node.js needed (Hugo modules fetch the theme)

```sh
git clone https://github.com/rh-aiservices-bu/CAI-Field-Manual-Hello-World.git
cd CAI-Field-Manual-Hello-World
hugo server
```

With a path in `baseURL`
(`https://rh-aiservices-bu.github.io/CAI-Field-Manual-Hello-World/`), the dev
server serves at `http://localhost:1313/CAI-Field-Manual-Hello-World/` — a 404
at the root is expected, not a broken build. Builds use the Shell commit pinned in
`hugo.yaml` `module.imports[].version`, not Shell `main` — deliberate, commit-precise;
the `hugo.work` workspace replacement below overrides it only for testing unpushed Shell work.

To verify a Shell working-tree change locally (before it is pushed), add a
workspace replacement — create `hugo.work` in this repo root containing:

```
go 1.27.3

use .

replace github.com/rh-aiservices-bu/CAI-Field-Manual-Shell => ../CAI-Field-Manual-Shell
```

Then run with the workspace:

```sh
HUGO_MODULE_WORKSPACE=hugo.work hugo server
```

Verify with `hugo config` / `hugo mod graph` that the replacement is active.
`hugo.work` is gitignored — never commit it.

### Publication gate

One implementation, used locally and by CI (both the deploy build and PR
checks run it). Locally the raw build is enough:

```sh
hugo --cleanDestinationDir --gc --minify --environment production \
  --printPathWarnings 2>&1 | grep -i warn | grep -v "css.Sass: libsass"
```

In CI the gate runs via the Shell checkout:

```sh
shell/scripts/publication-gate.sh --baseURL "https://example.invalid/docs/"
```

The build must succeed and the only WARN allowed is the known oink libsass
deprecation; anything else fails with exit 1.

Optional contract check (needs sibling Shell clone + `public/` from the
production build):

```sh
sh ../CAI-Field-Manual-Shell/scripts/check-contracts.sh
```

Note: `--panicOnWarning` is intentionally not used. The pinned theme version
(v1.4.0) still transpiles SCSS with Hugo's deprecated LibSass, which emits a
WARNING on Hugo ≥ 0.153 and would abort the build. The gate asserts the build
emits no WARN lines other than the known libsass one. Revisit when the theme
moves to the Dart Sass transpiler.

## One-time admin

- **GitHub Pages**: Settings → Pages → Source = **GitHub Actions**.
