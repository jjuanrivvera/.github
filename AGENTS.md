# AGENTS.md — jjuanrivvera CLI fleet conventions

Org-wide conventions for the **cliwright CLI fleet**: the ~9 Go + Cobra + GoReleaser CLIs under
`github.com/jjuanrivvera`. This file is the ORG layer — the shared infrastructure and house rules
that apply across repos. The GENERIC, per-CLI engineering patterns (and *when* to apply each) live
in **cliwright** (`jjuanrivvera/cliwright`, `skills/cliwright/GOAL.md`), which is a **public** tool
and deliberately carries nothing org-specific. Rule of thumb: *if it names `jjuanrivvera`, a tap
repo, or a reusable workflow, it belongs here; if any user of any org would want it, it belongs in
cliwright.*

## The fleet

| CLI (repo) | binary | API |
|---|---|---|
| garminctl | garminctl | Garmin Connect (wraps go-garmin) |
| cwctl → **wootctl** | wootctl (+`chatwoot` drop-in) | Chatwoot |
| slackctl | slackctl | Slack |
| tgctl | tgctl | Telegram Bot |
| n8n-cli | n8nctl | n8n |
| alegra-cli | alegra | Alegra (accounting) |
| adguard-cli | adguard-home | AdGuard Home |
| lemon-squeezy-cli | lsqueezy | Lemon Squeezy |
| canvas-cli | canvas | Canvas LMS (the most mature) |

Module path `github.com/jjuanrivvera/<repo>`. Each CLI also has two satellite repos: a Homebrew
tap `homebrew-<name>` and a Scoop bucket `scoop-<name>`.

## CI — one reusable workflow

Every CLI's `.github/workflows/ci.yml` is a thin caller of the fleet-canonical
**`jjuanrivvera/.github/.github/workflows/go-ci.yml`** (jobs: lint / security / test-matrix / build).
Change fleet CI policy THERE, not in N repos. Inputs (set only the genuine per-repo variation):

- `coverage-threshold` (string, default `"80"`; `""` skips the gate — the gate is inlined
  `go tool cover -func`, no repo script needed)
- `coverpkg` (bool, default false) — add `-coverpkg=./...` when coverage relies on cross-package
  integration tests (e.g. a `commands_test` that drives `internal/api`; lsqueezy)
- `run-gosec` / `run-docs-check` / `run-build` (bools, default true)
- `os-matrix` (JSON array, default 3-OS)

Repo-specific EXTRA jobs stay in the repo's own `ci.yml` (e.g. canvas's binary `integration` job) or
as separate workflow files (n8n `node-catalog`, alegra `smoke`/`spec-sync`, canvas `claude*`).

**Two non-negotiables baked into the reusable, each a lesson paid for once:**
1. **`go-version-file: go.mod`, never `go-version: stable`.** `stable` grabs the newest Go, so
   `govulncheck` runs against an already-patched stdlib and stays green while the declared toolchain
   floor is behind — a real stdlib CVE hides behind a passing CI. Keep each repo's go.mod
   `go`/`toolchain` directive current (fleet is on **go1.25.12**); a `govulncheck ./...` sweep is the
   audit.
2. **Build golangci-lint / gosec / govulncheck from source** (`go install …@latest`) with the job's
   Go — never the prebuilt actions (they lag the toolchain and reject a module on the next Go).

