# GBARecomp output

This folder contains C++ source generated from `Yu-Gi-Oh! - World Championship Tournament 2004 (USA) (En,Ja,Fr,De,Es,It).gba`.

## Build the generated source

Windows PowerShell:

```powershell
.\build.ps1
```

macOS or Linux:

```sh
./build.sh
```

The build creates the `gbarecomp_game` static library. This confirms that the
generated source compiles; it is not a complete playable port by itself.

To make a playable port, add game-specific configuration and integrate this
library with the GBARecomp runtime. Existing target repositories are useful
starting points: https://github.com/mstan/gbarecomp
