# Architecture

```
bin/arcade ── Core ──┬── DLLoader<IGraphics> ── lib/arcade_{sfml,sdl2,ncurses,libcaca}.so
                     └── DLLoader<IGames>    ── lib/arcade_{menu,snake,centipede}.so
```

## Cœur (`src/core/`)

1. `./arcade lib/arcade_sfml.so` : la bibliothèque passée en argument sert à afficher le menu.
2. Le menu (`lib/arcade_menu.so`, un `IGames` comme les autres) liste les jeux et les affichages trouvés dans `lib/`.
3. `Entrée` : le cœur décharge le menu, charge le jeu et l'affichage choisis, puis boucle toutes les 16 ms :
   `getEvent()` → `update(event)` → `getPixelMap()` → `draw()` → `drawText(score)` → `display()`.
4. `F2` / `F1` : la bibliothèque graphique (ou le jeu) suivante est chargée, la fenêtre recréée ; l'état du jeu reste en mémoire.
5. Fin de partie ou `Échap` : retour au menu, dans la bibliothèque graphique courante.

## Interfaces (`src/interfaces/`)

| Interface | Méthodes | Point d'entrée du `.so` |
| --- | --- | --- |
| `IGames` (+ `AGame`, base commune) | `init`, `update(Event)`, `getPixelMap`, `getScore`, `isGameOver`, `reset`, `getResolution` | `extern "C" std::unique_ptr<IGames> createGame()` |
| `IGraphics` | `createWindow`, `destroyWindow`, `draw(pixels)`, `drawText`, `display`, `getEvent` | `extern "C" std::unique_ptr<IGraphics> createGraphic()` |

`Event` (`src/core/Event.hpp`) porte les entrées communes (`UP`, `DOWN`, `LEFT`, `RIGHT`, `START`, `ACTION1`,
`NEXT_GRAPHIC`, `NEXT_GAME`, `RESTART`, `MENU`, `EXIT`…) et la structure `Pixel` : `x`, `y`, `symbol`, `color`.
Chaque affichage traduit ses touches en `Event` et dessine les `Pixel` à sa façon (rectangles ou caractères).

## `DLLoader<T>` (`src/lib/dynamic_loading/`)

Modèle générique autour de `dlopen` / `dlsym` / `dlclose` : `loadLibrary(path)`, `getInstance()` (appelle
`createGame` ou `createGraphic`), `unloadLibrary()`. Les erreurs de chargement lèvent une exception.

## Ajouter un module

Voir `doc/Arcade guide.pdf` dans le dépôt source : hériter de `IGraphics` ou `AGame`, exporter la fonction de
création, ajouter le fichier au Makefile du dossier, recompiler ; le `.so` apparaît dans `lib/` et dans le menu.