The **cliwright determinism gates** (`make verify`'s spec-check / spec-completeness / dod-check) are
NOT in the reusable — they run locally via `make verify` + the `.githooks` pre-commit hook. If you
want them enforced in CI too, add a `cliwright-gates.yml` reusable (calls `make verify`) — don't
re-inline per repo.

## Releases — one reusable workflow, all casks

Every `.github/workflows/release.yml` delegates to
**`jjuanrivvera/.github/.github/workflows/go-release.yml`** (`secrets: inherit`). Pipeline:
`go-version-file`, buildx + GHCR, cosign@v3 (keyless OIDC), syft SBOM, `goreleaser release --clean`,
Homebrew/Scoop bumps. Input:

- `promote-main` (bool, default false) — git-flow repos (releases tagged on `develop`) opt in; the
  step fast-forwards `main` to the released commit. n8n + alegra use it.

The `.goreleaser.yaml` config stays **per-repo** (it's already validated on every push by the go-ci
build job's `goreleaser check` + snapshot). Cosign signs with the reusable's default cosign — proven
across the fleet (alegra uses it unpinned and signs fine); the old `cosign-release: v2.6.3` pins were
defensive, not load-bearing.

**Distribution standard — Homebrew CASKS (not formulas).** Casks are the modern GoReleaser path for a
prebuilt binary. Every CLI's `.goreleaser` uses `homebrew_casks:` with `binaries: [<bin>]`, a tap
`repository:` pointed at `homebrew-<name>` with `token: "{{ .Env.HOMEBREW_TAP_TOKEN }}"`, and a
`hooks.post.install` that strips the macOS quarantine xattr (unsigned CLI → no Gatekeeper prompt).
`HOMEBREW_TAP_TOKEN` is a repo-scoped PAT (default `GITHUB_TOKEN` can't push cross-repo to the tap).

## Dependabot

Every repo has `.github/dependabot.yml` — grouped weekly `gomod` + `github-actions` (or
actions-only for the no-go-module repos: cliwright, `.github`). This is also the fleet's dependency-
CVE early warning (a grouped bump PR lands before govulncheck goes red on a released dep — the gap
that let adguard's go-sdk CVE slip through).

## Branching, versioning, the gate

- **git-flow**: `develop` integrates, `main` is releases; tag on develop → the release's
  `promote-main` fast-forwards main. Skip pre-release tags (`vX.Y.Z-rc1`) — the reusable guards on
  `!contains(ref_name, '-')`.
- **The gate is `make verify`** (deterministic: fmt/vet/lint/test + spec-check + spec-completeness +
  cover-check ≥80% + dod-check). `make judge` is the one LLM gate; `make accept` = verify + judge.
- Every CLI ships `AGENTS.md` with `CLAUDE.md` as a symlink to it, and a `DECISIONS.md` pinning
  ambiguous API assumptions.

## Known quirks & pending cleanups

- **wootctl never fires its release workflow on a tag push** — a repo-level GitHub quirk from the
  cwctl→wootctl rename (Actions enabled, workflow active, trigger correct, branch pushes fire CI;
  only tag→release is dead, across all versions; re-committing the workflow doesn't fix it).
  **Workaround: `gh workflow run release.yml --ref <tag>`** (the caller keeps `workflow_dispatch`).
- **Formula→Cask tap cleanup pending** for adguard, canvas, alegra: at their NEXT release, delete the
  stale `Formula/<name>-cli.rb` from `homebrew-<name>` — otherwise brew sees a formula+cask name
  collision (they only just converted from formulas).

## Conformance drift (bring these up to standard)

- **adguard-cli** predates several GOAL sections: no enumeration-derived `api-manifest.json` + spec
  gates, no `DECISIONS.md`, no `--dry-run` curl, no `agent guard`, no alias expansion; it hand-writes
  `newXCmd()` instead of the generic core. Its go-ci `run-gosec`/`run-docs-check` are off.
- **n8n-cli** lacks the AES-GCM encrypted-file keyring fallback the other repos ship (keyring-only) —
  breaks on headless hosts.
- **Root construction is inconsistent** across the fleet (n8n global `init()` root, wootctl
  `init()`-queue + `NewRootCmd(deps)`, adguard pure constructor). cliwright is canonicalizing on
  wootctl's hybrid; migrate the others when touched.

## cliwright — the generator

`jjuanrivvera/cliwright` (a Claude Code plugin/skill) generates these CLIs to a single standard,
driven via `/goal` until `make accept` passes. It is **public and org-agnostic**: its templates ship
a **standalone** `ci.yml`/`release.yml` (full inline workflows, NOT a `uses:` of this org repo), so a
stranger's generated CLI has zero dependency on `jjuanrivvera/.github`. When you improve a GENERIC
pattern, fold it into cliwright's GOAL.md/templates (with a *condition* for when to apply it — see its
conditional-patterns catalog); when you improve fleet INFRASTRUCTURE, update this file and the
reusable workflows.
