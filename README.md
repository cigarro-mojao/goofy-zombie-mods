# Goofy Zombie Mods

Public registry and author tooling for independently hosted Goofy Zombie mods. The game stays private. This repository contains **no mod source, manifests, icons or downloadable ZIPs**.

| Repository | Owns |
| --- | --- |
| `goofy-zombie-mods` | Minimal registry, schemas, public packaging helper, contributor docs and registry validation. |
| [gzm-god-mode](https://github.com/cigarro-mojao/gzm-god-mode) | God Mode manifest, resources, translations and packages. |
| [gzm-unlock-maximum-players](https://github.com/cigarro-mojao/gzm-unlock-maximum-players) | Player-limit mod manifest, resources, translations and packages. |

These starter mods keep their actual author credit. They use separate repositories and exactly the same registration flow as independent community authors.

## Minimal registry

`catalog.json` uses schema version 2. Each entry contains exactly:

```json
{
  "id": "your_mod",
  "url": "https://your-host.example/dist/your_mod-1.0.0-<hash>.zip",
  "sha256": "<64 lowercase hex characters>"
}
```

The ID reserves a registry identity and must match the downloaded manifest. The direct HTTPS URL locates the package. SHA-256 pins the exact approved bytes. Version, name, author, description, compatibility, rules, dependencies, icon and translations are read from the package's own `manifest.json` and files; none is duplicated in the registry. Size is bounded by the loader rather than repeated in each entry.

The game downloads/verifies author packages into a cache to populate **Mods → Store**, then reads the manifest and PNG preview from each ZIP. This first preview fetch does not install or enable the mod. **Install** reuses the verified archive; a new install starts disabled. Cached verified packages can be browsed/installed offline. Failed author packages are reported individually without hiding valid mods.

Catalog URL:

`https://raw.githubusercontent.com/cigarro-mojao/goofy-zombie-mods/main/catalog.json`

## Register your mod

See [CONTRIBUTING.md](CONTRIBUTING.md) and [the manifest/API guide](manifest_guide.md). Publish your package in your own repository/hosting first, then submit only its generated `dist/catalog-entry.json` entry in a registry pull request. Maintainers review ownership and behavior before approving the new URL/hash.

The public [packaging helper](tools/modkit.py) can be copied to an independent repository as `tools/build_package.py`. Its default layout is `mod/manifest.json` plus payload files and a generated `dist/` directory. It requires Python 3.11+, standard library only; no private game source or Godot import step.

## Validate and simulate publication

```sh
python -m unittest discover -s tests -v
python tools/validate_catalog.py
python tools/validate_catalog.py --online
python tools/validate_catalog.py --local-repos ..
```

The default validator checks the minimal index offline. `--online` downloads the registered author packages, verifies hashes, manifests, file limits and the cross-mod dependency graph. CI audits remote packages on registry pull requests and pushes to main.

`--local-repos ..` simulates the published result using sibling authors' `dist/catalog-entry.json` descriptors and ZIPs. It maps their **real public URLs** to those local author-owned archives without copying sources/artifacts into the registry. The game's integration suite uses this same mapping for its HTTP responses.

For actual publication, push both independent mod repositories' source/build tooling and `dist/` to their **main** branches first, then publish the registry on **main**. Local simulation does not push anything; remote package audits will succeed once those URLs exist. Retain previous content-addressed ZIPs in each author repository for older cached catalogs.

## API scope

Mod API v1 accepts data rules, translations and PNGs. Supported rules are `player.invulnerable` and `multiplayer.max_players`; new behavior requires a game API hook. Arbitrary scripts, scenes, native libraries and PCKs are not loaded. The existing repository license covers the tooling/docs; independently hosted mods carry their own license inside their package.
