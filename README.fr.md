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

## Développement

```sh
pnpm install
pnpm dev     # wiki de dev + rechargement à chaud ; l'URL (port libre aléatoire) s'affiche au démarrage
pnpm build   # dist/TW-Table-Plugin.json + docs/ (wiki de démo, publié par la CI)
```

## Installation

**Démo en ligne** : [https://nikorion.github.io/TW-Table/](https://nikorion.github.io/TW-Table/) — pour essayer le plugin avant de l'installer.

**Depuis la bibliothèque de plugins nikorion** (TiddlyWiki propose ensuite chaque nouvelle version en mise à jour) :

1. Dans votre wiki, créer un tiddler tagué `$:/tags/PluginLibrary`, avec un champ `url` valant `https://nikorion.github.io/tw-dev/library/index.html` et une `caption` comme `nikorion`.
2. Ouvrir *Panneau de configuration → Plugins → Obtenir d'autres plugins*, choisir la bibliothèque nikorion et installer **Table**.

**À la main** : télécharger [`TW-Table-Plugin.json`](https://nikorion.github.io/TW-Table/TW-Table-Plugin.json) et le glisser-déposer sur votre wiki.

Nécessite TiddlyWiki ≥ 5.3.5.

## Licence

MIT — voir `LICENSE`.
