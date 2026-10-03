# TW-Table

[English](README.md) · **Français**

Un plugin [TiddlyWiki](https://tiddlywiki.com) qui fournit `<<table>>` : un tableau HTML statique, en lecture seule, construit à partir d'un bloc CSV saisi dans le corps d'un tiddler — pas de filtre, pas de tiddler par ligne, rien n'est stocké.

Extrait de la fonction `table-csv` de [Shiraz](https://github.com/kookma/TW-Shiraz) (de Mohammad Rahmani) pour en faire un plugin indépendant, sans aucune dépendance à Shiraz. Pour un tableau éditable reposant sur des tiddlers, voir le plugin voisin [TW-Dynamic-Table](https://github.com/nikorion/TW-Dynamic-Table).

```
@@.nk-table-block
Title,Status
Buy milk,open
@@

<<table>>
```

La liste complète des paramètres, les procédures de format de colonne et les réglages sont documentés dans le readme du plugin lui-même (`src/table/language/<lang>/readme.tid`), visible depuis l'onglet Plugins du panneau de contrôle une fois le plugin installé.

## Sommaire

- [Développement](#développement)
- [Licence](#licence)

## Développement

```sh
pnpm install
pnpm dev     # wiki de dev + rechargement à chaud ; l'URL (port libre aléatoire) s'affiche au démarrage
pnpm build   # dist/TW-Table-Plugin.json + docs/TW-Table-Wiki.html
```

[↑ Retour au sommaire](#sommaire)

## Licence

MIT — voir `LICENSE`.

[↑ Retour au sommaire](#sommaire)
