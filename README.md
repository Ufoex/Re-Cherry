<img width="1287" height="832" alt="Re_cherry_logo" src="https://github.com/user-attachments/assets/ec7c9bdc-1438-44d9-9760-bfe054b9971d" />

This fork is dedicated to getting Re-Cherry building and running on **Linux**.
The original project (by MaxDeadBear) targets Windows via Visual Studio; this
fork tracks the Linux side of the build instead.

STATUS
----------------------------------------------
- `src/hooks.cpp` and `src/costume_switcher.cpp` use only portable STL
  (`chrono`, `filesystem`, `format`, `charconv`) — no WinAPI calls, no changes
  needed for Linux.
- `src/main.cpp` just registers the app via a RexGlue macro — nothing
  OS-specific.
- `CMakeLists.txt` already branches correctly on `WIN32`:
  ```cmake
  if(WIN32)
      add_executable(re_cherry WIN32 ${RECHERRY_SOURCES})
  else()
      add_executable(re_cherry ${RECHERRY_SOURCES})
  endif()
  ```
- `CMakePresets.json` already ships Linux configure/build presets
  (`linux-base`, Ninja generator, clang-20/clang++-20, Debug/Release/RelWithDebInfo).
- The remaining dependency is the **RexGlue SDK** itself. `generated/rexglue.cmake`,
  `generated/re_cherry_config.h` and `generated/re_cherry_init.h` are gitignored
  and produced by `rexglue codegen re_cherry_config.toml` — they don't exist in
  this repo until the SDK is installed and codegen is run, so full Linux support
  ultimately depends on the SDK, not on this repo's own sources.

HOW TO BUILD (Linux)
----------------------------------------------
1. Install RexGlue SDK following the Linux instructions on the
   [wiki](https://github.com/rexglue/rexglue-sdk/wiki).
2. Install `clang-20`, `clang++-20`, `cmake` (>= 3.25) and `ninja`.
3. Clone this repo.
4. Dump your copy of Lollipop: Chainsaw (Xbox 360) and use a tool like
   ISO-Extract to dump the contents of the ISO (INCLUDING the `default.xex` file).
5. Place the dumped contents in the `assets` folder (INCLUDING `default.xex`).
6. Put `rexglue` in your `PATH` and run `rexglue codegen re_cherry_config.toml`
   from the repo root to generate the files under `generated/`.
7. Configure and build with the Linux preset:
   ```sh
   cmake --preset linux-amd64-relwithdebinfo
   cmake --build --preset linux-amd64-relwithdebinfo
   ```
   (run `cmake --list-presets` to confirm the exact preset name for your setup)
8. Copy the `assets` folder with the dumped ISO contents into the build output
   directory (`out/build/<preset-name>`).

HOW TO USE
----------------------------------
Until a launcher is completed, go into the build output directory
(`out/build/<preset-name>`) and either place the `assets` folder with the
dumped `default.xex` there, or make a new folder elsewhere with just the
`ashgame` folder and `default.xex` alongside the built binary.

CURRENT ISSUES
--------------------------------------
- Screen-tearing.
- Linux build is untested end-to-end pending RexGlue SDK setup on Linux —
  see STATUS above for what's confirmed vs. still open.
- If you find crashes, please open an issue explaining where it happened and
  what you were doing.

CREDITS
----------------------------------------------
- MaxDeadBear — original Re-Cherry project (Windows).
- MadLadMikael — for helping setup and teaching how to use RexGlue SDK and GitHub.

DISCLAIMER: Please support the official re-release. While it may be
controversial and this may be the alternative you prefer, they worked hard to
bring new content there as well.

RE-CHERRY AND ITS DEVELOPERS DO NOT CONDONE PIRACY.
