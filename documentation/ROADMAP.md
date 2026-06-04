# Rust Global Radio App Technical Roadmap

This roadmap breaks the project into implementation phases, milestone gates, risk-managed sequencing, and detailed staged prompts that can be handed to an AI coding assistant one stage at a time. The sequencing prioritizes portability, reproducibility, shared-state correctness, and maintainability before visual polish.[cite:1]

## Delivery strategy

The project should be built as a Cargo workspace with a shared Rust core and separate targets for the Dioxus web/desktop app and the Ratatui CLI. The plan assumes Windows, Linux, and macOS support, runtime data fetching, stackable filters, ad-free classification, live/dead station health checks, and a reproducible Nix Flake workflow.[cite:1]

| Principle | Why it matters |
|---|---|
| Shared core first | Prevents filter, health, and playback logic from diverging across web, desktop, and CLI.[cite:1] |
| Reproducibility early | Nix, CI, and workspace structure should be stable before feature work expands.[cite:1] |
| Derived state discipline | Counts, list results, globe markers, and playback context must all come from the same filtered state pipeline.[cite:1] |
| Platform parity | Windows, Linux, and macOS support should be verified throughout, not only at the end.[cite:1] |
| Polish last | Loading, empty, and failure states matter, but they are safer after core behavior stabilizes.[cite:1] |

## Phase plan

| Phase | Focus | Main outputs | Exit criteria |
|---|---|---|---|
| 0 | Foundation | Cargo workspace, flake, CI, README bootstrap | `nix develop` works; workspace builds cleanly |
| 1 | Domain + ingestion | Station types, fetch pipeline, normalization, validation | Runtime station dataset produced reliably |
| 2 | Health + filter engine | Async health checks, ad policy derivation, stackable filters, derived counts | Dead stations excluded; counts and visibility consistent |
| 3 | Playback core | Playback abstractions, timers, persistence contracts, failure handling | Stations can play and failures feed back into health state |
| 4 | Web/Desktop shell | Dioxus UI, player bar, filter chips, URL state, globe sync | UI remains synchronized through filter/search/play cycles |
| 5 | CLI shell | Ratatui UX, keyboard flow, feature parity on core workflows | CLI supports discovery, filters, favorites, playback |
| 6 | Quality + release | Cross-platform QA, packaging, docs, deploy | Release candidate for web, desktop, and CLI |

## Dependency order

The safest implementation order is: workspace and reproducibility, then shared data model, then station validation and health classification, then derived filter logic, then playback, then shells and UX. This order reduces the chance of rewriting UI code later because the state model changed underneath it.[cite:1]

### Key dependency chain

1. Phase 0 must finish before any feature stage because every later stage depends on the workspace, flake, and build ergonomics.
2. Phase 1 must finish before Phase 2 because health checks and stackable filters depend on stable shared station types.
3. Phase 2 must finish before most of Phase 4 and Phase 5 because the visible dataset contract drives the list, counts, chips, markers, and command search behavior.[cite:1]
4. Phase 3 should land before final UX polish because playback failure states need to integrate with health re-checks and live/dead visibility.

## Phase 0 — Foundation

### Objectives

- Create the Cargo workspace.
- Add `radio-core`, `radio-web`, and `radio-cli` crates.
- Add `flake.nix` with pinned inputs and a working dev shell.
- Add CI commands for fmt, clippy, and test.
- Add initial README covering setup and platform notes.

### Milestone M0

A new developer can run `nix develop` and then build the workspace without hidden machine-specific setup.[cite:1]

### Risks

- Tauri and WASM dependencies vary by platform.
- Linux desktop builds may require WebKitGTK packages.
- Early path or shell assumptions can break Windows support.

### Acceptance criteria

- `nix develop` opens a working environment.
- `cargo build --workspace` passes.
- `cargo fmt --check`, `cargo clippy -- -D warnings`, and `cargo test` are wired into CI.
- No OS-specific scripts are required for basic development.

### AI prompt — Stage 0.1

```text
Create a Rust Cargo workspace for a portable global radio app with these crates:
- radio-core
- radio-web
- radio-cli

Requirements:
- cross-platform support for Windows, Linux, macOS
- clean workspace-level Cargo.toml
- shared lint/test settings where appropriate
- minimal starter code in each crate that compiles
- no business logic yet

Also create a README skeleton with sections for:
- project overview
- workspace structure
- development commands
- platform notes
```

### AI prompt — Stage 0.2

```text
Add a reproducible Nix Flake for this Rust workspace.

Requirements:
- pinned nixpkgs input
- devShell with Rust toolchain, cargo tools, wasm target tooling, and dependencies needed for Dioxus/Tauri development
- outputs for devShells, packages, apps, and checks where reasonable
- command ergonomics for nix develop and cargo workflows
- document any unavoidable platform-specific dependency caveats clearly

Do not add placeholder comments. Produce a working flake suitable for iterative development.
```

