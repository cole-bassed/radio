# Workflow

## Development Setup

Primary entry point:

- `nix develop`

## Common Commands

- `cargo build --workspace`
- `cargo test`
- `cargo fmt --check`
- `cargo clippy -- -D warnings`

## Expected Flow

1. Enter the Nix dev shell.
2. Build the workspace.
3. Run tests and lint.
4. Develop one phase or sub-stage at a time.
5. Keep the roadmap and changelog current.

## CI Expectations

- Run formatting checks.
- Run clippy with warnings denied.
- Run tests for all crates.
- Prefer caching where practical.

## Documentation Workflow

- Update `CHANGELOG.md` after each completed stage.
- Keep `ROADMAP.md` as the planning source of truth.
- Keep architecture and API docs synchronized with implemented code.

## Release Workflow

- Verify Windows, Linux, and macOS support.
- Validate the web deployment path.
- Validate desktop packaging.
- Confirm CLI usability.
