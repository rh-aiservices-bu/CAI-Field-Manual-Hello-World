# Backlog — CAI Field Manual — Hello World

Findings logged 2026-11-19 from a four-pass read-only repo review
(content/doctrine, security, config/build/CI, scripts/aux); each finding's
evidence re-checked against the working tree before logging. Scope: this
repo only — items that must be judged in the CAI-Field-Manual-Shell repo
live under "Open questions".

Rules: IDs are stable, never renumbered; mark `- [x]` when an item ships
and append the PR/commit. New findings take the next free B-number.

## Open

### B1 (HIGH) — Variant axes advertised everywhere, used nowhere

- [ ] Demonstrate the variant convention or soften the claims.
- Evidence: `grep -rln '{{< variant' content/` returns nothing — no shipped
  page carries a single `{{< variant ... >}}` block, while `hugo.yaml:79-102`
  declares axes `version` (3.4/3.5/3.6) and `mode` (gitops/manual/scripted).
- Contradicting copy: `content/hello-world/what-is-a-field-manual/showcase/index.md:19`
  says the selector "starts at RHAIE Version 3.6 ... so 3.4 and 3.5 notes
  stay hidden" (there is nothing to hide), and
  `content/hello-world/what-is-a-field-manual/writing-the-field-manual/index.md:17`
  claims "The Hello World chapters demonstrate all six" conventions.
- Fix: add demo variant blocks (natural home:
  `content/hello-world/editing/index.md`) and reword both claims, or soften
  the claims.

### B2 (MED) — how-to-use-it promises a flattened per-manual bundle nothing produces
- [ ] Enable LLMSFULL or drop the promise.
- Evidence: `content/hello-world/what-is-a-field-manual/how-to-use-it/index.md:20`
  advertises "one flattened markdown file per manual"; `hugo.yaml:53-56`
  documents LLMSFULL as per-section opt-in and no `outputs:` front matter
  exists anywhere in `content/`.
- Fix: add `outputs: [HTML, RSS, print, markdown, LLMSFULL]` to
  `content/hello-world/_index.md`, or drop the sentence.

### B3 (LOW) — editing §5: three commands, one Expected block
- [ ] Split into three command+Expected pairs.
- Evidence: `content/hello-world/editing/index.md:173-176` runs
  `hugo new content ...`, `hugo config | grep -i baseurl`, and
  `hugo mod graph` in one block; only `hugo mod graph` gets literal
  expected output (`editing/index.md:179-183`). Doctrine: every command
  gets a verify with literal expected output.

### B4 (LOW) — showcase AI-actions table omits "Copy markdown"
- [ ] Add the missing row.
- Evidence: the AI-actions table in
  `content/hello-world/what-is-a-field-manual/showcase/index.md:47-54`
  lists Open in ChatGPT/Claude/Gemini, Feed it to my AI Agent, View
  markdown, View edit history — while the screenshot alt text
  (`showcase/index.md:23`) and README both advertise Copy Markdown too.

### B5 (LOW) — page title "Customizing" vs "Customize this!" used everywhere else
- [ ] Align the name.
- Evidence: `content/hello-world/customize/index.md:2` sets
  `title: Customizing`; the `data/home.yaml:38` card, `README.md:36`, and
  `content/hello-world/what-is-a-field-manual/how-to-use-it/index.md:13`
  all say "Customize this!".
- Fix: retitle the page (check nav/pager wording after) or change the
  other references.

### B6 (LOW) — home.yaml CTA nits
- [ ] Fix capitalization and duplication.
- Evidence: `data/home.yaml:50` `label: Github Issues` → "GitHub Issues";
  the card's label and text ("Report issues here") duplicate each other.

### B7 (LOW) — content/_index.md front matter lacks weight/icon
- [ ] Align `content/_index.md` front matter with the manual's landing page.
- Evidence: `content/_index.md` declares only `title`, `description`, and a
  `cascade:` block — no `weight` or `icon`; the sibling landing page
  `content/hello-world/_index.md:4-5` sets `weight: 10` and `icon:`.
- Fix: add matching `weight`/`icon` (or confirm Home intentionally omits
  them) so section front matter is consistent.

