# TW-Table — contexte projet pour Claude

> **Avant toute tâche sur ce plugin, consulter d'abord le `CLAUDE.md` du workspace** (`../CLAUDE.md`) et ses `guides/` : outillage de dev commun (pnpm, `dev.cjs`/HMR, Ctrl+C, git push), pièges PowerShell/Windows, `publishFilter`, conventions modules JS, symlink. Ci-dessous : uniquement le spécifique à TW-Table.

## Ce que c'est
Plugin TiddlyWiki (`$:/plugins/nikorion/table`) fournissant la macro `<<table>>` : tableau HTML **statique, en lecture seule**, rendu depuis un bloc de texte CSV saisi dans le corps d'un tiddler (`@@.tblcsv-block ... @@`) — pas de filtre, pas de tiddler par ligne, rien de stocké, rien d'éditable. Aucun JS : que du wikitext + CSS.

**Origine : extrait de [Shiraz](https://github.com/kookma/TW-Shiraz)** (macro `table-csv`, dossier `tables/procs/ct-*` de ce plugin tiers, auteur Mohammad Rahmani, MIT). Reprend le nom `TW-Table` libéré le 2026-09-24 quand le plugin Dynamic Table (`dt-*`, l'autre moitié du dossier `tables/` de Shiraz) a été renommé [[TW-Dynamic-Table]] — voir son CLAUDE.md et `guides/extraire-plugin.md` du workspace pour l'historique de la séparation `dt-*`/`ct-*`.

## À ne pas confondre avec TW-Dynamic-Table
Plugin frère, née du même dossier Shiraz `tables/` mais fonctionnellement disjoint : `<<dyntable>>` (TW-Dynamic-Table) génère un tableau depuis un **filtre** (une ligne par tiddler, éditable, triable en persistant, paginable) ; `<<table>>` (ici) génère un tableau depuis un **bloc de texte CSV** collé dans un tiddler (aucun tiddler par ligne, non éditable, tri en mémoire non persistant sauf état de tri). Les deux sont installables ensemble sans collision : macros, classes CSS (`tblcsv-*` ici vs `tbldyn-*` chez dyntable), tiddler d'état (`$:/state/nikorion/table/…` ici vs `$:/state/nikorion/dyntable`) et procédures internes sont dans des espaces de noms disjoints — voir « Piège évité » plus bas pour le seul point de vigilance.

## Piège évité : nom de procédure globale `table-lingo`
Ce plugin **n'utilise pas** le nom `table-lingo` pour sa procédure de traduction (une seule chaîne à traduire : le libellé de l'onglet Réglages), alors que le nom court du plugin est `table` — parce que TW-Dynamic-Table définit déjà une procédure globale `table-lingo` (conservée telle quelle lors de son renommage en `dyntable`, voir son CLAUDE.md). Une procédure `table-lingo` ici l'écraserait silencieusement (ou serait écrasée par elle) si les deux plugins sont installés ensemble. Utilisé à la place : `tablecsv-lingo` (`language/lingo.tid`).

## Renommages faits lors du portage (namespace Shiraz → nikorion/table)
| Shiraz | TW-Table |
|---|---|
| `$:/plugins/kookma/shiraz/tables/procs/ct-*` | `$:/plugins/nikorion/table/procedures/ct-*` (titres de fichiers gardés, continuité avec la source) |
| macro `table-csv` | `table` |
| `$:/state/tablecsv/…` | `$:/state/nikorion/table/…` |
| classe CSS `shiraz-csvtable-header` | `tblcsv-header` |
| classe CSS `shiraz-star` | `tblcsv-star` |
| valeur par défaut du paramètre `dclass` : `"dblock"` | `"tblcsv-block"` |
| `ct-apps.tid` (`nomenclature`/`mathbox`/`subscripts`/`superscripts`, enrobages KaTeX de `table-csv`) | **non porté** (niche, dépendant de KaTeX, facile à récrire soi-même) |

Procédures de format de colonne (`ct-formats-basic/date/math/misc/task.tid`) : noms **inchangés** (`text`, `code`, `date`, `email`, `rate`, `checkbox`, `todo`, `katex`…) — aucun risque malgré leur généricité : elles ne sont **pas** taggées `$:/tags/Global`, seulement importées localement dans la procédure `table` via `\import [all[tiddlers+shadows]prefix[$:/plugins/nikorion/table/procedures/ct-formats]!is[draft]]` (mécanisme déjà en place côté Shiraz, conservé tel quel — pas de pollution de l'espace de noms global).

Dépendance optionnelle non fournie : un plugin KaTeX (widget `<$latex>`, requis par `katex`/`katex-inline`/`pu`/`equation` dans `ct-formats-math.tid`) — absent du workspace nikorion, ces formats ne rendent rien sans lui (comportement hérité de Shiraz tel quel, pas de garde `is[missing]` ajoutée).

## Structure
```
src/table/
  procedures/
    ct-table-csv.tid       ← point d'entrée, \procedure table(...) (fichier gardé sous son nom d'origine)
    ct-header-template.tid ← en-tête de colonne cliquable (tri)
    ct-formats-basic.tid   ← text / code / transclude
    ct-formats-date.tid    ← date / shortdate / longdate
    ct-formats-math.tid    ← katex / katex-inline / pu / equation (nécessite un plugin KaTeX externe)
    ct-formats-misc.tid    ← email / rate (notation étoilée)
    ct-formats-task.tid    ← checkbox / todo (+ todo-action, bascule x/- dans la source)
  styles/
    ct-tables.css(+.meta)  ← masque les <p> générés par @@…@@, capitalise les boutons d'en-tête
    ct-star.css(+.meta)    ← icône étoile (notation)
    ct-katex.css(+.meta)   ← alignement KaTeX, cellules .table-mathbox
  language/
    lingo.tid                      ← tablecsv-lingo (une seule chaîne : le libellé de l'onglet Réglages)
    en-GB|fr-FR/{readme,history,license}.tid, settings.multids
  settings.tid              ← onglet ControlPanel, placeholder (rien à configurer pour l'instant)
  readme.tid / history.tid / licence.tid  ← sélecteurs de langue
  plugin.info

wiki/                      ← wiki TW de dev : Playground.tid (anglais en dur, pas d'i18n)
dist/                      ← généré par pnpm build, gitignored
docs/                      ← TW-Table-Wiki.html standalone (distribution)
```

## Spécificités dev
- Aucun module JS → pas de `pnpm lint`, `nodemon.json` ne surveille que `plugin.info`.
- HMR : tout est `.tid`/`.css`/`.multids`, poussé à chaud dans le navigateur déjà ouvert. Un changement de `plugin.info` reboote (nodemon).
- `pnpm build` → `dist/TW-Table-Plugin.json` + `docs/TW-Table-Wiki.html`.
- `pnpm build` validé (2026-09-24, aucune erreur de parsing wikitext) ; rendu visuel du Playground (`pnpm dev`) pas encore vérifié dans un navigateur.
