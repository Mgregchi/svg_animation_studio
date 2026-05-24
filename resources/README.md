# Resources

This directory contains static assets, documentation guides, and example scenes for SVG Animation Studio. These resources are loaded dynamically by the application.

---

## Directory Structure

- [`docs/`](docs/) — Detailed documentation guides covering animation concepts, export formats, AI prompting, and the JSON schema.
- [`examples/`](examples/) — Curated example animation scenes stored as JSON files. These can be loaded directly into the studio.
- [`index.json`](index.json) — The manifest file that lists and indexes all example scenes displayed in the application's gallery.

---

## Scene Index Manifest (`index.json`)

The `index.json` file serves as the registry for all example scenes that appear in the app's gallery under **Help → Examples**.

When adding a new example scene:
1. Export the scene as JSON and place it in the `examples/` directory.
2. Register the scene in `index.json` using the following schema:

```json
{
  "id": "unique-kebab-case-id",
  "name": "Display Name",
  "description": "Short description of what the animation does and what it demonstrates.",
  "tags": ["motion", "beginner", "loop", "ui", "reveal"],
  "file": "examples/unique-kebab-case-id.json"
}
```

---

## Contributing

To contribute a new example scene or documentation guide, please refer to the main repository [CONTRIBUTING.md](../CONTRIBUTING.md) for quality guidelines and instructions.
