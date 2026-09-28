# AGENTS.md — layer-build-toolchain

Standalone candy repo for the `build-toolchain` layer. The candy lives in
`charly.yml` at the repo root: the native-compilation package list with per-distro
sections, the `CCACHE_DISABLE` environment, an ordered `plan:` of build-time
`check:` steps, and the embedded `skill:` entity projected into the marketplace
corpus as `/charly-coder:build-toolchain`. There is no source tree and no service
of its own.

Canonical files:

- `charly.yml` — the `build-toolchain:` candy entity and the `build-toolchain-skill:` skill entity.
- `.github/workflows/` — the org-wide `charly/pr-validator` gate; there is no per-repo candy gate.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-coder:build-toolchain` — the owning skill. The package grouping, the
  per-distro parity story, and which builder stages consume it. Load before
  editing or troubleshooting the candy.
- `/charly-distros:fedora-builder` / `/charly-distros:arch-builder` — the
  multi-stage builders that depend on this candy's binaries. Load when a builder
  stage's compilation fails.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, package sections, per-distro `distro:`
  blocks). Load before editing any entity field or plan step.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the org-wide `charly/pr-validator` (required check
  `validate / validate`); there is no per-repo candy gate.
- There is no live bed: the candy is package-only, so the evidence is its
  `plan:` `check:` steps, which assert each headline binary (`gcc`, `make`,
  `cmake`, `cargo`, `nasm`, `gdb`, `ccache`, `git`) exists and reports a version.
  Real compilation is exercised by the consuming builder stages.

## Modify this repo

- Edit the `build-toolchain:` candy entity AND the `build-toolchain-skill:` skill
  entity in `charly.yml` together. The skill is the projected usage source, so a
  package or behaviour change that is not mirrored in the skill leaves the corpus
  stale.
- Add shared system libraries under the appropriate per-distro `package:` section
  (keep `rpm:`/`pac:`/`deb:` parity); truly box-specific deps stay in their own
  box's candy. New behaviour claims go in `plan:` as observable `check:` steps.
- Keep `version:` at the schema stamp the pinned CI charly supports.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
