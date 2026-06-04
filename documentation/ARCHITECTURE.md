# Architecture

## Overview

This project is a Rust-first, cross-platform global radio application organized as a Cargo workspace with shared core logic and separate UI shells for web/desktop and CLI.

## Workspace Layout

- `radio-core`: shared domain models, fetch pipeline, validation, health-check engine, filter derivation, playback contracts, persistence contracts.
- `radio-web`: Dioxus SPA for web and desktop UI.
- `radio-cli`: Ratatui terminal application.
- Optional `radio-desktop`: Tauri shell if separation from the web app becomes useful.

## Core Principles

- Keep business logic in `radio-core`.
- Avoid duplicate filtering, health, or playback state logic in UI crates.
- Derive visible stations from a single shared pipeline.
- Keep platform-specific concerns in thin adapters.
- Prefer correctness and maintainability over flashy shortcuts.

## Data Flow

1. Fetch runtime station data from radio-browser.
2. Normalize into shared types.
3. Apply structural validation.
4. Run asynchronous health checks.
5. Derive visible stations from current filters and station state.
6. Render the same source of truth in web, desktop, and CLI shells.

## UI Responsibilities

### radio-web

- Station discovery list.
- Filter controls and active chips.
- Search modal.
- Bottom player bar.
- Globe visualization via globe.gl interop.

### radio-cli

- Compact station list.
- Keyboard-first filtering and search.
- Playback and timer controls.
- Health indicators and favorites.

## Boundaries

- `radio-core` must not depend on UI frameworks.
- Web-specific globe interop should not leak into core logic.
- CLI rendering should reuse core state and not reimplement station derivation.

## Quality Gates

- `cargo fmt --check`
- `cargo clippy -- -D warnings`
- `cargo test`
- Cross-platform validation on Windows, Linux, and macOS.
