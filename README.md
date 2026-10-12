# BmStudio 0.21

An editor for the cooked packages of **Batman: Arkham Asylum (PC, UE3 v576 / licensee 21)**.
It reads and writes `.u` / `.upk` / `.umap` files without re-cooking. Saving without changes produces a byte-identical file.
It also opens the **2007 beta (Xbox 360, `.xxx`)** packages and converts their content to the PC game.

Main features:
- object tree, property editor, hex view, integrity check, copying objects between packages
- mesh, texture and animation viewer; export and import (FBX, PSA, DDS)
- level viewport, asset library, lights and emitters, collision (invisible walls), cutting holes in walls
- room import from another map (or from the DLC) into a map
- UnrealScript (decompile and compile functions)
- 2007 beta → game:
  - converts meshes, materials, textures, characters and animations
  - rebuilds whole rooms with BSP collision, mesh collision shapes and lights (automatic lights when the beta dump has no lights for the room)
  - generates beta enemies for BmModLoader

No game data is included. Everything is read from the user's own installation.

## Layout

| Folder | Contents |
|---|---|
| `bmstudio/` | the application (Win32 + Direct3D 9 + Dear ImGui). `app.cpp` includes the `.hpp` files of each screen |
| `bmstudio/lang.hpp` | UI strings (English). `lang_ptbr.hpp` is the optional Brazilian Portuguese translation |
| `bmcpp/` | the header-only library: package format (`bmpkg`, `bmasset`), levels and BSP (`bmlevel`, `bmbspedit`), room import (`bmroomimp`), beta (`bmbeta*`), UnrealScript (`ucc_*`), etc. |
| `bmcpp/mz/` | miniz (zlib), used for compressed packages |
| `bmcpp/mspack/` | the libmspack LZX decompressor, used for Xbox 360 packages |
| `tools/` | `betaroomfix` (fixes installed beta rooms; `--folder CookedPC` scans everything) and `betatexfix` (texture settings of the beta Batman) |
| `third_party/imgui/` | Dear ImGui 1.93 WIP with the Win32 and DX9 backends |

## Building

The result is a static 32-bit `.exe` (no extra DLLs needed).

**Linux (cross-compiling):**
```
sudo apt install g++-mingw-w64-i686 mingw-w64-tools
./build.sh
```

**Windows (MSYS2):** open the **MINGW32** terminal, install the compiler and run the script with an empty prefix:
```
pacman -S mingw-w64-i686-gcc
CROSS= ./build.sh
```

The output goes to `build/`: `BmStudio.exe`, `betaroomfix.exe` and `betatexfix.exe`.
The first build takes about 2 minutes, almost all of it in `app.cpp`.

Use the **gnu++17** standard. The executable is linked with `--large-address-aware`, because large maps need more than 2 GB of memory.

## Languages

English is built in. If `bmstudio/lang_ptbr.hpp` is present, the Language menu also offers Brazilian Portuguese (picked automatically on a Portuguese Windows).
Deleting that file removes the translation; everything falls back to English.
A new language is a new `lang_xx.hpp` with the same string order as `enum L` in `lang.hpp`.

## Running

- On first use BmStudio asks for the game's `BmGame\CookedPC` folder, to find the script packages `Core.u`, `Engine.u` and `BmGame.u`.
- Beta features need the `CookedXenon` folder of the 2007 beta (with `Textures.tfc` / `CharTextures.tfc`).
- Several `*_Lights.xxx` files in the known beta dumps are zeroed. The conversion log says "damaged file in your beta dump (all zeros)" and automatic lights are used.

## Test hooks

The program has hooks for automated tests: `BMS_AUTOBETA`, `BMS_AUTOCONV`, `BMS_AUTORIMP`, `BMS_AUTOPREVALL`, `BMS_AUTOENEMY`, etc. They only run when the environment variable is set, so normal use is not affected. They allow testing the features without the UI (for example under Wine).

## Third-party licenses

- **Dear ImGui:** MIT (`third_party/imgui/LICENSE.txt`).
- **miniz:** MIT (header of `bmcpp/mz/miniz.h`).
- **libmspack (`lzxd.c` and headers):** LGPL 2.1. Its source is included, as the license requires.
- **stb_image / stb_dxt:** public domain or MIT.

Batman: Arkham Asylum, Unreal Engine and the package formats belong to their respective owners. This is a non-commercial fan project.
