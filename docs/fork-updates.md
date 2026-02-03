# Fork Updates

This file tracks fork-specific changes to help with upstream rebases.

## Changelog

### 2026-02-03
- Synced with upstream (167 commits)
- Migrated to `fs2` crate for stable cross-platform file locking
- Updated to Rust toolchain 1.93.0

### 2026-01-27
- Added `service_tier` config option (e.g., `"priority"`)
- Threaded `service_tier` through Responses API and Chat Completions requests
- Exposed `service_tier` in app-server protocol (v1/v2) and config schema

## Fork Features

### Service Tier Configuration

Allows setting OpenAI service tier for API requests:

```toml
# ~/.codex/config.toml
service_tier = "priority"
```

Useful for users with priority API access who want faster processing.

### File Locking (fs2)

Uses the stable `fs2` crate instead of unstable `std::fs` file locking APIs, ensuring compatibility across Rust toolchain updates.

## Maintaining This Fork

### Remotes Setup

```shell
git remote add upstream https://github.com/openai/codex.git
```

### Sync with Upstream (Rebase)

```shell
git fetch upstream
git checkout main
git rebase upstream/main
# Resolve any conflicts
git push --force-with-lease origin main
```

### Validation After Sync

```shell
cd codex-rs
just fmt
cargo build -p codex-cli
cargo test -p codex-core
```