### AI prompt — Stage 0.3

```text
Set up CI and local quality gates for this Cargo workspace.

Requirements:
- cargo fmt --check
- cargo clippy -- -D warnings
- cargo test
- reasonable caching if using GitHub Actions
- keep the workflow portable and minimal

Also update the README with exact commands for local verification.
```

## Phase 1 — Domain model and ingestion

### Objectives

- Define the canonical `Station`, `StationHealth`, `AdPolicy`, and filter-state types in `radio-core`.
- Fetch the runtime dataset from radio-browser.
- Normalize and validate incoming station data.
- Reject malformed or unusable stations early.

### Milestone M1

The app can fetch and normalize a runtime station dataset into shared Rust types with deterministic pre-filtering.[cite:1]

### Risks

- Remote data quality may be inconsistent.
- Field naming and nullability can drift.
- Premature UI assumptions may leak into domain modeling.

### Acceptance criteria

- Runtime fetch works with retries and backoff.
- Pre-filter removes invalid URLs, codecs, coordinates, and empty identities.
- Shared model is decoupled from UI concerns.
- Unit tests cover parsing and validation behavior.

### AI prompt — Stage 1.1

```text
In radio-core, define the shared domain model for a radio application.

Include:
- Station struct
- StationHealth enum: Unknown, Checking, Live, Dead
- AdPolicy enum: AdFree, HasAds, Unknown
- filter state types that can support stackable filters later

Constraints:
- keep the model platform-agnostic
- derive serde traits where needed
- prepare for runtime ingestion from radio-browser
- do not add UI code
```

### AI prompt — Stage 1.2

```text
Implement runtime ingestion in radio-core for:
https://de1.api.radio-browser.info/json/stations/topclick/500

Requirements:
- async fetch in Rust
- 3 retries with exponential backoff
- serde-based parsing
- normalize incoming records into Station
- preserve stationuuid, name, url_resolved, codec, country, countrycode, tags, geo_lat, geo_long
- set health = Unknown initially
- set ad_policy = Unknown initially

Also add focused tests for parsing and normalization.
```

### AI prompt — Stage 1.3

```text
Implement pre-filter validation for the shared station dataset.

Discard stations that fail any of these checks:
- missing or non-HTTPS resolved URL
- unsupported codec
- invalid latitude/longitude
- missing station UUID
- empty name

Return a clean validated collection ready for later health checks.
Add unit tests that cover edge cases and malformed inputs.
```

## Phase 2 — Health engine and derived filtering

### Objectives

- Add background health checks with bounded concurrency.
- Derive conservative ad policy classification from metadata.
- Implement stackable filters in shared core logic.
- Recompute filter counts from the current filtered context.
- Establish a single visible-station derivation pipeline.

### Milestone M2

Dead stations are automatically excluded, ad-free filtering works conservatively, and all counts/projections come from one shared derivation path.[cite:1]

### Risks

- Separate filtering logic in different shells can drift.
- Ad-free classification can overclaim if heuristics are too aggressive.
- Large-scale re-renders may occur if per-station updates replace the full dataset.

### Acceptance criteria

- Health checks use bounded concurrency and timeouts.
- Live/dead status updates are incremental.
- Filters are stackable and deterministic.
- Option counts recompute relative to the active selection.
- Unknown ad state remains distinct from AdFree.

### AI prompt — Stage 2.1

```text
Implement a health-check engine in radio-core.

Requirements:
- async Rust implementation
- one health-check task per station with bounded concurrency via semaphore
- 5 second timeout per station
- HTTP HEAD against url_resolved
- 2xx/3xx => Live
- timeout / network error / 4xx / 5xx => Dead
- incremental status updates instead of full dataset replacement
- API surface suitable for both Dioxus and Ratatui consumers

Also add tests for timeout/error/success mapping logic where practical.
```

### AI prompt — Stage 2.2

```text
Implement conservative ad-policy classification in radio-core.

Requirements:
- derive AdFree, HasAds, or Unknown from station name/tags/metadata heuristics
- avoid false positives for AdFree
- keep Unknown distinct
- make the classifier pure and testable
- add tests covering ambiguous and strongly signaled cases
```

### AI prompt — Stage 2.3

```text
Implement stackable filter logic in radio-core.

Filters to support:
- search text
- country (multi-select)
- genre/tag (multi-select)
- favorites only
- ad-free only
- live only
- codec (multi-select)

Composition rules:
- filters across categories are AND-composed
- multiple selected values within one category are OR-composed

Requirements:
- one canonical derived visible-stations function
- filter counts recomputed relative to the current active filter set
- dead stations excluded by default
- no UI code

Add tests that prove filter composition and count recomputation behavior.
```