### B8 (MED) — Visualizer loads executable third-party JS/CSS from a personal GitHub Pages account, unpinned
- [ ] Vendor or pin the FlowStory library.
- Evidence: `flow.html:7` and `static/visualizers/fm-flow.html:7` load
  `<link rel="stylesheet" href="https://noyitz.github.io/flowstory/templates/style.css">`,
  and `flow.html:55` / `fm-flow.html:55`
  `import { FlowStory } from 'https://noyitz.github.io/flowstory/src/index.js';`
  — every view of the flow page executes whatever is currently deployed to
  that personal Pages site; no version pin, and ES-module imports cannot
  use SRI, so there is no integrity check.
- Fix: vendor the library into `static/` (or import a pinned self-hosted
  copy) so the published site never loads executable JS from
  noyitz.github.io. Severity honest for a public docs page: medium, not
  high — no secrets on the page.

### B9 (MED) — CI runs the publication gate from Shell `ref: main`; actions tag-pinned
- [ ] Pin the gate-script source and the actions.
- Evidence: both jobs check out the Shell at
  `.github/workflows/github-pages.yaml:42` / `:98` `ref: main` while the
  module is pinned (`go.mod:7`
  `...Shell v0.0.0-20261118202543-4adf78646ad1`, `hugo.yaml:119`
  `version: 4adf78646ad1`); with `GOWORK/HUGO_MODULE_WORKSPACE: off`
  (`github-pages.yaml:23-24`) that checkout is dead weight for the Hugo
  build — its only consumed artifact is
  `shell/scripts/publication-gate.sh` (`github-pages.yaml:78`, `:129`), so
  the repo's sole correctness check runs from an unpinned mutable ref.
  All actions are tag-pinned, none SHA-pinned: `checkout@v7` (`:34,39,90,95`),
  `setup-go@v7` (`:46,102`), `configure-pages@v6` (`:54`),
  `peaceiris/actions-hugo@v3` (`:58,109`), `upload-pages-artifact@v5`
  (`:81`), `deploy-pages@v5` (`:142`).
- Fix: `ref: 4adf78646ad1` (or a pinned hash) for the Shell checkout, and
  SHA-pin the actions — `peaceiris/actions-hugo` is the only third-party
  one.

### B10 (LOW) — flow diagram's bundle list omits what-is-a-field-manual and reads as a shipped inventory
- [ ] Update the diagram's Hello-World node.
- Evidence: `flow.html:104` / `static/visualizers/fm-flow.html:104` set
  `sublabel: "template · 6 bundles"`, and `:146-147` describe "Blank
  skeleton with 6 bundles" with content
  `(editing · set-up · operate · things-that-broke · customize · teardown)`.
  That six-bundle list mirrors AGENTS.md's template definition (which also
  omits `what-is-a-field-manual/`), but the repo actually ships
  `editing/`, `customize/`, `what-is-a-field-manual/` — and unlike the
  README, this diagram renders to end readers on the shipped flow page
  (`content/hello-world/what-is-a-field-manual/flow/index.md:8`).
- Fix: name the shipped bundles (or mark the four template ones as
  placeholders) so readers of the flow page see the real state.

### B11 (MED) — CI installs unpinned sass-embedded the build doesn't use
- [ ] Drop the steps or pin a version.
- Evidence: `.github/workflows/github-pages.yaml:64` and `:115`
  `run: npm install -g sass-embedded` (unpinned `latest`, from npm, on
  every CI run) — but `hugo.yaml` has no `transpiler`/`dartsass` key and
  `README.md:152` states the pinned theme "still transpiles SCSS with
  Hugo's deprecated LibSass"; `README.md:90` also claims "no Node.js
  needed".
- Fix: remove the sass-embedded install steps until the theme switches to
  Dart Sass (then pin the version), or reword the README prerequisite.

### B12 (LOW) — serve.sh --stop kills a recycled PID from a stale pidfile
- [ ] Verify PID ownership before killing.
- Evidence: `scripts/serve.sh:131` reads `pid="$(cat "$PIDFILE" ...)"`,
  then `:139` `pkill -P "$pid"`, `:147` `kill -9 "$pid"` — only a
  liveness check (`kill -0`), no command-line match; after a crash/reboot
  a recycled PID belonging to any local process gets SIGKILLed with its
  children.
