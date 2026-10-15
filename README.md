<h1 align="center">xrtui</h1>

<p align="center">
  <b>A keyboard-driven terminal UI for managing your displays on X11.</b><br>
  Toggle monitors, switch resolutions and refresh rates, and position screens, all without leaving the terminal.
</p>

<p align="center">
  <img alt="language: C11" src="https://img.shields.io/badge/language-C11-blue">
  <img alt="platform: X11" src="https://img.shields.io/badge/platform-X11-orange">
  <img alt="license: GPL-2.0" src="https://img.shields.io/badge/license-GPL--2.0-green">
</p>

---

## Overview

`xrtui` shows every connected monitor in a single table and lets you edit it with vim-style keys. Changes are queued as you make them and only applied when you press `s`, so you can line everything up first and apply it in one go.

It talks to the X server directly through **Xlib** and the **XRandR extension**. It does not parse `xrandr` output, and it has no runtime dependencies beyond the libraries below.

## Features

- **Live overview** of every connected output: name, active state, primary flag, resolution, refresh rate, and X/Y position
- **Enable or disable** a monitor
- **Pick a resolution** from the modes the monitor actually supports
- **Pick a refresh rate** for the selected resolution
- **Set exact X/Y position** to arrange screens side by side or stacked
- **Queued edits**: changed rows are marked with `*` until you apply them
- **Vim-style navigation** (`h` `j` `k` `l`), with arrow keys and Enter also supported
- **Reload** from the X server at any time to discard pending changes

## Preview

![XRTUIPRV](xrtui_)


## Requirements

- A running **X11** session (`xrtui` connects through `XOpenDisplay`, so it needs an X server)
- A C compiler (`gcc`) and `make`
- Development headers for **ncurses**, **libX11**, and **libXrandr**

Install the dependencies:

| Distro | Command |
| --- | --- |
| Debian / Ubuntu | `sudo apt install build-essential libncurses-dev libx11-dev libxrandr-dev` |
| Arch | `sudo pacman -S base-devel ncurses libx11 libxrandr` |
| Fedora | `sudo dnf install gcc make ncurses-devel libX11-devel libXrandr-devel` |

## Build

```sh
git clone https://github.com/pablodeepblue/xrtui.git
cd xrtui
make
```

This produces an `xrtui` binary in the project root.

| Target | What it does |
| --- | --- |
| `make` | Optimized build (`-O2`) |
| `make debug` | Clean rebuild with debug symbols and no optimization (`-g -O0`) |
| `make clean` | Remove the `build/` directory and the binary |

To install it system-wide:

```sh
sudo install -m 755 xrtui /usr/local/bin/xrtui
```

## Usage

Run it from a terminal inside your X session:

```sh
./xrtui
```

### Keybindings

**Anywhere**

| Key | Action |
| --- | --- |
| `s` | Apply all pending changes to the displays ("sync monitors") |
| `r` | Reload the current state from the X server, discarding pending edits |
| `q` | Quit |

**In the table**

| Key | Action |
| --- | --- |
| `h` / `←` | Move one column left |
| `l` / `→` | Move one column right |
| `k` / `↑` | Move one row up |
| `j` / `↓` | Move one row down |
| `Enter` / `i` | Edit the selected cell (Active, Resolution, Refresh, X and Y) |
| `Esc` | Quit |

**In a pick list** (Active, Resolution, Refresh)

| Key | Action |
| --- | --- |
| `k` / `↑`, `j` / `↓` | Move the highlight |
| `Enter` / `l` | Choose the highlighted option |
| `h` / `←` / `Esc` | Cancel and go back |

**In number entry** (X Position, Y Position)

| Key | Action |
| --- | --- |
| `0`-`9` | Type a value |
| `Backspace` / `x` | Delete the last digit |
| `Enter` / `l` | Confirm |
| `h` / `Esc` | Cancel and go back |

### Typical workflow

1. Use `j`/`k` and `h`/`l` to move to the cell you want to change.
2. Press `Enter` to open its pick list or number field, then choose a value.
3. Repeat for any other monitors. Edited outputs are marked with `*`.
4. Press `s` to apply everything at once.
5. Press `r` to reload and confirm the new state, or `q` to quit.

### Arranging monitors

Positions are absolute pixel coordinates. To put a 1920-pixel-wide monitor on the left and a second monitor to its right, leave the first at `0, 0` and set the second monitor's X Position to `1920`.

## Current limitations

- **X11 only.** There is no native Wayland support.
- The **Output** and **Primary** columns are display-only for now; the primary output cannot yet be changed from the UI.
- Rotation is not supported, and applied outputs are always set to normal orientation.
- X and Y positions accept non-negative whole numbers only, so negative offsets can't be entered.
- Choosing a new resolution selects the first refresh rate listed for it; change the refresh rate afterwards if you want a different one.
- Up to 16 outputs, with up to 64 resolutions per output, are tracked.

## Roadmap

- [ ] Change the primary monitor
- [ ] Rotation
- [ ] Helpers to place a screen directly to the left of another

## Contributing

Issues and pull requests are welcome. The whole program lives in [`src/xrtui.c`](src/xrtui.c), and `make debug` gives you a debug build to work with.

## License

Released under the [GNU General Public License v2.0](LICENSE).
