# SWAT Mods Catalog

The launcher reads `catalog.json` from the default branch.

## Catalog format

- `schemaVersion`: currently `1`.
- `catalogType`: must be `mods`.
- `items`: published mod entries.

Each mod entry:

| Field | Meaning |
|---|---|
| `id` | Stable unique mod identity. Must match `mod.json`'s `id`. |
| `name` | Optional internal/display name. |
| `title` | Human-readable title. |
| `author` | Author display name. |
| `description` | Short description. |
| `version` | Published mod version. |
| `tags` | Searchable tags. |
| `gameBuildGuids` | Compatible SWAT Game build GUIDs. |
| `downloadUrl` | HTTPS URL to a ZIP containing the mod manifest and files. |
| `repositoryUrl` | Optional source/project URL. |
| `updatedAt` | ISO 8601 update timestamp. |
| `stars` | Optional catalog popularity value. |
| `archived` | Whether the entry is unavailable for new installs. |

## Identity and duplicates

`id` is the identity key, not the title, ZIP filename, or hosting repository. It must remain stable across releases and match the ID inside the downloaded `mod.json`.

The launcher compares catalog IDs with its local library. If the ID already exists locally, it marks the catalog item as already owned and prevents a second install, even if the catalog lists a newer version. Version upgrades are a separate workflow.

Do not reuse an ID for a different mod. Do not publish multiple entries with the same ID.

## Example

```json
{
  "schemaVersion": 1,
  "catalogType": "mods",
  "items": [
    {
      "id": "com.author.examplemod",
      "title": "Example Mod",
      "author": "Author",
      "description": "Example catalog entry.",
      "version": "1.0.0",
      "tags": ["example"],
      "gameBuildGuids": [],
      "downloadUrl": "https://example.com/example-mod.zip",
      "repositoryUrl": "https://github.com/author/example-mod",
      "updatedAt": "2026-11-01T00:00:00Z",
      "stars": 0,
      "archived": false
    }
  ]
}
```
