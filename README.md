# Radio

A portable, reproducible Rust-native global radio application with shared core logic, cross-platform desktop and web targets, and a minimal CLI.

## License

This project is dual licensed under the MIT License and the Apache License, Version 2.0. You may use this project under either license.

## Project layout

- [Documentation](documentation) — roadmap, architecture, API, platform, workflow, acceptance criteria, and design docs.
- [Core Application](packages/core) — shared domain, ingestion, health, filtering, playback contracts, persistence contracts.
- [Command-line Interface](packages/cli) — Ratatui CLI.
- [Web/Desktop User Interface](packages/web) — Dioxus web/desktop UI.

## Development

- `nix develop`
- `cargo build --workspace`
- `cargo test`
- `cargo fmt --check`
- `cargo clippy -- -D warnings`