- Fix: match `ps -p "$pid" -o command=` against this serve.sh supervisor
  before killing. Low: local dev tool, pidfile write already implies repo
  write access.

### B13 (LOW) — serve.sh --status exits 0 when the server is alive but unhealthy
- [ ] Make --status exit nonzero on health FAIL.
- Evidence: `scripts/serve.sh:113-114` — alive PID + failed probe prints
  `warn "health  FAIL (not responding)"` then `exit 0`, while the
  stale-pidfile branch (`:117-118`) exits 1; a monitoring caller cannot
  distinguish healthy from hung.
- Fix: `exit 1` (or a distinct code) on the health-FAIL path.

### B14 (LOW) — pr-gate job inherits `pages: write` / `id-token: write` on untrusted checkouts
- [ ] Add job-level permissions to pr-gate.
- Evidence: workflow-level `.github/workflows/github-pages.yaml:11-14`
  grants `pages: write` / `id-token: write`; the pr-gate job
  (`:84-88`, `if: github.event_name == 'pull_request'`) has no job-level
  override while checking out PR code. Currently inert — pr-gate has no
  `secrets.*`, deploy is gated to push/workflow_dispatch (`:138`) — but
  any future step or compromised action there inherits OIDC + Pages write.
- Fix: job-level `permissions: contents: read` on pr-gate
  (defense-in-depth).

### B15 (LOW) — FlowStory load-error overlay interpolates error text into innerHTML
- [ ] Build the error overlay with textContent.
- Evidence: `flow.html:252` / `static/visualizers/fm-flow.html:252`
  `document.body.innerHTML = \`...<pre>${e.message}\n\n${e.stack||''}</pre>\`;`
  — unescaped, though the only source is a FlowStory load failure;
  attacker-controlled only via B8's remote library.
- Fix: use `textContent` for the message/stack nodes (hygiene).

### B16 (LOW) — visualizer shortcode has no guards for missing/misspelled params
- [ ] Fail loudly on bad `embed`/`file` values.
- Evidence: `layouts/_shortcodes/visualizer.html:8` and `:30`
  `{{ if eq (.Get "embed") "true" }}` with no default or warning —
  `embed="True"`/`embed=1` silently renders the bare link at `:52`;
  missing `file` yields `href=""` via `relURL` (`:33-34,52`) instead of a
  template error.
- Fix: validate params (e.g. `with`/`default` guards or `errorf`) so a
  typo fails the build.

### B17 (LOW) — LICENSE ships the un-filled Apache boilerplate copyright
- [ ] Fill in the copyright line.
- Evidence: `LICENSE:189` `Copyright [yyyy] [name of copyright owner]`.
- Fix: replace the bracketed placeholder with the project copyright.

### B18 (LOW) — README "Where things live" omits four shipped root files
- [ ] Extend the README list (and AGENTS.md wording).
- Evidence: `README.md:64-69` lists `hugo.yaml`, `content/hello-world/`,
  `_archive/`, and the Shell module — `flow.html`,
  `layouts/_shortcodes/visualizer.html`, `static/visualizers/fm-flow.html`,
  and `archetypes/default.md` are undocumented; the `layouts/`/`static/`
  files also sit oddly under AGENTS.md's "Layouts, assets, static, and
  scripts live in the Shell repo".
- Fix: document the local shortcode/visualizer/archetype files and note
  the local-layout exception.

### B19 (LOW) — zh/fr language blocks declared but disabled with zero translated content
- [ ] Confirm the disabled-language declarations are still needed.
- Evidence: `hugo.yaml:7` `disableLanguages: [zh, fr]` with full blocks at
  `hugo.yaml:23-33`; no `*.zh.md`/`*.fr.md` files exist anywhere in
  `content/`. The rationale comment (`hugo.yaml:21-22`) says they are kept
  "so Hugo recognizes .zh.md and .fr.md as translations instead of treating
  them as duplicate English pages".
- Fix: keep if the Shell contract needs the declarations (see Open
  questions), else drop the blocks and `disableLanguages`.

