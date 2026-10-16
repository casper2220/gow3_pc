# God of War III Remastered for PC

**English** | [Português (Brasil)](README.pt-BR.md)

> **This is a fork of [FelipePrado0/gow3_pc](https://github.com/FelipePrado0/gow3_pc).**
> All the credit for the port goes to Felipe Prado and everyone listed in [Credits](#credits).
> This fork adds the stuff below on top of it. For the official version, releases and support,
> head to the original repo.

## What this fork adds

### 🐧 Native Linux
No Wine, no Proton. Tested on CachyOS (Arch) with GCC 16 and an NVIDIA RTX 5070 laptop.
- Build fixes for newer compilers and Vulkan headers (xbyak include path, VMA header fallback, a
  `static_assert` that only worked in C, missing `-lm`).
- Pipeline binary cache turned off on Linux, because NVIDIA's driver doesn't support
  `VK_KHR_pipeline_binary` yet and the game crashed.
- `run.sh` uses the same defaults as `run.py`: the memory/VBlank values the patches need, linear
  readback, and so on.
- The window renders at real pixel size with desktop scaling. Before, at 150% scaling on KDE, the
  game rendered at a smaller size and got stretched.
- `GOW3_GPU_TYPE=integrated|discrete` picks the GPU. Handy on laptops.
- A Linux launcher, plus `install-desktop.sh` to add it to your app menu.
- [LINUX.md](LINUX.md) has setup steps for Arch, Fedora, Ubuntu and Steam Deck.

### 🇪🇺 European version (CUSA01715)
- Its own patch file: a fix for the black screen on the intro (v01.00), plus the texture fix and
  resolution patches for v01.02. They're ported from the US ones, with every address checked.
- Patches can have one profile per game version, and the launcher remembers your picks for each.
- Fixed a bug where the US patch file got applied to the EU game.
- A separate update folder (`CUSAxxxxx-UPDATE`, like shadPS4 does) is layered on top of the game at
  start. Neither folder gets modified.

### ✨ FSR 3.1 and DLSS that actually work
The temporal upscaler was still using hooks made for Bloodborne, so it never really ran in God of
War III. The menu said "FSR 3.1", but 0 frames were being upscaled.
- It's hooked where God of War III keeps its camera, depth and final composite, and runs right
  before the HUD, so the HUD stays sharp.
- Jitter is set up properly. Shadows, full-screen passes and the HUD are left alone.
- Upscaling from a lower render resolution works too, so you get more FPS.
- DLSS on Linux. It loads NVIDIA's own library from the driver, so nothing from NVIDIA is in this
  repo. You can pick the DLSS model (the default is DLSS 4.5), and the overlay shows which version
  is running.
- The log prints how many frames got upscaled every 10 seconds.
- [docs/upscaler.md](docs/upscaler.md) explains how God of War III's frame is put together.

### 🎛️ Launcher
- An update folder picker that finds `<game>-UPDATE` on its own.
- A GPU list from Vulkan, showing which one is dedicated and which is integrated.
- Upscaler options: FSR 3.1 / DLSS, the DLSS model, an upscaler mode that works like in normal PC
  games, and the output resolution.
- Options that don't apply to your build are greyed out, with a note explaining why.
- The Linux launcher saves `last_run.log`, and the log page has a filter.

### Heads-up
- Windows isn't tested with these changes.
- Still missing: reactive/transparency masks for FSR, FSR 4, and a proper check of motion on
  characters.
- The text below is the original project's README.

---

A PC port of **God of War III Remastered** (PlayStation 4) for Windows 10 and 11.

The CPU side is native: the game's original executable runs directly on the PC's processor (the
PS4 uses the same x86-64 architecture), and a runtime written for this game replaces the PS4
system libraries, so there is no emulator. The graphics are translated: they run on the
[shadPS4](https://github.com/shadps4-emu/shadPS4) video core, which converts the PS4 GPU commands
and shaders to Vulkan, with the God of War III rendering fixes from
[cuesta4's `eltutz` branch](https://github.com/cuesta4/shadPS4/tree/eltutz). Running the game code
directly on the CPU is also how shadPS4 works; what this project changes is the system layer and
the game-specific work (patches, background shader cache, launcher and in-game menu).
It is a fork of [Supermedo's Windows port](https://github.com/Supermedo/bloodborne_pc) of
[deadinside28's bloodborne_pc](https://github.com/deadinside28/bloodborne_pc) (see
[Credits](#credits)).

> **No game files are included.** You need a dump from your own PS4 (CUSA01623, version 01.02).
> This project is not affiliated with Sony Interactive Entertainment or Santa Monica Studio.
> God of War is a trademark of Sony Interactive Entertainment.

On the test machine (AMD Radeon RX 6700 XT, Ryzen 5 5600X, 32 GB) the game runs at 144 FPS on
average (55 to 240) at 1080p with the 240 FPS engine frame rate, and at 108 FPS on average at 4K
with FSR 1.

**Tested and playable from start to finish** at native 1080p with the 240 FPS engine frame
rate. At other render resolutions some textures look broken; a fix is in progress.

Progress is tracked (in Portuguese) in [docs/gow3/ROADMAP.md](docs/gow3/ROADMAP.md).

## What works

- Opening, menus, video cutscenes (H.264) and gameplay.
- Audio: music, voices and effects (ATRAC9 and MP3).
- Controller (DualSense, Xbox and others, through SDL3) and keyboard at the same time, both
  remappable.
- Saving and loading: the save list is shown on screen, as on the PS4. Autosave works too. The
  launcher backs the saves up before every start.
- Patches: render resolution of 480p, 720p, 1440p, 1800p or 4K, corrupted texture fix and skip
  videos with the X button (community); engine frame rate of 120 FPS (kvicken) or 240 FPS
  (adapted by Felipe Prado).
- **Instant start:** the saved shader cache loads in the background while you play, and new
  shaders compile in the background too, so the game does not stop to build them.
- **Launcher** in the game's colors, in English with partial translations: quick settings on the home page, display,
  performance, game folder and save backups, patches, mods (order, presets and conflicts),
  controls with PlayStation or Xbox button icons, updates and diagnostics.
- **In-game menu** (Insert on the keyboard, R3+L2 on the controller), with four tabs:
  - **Game:** cheats (infinite health, magic, item meter, Rage of Sparta, max red orbs);
    multipliers from 0.1x to 100x for red, green (health), blue (magic) and gold (Rage of
    Sparta) orbs and for damage dealt and taken; an enemy health bar.
  - **Image:** display mode (windowed, borderless, fullscreen), VSync, FSR 1 upscaling and
    RCAS sharpening.
  - **Performance:** frame rate limit (30 to unlimited), frames queued, background shader
    compiling; render resolution, engine frame rate and GPU readback options apply with one
    "Apply and restart" button.
  - **Overlay:** a performance overlay with FPS, frametime, upscaler, GPU (name and use), CPU,
    RAM and VRAM, each optional, with font size, corner, background opacity and layout. The
    numbers are the game's own use, not the whole system's.

## Download and play

**You need**
- Windows 10 (1903 or newer) or Windows 11, 64-bit.
- A graphics card with Vulkan 1.3 and an up-to-date driver.
- Your own copy of God of War III Remastered: **CUSA01623 with the 01.02 update**, as a folder
  that contains `eboot.bin`, `sce_module` and `sce_sys`. No game files are included in this
  download.

**Steps**
1. Download `gow3-windows.zip` from the
   [Releases page](https://github.com/FelipePrado0/gow3_pc/releases/latest).
2. Extract it to a folder of your choice, for example `C:\Games\gow3-windows`. Do not run it from
   inside the zip.
3. Open **`God of War III.exe`**. If Windows shows "Windows protected your PC", click
   **More info**, then **Run anyway**: the program is not digitally signed.
4. Click **Choose the game folder**. It takes you to the **Game** page. Next to **Game folder**,
   click **Browse…** and select the folder with `eboot.bin`.
5. Back on **Home**, the cover art and "CUSA01623 · version 01.02" appear. Adjust **Quick
   settings** if you like: display mode, frame rate limit, render resolution and engine frame
   rate.
6. Click **PLAY**. The first start takes a little longer while the game image is prepared. The
   next starts are quick.

Patches and fixes are included and applied automatically: texture fix, skip videos with X and
120 FPS are on by default. Change the resolution and frame rate on **Home** or **Performance**.

**While playing**
- **Insert** on the keyboard, or **R3 + L2** on the controller, opens the port's menu: cheats,
  multipliers, image, performance and the performance overlay.
- Saved shaders load in the background. A small "Loading shader cache" note in the corner shows
  the progress.

**Next time**
- **`Play God of War III.exe`** starts the game straight away with your saved settings. You can
  make a desktop shortcut to it, or add it to Steam with **Add a Non-Steam Game**.
- Your saves, shader cache and log are in the `user` folder next to `God of War III.exe`. Keep
  that folder when you update.
- **Advanced → Check for updates** downloads and installs a new version. Your saves and settings
  are kept.

**If something goes wrong**
- Black screen at start: **Advanced → Clear shader cache**, then start again.
- The game does not open: check the **Log** page, or send `user\last_run.log` with your report.

## Requirements

- Windows 10 (1903 or newer) or Windows 11, 64-bit.
- A graphics card with Vulkan 1.3 and a current driver.
- Your copy of the game: CUSA01623 with the 01.02 update applied (the folder with `eboot.bin`,
  `sce_module` and `sce_sys`).
- The patches are applied only to the `eboot.bin` they were checked against byte by byte
  (sha256 `d85c8135d330c3b601bf5dc3f1dd86bd6119fb8a9fce372bed2c9785f9a79299`), and the in-game
  hooks (cheats, multipliers, health bar) only where the code they change has the expected
  bytes. Otherwise the game starts without them, and the log and the menu say why.
- To build from source: [MSYS2](https://www.msys2.org) and [Python 3](https://www.python.org) with Pillow
  (`pip install pillow`, used for the game's icon and cover art).

## Building from source

Development happens on the `gow3` branch; `main` receives tested merges.

1. Install MSYS2 (`winget install MSYS2.MSYS2`) and, in the **MSYS2 CLANG64** shell, the
   packages listed in [packaging/windows/README.md](packaging/windows/README.md).
2. Clone the repository with its submodules and build:

   ```bash
   git clone --recursive https://github.com/FelipePrado0/gow3_pc
   cd gow3_pc
   git checkout gow3   # the newest work; skip it to build main
   bash build.sh
   ```

3. Start the launcher (in PowerShell, in the project folder):

   ```powershell
   python launcher/gow3_launcher_win.py
   ```

4. On **Game**, choose the game folder. Adjust **Display**, **Performance** and **Patches** if
   you like, then press **PLAY**.

To play with the saved settings without opening the launcher's window (for a shortcut or Steam):

```powershell
python launcher/gow3_launcher_win.py --play
```

Or without the launcher at all:

```powershell
$env:GOW3_GAME_DIR = 'D:\path\CUSA01623'
python run.py
```

Settings are kept in `gow3.ini` (the game and the in-game menu) and in
`%APPDATA%\gow3-launcher\settings.json` (the launcher). Saves, the shader cache and
`last_run.log` go to the project's `user` folder.

### Keyboard controls

Defaults; change them on the launcher's **Controls** page.

| Key | Button |
|---|---|
| WASD | left stick |
| Arrows | right stick (camera) |
| Space | Cross |
| Left Shift | Circle |
| E | Square |
| Q | Triangle |
| 1 and 3 | L1 and R1 |
| R and F | L2 and R2 |
| Z and C | L3 and R3 |
| I, K, J, L | d-pad |
| Enter | Options |
| Tab | touchpad |
| Insert | the port's menu (R3+L2 on the controller) |

In the menu: L1/R1 or Q/E switch tabs. In the save list: arrows or the d-pad choose, Enter or
Cross confirm, Esc or Circle cancel.

## Tests

```bash
python -m unittest discover -s tests
bash build.sh --test
ninja -C out/gpu stat-multipliers-test perf-overlay-test warmup-inbox-test graphics-settings-test
out/gpu/stat-multipliers-test.exe out/eboot.elf
out/gpu/perf-overlay-test.exe
out/gpu/warmup-inbox-test.exe
out/gpu/graphics-settings-test.exe
```

The C++ tests are targets in [gpu/CMakeLists.txt](gpu/CMakeLists.txt); the ones that check the
game's code take the prepared executable (`out/eboot.elf`) as their argument.

To check that every system function the game uses is implemented, without starting the game:

```bash
out/gow3-probe.exe out/boot-linked.bin --app0 <game folder> --check-imports
```

## How it works

| Part | Where | What it does |
|---|---|---|
| Preparation | `scripts/` | Reads `eboot.bin`, applies its relocations and links the game's own `libc` and `libSceFios2` into one image |
| Loader | `src/probe.c` | Loads the image, installs the validated hooks (cheats, multipliers, health bar) and starts the game's original code |
| Runtime | `src/runtime_*.c` | Reimplements the PS4 system functions: memory, threads, files, audio, video, controller, saves and services |
| GPU | `gpu/shadps4/` | The shadPS4 video core, translating the PS4 GPU commands and shaders to Vulkan, with cuesta4's God of War III fixes (`eltutz` branch) and this port's speed work (background shader cache, deferred readbacks) |
| In-game menu | `gpu/shim/gow3_overlay.cpp` | The Dear ImGui menu and performance overlay; settings in `gpu/shim/gow3_settings.cpp` |
| Patches | `patches/God_of_War_III_Remastered.xml`, `scripts/patches.py` | Applied in memory at start, without changing the game files |
| Launcher | `launcher/gow3_launcher_win.py` | Settings, patches, mods, controls and the play button |

More documentation: [mods](docs/MODS.md), [frame map and upscaler](docs/upscaler.md),
[parallel GPU work](docs/parallel_gpu.md) and the [roadmap](docs/gow3/ROADMAP.md).

## Credits

This project is a fork of Supermedo's Windows port, which builds on deadinside28's original:

- **deadinside28**: [bloodborne_pc](https://github.com/deadinside28/bloodborne_pc), the original
  native port.
- **Supermedo** (Mohammed Albarghouthi): [bloodborne_pc for Windows](https://github.com/Supermedo/bloodborne_pc),
  the fork this project comes from.
- **cuesta4**: [shadPS4 fork focused on God of War III Remastered](https://github.com/cuesta4/shadPS4/tree/eltutz)
  (branch `eltutz`). The game's rendering in this port depends on its fixes: the GNM blend
  rewrite, depth/stencil state and image aliasing. cuesta4 is also a co-author of the
  resolution, texture fix, 120 FPS and skip videos patches (below). If you play on shadPS4,
  this is the build to use for God of War III.
- **shadPS4 team**: the [shadPS4](https://github.com/shadps4-emu/shadPS4) video core, which does
  all the GPU and shader translation to Vulkan in this port.

Patches: kvicken and cuesta4 (resolution, texture fix, 120 FPS and skip videos, building on the
PS3 patches by illusion0001), published for shadPS4 in
[shadps4-emu/ps4_cheats#145](https://github.com/shadps4-emu/ps4_cheats/pull/145) and adapted to
this executable. The 240 FPS patch was adapted by Felipe Prado from the 120 FPS patch.
Cheats: adapted from Celogamez's [GoldHEN cheats](https://github.com/GoldHEN/GoldHEN_Cheat_Repository).

Also used: [LibAtrac9](https://github.com/Thealexbarney/LibAtrac9), [FFmpeg](https://ffmpeg.org),
[SDL3](https://github.com/libsdl-org/SDL), [Dear ImGui](https://github.com/ocornut/imgui),
[sirit](https://github.com/shadps4-emu/sirit), [magic_enum](https://github.com/Neargye/magic_enum),
[miniz](https://github.com/richgel999/miniz), [xbyak](https://github.com/herumi/xbyak),
[Vulkan Memory Allocator](https://github.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator),
[FSR-Vulkan](https://github.com/FireBurn/FSR-Vulkan) and the AMD FidelityFX SDK,
[MSYS2](https://www.msys2.org), [LLVM](https://llvm.org) and [Pillow](https://python-pillow.org).

## License

GNU GPL v2 or later ([LICENSE](LICENSE)). Third-party components keep their own licenses.
