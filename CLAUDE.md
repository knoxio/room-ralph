# room-ralph — Agent Wrapper

## What is room-ralph?

room-ralph is an autonomous agent wrapper for [room](https://github.com/knoxio/room).
It runs `claude -p` in a loop with automatic context restart, progress persistence,
and room integration.

## Workspace structure

```
Cargo.toml              — virtual workspace root
Cargo.lock              — shared across workspace members
crates/
  room-ralph/           — agent wrapper binary + library
  room-plugin-agent/    — /agent and /spawn plugin for room-daemon
```

## Key files

```
crates/room-ralph/src/
  main.rs              — CLI entry point, logging setup, tmux launch
  lib.rs               — Cli struct (clap), module declarations
  loop_runner.rs       — Main ralph loop: iterate, prompt, claude, output
  prompt.rs            — Prompt building from context, personality, messages
  claude.rs            — Claude subprocess: spawn, parse output, tool config
  personalities.rs     — Built-in personalities (coder, reviewer, etc.)
  agent_meta.rs        — .room-agent.json metadata file (identity persistence)
  monitor.rs           — Context usage tracking, restart thresholds
  progress.rs          — Progress file I/O for cross-session state
  room.rs              — Room CLI wrapper: join/send/poll/set_status

crates/room-plugin-agent/src/
  lib.rs               — /agent and /spawn plugin: spawn, stop, list, logs
```

## Dependencies

- `room-protocol` from crates.io — wire format types, Plugin trait
- `room` CLI binary — must be on PATH for room commands
- `claude` CLI binary — must be on PATH for Claude interaction

## Pre-push checklist

```bash
cargo check
cargo fmt
cargo clippy -- -D warnings
cargo test
```

## Running tests

```bash
cargo test                           # all tests
cargo test -p room-ralph             # ralph unit + integration tests
cargo test -p room-plugin-agent      # agent plugin tests
```

Integration tests use mock `room` and `claude` binaries. Tests that modify
PATH are serialized via `PATH_LOCK`.