### B20 (LOW) — googleAnalytics ID committed but undocumented
- [ ] Add a README line noting GA on the published site.
- Evidence: `hugo.yaml:47-48` `googleAnalytics:` / `ID: G-7ZXCS2MF1D`
  (public-by-design, not a credential) appears nowhere in README or
  AGENTS.md.

### B21 (LOW) — only one archetype; its defaults target leaf pages, not section bundles
- [ ] Confirm or add a section archetype.
- Evidence: `archetypes/` contains only `default.md`, whose front matter
  (`archetypes/default.md:4-5` `weight: 10` / `icon: fa-solid fa-file-lines`)
  suits `hugo new content` leaf pages, while manual bundles are `_index.md`
  sections.
- Fix: confirm intent or add an `index-bundle.md` archetype for new
  chapters.

### B22 (LOW) — kill_tree's forceful child-kill fallback is unreachable
- [ ] Fix the ordering or switch to a process-group kill.
- Evidence: `scripts/serve.sh:71-78` — `:76` SIGKILLs the target PID
  *before* `:77` runs `pkill -9 -P "$pid"`, but children reparent to init
  the moment the parent dies, so the forceful child-kill fallback can
  never match anything (dead code); only `:73`'s initial `pkill -P` ever
  reaches children. Bounded: hugo exits on SIGTERM (`:74`) in the normal
  path, and the script warns when the port stays bound
  (`serve.sh:152-153`).
- Fix: kill children before escalating on the parent, or run the
  supervisor in its own process group (`setsid`) and kill `-- -PGID`.

### B23 (LOW) — serve.sh never validates PORT/BIND before building the probe URL
- [ ] Validate PORT (numeric) and BIND before probing.
- Evidence: `scripts/serve.sh:29-30` accept any caller value; `:61` builds
  `local url="http://${BIND}:${PORT}${HEALTH_PATH}"` and `:67` opens
  `/dev/tcp/"$BIND"/"$PORT"`. No eval, target loopback by default — a
  malformed value only fails the probe (hardening, not a vuln).

### B24 (LOW) — root flow.html is an unreferenced dead copy of static/visualizers/fm-flow.html
- [ ] Delete the root copy (or document why it exists).
- Evidence: `cmp flow.html static/visualizers/fm-flow.html` → byte-identical
  (256 lines each); repo-wide grep finds the only consumer is
  `content/hello-world/what-is-a-field-manual/flow/index.md:8`
  `{{< visualizer file="visualizers/fm-flow.html" ... >}}` (resolves to the
  static/ copy); nothing references root `flow.html`, and it is absent
  from README's "Where things live" and AGENTS.md's file list. Risk:
  one-sided future edits ship a stale diagram.

## Open questions — verify in CAI-Field-Manual-Shell
- Does the Shell define its own `layouts/_shortcodes/visualizer.html` that
  this repo's copy shadows via the union FS? (This copy's header comment,
  `layouts/_shortcodes/visualizer.html:2-6`, says it exists to avoid
  shadowing the Shell's `assets/scss/_styles_project.scss`; if Shell ships
  a newer shortcode, the local copy silently wins.)
- Do Shell's `shell/scripts/publication-gate.sh` / `check-contracts.sh`
  reference root `flow.html` — i.e. is the dead root copy load-bearing for
  a cross-repo contract (B24)?
- Does the Shell's `static/` also ship `visualizers/fm-flow.html`, and
  which copy does `relURL` resolve in a Library aggregate build?
- Do the Shell's config/contracts require the zh/fr `languages:` blocks
  that `hugo.yaml:7` disables (B19)?
- Is `go.sum` tidy — are the five stale Shell pseudo-versions
  (`go.sum:3-12`) prunable under the repo's `go get`-only policy (no
  `hugo mod tidy`)?
- Is `shell/scripts/publication-gate.sh` stable across Shell commits,
  given CI runs it from `ref: main` rather than the pinned 4adf78646ad1
  (B9)?

## Reviewed clean (no action)
- Every customize token used (`user`, `cluster`) is declared in
  `hugo.yaml:107-109`; no invalid variant/customize ids exist; the
  variants/customize param shape is well-formed (each axis has `neutral`
  + `default` among its option ids).