### AI prompt — Stage 2.4

```text
Design and implement a shared derived-state API in radio-core.

Goal:
Expose a clean interface so web/desktop and CLI can both ask for:
- visible stations
- station counts
- available filter options with dynamic counts
- selected station lookup by UUID

Constraints:
- avoid duplicating filter logic in consumers
- optimize for correctness and maintainability first
- keep the API ergonomic for Dioxus reactive state and Ratatui redraw loops
```

## Phase 3 — Playback core

### Objectives

- Define playback abstractions that can support web/desktop and CLI.
- Add sleep timer logic.
- Add failure handling that feeds back into health checks.
- Add persistence contracts for favorites and preferences.

### Milestone M3

A selected station can be played, timed, retried, and reflected back into station health state without bypassing the shared model.

### Risks

- Playback implementation differs substantially across targets.
- Browser autoplay rules may require explicit user gesture handling.
- Persistence behavior differs between web and desktop environments.

### Acceptance criteria

- Shared playback state contract exists.
- Sleep timer logic is independent of UI shell.
- Playback failures can trigger re-check flow.
- Favorites persistence interface is abstracted cleanly.

### AI prompt — Stage 3.1

```text
Design a shared playback state model for a Rust global radio app.

Include concepts for:
- currently selected station UUID
- now playing / paused / buffering / error states
- volume
- sleep timer
- playback failure and retry intent

Constraints:
- shared logic should live in radio-core where practical
- avoid target-specific implementation details in the core types
- prepare for web/desktop and CLI playback adapters
```

### AI prompt — Stage 3.2

```text
Implement sleep timer logic and playback failure state transitions in radio-core.

Requirements:
- timers for 15/30/60/90 minute presets
- deterministic state transitions
- ability to trigger a station health re-check on playback failure
- no UI code
- add unit tests for timer expiry and failure transitions
```

### AI prompt — Stage 3.3

```text
Define persistence traits/interfaces for favorites and lightweight preferences.

Requirements:
- abstract storage behind traits so web, desktop, and CLI can use different backends
- favorites must integrate with shared filtering logic
- avoid embedding platform-specific assumptions into radio-core
```

## Phase 4 — Web/Desktop shell

### Objectives

- Build the Dioxus SPA.
- Add visible filters, active chips, station grid, command palette search, player bar, and URL-reflected state.
- Integrate globe.gl with stationuuid-keyed marker reuse.
- Keep list, count, markers, and selection synchronized.

### Milestone M4

The Dioxus app behaves like a cohesive product: searching, filtering, selecting, playing, and globe interaction remain synchronized without state drift.[cite:1]

### Risks

- JS interop with globe.gl can create marker lifecycle bugs.
- URL state can diverge from in-memory state if not designed carefully.
- Performance can degrade if the list and globe redraw from separate logic.

### Acceptance criteria

- Filters are visible above content and represented as chips.
- URL reflects the active filter state.
- Cmd/Ctrl+K search works.
- Globe markers are keyed and updated incrementally.
- Clicking marker and card syncs selection both ways.

### AI prompt — Stage 4.1

```text
Build the initial Dioxus shell for the radio-web crate.

Requirements:
- single-page layout
- left pane for discovery/listing
- right pane reserved for globe
- bottom player bar
- token-driven styling system; Tailwind optional, not required
- restrained white minimal product aesthetic
- no final branding system yet

Use stub data adapters if needed temporarily, but structure the shell to consume radio-core state cleanly.
```

### AI prompt — Stage 4.2

```text
Integrate radio-core derived state into the Dioxus app.

Requirements:
- visible station grid driven from one derived-state pipeline
- filter controls above content
- active filter chips
- clear-all action
- live station count
- command palette style search modal with Cmd+K / Ctrl+K
- URL-reflected filter state where practical

Do not duplicate filter logic in UI components.
```

### AI prompt — Stage 4.3

```text
Integrate globe.gl into the Dioxus app via minimal browser interop.

Requirements:
- tiny colored dot markers only
- no persistent labels on globe
- hover tooltip only
- click marker to select/play station
- selecting a card highlights marker and flies camera
- marker identity keyed by stationuuid
- markers updated incrementally across filter changes
- never clear and rebuild the full marker set on each filter change

Focus on correctness of synchronization before visual polish.
```

### AI prompt — Stage 4.4

```text
Connect real playback state to the Dioxus UI.

Requirements:
- bottom player bar with station metadata, playback controls, volume, favorite toggle, sleep timer
- playback failure must surface useful feedback
- if a station becomes Dead, remove it from visible results and update the UI coherently
- loading, empty, no-results, and failure states must all look intentional
```

