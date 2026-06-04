# Acceptance Criteria

## Phase 0

- Workspace builds successfully.
- Nix shell works.
- CI commands are wired up.

## Phase 1

- Runtime station data is fetched and normalized.
- Invalid records are filtered out.
- Shared types exist in `radio-core`.

## Phase 2

- Health checks run asynchronously.
- Dead stations are filtered out.
- Stackable filters work.
- Filter counts are derived from the current active set.

## Phase 3

- Playback works through shared contracts.
- Sleep timer logic works.
- Playback failure feeds into retry or re-check behavior.

## Phase 4

- Dioxus web/desktop UI is functional.
- Filter chips and URL state are synchronized.
- Globe markers stay in sync with the list.

## Phase 5

- Ratatui CLI supports core workflows.
- Keyboard-driven discovery and playback work.
- CLI uses the same shared core logic.

## Phase 6

- The app is documented.
- Packaging and deployment are validated.
- Cross-platform behavior is confirmed.
