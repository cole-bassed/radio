# AI Prompt Context

Use this file as the first reference for AI-assisted development sessions.

## Current Project

Portable, reproducible Rust global radio application with shared core logic and cross-platform targets.

## Current Phase

Phase 0 — Foundation
Stage 0.1 — Create the workspace skeleton only.

## Read First

- documentation/README.md
- documentation/ROADMAP.md
- documentation/ARCHITECTURE.md
- documentation/API.md
- documentation/PLATFORM.md
- documentation/WORKFLOW.md
- documentation/ACCEPTANCE_CRITERIA.md
- documentation/DESIGN.md

## Non-Negotiables

- Rust-first architecture.
- Cross-platform: Windows, Linux, macOS.
- Reproducible via Nix Flake.
- Shared core logic must not be duplicated across targets.
- Runtime station data only.
- Dead stations filtered automatically later in the roadmap.
- Filters must be stackable later in the roadmap.
- Honor system/user color modes and accessibility.
- Use a collapsible filter bar concept for the UI.
- Keep branding light for now.
- Prefer correctness, maintainability, portability, and reproducibility.

## This Session Must Do

- Create the initial Cargo workspace skeleton.
- Create the crate scaffolding for:
  - crates/radio-core
  - crates/radio-web
  - crates/radio-cli
  - apps/radio-desktop
- Keep only the minimal compileable structure needed for Phase 0.

## This Session Must Not Do

- Do not implement ingestion.
- Do not implement health checks.
- Do not implement filtering logic.
- Do not implement playback logic.
- Do not implement globe logic.
- Do not implement final UI polish.
- Do not work on future phases.

## Output Expectations

- Clean workspace skeleton.
- Minimal compileable crate structure.
- No unnecessary business logic.
- Brief summary of changes and assumptions.
