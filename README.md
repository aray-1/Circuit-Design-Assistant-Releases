# Circuit Design Assistant

Design SystemVerilog by drawing it: a block-diagram editor that generates RTL.

## Install (Windows)

1. Download the latest **`CircuitDesignAssistant-Setup-vX.Y.Z.exe`** from the
   [Releases](../../releases) page.
2. Run it. It installs for your user only — **no administrator rights needed**.
3. Choose whether to add a desktop shortcut, then finish.

The installer registers `.cida` (projects) and `.cidalib` (libraries) with their
own icons; double-clicking a `.cida` opens it. Example projects are placed in
**Documents\Circuit Design Assistant Examples**.

To uninstall: *Settings → Apps*, or the "Uninstall" entry in the Start menu.

## Requirements

- Windows 10 or 11, 64-bit.
- No separate Python install is needed — the application is self-contained.
- The Claude terminal inside the app uses your own Claude Code login, if you have one.

## Releases

Each release on the [Releases](../../releases) page lists what changed and carries the
installer. To check a download, compare its SHA-256 (PowerShell:
`Get-FileHash CircuitDesignAssistant-Setup-vX.Y.Z.exe`) with `releases/SHA256SUMS.txt`.

## Reporting issues

Found a bug or have an idea? In the app, use **Help > Report an Issue** (or the toolbar
button): it opens a pre-filled form here with the version you use. You can also open
an [issue](../../issues/new/choose) directly; please state the version
(Help > Report an Issue shows it).
