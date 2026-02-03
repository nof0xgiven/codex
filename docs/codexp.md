# codexp fork notes

This repo is a fork of OpenAI Codex. The `codexp` command is a local alias to the
debug binary built from this fork, using a dedicated Codex home.

## What `codexp` is

`codexp` is an alias that points to the debug build of the Codex CLI binary in
this repo, with a custom CODEX_HOME:

- Binary: `codex-rs/target/debug/codex`
- Home: `/Users/ava/.codex-priority`

If `codexp` is missing, it is defined in your shell configuration (e.g. `.zshrc`).

## Where configuration lives

The priority profile lives in:

- `/Users/ava/.codex-priority/config.toml`

Key fields for the API-key/priority setup:

- `forced_login_method = "api"`
- `service_tier = "priority"`

Do not commit secrets. `auth.json` and any API keys in the config must remain
local-only.

## Rebuild `codexp`

Rebuild when you want the latest fork changes in the binary:

- Use the pinned toolchain: `rust-toolchain.toml` is set to `1.93.0`.
- Build command (from `codex-rs`):
  - `cargo +1.93.0 build -p codex-cli`

This updates `codex-rs/target/debug/codex`.

## Verify service tier is sent

The most reliable proof is a real request with trace logging enabled.

Steps:
1) Ensure `service_tier = "priority"` is set in `/Users/ava/.codex-priority/config.toml`.
2) Run `codexp` with:
   - `CODEX_HOME=/Users/ava/.codex-priority`
   - `RUST_LOG=codex_client=trace`
3) Send a minimal prompt (e.g., a short `exec` command).
4) Confirm the trace log line includes `"service_tier":"priority"` in the request
   payload.

Note: trace logs include the request payload and may contain sensitive data.
Capture only the minimum needed for verification.

## Sync this fork with upstream

We prefer a merge when you want the safest path that preserves your existing
work, and rebase only when you explicitly want linear history.

Merge flow:
1) `git fetch upstream`
2) `git checkout main`
3) `git merge --no-ff upstream/main`
4) Resolve conflicts if needed.
5) Push with `git push origin main`.

## Validation checklist

After upstream merges or local changes:

- `just fmt` (from `codex-rs`)
- Build `codex-cli`:
  - `cargo +1.93.0 build -p codex-cli`
- Run targeted tests when feasible:
  - Example: `cargo +1.93.0 test -p codex-core`

If a build fails due to file-locking APIs, use `fs2`-based locks (stable) instead
of the unstable `std::fs` file lock APIs.
