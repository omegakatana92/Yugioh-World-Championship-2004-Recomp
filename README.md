# Yu-Gi-Oh! World Championship Tournament 2004 — Recomp

A native recompilation project for **Yu-Gi-Oh! - World Championship Tournament 2004** on the Game Boy Advance, built with [GBARecomp](https://github.com/mstan/gbarecomp).

## ROM Required

This project does not include the original game ROM or copyrighted assets.

To play, you must provide your own legally obtained ROM copy of *Yu-Gi-Oh! - World Championship Tournament 2004*.

The original ROM must not be uploaded to this repository or included with releases.

## Features

* Game Boy Advance static recompilation
* Native PC executable
* Keyboard controls
* Fullscreen support (Alt + Enter)
* Save state support
* Modern windowed presentation

## Controls

| Keyboard Key    | Game Boy Advance Button / Function |
| --------------- | ---------------------------------- |
| **Arrow Keys**  | D-Pad                              |
| **X**           | A Button                           |
| **Z**           | B Button                           |
| **C**           | L Button                           |
| **V**           | R Button                           |
| **Enter**       | Start                              |
| **Right Shift** | Select                             |
| **Alt + Enter** | Fullscreen Toggle                  |
| **Shift + P**   | Pause / Resume                     |
| **Tab**         | Turbo / Fast-Forward               |

## Building From Source

### Requirements

* Git
* CMake
* A supported C/C++ compiler (MSVC, Clang, or MinGW)
* Ninja (recommended) or Visual Studio
* Your own legally obtained ROM

### Clone the Repository

```bash
git clone https://github.com/YOUR-USERNAME/YGO-Recomp.git
cd YGO-Recomp
```

### Configure the Build

```bash
cmake -B build -G Ninja ..
```

### Build

```bash
cmake --build build --config Release
```

The resulting executable will be placed in the project's build output directory.

### Run

Place your ROM file in the same directory as the executable or pass it as an argument:

```bash
.\build\Release\YGORecomp.exe "C:\path\to\Yu-Gi-Oh! - World Championship Tournament 2004 (USA) (En,Ja,Fr,De,Es,It).gba"
```

Or launch without arguments to be prompted for the ROM file.

## Project Structure

This project follows the [GBARecomp](https://github.com/mstan/gbarecomp) structure:

| Path | Purpose |
|------|---------|
| `generated/` | Static recompiled C++ shards + dispatch table |
| `framework/` | Game-specific runtime headers |
| `main.cpp` | Entry point, environment defaults, launcher options |
| `game.toml` | Cartridge config (ROM path, save type, entry point) |
| `CMakeLists.txt` | Build configuration |

## Disclaimer

This project is a work in progress and is not affiliated with or endorsed by Konami or any other rights holder.

The original game ROM is not distributed with this project. Users must provide their own legally obtained copy.

## License

See the [LICENSE](LICENSE) file for details.
