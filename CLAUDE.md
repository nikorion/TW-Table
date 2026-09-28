# TW-Table — contexte projet pour Claude

> **Avant toute tâche sur ce plugin, consulter d'abord le `CLAUDE.md` du workspace** (`../CLAUDE.md`) et ses `guides/` : outillage de dev commun (pnpm, `dev.cjs`/HMR, Ctrl+C, git push), pièges PowerShell/Windows, `publishFilter`, conventions modules JS, symlink. Ci-dessous : uniquement le spécifique à TW-Table.

## Ce que c'est
Plugin TiddlyWiki (`$:/plugins/nikorion/table`) fournissant la macro `<<table>>` : tableau HTML **statique, en lecture seule**, rendu depuis un bloc de texte CSV saisi dans le corps d'un tiddler (`@@.nk-table-block ... @@`) — pas de filtre, pas de tiddler par ligne, rien de stocké, rien d'éditable. Aucun JS : que du wikitext + CSS.

**Origine : extrait de [Shiraz](https://github.com/kookma/TW-Shiraz)** (macro `table-csv`, dossier `tables/procs/ct-*` de ce plugin tiers, auteur Mohammad Rahmani, MIT). Reprend le nom `TW-Table` libéré le 2026-09-24 quand le plugin Dynamic Table (`dt-*`, l'autre moitié du dossier `tables/` de Shiraz) a été renommé [[TW-Dynamic-Table]] — voir son CLAUDE.md et `guides/extraire-plugin.md` du workspace pour l'historique de la séparation `dt-*`/`ct-*`.

## À ne pas confondre avec TW-Dynamic-Table
Plugin frère, née du même dossier Shiraz `tables/` mais fonctionnellement disjoint : `<<dyntable>>` (TW-Dynamic-Table) génère un tableau depuis un **filtre** (une ligne par tiddler, éditable, triable en persistant, paginable) ; `<<table>>` (ici) génère un tableau depuis un **bloc de texte CSV** collé dans un tiddler (aucun tiddler par ligne, non éditable, tri en mémoire non persistant sauf état de tri). Les deux sont installables ensemble sans collision : macros, classes CSS (`nk-table-*` ici vs `nk-dyntable-*` chez dyntable), tiddler d'état (`$:/state/nikorion/table/…` ici vs `$:/state/nikorion/dyntable`) et procédures internes sont dans des espaces de noms disjoints — voir « Piège évité » plus bas pour le seul point de vigilance.

## Procédure de traduction `table-lingo`
Une seule chaîne à traduire (libellé de l'onglet Réglages) → `table-lingo` seule, sans `-text`/`-value` (`language/lingo.tid`). Nom libre depuis le 2026-09-29 : TW-Dynamic-Table, qui l'occupait (héritage de son ancien nom court `table`), utilise désormais `dyntable-lingo` ; avant, ce plugin s'appelait `tablecsv-lingo`. Ne pas redonner à dyntable un nom `table-*` global.

## Renommages faits lors du portage (namespace Shiraz → nikorion/table)
| Shiraz | TW-Table |
|---|---|
| `$:/plugins/kookma/shiraz/tables/procs/ct-*` | `$:/plugins/nikorion/table/procedures/nk-table-*` (préfixe `ct-` gardé jusqu'au 2026-09-29, puis `nk-table-`, convention du workspace ; `ct-table-csv` → `nk-table`) |
| macro `table-csv` | `table` |
| `$:/state/tablecsv/…` | `$:/state/nikorion/table/…` |
| classe CSS `shiraz-csvtable-header` | `nk-table-header` (ex-`tblcsv-header`) |
| classe `tbl-sort-svg` des boutons de tri (stylée par la seule CSS de dyntable) | `nk-table-sort-svg`, règle propre dans `styles/nk-table.css` (2026-09-29 : les boutons perdaient leur style sans dyntable) |
| classe CSS `shiraz-star` | `nk-table-star` |
| valeur par défaut du paramètre `dclass` : `"dblock"` | `"nk-table-block"` |
| `ct-apps.tid` (`nomenclature`/`mathbox`/`subscripts`/`superscripts`, enrobages KaTeX de `table-csv`) | **non porté** (niche, dépendant de KaTeX, facile à récrire soi-même) |

Procédures de format de colonne (`nk-table-formats-basic/date/math/misc/task.tid`) : noms **inchangés** (`text`, `code`, `date`, `email`, `rate`, `checkbox`, `todo`, `katex`…) — aucun risque malgré leur généricité : elles ne sont **pas** taggées `$:/tags/Global`, seulement importées localement dans la procédure `table` via `\import [all[tiddlers+shadows]prefix[$:/plugins/nikorion/table/procedures/nk-table-formats]!is[draft]]` (mécanisme déjà en place côté Shiraz, conservé tel quel — pas de pollution de l'espace de noms global).

Dépendance optionnelle non fournie : un plugin KaTeX (widget `<$latex>`, requis par `katex`/`katex-inline`/`pu`/`equation` dans `nk-table-formats-math.tid`) — absent du workspace nikorion, ces formats ne rendent rien sans lui (comportement hérité de Shiraz tel quel, pas de garde `is[missing]` ajoutée).

## Structure
```
src/table/
  procedures/
    nk-table.tid           ← point d'entrée, \procedure table(...) (ex-`ct-table-csv` de Shiraz)
    nk-table-header-template.tid ← en-tête de colonne cliquable (tri)
    nk-table-formats-basic.tid   ← text / code / transclude
    nk-table-formats-date.tid    ← date / shortdate / longdate
    nk-table-formats-math.tid    ← katex / katex-inline / pu / equation (nécessite un plugin KaTeX externe)
    nk-table-formats-misc.tid    ← email / rate (notation étoilée)
    nk-table-formats-task.tid    ← checkbox / todo (+ todo-action, bascule x/- dans la source)
  styles/
    nk-table.css(+.meta)  ← masque les <p> générés par @@…@@, capitalise les boutons d'en-tête
    nk-table-star.css(+.meta)    ← icône étoile (notation)
    nk-table-katex.css(+.meta)   ← alignement KaTeX, cellules .table-mathbox
    table-variants.css(+.meta) ← `table-borderless`/`table-hover`/`thead-*`/`table-striped-*`/`table-rounded*`… : copie de celle de TW-Dynamic-Table (elle-même portée de Shiraz `styles/tables.css`) moins les règles `tfoot-*`/pied de tableau (`nk-dyntable-*`), sans objet en statique. Copie assumée (pas de plugin partagé) : si les deux sont installés les règles communes sont chargées deux fois ; **toute correction de variante à répercuter dans les deux fichiers**. Tiny-Bootstrap ne les fournit pas
  language/
    lingo.tid                      ← table-lingo (une seule chaîne : le libellé de l'onglet Réglages)
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
