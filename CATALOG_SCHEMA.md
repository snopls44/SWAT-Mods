# SWAT Mods Catalog

The launcher reads `catalog.json` from the default branch. `schemaVersion` is `1`, `catalogType` must be `mods`, and `items` contains published mods.

Each entry uses a stable `id` matching the ZIP's `mod.json`, plus title, author, description, version, tags, confirmed `gameBuildGuids`, HTTPS `downloadUrl`, optional `repositoryUrl`, ISO 8601 `updatedAt`, stars and archived state.

## Identity, duplicates and updates
The ID is canonical; title, ZIP filename and repository URL are not identity keys.
- Same ID and same version: already imported/installed; do not duplicate.
- Same ID and newer catalog version: offer an update to the existing mod.
- Same ID and older catalog version: never downgrade automatically.
- Different ID: a different mod.

An update replaces the existing version while preserving the same ID. The launcher should stage and validate the new package before replacement and keep the previous version recoverable if the update fails. Manual GitHub download/import must also match by ID rather than create a duplicate.

Never reuse an ID for an unrelated mod or publish duplicate IDs.