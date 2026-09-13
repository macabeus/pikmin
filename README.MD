## About this fork (`asmlift-benchmark` branch)

This branch exists to make the [asmlift](https://github.com/macabeus/asmlift) decompiler
benchmark reproducible. It is the upstream
[projectPiki/pikmin](https://github.com/projectPiki/pikmin) tree at commit
[`35e28e7c`](https://github.com/projectPiki/pikmin/commit/35e28e7c6f1f0788a6644b4b1f1fc18da6e5a7ba)
("Update README.MD", 2026-09-07), the exact commit the benchmark's functions are vendored
from, plus one integration commit touching exactly two files:

- `decomp.yaml`: new. It adds a `tools.asmlift` block pointing asmlift at the project's symbol
  source (`tools.asmlift.elf`): `build/GPIE01_01/main.elf`, the ELF the ordinary matching
  build already links. No derived ELF and no extra build target is needed. That ELF carries a
  sized `.symtab` for every function and object and no DWARF, and asmlift reads it as is.
- `README.MD`: this section.
- Nothing else differs from upstream: no `src/` byte, no rename, no build fix.

The build needs exactly what the upstream [Dependencies](#dependencies) section lists
(Python, ninja, and on macOS wine-crossover), plus a `GPIE01_01` (USA Rev 1) disc image in
`orig/GPIE01_01/`. The project's pinned tools download into `build/` on first run.

To reproduce the benchmark rows: build the project (the DOL must match), then follow the
per-function scripts published in the benchmark report. The build writes the symbol source
itself; no further command derives it:

    python3 configure.py
    ninja            # ends with `build/GPIE01_01/main.dol: OK`
    ls build/GPIE01_01/main.elf

---

Pikmin  
[![Build Status]][actions] [![Code Progress]][progress] [![Data Progress]][progress] [![Linked Progress]][progress] [![Discord Badge]][discord]
=============

[Build Status]: https://github.com/projectPiki/pikmin/actions/workflows/build.yml/badge.svg
[actions]: https://github.com/projectPiki/pikmin/actions/workflows/build.yml
[Code Progress]: https://decomp.dev/projectPiki/pikmin.svg?mode=shield&measure=code&label=Code&category=all
[Data Progress]: https://decomp.dev/projectPiki/pikmin.svg?mode=shield&measure=data&label=Data&category=all
[Linked Progress]: https://decomp.dev/projectPiki/pikmin.svg?mode=shield&measure=complete_code&label=Linked%20Code&category=all
[Discord Badge]: https://img.shields.io/discord/933849697485983765?color=%237289DA&logo=discord&logoColor=%23FFFFFF
[discord]: https://discord.gg/CWKqYMePX8
[progress]: https://decomp.dev/projectPiki/pikmin

A complete matching decompilation of Pikmin (GameCube, 2001).

Supported versions (100% matching):
- `GPIE01_00`: USA (Rev 0)
- `GPIE01_01`: USA (Rev 1) - Default Version
- `GPIJ01_01`: Japan (Rev 1)
- `GPIJ01_02`: Japan (Rev 2)
- `GPIP01_00`: Europe (Rev 0)

Versions to be worked on (not yet 100%):
- `G98E01_PIKIDEMO`: USA Interactive Multi-Game Demo Disc - January 2002 "zz_PikminDemo-1210.tgc"
- `DPIJ01_PIKIDEMO`: 店頭ゲームデモディスク Oct'2001 (Pikmin Jitsuen-you Sample Disc) "zz_pikmin_game/PikiDemo.tgc"
- `G98P01_PIKIDEMO`: Europe Interactive Multi-Game Demo Disc - May 2002 "zz_PikiDemo.tgc"

Note: this repository contains **no game assets** - an existing copy of the game is required.

Index
-----

- [Dependencies](#dependencies)
  - [Windows](#windows)
  - [macOS](#macos)
  - [Linux](#linux)
- [Building](#building)
- [Diffing](#diffing)

Dependencies
------------

### Windows

On Windows, it's **highly recommended** to use native tooling. WSL or msys2 are **not** required.  
When running under WSL, [objdiff](#diffing) is unable to get filesystem notifications for automatic rebuilds.

- Install [Python](https://www.python.org/downloads/) and add it to `%PATH%`.
  - Also available from the [Windows Store](https://apps.microsoft.com/store/detail/python-311/9NRWMJP3717K).
- Download [ninja](https://github.com/ninja-build/ninja/releases) and add it to `%PATH%`.
  - Quick install via pip: `pip install ninja`

### macOS

- Install [ninja](https://github.com/ninja-build/ninja/wiki/Pre-built-Ninja-packages):

  ```sh
  brew install ninja
  ```

- Install [wine-crossover](https://github.com/Gcenx/homebrew-wine):

  ```sh
  brew install --cask --no-quarantine gcenx/wine/wine-crossover
  ```

After OS upgrades, if macOS complains about `Wine Crossover.app` being unverified, you can unquarantine it using:

```sh
sudo xattr -rd com.apple.quarantine '/Applications/Wine Crossover.app'
```

### Linux

- Install [ninja](https://github.com/ninja-build/ninja/wiki/Pre-built-Ninja-packages).
- For non-x86(_64) platforms: Install wine from your package manager.
  - For x86(_64), [wibo](https://github.com/decompals/wibo), a minimal 32-bit Windows binary wrapper, will be automatically downloaded and used.

Building
--------

- Clone the repository:

  ```sh
  git clone https://github.com/projectPiki/pikmin.git
  ```

- Copy your game's disc image to `orig/GPIE01_01`. (Or the appropriate version folder.)
  - Supported formats: ISO (GCM), RVZ, WIA, WBFS, CISO, NFS, GCZ, TGC
  - After the initial build, the disc image can be deleted to save space.

- Configure:

  ```sh
  python configure.py
  ```

  To use version other than `GPIE01_01`, add `--version`. Add `--help` to see all available options.
- Build:

  ```sh
  ninja
  ```

Diffing
-------

Once the initial build succeeds, an `objdiff.json` should exist in the project root.

Download the latest release from [encounter/objdiff](https://github.com/encounter/objdiff). Under project settings, set `Project directory`. The configuration should be loaded automatically.

Select an object from the left sidebar to begin diffing. Changes to the project will rebuild automatically: changes to source files, headers, `configure.py`, `splits.txt` or `symbols.txt`.

