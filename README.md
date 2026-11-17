# omarchy-audacity

Audacity, working properly on [Omarchy](https://omarchy.org).

Hyprland draws no title bars, so Audacity's window had nothing to grab: you
couldn't drag it to another monitor or into a tile next to other windows. This
build adds what Chromium and GNOME apps have through their header bars:

- **Drag empty toolbar space** (anywhere on the toolbar area that isn't a
  button, slider or field) **to move the window.** Hyprland takes over the
  drag, so it snaps into tiles and moves across monitors like any other window.
- **Drag the empty end of the menu bar** the same way. Menu titles still open
  their menus.
- Toolbars are still rearranged by their dotted grabbers, as before.

It also leaves out everything that talks to the internet or interrupts you:

- No update checks, crash reports, error reporting (Sentry), audio.com upload
  or sign-in, MuseHub, or `audacity://` link handling. They are not compiled
  in, and Audacity makes no network connections. The only socket is one on
  127.0.0.1 that lets opening a file go to the window that's already running.
- No What's New dialog at startup.

Help links still open in your browser when you click them.

Everything else is stock Audacity 3.7.9, built the way Arch builds it, so
projects (`.aup3`), effects and plugins work as usual.

## Install

```sh
bin/install
```

This runs `makepkg -fsi`: it downloads the Audacity 3.7.9 release source, applies
the patch, builds it (a few minutes on a fast machine; the build tree is large,
so set `BUILDDIR` to put it on a roomy disk), and installs the
`omarchy-audacity` package. **Omarchy Audacity** is then in the app launcher
(Super+Space), and `omarchy-audacity` starts it from a terminal.

It installs to `/opt/omarchy-audacity` beside the stock `audacity` package, so
you can keep both or remove the stock one. Both read the same settings
(`~/.config/audacity`).

## How it works

| File | What it does |
| --- | --- |
| `PKGBUILD` | Builds Audacity from its release tarball with Arch's options, networking and What's New off, into `/opt/omarchy-audacity` |
| `0001-move-the-window-by-empty-toolbar-and-menu-bar-space.patch` | A press on empty toolbar space or the menu bar's empty end calls `gtk_window_begin_move_drag`, which hands the drag to Hyprland |
| `omarchy-audacity` | Launcher: runs Audacity through XWayland (`GDK_BACKEND=x11`), as Arch's own launcher does |
| `omarchy-audacity.desktop` | The app launcher entry |

To move to a newer Audacity, change `pkgver` and the tarball checksum in
`PKGBUILD`, and refresh the patch if it no longer applies.

## License

GPL-3.0-or-later, like Audacity. See `LICENSE`.
