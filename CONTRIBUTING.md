# Register or update an independently hosted mod

1. Create your own public mod repository or HTTPS hosting location. This registry never owns your source or manifest.
2. Follow [Mod API v1](manifest_guide.md). Put the package payload in `mod/`, including its authoritative `manifest.json`, optional PNGs/translations, documentation and license.
3. Copy `tools/modkit.py` from this repository to your repo as `tools/build_package.py`. Run `python tools/build_package.py` and `python tools/build_package.py --check` (Python 3.11+). GitHub repositories referenced by the manifest default to their `main/dist` raw HTTPS directory; other hosts use `--base-url https://host/path`.
4. Commit and publish your payload, tool and `dist/` **in your own repository**. Retain old content-addressed ZIPs. Ensure the direct URL from `dist/catalog-entry.json` returns the ZIP without redirection.
5. Fork `goofy-zombie-mods` and add only the `{id, url, sha256}` object from your generated descriptor to `catalog.json.mods`.
6. Run `python tools/validate_catalog.py --online` and `python -m unittest discover -s tests -v`.
7. Open a pull request against **main**. Explain ownership of the mod ID, changes, tested game versions, dependencies, multiplayer behavior and license. CI audits the actual published packages; maintainers review and test before merging.
8. After merge, players refresh **Mods → Store**. Metadata comes from your own package, and the same verified ZIP is used for installation.

**Do not submit your manifest, code, assets, translations, icon or ZIP to this registry.** Do not add display metadata, versions, file sizes, icon URLs or dependency lists to catalog entries. The registry ID identifies the approved namespace; URL and SHA-256 identify the exact approved downloadable package.

For updates, keep your ID, increment your manifest version, build/publish a new immutable ZIP, and submit its new registry URL/hash. Maintainers verify that the original author approved updates to existing IDs. A mutable URL changing its bytes fails the registered checksum rather than silently changing approved content.

For local simulation before publication, keep independent repositories as siblings, each with its own `dist/catalog-entry.json` and matching ZIP. Run `python tools/validate_catalog.py --local-repos ..`. The game test runner also resolves these exact URLs against author-owned local artifacts. This is an offline simulation and does not upload anything.

## Repository responsibilities

- Author repositories own payloads, licensing, packages, version history and package validation.
- This repository owns the minimal registry, schemas/tooling, namespace review and cross-package audit.
- The private game owns the runtime loader and public rule hooks.

A new API capability should be proposed with its intended behavior and data contract. The manifest is authoritative for content, but authors cannot introduce unsupported rules without a corresponding game API update.
