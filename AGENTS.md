# Repository Guidance

Hugo Extended >= 0.168 + Go >= 1.27 documentation site (theme via Hugo modules,
no Node.js). README.md is the source of truth for setup and architecture.

This repo is the **Hello World field manual site** — it owns all content
(landing, doctrine, manuals) and imports
[`CAI-Field-Manual-Shell`](https://github.com/rh-aiservices-bu/CAI-Field-Manual-Shell)
as a Hugo module for look-and-feel and rules. Layouts, assets, static, and
scripts live in the Shell repo — propose shell/chrome changes there, not here.
(The pinned oink-file re-diff rule applies to the Shell repo's `layouts/` and
`assets/js/` files.)

## Before you push: the publication gate (mandatory)

There is no test suite. The only correctness check is the production build:

```sh
hugo --cleanDestinationDir --gc --minify --environment production \
  --printPathWarnings 2>&1 | grep -i warn | grep -v "css.Sass: libsass"
```

No output = clean. Build failure or any other WARN = not done. Fix and re-run.
Note the root 404 is expected: with a path in baseURL the dev server serves at
`http://localhost:1313/CAI-Field-Manual-Hello-World/`.

In CI the gate runs via `shell/scripts/publication-gate.sh` (see `.github/workflows/github-pages.yaml`);
locally the raw pipeline above works without the sibling checkout.

## Contract check (optional, needs sibling clone)

After a production build, validate dual-consumption contracts against the sibling
Shell checkout:

```sh
sh ../CAI-Field-Manual-Shell/scripts/check-contracts.sh
```

Requires `public/` from the production build and checks that every variant axis/option
and customize token used in `content/` is declared in this repo's `hugo.yaml`.

## Content rules
- Manuals live in `content/field-manual/<manual>/`. New manual = duplicate the
  blank skeleton in `content/field-manual/hello-world/`.
- Author doctrine: read `content/writing-the-field-manual/conventions.md` before
  writing manual pages. Six conventions: chronological information architecture
  (field-work timeline → `1..N` chapters with `N.M` children; manual name in the
  selector above the left nav; `h1/h2/h3` right TOC), status-gate verification
  (every command + verify + literal expected output), inline known issues,
  variant selector axes, customize tokens, dual consumption.
- Nav shape (target, not yet shipped): left nav must read `1` → `N` top chapters
  with two-level fold/unfold (`N.M`), header is `CAI Field Manual` only, manual
  name sits in the selector directly above the tree. Levers (no pinned-file
  edits): per-manual `sidebar_root_for: self` + `params.ui.sidebar_root_menu:
  true` (`layouts/_partials/shell/root-menu.html:19-25` above `shell/sidebar.html:50`;
  `sidebar-root.html:9-23` resolves per-manual root; `sidebar-root-for` semantics
  in `sidebar-root.html`/`config.html:33-35`). **Selector scope:** `shell/root-
  menu-roots.html:7-26` seeds the dropdown from *every* `Home.Sections`
  (unless `sidebar_root_menu: false`) plus `sidebar_root_for: self` roots (TEST B
  pop showed `field-manual`, `writing-the-field-manual`, `chat`, etc. together);
  to present this as a manual picker, every non-manual top-level section
  (`what-is-a-field-manual`, `writing-the-field-manual`, `potential-improvements`,
  `chat`, …) must carry `sidebar_root_menu: false` and each manual must be a
  top-level section or `sidebar_root_for: self` root — otherwise the dropdown is
  a site-section switcher, not a manual picker. Follow with a sanctioned
  `assets/scss/_styles_project.scss` counter fix so the per-manual root row
  (`ul--0`, `td-shell-tree__item--root`) does not consume `1.` — e.g.
  `& > .td-shell-tree__list:has(> .td-shell-tree__item--root){counter-reset:none}`
  + `.td-shell-tree__item--root{counter-increment:none}` so chapters render
  `1.` and children `1.1` (bare `counter-increment:none` alone yields `0.1` via
  `counters(td-nav,".")`). Verify in a throwaway production build
  (`hugo --cleanDestinationDir --gc --minify --environment production
  --printPathWarnings`) that `::before` renders `1.` and prev/next pager still
  roots at `FirstSection` (`layouts/_partials/pager-state.html` unchanged).
  **Gap:** site today uses `params.ui.docs_sidebar_root: home` with whole-site
  tree (hello-world at `list--3` → `N.M.K` via `counters()` at
  `_styles_project.scss:187-215`); this nav is target — implement the levers
  and scope filter above before claiming `hello-world` demonstrates it (per
  `conventions.md:8`).
- Display options: variant blocks `{{< variant mode gitops >}}` (also
  `manual`, `scripted`) and `{{< variant version "3.4" >}}`. Option ids must
  exist in this repo's `hugo.yaml` `params.variants` (validated by
  check-contracts.sh). Never delete a variant block to fix a rendering
  complaint — markdown/llms.txt outputs show ALL variants.
- Customize tokens: `{{< customize user >}}` and `{{< customize cluster >}}`.
  Token ids must exist in this repo's `hugo.yaml` `params.customize`. Never
  hardcode names or cluster hosts.

## Do not touch without re-diffing

Chrome files live in the Shell repo. If you propose shell changes there, these
files are pinned oink v1.4.0 copies — each carries a provenance comment naming
its one deliberate change. Any edit beyond that change requires re-diffing
against `~/go/pkg/mod/github.com/pgsty/oink@v1.4.0/`:
`layouts/docs/baseof.html`, `layouts/_partials/shell/sidebar-tree.html`,
`layouts/_partials/pager-state.html`, `assets/js/action-registry.js`,
`assets/js/page-actions.js`, `layouts/_partials/actions/context.html`,
`layouts/_partials/actions/title-menu.html`.

Sanctioned extension points (free to edit in Shell): `td-variant-selector.html`,
`hooks/head-end.html`, `hooks/body-end.html`, the customize shortcodes,
`static/js/`, `static/css/`, `hugo.yaml`, `data/home.yaml`, `content/`.

## Verification for JS changes

Interactive behavior lives in `static/js/` and `assets/js/` (in the Shell
module). If you change it, verify the rendered page actually works (dev server
+ browser), not just that the build passes.

## Updating the shell

```sh
hugo mod get -u github.com/rh-aiservices-bu/CAI-Field-Manual-Shell
```

Then re-run the publication gate.

## omp agent (CI)

The repo runs an [omp](https://omp.sh)-based coding agent via
`.github/workflows/omp.yml`. Comment `/omp <request>` on any issue or PR
review thread to invoke it; it reads this file and README.md, and opens a
draft PR from a branch `omp/issue-<n>` for any change. Configuration is three
repo-level settings — `AGENT_BASE_URL` (variable), `AGENT_MODEL` (variable),
`AGENT_API_KEY` (secret) — never in files. The publication gate above applies
to the agent exactly as it applies to you.
