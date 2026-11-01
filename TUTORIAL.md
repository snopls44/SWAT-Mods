# SWAT Launcher: Mod publishing and package tutorial

This guide covers the SWAT Launcher package format, local import, catalog publishing and version updates. It does **not** teach how to program or create a mod. A separate mod-development tutorial may be added in the future.

## How the launcher is organized

- **Vanilla installation:** the existing game installation.
- **Modded instance:** an independent copy of an already installed Vanilla game. Creating an instance does not download or install the game.
- **Local mod library:** stores imported mod packages and their metadata.
- **Modded instance management:** the intended place to install, disable, update and remove mods in that particular game copy.

Importing a ZIP into the local library is not the same as installing it into an instance. The general per-instance mod deployment workflow is still being developed; do not claim that a library import has applied the mod to the game.

## 1. Package layout

The ZIP must contain exactly one `mod.json` at its root, or inside the single top-level folder produced by a GitHub source archive. Every file declared in `mod.json` must exist in the ZIP.

Example for a BepInEx plugin:

```text
ExampleMod/
├── mod.json
└── BepInEx/
    └── plugins/
        └── ExampleMod/
            └── ExampleMod.dll
```

The manifest maps each package file to a relative destination:
- `source`: path inside the ZIP.
- `target`: relative path inside the selected Modded instance.

For a BepInEx plugin, a typical target is `BepInEx/plugins/ExampleMod/ExampleMod.dll`.

Never use absolute paths, drive letters, or `..` traversal. Do not include the game itself, game assemblies, or a copy of BepInEx in a normal plugin package. The target game copy already provides the game files; BepInEx is installed separately when needed. Modded Queue or another plugin API is an optional dependency only when the mod actually uses it.

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
- `files`: at least one `source` / `target` mapping.

Optional metadata includes website, support, license, tags, game build GUID compatibility, dependencies and loader information.

Use `game.buildGuids` for exact SWAT Game build GUIDs confirmed to work. Leave the list empty when compatibility is unknown; never guess. Declare BepInEx and other mod dependencies accurately. Modded Queue is optional and must not be treated as a universal requirement.

## 3. Import and validate locally

1. Create a ZIP containing `mod.json` and all files declared by `files`.
2. Open **Community → Local** in SWAT Launcher.
3. Drop the ZIP into the import area or click to browse.
4. Confirm the title, author, version and stable ID in the local library.
5. Keep the ZIP used for the release.

The launcher validates the manifest, required files, safe paths and package structure. It stores the imported package in its local library. This step does not install the game, create an instance, or deploy the mod into an instance.

## 4. Publish in SWAT-Mods

1. Upload the ZIP to a stable HTTPS download location.
2. Add an entry to the root `catalog.json`.
3. Copy the exact `id` from `mod.json` into the catalog entry.
4. Keep the catalog `version` equal to the manifest version.
5. Set `downloadUrl` to the direct ZIP URL.
6. Add only confirmed compatible build GUIDs.
7. Commit the catalog change to the repository's `main` branch.

The launcher reads this curated catalog. It does not search arbitrary GitHub repositories for mods.

## 5. Publish a new version

1. Keep the same `id`. A new release is still the same mod.
2. Increase the version in both `mod.json` and the catalog entry.
3. Publish the new ZIP and change `downloadUrl`.
4. Update compatibility information and `updatedAt`.
5. Commit the catalog update.

The launcher uses the stable ID to recognize an already imported mod, even if its ZIP or hosting repository changes. The intended update flow is through the launcher, with manual GitHub download/import as a fallback. The in-app update/replace workflow is not yet complete, so do not describe it as available until it is implemented and tested. A newer version must update the existing mod identity, not create a duplicate mod.

## 6. Install into an instance

A Modded instance is created from an existing Vanilla installation and contains its own game copy. It is not a game installer.

When per-instance mod deployment is available, select the target Modded instance and install the mod there. Keep Vanilla unchanged. Each instance should be able to have its own enabled/disabled mod set and versions.

## Stable identity and duplicate prevention

The `mod.json` `id` is the canonical identity. Titles, filenames, authors, GitHub repository names and download URLs are not identity keys.

- Same ID and same version: already imported.
- Same ID and newer version: update candidate for the existing mod.
- Different ID: a different mod, even if its title looks similar.

Never change an ID just to bypass a duplicate warning. Never reuse an ID for an unrelated project.

## Troubleshooting

- **Missing manifest:** put exactly one `mod.json` at the package root.
- **Missing declared file:** check every `files[].source` against the ZIP contents.
- **Invalid destination:** use a safe relative path, e.g. `BepInEx/plugins/ExampleMod/ExampleMod.dll`.
- **Already in library:** the same ID and version is already imported. Keep the ID and use the update workflow when available.
- **Mod does not load:** verify the selected instance has the required loader, the correct game build is supported, and any declared optional dependencies are installed.
