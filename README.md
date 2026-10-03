# TW-Table

**English** · [Français](README.fr.md)

A [TiddlyWiki](https://tiddlywiki.com) plugin providing `<<table>>`: a static, read-only HTML table rendered from a CSV block typed in a tiddler's body — no filter, no per-row tiddler, nothing stored.

Extracted from [Shiraz](https://github.com/kookma/TW-Shiraz)'s `table-csv` feature (by Mohammad Rahmani) as an independent plugin, with no dependency on Shiraz itself. For an editable table backed by tiddlers instead, see the sibling plugin [TW-Dynamic-Table](https://github.com/nikorion/TW-Dynamic-Table).

```
@@.nk-table-block
Title,Status
Buy milk,open
@@

<<table>>
```

Full parameter list, column-format procedures and settings are documented in the plugin's own readme (`src/table/language/<lang>/readme.tid`), visible from the control panel's Plugins tab once installed.

## Contents

- [Development](#development)
- [License](#license)

## Development

```sh
pnpm install
pnpm dev     # dev wiki + hot reload; the URL (random free port) is printed on start
pnpm build   # dist/TW-Table-Plugin.json + docs/TW-Table-Wiki.html
```

[↑ Back to contents](#contents)

## License

MIT — see `LICENSE`.

[↑ Back to contents](#contents)