## Phase 5 — CLI shell

### Objectives

- Build the Ratatui terminal app.
- Reuse shared filter and playback logic.
- Implement keyboard-driven discovery and playback.
- Support live-only and ad-free filtering.

### Milestone M5

The CLI delivers the core product experience in a minimal environment without diverging from shared filtering and health semantics.

### Risks

- Terminal redraw performance and keyboard handling can become tangled.
- A second implementation of filtering may accidentally emerge.
- Audio backend behavior may vary by platform.

### Acceptance criteria

- Search, favorites, ad-free, live-only, country, and genre interactions work.
- Health indicators are clear.
- Playback state is understandable.
- Shared logic from radio-core is reused rather than rewritten.

### AI prompt — Stage 5.1

```text
Build the initial Ratatui shell for radio-cli.

Requirements:
- top pane for station list
- bottom pane for now playing and controls/status
- keyboard navigation with arrows and Enter
- clean separation between UI rendering and shared state consumption
- no duplicated domain logic
```

### AI prompt — Stage 5.2

```text
Integrate shared filtering and search into the Ratatui app.

Requirements:
- stacked filters matching radio-core behavior
- keybindings for ad-free, live-only, favorites, country, and genre flows
- clear display of active filters
- list should always reflect the same visible-stations logic as the web app
```

### AI prompt — Stage 5.3

```text
Integrate playback status, favorites, and sleep timer behavior into the Ratatui app.

Requirements:
- keyboard-driven playback and timer controls
- health and error visibility
- reuse shared playback state concepts from radio-core
- preserve portability across Windows, Linux, and macOS terminals
```

## Phase 6 — Quality, packaging, and release

### Objectives

- Polish loading/empty/error states.
- Run cross-platform QA.
- Package desktop and CLI builds.
- Finalize docs and deployment.

### Milestone M6

A release candidate exists for web, desktop, and CLI, with documented setup paths and known platform caveats.[cite:1]

### Risks

- Platform packaging differences may surface late.
- Desktop/web behavior may diverge under real network conditions.
- Performance regressions may appear once real data is exercised repeatedly.

### Acceptance criteria

- Windows, Linux, and macOS are tested explicitly.
- README documents setup, run, build, and caveats.
- Web deployment path is documented.
- Desktop packaging path is documented.
- CLI usage guide is complete.

### AI prompt — Stage 6.1

```text
Audit the project for cross-platform readiness.

Requirements:
- review Windows, Linux, and macOS concerns
- identify any path, shell, dependency, or audio backend assumptions
- fix portability issues where possible
- document unavoidable caveats clearly in the README
```

### AI prompt — Stage 6.2

```text
Polish product states and quality details across web and CLI.

Review and improve:
- loading states
- empty states
- no-results states
- playback failure states
- health-check-in-progress states
- keyboard/help discoverability

Focus on clarity, consistency, and maintainability rather than visual over-design.
```

### AI prompt — Stage 6.3

```text
Prepare release-oriented documentation and packaging notes.

Requirements:
- final README with setup and run instructions
- Nix workflow documentation
- desktop build notes
- web deployment notes for kimi.page
- CLI usage examples
- known limitations and future phase ideas
```

## Effort and risk profile

| Area | Effort | Risk | Notes |
|---|---|---|---|
| Foundation | Medium | Medium | Nix + Tauri + WASM setup is the main complexity |
| Domain ingestion | Medium | Low | Mostly data modeling and validation |
| Health/filter engine | High | High | Central correctness risk for the whole product |
| Playback core | High | High | Cross-target playback behavior is the hardest systems area |
| Web/Desktop shell | High | High | Globe sync and reactive state can get brittle |
| CLI shell | Medium | Medium | Simpler UI, but parity pressure remains |
| Release/polish | Medium | Medium | Platform packaging and QA often uncover late issues |

## Recommended working rhythm

Use one AI prompt per sub-stage and keep each output reviewable. Do not ask for the whole product at once; instead, validate each phase before moving forward, especially around shared state, health transitions, playback errors, and portability concerns.[cite:1]

A practical cadence is:
- generate code for one sub-stage
- run build/tests
- manually inspect architecture and API choices
- fix issues
- only then continue to the next sub-stage

## Prompting guidance

Each sub-stage prompt should restate these non-negotiables:

- portable across Windows, Linux, macOS
- reproducible with Nix Flake
- Cargo workspace
- runtime station data only
- shared core logic first
- dead stations filtered automatically
- ad-free filter supported conservatively
- stackable filters
- no duplicated filter logic between shells
- Rust-first, with non-Rust code limited to unavoidable integration concerns

This keeps later prompts from regressing the project’s core architectural goals.
