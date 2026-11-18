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
- Manual lives in `content/hello-world/` — one manual per repo. New manual = clone this repo and retitle `content/hello-world/_index.md` (6 section bundles: editing, set-up, operate, things-that-broke, customize, teardown, each `index.md` + code).
- Previous doctrine (`writing-the-field-manual/conventions.md`) and auxiliary sections (`chat`, `potential-improvements`, `what-is-a-field-manual`, `why-this-approach`) were archived to `_archive/` before the hello-world reset — see `_archive/README.md`. Restore them only if you intend to reintroduce multi-manual scope.
- Author doctrine: the six conventions are chronological information architecture (field-work timeline → `1..N` chapters with `N.M` children; manual name in the selector above the left nav; `h1/h2/h3` right TOC), status-gate verification (every command + verify + literal expected output), inline known issues, variant selector axes, customize tokens, dual consumption. Until doctrine is restored, follow them from the archived `conventions.md` or ADRs.
- Nav shape (target, not yet shipped): left nav must read `1` → `N` top chapters with two-level fold/unfold (`N.M`), header is `CAI Field Manual` only, manual name sits in the selector directly above the tree. Levers: per-manual `sidebar_root_for: self` + `params.ui.sidebar_root_menu: true` (`layouts/_partials/shell/root-menu.html:19-25` above `shell/sidebar.html:50`; `sidebar-root.html:9-23` resolves per-manual root; `sidebar-root-for` semantics in `sidebar-root.html`/`config.html:33-35`) plus Shell `layouts/_partials/shell/sidebar-tree.html` cover promotion (third deliberate change: Home and `sidebar_root_for: self` are covers, children render at `ul--1`/`ulNr 1`; small-screen `d-md-none` cover row is `IsHome`-only because self-roots already have the always-visible `td-shell-root--static` link in the header). **Selector scope:** `shell/root-menu-roots.html:7-26` seeds the dropdown from *every* `Home.Sections` (unless `sidebar_root_menu: false`) plus `sidebar_root_for: self` roots. The per-manual SCSS guard `& > .td-shell-tree__list:has(> .td-shell-tree__item--root){counter-reset:none}` + `.td-shell-tree__item--root{counter-increment:none}` now only serves `docs-sidebar-tree.html` (`data/docs_nav.json` path); `sidebar-tree.html` no longer emits a `ul--0`/`td-shell-tree__item--root` cover row. Verify in a throwaway production build (`hugo --cleanDestinationDir --gc --minify --environment production --printPathWarnings`) that `::before` renders `1.` and prev/next pager still roots at `FirstSection` (`layouts/_partials/pager-state.html` unchanged).
- Display options: variant blocks `{{< variant mode gitops >}}` (also `manual`, `scripted`) and `{{< variant version "3.4" >}}`. Option ids must exist in this repo's `hugo.yaml` `params.variants` (validated by check-contracts.sh). Never delete a variant block to fix a rendering complaint — markdown/llms.txt outputs show ALL variants.
- Customize tokens: `{{< customize user >}}` and `{{< customize cluster >}}`. Token ids must exist in this repo's `hugo.yaml` `params.customize`. Never hardcode names or cluster hosts.

## Do not touch without re-diffing

Chrome files live in the Shell repo. If you propose shell changes there, these
files are pinned oink v1.4.0 copies — each carries a provenance comment naming
its one deliberate change. Any edit beyond that change requires re-diffing
against `~/go/pkg/mod/github.com/pgsty/oink@v1.4.0/`:
`layouts/docs/baseof.html`, `layouts/_partials/shell/sidebar-tree.html`,
`layouts/_partials/pager-state.html`, `assets/js/action-registry.js`,
`assets/js/page-actions.js`, `layouts/_partials/actions/context.html`,
`layouts/_partials/actions/title-menu.html`. The Shell also ships a fixup
`layouts/_partials/favicons.html` (hardcoded favicon list — no re-diff needed).

Sanctioned extension points in Shell (free to edit there):
`td-variant-selector.html`, `hooks/head-end.html`, `hooks/body-end.html`,
the customize shortcodes, `static/js/`, `static/css/`, `assets/scss/_styles_project.scss`.

Local to this repo (free to edit here): `hugo.yaml` (site config: title, baseURL, variants, customize, menus), `data/home.yaml`, `content/hello-world/` (the manual — 6 bundles). Archived content lives in `_archive/` (gitignored; see `_archive/README.md`).

## Verification for JS changes

Interactive behavior lives in `static/js/` and `assets/js/` (in the Shell
module). If you change it, verify the rendered page actually works (dev server
+ browser), not just that the build passes.

## Updating the shell

Edit the Shell import's `version:` hash in `hugo.yaml` to the new commit
(e.g. `git ls-remote https://github.com/rh-aiservices-bu/CAI-Field-Manual-Shell main | cut -c1-12`).
`version:` accepts semver, branch names, or commit hashes (Hugo ≥ 0.150) — a commit hash is
deliberate, commit-precise, no Shell tagging. Hugo resolves the pinned commit directly, so the
build passes even with stale `go.mod`/`go.sum`; to keep them in sync after a bump run
`go get github.com/rh-aiservices-bu/CAI-Field-Manual-Shell@<hash>` (don't use `hugo mod get` —
it rewrites `go.mod`/`go.sum` to Shell `main`, dirtying the lockfiles; don't use `hugo mod tidy`
either — it can prune the Shell entry). Verify `hugo mod graph` shows the pinned pseudo-version
and still shows `github.com/pgsty/oink v1.4.0` (the pinned theme). Re-run the publication gate.
