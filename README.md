# Radio

A portable, reproducible Rust app for discovering and listening to live radio across web, desktop, and CLI — built around a shared core, clean filtering, and a music-first experience.

## Why it exists

Radio should feel effortless. This project is designed to stay out of the way while making station discovery, filtering, and playback fast, reliable, and pleasant across Windows, Linux, and macOS.

## What makes it different

- Shared Rust core for consistent behavior across every target.
- Runtime station data with automatic health filtering.
- Stackable filters for search, country, genre, codec, favorites, live-only, and ad-free stations.
- Modern, minimal UI with a collapsible filter bar.
- Honors system and user color modes and accessibility preferences.
- Reproducible development with Nix.

## Targets

- [Shared Core](packages/core) — shared domain logic, data ingestion, validation, health, filtering, and playback contracts.
- [Command-line Interface](packages/cli) — Ratatui CLI.
- [Web User Interface](packages/web) — Dioxus web UI.
- [Desktop Application](packages/app) — Dioxus desktop UI.

## Documentation

- [Documentation Index](documentation/README.md) — entry point to the project docs.
- [Roadmap](documentation/ROADMAP.md)
- [Architecture](documentation/ARCHITECTURE.md)
- [API](documentation/API.md)
- [Platform](documentation/PLATFORM.md)
- [Workflow](documentation/WORKFLOW.md)
- [Acceptance Criteria](documentation/ACCEPTANCE_CRITERIA.md)
- [Design](documentation/DESIGN.md)

## Development

- `nix develop`
- `cargo build --workspace`
- `cargo test`
- `cargo fmt --check`
- `cargo clippy -- -D warnings`

## License

Dual licensed under the MIT License and the Apache License, Version 2.0.
