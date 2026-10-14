# Architecture

You do not need to understand the internals to use Immich ReverseGeo, but the overall shape is simple: the app reads coordinates from immich, matches them against built-in and downloaded location data, and writes the final place names back.

For a plain-language explanation of the active geographic data sources, see [Data Sources](<https://immich-reversegeo.github.io/data-sources/index.md>).

## Main components

### Web app

The web UI handles settings, processing, coordinate lookups, country caches, location reviews, saved scans, change history, and logs.

### Built-in data

Country matching and airport matching are available right away from data shipped with the app.

### Downloaded country data

Extra per-country data is downloaded when needed so state and city matching can be more precise.

### Processing pipeline

The background processor:

1. reads unprocessed assets from immich
2. resolves bundled country
3. resolves administrative areas from cached Overture divisions and optionally cached GADM country packages
4. optionally queries bundled airport infrastructure
5. writes city/state/country back to immich when a complete result is available

### Reviewing existing names

Location Management uses the same location matching to scan assets with GPS coordinates, including those with existing names. Scanning saves the existing and proposed names without changing Immich. You can reopen saved results or continue unfinished scans later.

Applying selected differences writes the exact reviewed names directly, after checking that the existing names and coordinates still match the snapshot. Change History records previous names and outcomes, with undo available when the applied metadata has not changed. These records live under `/data`, so preserve that volume across container replacements. They do not replace an Immich database backup.

See [Location Management](<https://immich-reversegeo.github.io/using-the-app/#location-management>) for the review, confirmation, and recovery steps.

### Active data sources

### Built in

Country matching data and airport matching data are included in the app image.

### Downloaded on demand

Country-specific Overture and optional GADM data is downloaded locally when needed for better state and city matching.

### Optional live lookup help

The Lookup page can also show live Overture Places diagnostics when you want to inspect a point more deeply.

### Legacy project

Older geoBoundaries, static-airport, and Foursquare code is preserved in the legacy project but is no longer part of the active runtime path.
