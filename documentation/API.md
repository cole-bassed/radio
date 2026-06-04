# API

## Shared Domain Types

### Station

- `stationuuid: String`
- `name: String`
- `url_resolved: String`
- `codec: String`
- `country: String`
- `countrycode: String`
- `tags: String`
- `geo_lat: f64`
- `geo_long: f64`
- `health: StationHealth`
- `ad_policy: AdPolicy`
- `is_favorite: bool`

### StationHealth

- `Unknown`
- `Checking`
- `Live`
- `Dead`

### AdPolicy

- `AdFree`
- `HasAds`
- `Unknown`

## Shared Behaviors

### Ingestion

- Fetch runtime station data from radio-browser.
- Normalize input into `Station`.
- Validate URL, codec, coordinates, UUID, and name.

### Health Engine

- Run bounded-concurrency health checks.
- Map successful responses to `Live`.
- Map timeouts and errors to `Dead`.
- Emit incremental updates.

### Derived State

Expose a shared API for:

- visible stations
- filtered counts
- filter option counts
- station lookup by UUID
- selection synchronization

### Playback Contract

Define playback as a shared capability so each target can provide an adapter without duplicating application rules.

### Persistence Contract

Define a storage interface for:

- favorites
- lightweight preferences
- sleep timer preferences if needed

## Error Semantics

- Invalid remote data should be rejected early.
- Playback failures should feed back into health or retry logic.
- Unknown classification states must remain distinct from positive assertions.
