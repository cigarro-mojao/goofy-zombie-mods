# Mod Instructions Guide

## 📦 Mod Folder Structure

### Mod's directory structure

```css
mod_template/
├─ manifest.json   <-- [REQUIRED] Mod metadata and configuration
├─ icon.png        <-- [OPTIONAL] Icon shown in the public gallery
├─ README.md       <-- [OPTIONAL] Documentation for users or developers
├─ translations/   <-- [OPTIONAL] Per-locale strings (see Localization below)
│  ├─ en.json
│  └─ es.json
└─ code...         <-- [REQUIRED] Your actual mod logic and assets
```

## 🛠️ Mod Manifest Guide & Template

### Guide `manifest.json`

| KEY | TYPE | DESCRIPTION | REQUIREMENT |
| :--- | :--- | :--- | :--- |
| **IDENTIFICATION**              |
| `id`           | `string` | Unique internal name. <br> • Format: `snake_case` <br> • Example: `mod_template` | **Required** |
| `version`      | `string` | Current version of your mod. <br> • Format: `MAJOR.MINOR.PATCH` <br> • Example: `1.0.0` | **Required** |
| **PUBLIC INFO**                 |
| `icon`         | `string` | Path to `icon.png`. <br> • Format: PNG - 32 x (32 * frame) pixels. | Optional |
| `name`         | `string` \| `object` | Public name shown in the mod manager. <br> May be a per-locale map: `{"en": "...", "es": "..."}`. | **Required** |
| `author`       | `string` | Name or alias of the creator. | **Required** |
| `description`  | `string` \| `object` | Short explanation of what the mod does. <br> May be a per-locale map: `{"en": "...", "es": "..."}`. | Optional |
| **METADATA**                   |
| `tags`         | `array` | Keywords for filtering. The accepted tags are: <ul><li>`gameplay`</li><li>`visual`</li><li>`multiplayer`</li></ul> • Format: `["tag", ...]` <br> • Example: `["visual", "gameplay"]` | Optional |
| **REQUIREMENTS**                |
| `game_version` | `string` | The version of the game this mod is compatible with. <br> Use `*` as wildcard for patches. <br> • Format: `MAJOR.MINOR.PATCH` <br> • Example: `1.0.*` | **Required** |
| `dependencies` | `object` | List of other mod IDs needed to run. <br> Use `*` as wildcard for patches. <br> • Format: `{"mod_id": "version", ...}` <br> • Example: `{"mod_123": "1.1.*", "mod_456": "1.3.2"}` | Optional |
| **LINKS**                       |
| `website`      | `string` | URL to the official mod page or download site. | Optional |
| `repository`   | `string` | URL to the source code repository from `GitHub` or `GitLab`. <br> Must be a `public` repository and only `main` branch will be accepted in order to ensure transparency. | Optional |
| **CREDITS**                     |
| `credits`      | `array` | People who has contributed. <br> Expects Name & Social Media Link. <br> • Format: `[{"name": "{NAME}", "link": "{LINK}", ...}]` <br> • Example: `[{"name": "G.A.B.R.A.", "link": "https://..."}]` | Optional |
| **STATUS**                      |
| `last_update`  | `string` | DateTime of the mod last change. <br> • Format: `YYYY-MM-DD HH:MM:SS` <br> • Example: `2000-01-01 00:00:00` | Optional |
| `checksum`     | `string` | File integrity hash. <br> • Format: `SHA-256`. | **Required** |
| `enabled`      | `bool` | Mod's activity status. <br> • `true`: active. <br> • `false`: installed but inactive. | Optional |

---

### Template `manifest.json`

```json
{
	"id": "unlock_maximum_players",
	"version": "1.0.0",
	"icon": "icon.png",
	"name": "Unlock Maximum Players",
	"author": "G.A.B.R.A.",
	"description": "Removes the default multiplayer limit of 3 players.",
	"tags": [
        "multiplayer"
	],
	"game_version": "1.0",
	"dependencies": {},
	"website": "",
	"repository": "",
	"credits": [
		{
			"name": "G.A.B.R.A.",
			"link": ""
		}
	],
	"last_update": "2000-01-01 00:00:00",
	"checksum": "",
	"enabled": true
}
```

---

## 🌐 Localization (i18n)

Each mod is self-contained and ships its own translations — it does not depend on the game's
translation files.

1. **Manifest fields**: `name` and `description` can be a plain string OR a per-locale map. The game
   picks the active locale (falling back to the base language, then `en`):

   ```json
   "name": { "en": "God Mode", "es": "Modo Dios" },
   "description": { "en": "Become invincible.", "es": "Vuélvete invencible." }
   ```

2. **Mod content strings**: put a `translations/` folder with one JSON per locale, named by locale
   code (`en.json`, `es.json`, `fr.json`, …). Each file maps translation keys to text:

   ```json
   { "MOD_GOD_MODE_TAGLINE": "Nothing can stop you now." }
   ```

   When the mod is installed, the game loads these at startup and registers them with the
   `TranslationServer`, so the keys resolve via `tr("MOD_GOD_MODE_TAGLINE")` like any other string.
   Use a unique prefix per mod (e.g. `MOD_<ID>_*`) to avoid clashing with other mods.

Supported locale codes: `en, es, ca, fr, de, ru, el, ja, ko, zh_CN, zh_TW`.