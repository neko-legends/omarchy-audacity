Omarchy Audacity: Audacity 3.7.9 for Omarchy. Drag empty toolbar space to move
the window, starts in under a second, no splash, no network features.

Run ./omarchy-audacity-portable, or open it from the Neko Legends Control
Center. It uses the system libraries Arch's audacity package uses; if it won't
start, install them once with:

    sudo pacman -S --needed audacity

(or just its dependencies). To put it in the Omarchy app launcher as a package
instead, clone https://github.com/neko-legends/omarchy-audacity and run
bin/install.
