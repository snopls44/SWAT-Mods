# SWAT Launcher: Mod publishing and installation tutorial

This guide explains how to package a mod for the SWAT Launcher, add it to the community catalog, install it, and publish updates. It does **not** explain how to program or create the mod itself. A separate mod-development tutorial may be published in the future.

## Important concepts

- A **Vanilla installation** is the existing game installation.
- A **Modded instance** is an independent working copy made from an already installed Vanilla game. Creating an instance does not download or install the game.
- Mods are imported into the launcher's local mod library, then managed/applied to a selected Modded instance.
- The stable `mod.json` `id` identifies the mod. Keep it unchanged across versions and distribution sources.
- A mod ZIP is a package of the mod's files plus its `mod.json` manifest.

## 1. Package layout

The ZIP must contain exactly one `mod.json` at its root (or inside the single top-level folder produced by a GitHub source archive). Files listed in `mod.json` must exist in the ZIP.

Example:

```text
ExampleMod/
├── mod.json
└── BepInEx/
    └── plugins/
        └── ExampleMod/
            └── ExampleMod.dll
```

The launcher reads the manifest and validates the declared source and target paths. The `source` is the path inside the ZIP; `target` is the relative path where the file is placed inside the selected Modded instance. Do not use absolute paths, drive letters, or `..` path traversal.

## 2. Fill in mod.json

Start from [templates/mod.json](templates/mod.json).

Required fields:
- `schemaVersion`: currently `1`.
- `id`: globally unique, stable ID such as `com.author.examplemod`.
- `name`: internal name.
- `title`: display title.
- `author.name`: author display name.
- `description`: what the mod does.
- `version`: mod release version.
- `files`: at least one file mapping with `source` and `target`.

Optional metadata includes website, support, license, tags, game build GUID compatibility, dependencies and loader information.

The `game.buildGuids` array should contain the exact SWAT Game build GUIDs the mod supports when known. Leave it empty if compatibility has not been confirmed; do not guess.

## 3. Test locally

1. Create a ZIP containing `mod.json` and every file declared by its `files` list.
2. In SWAT Launcher, open **Community → Local**.
3. Drop the ZIP into the import area or click it to browse.
4. Confirm that the mod appears in the local library with the expected ID and version.
5. Keep the original ZIP as your release artifact.

Importing a mod into the local library does not itself install the game or create a game instance.

## 4. Publish in SWAT-Mods

1. Upload the ZIP to a stable HTTPS download location.
2. Add one entry to `catalog.json` using the catalog schema.
3. Set the catalog entry `id` to exactly the same value as `mod.json` `id`.
4. Set `version` to the same version as the manifest.
5. Set `downloadUrl` to the direct ZIP download URL.
6. Set `gameBuildGuids` to the compatible build GUIDs.
7. Commit the catalog change to the repository's `main` branch.

The launcher reads the catalog from this repository. It does not search arbitrary GitHub repositories for mods.

## 5. Publish an update

1. Keep the existing `id`. Never create a new ID just because the mod has a new version or a new download URL.
2. Increase `version` in both `mod.json` and the catalog entry.
3. Publish the new ZIP and update `downloadUrl`.
4. Update the compatibility GUIDs and `updatedAt` as appropriate.
5. Commit the updated catalog entry.

The launcher can recognize that a catalog entry refers to a mod already present in the local library by comparing IDs. Updating a mod through the app is a separate update workflow; until that workflow is available, download the new ZIP and import it manually. The launcher must not install a second copy under a different identity.

## 6. Install into a Modded instance

A Modded instance must already exist and is based on an existing Vanilla installation. The instance is not a second game download.

The launcher's instance-level mod management is the place where imported mods are applied, disabled, updated or removed. Do not manually copy files into the Vanilla source installation.

## Identity and duplicate prevention

The stable ID is the canonical identity. Titles, filenames, authors, GitHub repository names and download URLs are not identity keys.

- Same ID and same version: already present.
- Same ID and newer catalog version: update candidate for the same mod, not a new mod.
- Different ID: a different mod, even if its title looks similar.

Do not publish duplicate IDs in the catalog or reuse an ID for an unrelated project.

## Troubleshooting

- **ZIP rejected / missing mod.json:** put one valid `mod.json` at the package root.
- **Package missing a declared file:** check every `files[].source` path against the ZIP contents.
- **Invalid target:** use a safe relative path such as `BepInEx/plugins/ExampleMod/ExampleMod.dll`.
- **Already in library:** the same ID and version are already imported. Do not change the ID to bypass this check.
