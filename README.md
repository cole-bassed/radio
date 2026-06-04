# Radio

A portable, reproducible Rust app for discovering and listening to radio across web, desktop, and CLI — built around a shared core, clean filtering, and a music-first experience.

## Why it exists

This project grew out of a simple preference: I enjoy listening to music in the terminal, and I wanted a reliable cross-platform way to do it. Radio is built to make that experience calm, fast, and practical, with a shared Rust core and support for web, desktop, and CLI use.

## Reproducibility

Radio is designed to be reproducible across systems.

- **Nix Flakes** provide the primary fully reproducible development environment.
- **mise** provides a lightweight alternative for systems without Nix.
- Both are intended to make setup consistent across Windows, Linux, and macOS.

## What sets it apart

- Periodically checks station status on a best-effort basis.
- Filters out stations that are currently unhealthy or failing checks.
- Presents a cleaner set of stations less likely to hit dead links.
- Supports stackable filters, favorites, search, and sleep timers.
- Uses a shared Rust core for consistent behavior across targets.
- Reproducible development through Nix flakes and mise.

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

- Use `nix develop` for the primary reproducible environment.
- Use mise for lightweight task and tool management on systems without Nix.
- `cargo build --workspace`
- `cargo test`
- `cargo fmt --check`
- `cargo clippy -- -D warnings`

## License

Dual licensed under the MIT License and the Apache License, Version 2.0.