- All 12 `relref` internal links resolve; referenced showcase PNGs exist.
- No body `h1` on any page; heading nesting never skips levels.
- No TODO/FIXME/placeholder markers in content (only the two intentional
  `draft: true` teaching mentions); none in hugo.yaml, go.mod, go.sum,
  .gitignore, workflow, LICENSE, archetypes, serve.sh, or the flow HTML.
- `data/home.yaml` card links resolve; stub cards intentionally use
  `url: ""`; CTA is the correct GitHub issues URL.
- Customize defaults stated in page copy (guest, cluster suffix, Show all,
  3.6) match `hugo.yaml` params.
- Content-review claims that failed re-verification, dropped: README
  placeholder wording is actually correct (`README.md:62,67` explicitly
  separate "Shipped here: …" from "template placeholders: …"); the
  editing §4 tree diagram already annotates all four stub bundles
  (`content/hello-world/editing/index.md:142-148`
  `# template placeholder — create when you write this chapter`); a
  scripts-review claim that the daemon re-exec drops
  `--renderToMemory --printPathWarnings` is disproven — the daemon child
  rebuilds `HUGO_ARGS` with the fixed flags (`scripts/serve.sh:197`).
- serve.sh: all expansions quoted, hugo launched from arrays
  (`scripts/serve.sh:265-272`); no `eval`/`curl|bash`; daemon env
  exports cover PORT/BIND/HEALTH_*/PIDFILE/LOGFILE/HUGO_BIN
  (`serve.sh:36-38`); `.hugo_build.lock` removal correctly gated on
  port-free + age (`serve.sh:281-293`); backoff math
  (1→2→4→8→10, reset on health OK) sound; port-in-use preflight warns and
  lets hugo fail into the crash-loop (`serve.sh:186-191`); INT/TERM-only
  teardown with pidfile cleanup (`serve.sh:230,238-244`); exec bit is
  0755.
- Hardcoded hosts/names (`opentlc.com`, `ocp.`, `guest`, `admin`): zero
  matches in serve.sh, flow.html, fm-flow.html, layouts/, archetypes/ —
  AGENTS.md's no-hardcode rule holds for these files.
- `layouts/_shortcodes/visualizer.html` sinks: only author-supplied params
  through `relURL` (URL-escaped) plus a numeric `.Ordinal` id — no
  innerHTML/eval/document.write/postMessage; `archetypes/default.md` is a
  title pipeline over the author's own name; sole shortcode consumer is
  `content/hello-world/what-is-a-field-manual/flow/index.md:8`.
- Secrets sweep: only hit is `hugo.yaml:48` GA measurement ID
  `G-7ZXCS2MF1D` (public by design); no token-shaped strings anywhere.
  Client-side pattern sweep (postMessage, localStorage, sessionStorage,
  document.write, eval, new Function, fetch, importScripts): zero hits in
  shipped HTML/JS.
- go.mod/go.sum consistency: module path matches repo, `go 1.27.3`
  matches the workflow's `GO_VERSION: 1.27.3` and README's "Go ≥ 1.27";
  oink `v1.4.0` indirect pin present; Shell pseudo-version
  (`go.mod:7`) matches `hugo.yaml:119` exactly; `HUGO_VERSION: "0.168.0"`
  matches `hugoVersion.min: 0.168.0` and README.
- `.gitignore` covers `public/`, `resources/`, `.hugo_cache/`,
  `.hugo_build.lock`, `.DS_Store`, `/hugo.direct.sum`, `/_archive/`,
  `/hugo.work`, `/hugo.work.sum`; serve.sh's pid/log default into
  `.hugo_cache/` (`scripts/serve.sh:33-34`), so those artifacts are
  covered too.
- Workflow deploy chain gated: build skips `pull_request`
  (`github-pages.yaml:30`), deploy requires push/workflow_dispatch
  (`:138`) — PR runs cannot reach `deploy-pages`.
- Hardening note (not a defect): `github-pages.yaml:78` interpolates
  `${{ steps.pages.outputs.base_url }}` into a run block, but it is
  shell-quoted, comes from admin-controlled Pages settings, and the build
  job never runs on pull_request; the robust pattern would be an `env:`
  passthrough.
- LICENSE is otherwise intact Apache-2.0 apart from B17.
