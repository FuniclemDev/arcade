# Installer et lancer Arcade sur Fedora

Testé sur Fedora 39.

```sh
sudo dnf install gcc-c++ make SFML-devel SDL2-devel SDL2_ttf-devel ncurses-devel libcaca-devel
git clone git@github.com:FuniclemDev/arcade-source.git
cd arcade-source
make            # cœur (./arcade), jeux et affichages (lib/*.so)
./arcade lib/arcade_sfml.so
```

- Lancer depuis la racine du dépôt : les `.so` sont cherchés dans `lib/` et la police dans `assets/`.
- Bibliothèque de départ au choix : `arcade_sfml.so`, `arcade_sdl2.so`, `arcade_ncurses.so`, `arcade_libcaca.so`.
- **Lancer depuis un terminal** : ncurses dessine dans le terminal qui a lancé le programme (sans terminal, il échoue).
- Menu : `↑` `↓` choisissent le jeu, `←` `→` l'affichage, `Entrée` lance.
- En jeu : flèches, `Espace` tirer, `F1` jeu suivant, `F2` affichage suivant, `R` recommencer, `Échap` menu, `Retour arrière` quitter.
- `make games`, `make graphicals`, `make core` compilent une seule partie ; `make fclean` nettoie.
