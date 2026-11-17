# omarchy-audacity

Audacity, working properly on [Omarchy](https://omarchy.org).

Hyprland draws no title bars, so Audacity's window had nothing to grab: you
couldn't drag it to another monitor or into a tile next to other windows. This
build adds what Chromium and GNOME apps have through their header bars:

- **Drag empty toolbar space** (anywhere on the toolbar area that isn't a
  button, slider or field) **to move the window.** Hyprland takes over the
  drag, so it snaps into tiles and moves across monitors like any other window.
  This needs Audacity to run as a native Wayland app, which the launcher does.
- **Drag the empty end of the menu bar** the same way. Menu titles still open
  their menus.
- Toolbars are still rearranged by their dotted grabbers, as before.

It starts about **10× faster, with no splash screen**:

| | Stock 3.7.9 | omarchy-audacity |
| --- | --- | --- |
| Launch to main window | 9.5 s, behind an "Audacity is starting up..." splash | 0.9 s, straight to the window |

Measured on Omarchy with five sound cards (two NVIDIA HDMI, three USB) under
PipeWire. At every start, stock Audacity opens each of the 40-odd input
devices PortAudio lists and reads its mixer through PortMixer, thousands of
ALSA control reads, all on the main thread. That is the lag behind the splash,
and long enough for Hyprland to offer to terminate the window. This build
lists the devices without opening them. Those per-device "sources" only matter
for old cards with a capture selector, and the input level control still reads
the mixer of the device you record from.

It also leaves out everything that talks to the internet or interrupts you:

- No update checks, crash reports, error reporting (Sentry), audio.com upload
  or sign-in, MuseHub, or `audacity://` link handling. They are not compiled
  in, and Audacity makes no network connections. The only socket is one on
  127.0.0.1 that lets opening a file go to the window that's already running.
- No What's New dialog or splash screen at startup.

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

Or get it from the [Neko Legends Control Center](https://github.com/neko-legends/neko-legends-control-center),
which downloads `omarchy-audacity-linux-x86_64.zip` from the latest
[release](https://github.com/neko-legends/omarchy-audacity/releases) and runs
`omarchy-audacity-portable` from wherever it unpacks it. It uses the same system
libraries as Arch's `audacity` package. Pushing a `v*` tag builds the package on
Arch and attaches that zip (`.github/workflows/release.yml`).

It installs to `/opt/omarchy-audacity` beside the stock `audacity` package, so
you can keep both or remove the stock one. Both read the same settings
(`~/.config/audacity`).

## How it works

| File | What it does |
| --- | --- |
| `PKGBUILD` | Builds Audacity from its release tarball with Arch's options, networking and What's New off, into `/opt/omarchy-audacity` |
| `0001-move-the-window-by-empty-toolbar-and-menu-bar-space.patch` | A press on empty toolbar space or the menu bar's empty end calls `gtk_window_begin_move_drag`, which hands the drag to Hyprland |
| `0002-start-quickly-without-a-splash-or-probing-every-audio-device.patch` | Skips opening every input device and its mixer at startup, and the splash screen |
| `omarchy-audacity` | Launcher: runs Audacity as a native Wayland app (`GDK_BACKEND=wayland`). Arch's own launcher forces XWayland, where Hyprland ignores the move request and dragging does nothing |
| `omarchy-audacity.desktop` | The app launcher entry |

To move to a newer Audacity, change `pkgver` and the tarball checksum in
`PKGBUILD`, and refresh the patch if it no longer applies.

## License

GPL-3.0-or-later, like Audacity. See `LICENSE`.
