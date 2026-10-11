Immich ReverseGeo is a self-hosted companion for [immich](<https://immich.app>) that improves location names when the built-in results feel too broad, too generic, or simply wrong for your library.

[Get started](<https://immich-reversegeo.github.io/getting-started>) [Installation](<https://immich-reversegeo.github.io/installation>) [GitHub](<https://github.com/immich-reversegeo/immich-reversegeo>)

## Check a coordinate before processing

Open **Lookup**, paste coordinates from a photo you know, and inspect the country, state, city, and matching sources before running against your library. Lookup previews location names without updating Immich assets; it may download country data when needed.

## What it can help with

- Missing or overly broad location names on photos that already have GPS coordinates.
- Matching around coastlines, islands, administrative boundaries, and airport areas.
- Repeatable processing with locally cached geographic data.

Results depend on the available source data. It cannot add missing GPS coordinates or guarantee a better name for every point. Compare known locations in Lookup first, then choose the settings that fit your library.

To replace existing location names, back up the database and clear the affected assets' location fields before reprocessing. See [Start manual processing](<https://immich-reversegeo.github.io/getting-started/#5-start-manual-processing>) for the workflow.

See [Using the App](<https://immich-reversegeo.github.io/using-the-app/index.md>) for the workflow and [Data Sources](<https://immich-reversegeo.github.io/data-sources/index.md>) for coverage and licence limits.

## Before You Run It On Real Data

- It needs direct access to your immich database.
- immich's own reverse geocoding should be turned off first.
- It writes back to immich metadata.
- It can clear existing immich location values from the Data page.
- It needs internet access the first time it downloads extra data for a country.
- Large country downloads can consume hundreds of megabytes each.

> [!CAUTION]
>
> **Turn off immich's built-in reverse geocoding**
>
> Immich ReverseGeo writes `city`, `state`, and `country` back into immich.
>
> If immich's own reverse geocoding remains enabled, both systems can overwrite the same fields and step on each other's results.
>
> Disable immich's built-in reverse geocoding before running Immich ReverseGeo. See immich's official docs: [Reverse Geocoding](<https://docs.immich.app/features/reverse-geocoding/>) and [Reverse Geocoding Settings](<https://docs.immich.app/administration/system-settings/#reverse-geocoding-settings>).

> [!WARNING]
>
> **Back up your immich database first**
>
> Immich ReverseGeo reads from and writes to immich's database.
>
> Before running processing on real assets, and before clearing existing location fields, create a proper immich backup first: [Backup and Restore | Immich](<https://docs.immich.app/administration/backup-and-restore/>)

## Quick Links

[Getting Started](<https://immich-reversegeo.github.io/getting-started>) [Installation](<https://immich-reversegeo.github.io/installation>) [Configuration](<https://immich-reversegeo.github.io/configuration>) [Using the App](<https://immich-reversegeo.github.io/using-the-app>) [Data Sources](<https://immich-reversegeo.github.io/data-sources>) [Architecture](<https://immich-reversegeo.github.io/architecture>) [Changelog](<https://immich-reversegeo.github.io/changelog>) [Troubleshooting](<https://immich-reversegeo.github.io/troubleshooting>)
