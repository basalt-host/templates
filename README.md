# Basalt Game Templates

Official game server templates for [Basalt](https://basalt.host).

A template is a declarative JSON file that tells Basalt how to run a game server: which Docker image to use, which settings to expose in the panel (grouped into categories, with conditional fields), which paths the file browser should show, and what gets backed up. The panel renders the settings form directly from the template, so adding support for a new game requires no code changes.

## Available templates

| Game | Template | Docker image |
| --- | --- | --- |
| Minecraft | [`minecraft/itzg.template.json`](minecraft/itzg.template.json) | [`itzg/minecraft-server`](https://hub.docker.com/r/itzg/minecraft-server) |
| Hytale | [`hytale/hybrowse.template.json`](hytale/hybrowse.template.json) | [`hybrowse/hytale-server`](https://hub.docker.com/r/hybrowse/hytale-server) |

The Minecraft template supports vanilla plus 30+ server types (Paper, Fabric, Forge, NeoForge, Purpur, Folia, and more) and modpack installation from Modrinth, CurseForge, and Feed The Beast. The Hytale template is powered by the Hybrowse production image with auto-download, CurseForge mod management, and internal backups.

## Repository layout

```
template.schema.json     JSON Schema (draft 2020-12) that all templates must satisfy
<game>/<name>.template.json   One template per file, grouped by game
assets/                  Game logos referenced by the templates
```

## Template format

Every template must conform to [`template.schema.json`](template.schema.json). The top level looks like this:

```jsonc
{
  "id": "c0b5e759-0b8a-4b21-906a-82a14b59f180",   // UUID
  "game": "Minecraft",
  "name": "itzg/docker-minecraft-server",
  "description": "Official Basalt Minecraft server template.",
  "version": "2026.7.15",                          // must match \d+.\d+.\d+
  "author": "Basalt Team",
  "logo": "https://raw.githubusercontent.com/basalt-host/templates/refs/heads/main/assets/minecraft.png",
  "settings": { /* versions, categories, fileBrowser, playit */ },
  "docker":   { /* image, startupCommand, environment */ },
  "backups":  { /* enabled, targets */ }
}
```

Key concepts:

- **Categories and fields** (`settings.categories`): the settings form shown in the panel. Fields map to container environment variables by `key` and declare a `kind` (`TEXT`, `NUMBER`, `BOOLEAN`, `PASSWORD`, `SELECT`, `COMBOBOX`, `LIST`, `SLIDER`, `TEXTAREA`, `TIMEZONE`, and others).
- **Conditional fields** (`expand` / `enable`): fields and select options can reveal nested children when a condition matches (`equals`, `notEquals`, `in`, `notIn`, `nonEmpty`, `isEmpty`). This keeps the form small while still exposing advanced options.
- **File browser** (`settings.fileBrowser`): the container root folder shown to users, plus read-only and hidden paths.
- **Playit** (`settings.playit`): default tunnel settings (protocol and port) for exposing the server through playit.gg.
- **Docker** (`docker`): the image to run, an optional startup command with `{{ENV_KEY}}` placeholders, and default environment variables.
- **Backups** (`backups.targets`): container paths that Basalt can archive, each with a stable `id` and a human-readable `label`.

## Validating a template

Validate against the schema before opening a PR:

```bash
uvx check-jsonschema --schemafile template.schema.json path/to/your.template.json
```

## Requesting or contributing a template

- **Request a template**: open a [template request](https://github.com/basalt-host/issues/issues/new?template=template_request.yml) in the Basalt issue tracker with links to the dedicated server download and setup docs.
- **Contribute a template**: fork this repo and open a PR. Place the file at `<game>/<name>.template.json`, add a logo under `assets/`, generate a fresh UUID for `id`, and make sure validation passes.

## License

[MPL-2.0](LICENSE)
