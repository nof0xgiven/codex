# Fork Updates

This file tracks fork-specific changes to help with upstream rebases.

## Changelog

### 2026-02-13
- Rebased onto upstream/main (325 commits ahead)
- Dropped `fs2` file locking — upstream now uses `std::fs::File::try_lock()` (stable since Rust 1.89)
- Dropped `personality` -> `model_personality` rename — upstream still uses `personality`
- Re-applied `service_tier` feature cleanly on fresh upstream base

### 2026-02-03
- Synced with upstream (167 commits)
- Migrated to `fs2` crate for stable cross-platform file locking
- Updated to Rust toolchain 1.93.0

### 2026-01-27
- Added `service_tier` config option (e.g., `"priority"`)
- Threaded `service_tier` through Responses API requests
- Exposed `service_tier` in app-server protocol (v1/v2) and config schema

## Fork Features

### Service Tier Configuration

Allows setting OpenAI service tier for API requests:

```toml
# ~/.codex/config.toml
service_tier = "priority"
```

Available values: `auto`, `default`, `flex`, `priority`.

Useful for users with priority API access who want faster processing.

## Maintaining This Fork

### Remotes Setup

```shell
git remote add upstream https://github.com/openai/codex.git
```

### Sync with Upstream

For small syncs (< 50 commits behind):
```shell
git fetch upstream
git checkout main
git merge --no-ff upstream/main
```

For large divergences (100+ commits behind):
```shell
git fetch upstream
git branch rebase-backup-$(date +%Y%m%d-%H%M) main
git reset --hard upstream/main
# Re-apply service_tier changes manually (see commit history)
```

### Files Modified by This Fork

| File | Change |
|------|--------|
| `codex-rs/protocol/src/config_types.rs` | `ServiceTier` enum |
| `codex-rs/core/src/config/mod.rs` | Config + ConfigToml fields |
| `codex-rs/core/src/config/profile.rs` | Profile field |
| `codex-rs/core/src/client.rs` | Client state + request building |
| `codex-rs/core/src/codex.rs` | Pass service_tier to ModelClient |
| `codex-rs/codex-api/src/common.rs` | API request structs |
| `codex-rs/app-server-protocol/src/protocol/v1.rs` | Protocol v1 |
| `codex-rs/app-server-protocol/src/protocol/v2.rs` | Protocol v2 |
| `codex-rs/core/config.schema.json` | JSON schema |

### Validation After Sync

```shell
cd codex-rs
RUSTC="$HOME/.rustup/toolchains/1.93.0-aarch64-apple-darwin/bin/rustc" cargo build -p codex-cli
RUSTC="$HOME/.rustup/toolchains/1.93.0-aarch64-apple-darwin/bin/rustc" cargo test -p codex-core --lib -- config::
```
