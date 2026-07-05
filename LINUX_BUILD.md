# Linux build notes (WIP)

Investigation into compiling Re-Cherry on Linux, done on the `linux-support` branch.

## Findings

- `src/hooks.cpp` and `src/costume_switcher.cpp` use only portable STL (`chrono`,
  `filesystem`, `format`, `charconv`) — no WinAPI calls. No changes needed here.
- `src/main.cpp` just registers the app via a RexGlue macro — nothing OS-specific.
- `CMakeLists.txt` already branches on `WIN32` correctly:
  ```cmake
  if(WIN32)
      add_executable(re_cherry WIN32 ${RECHERRY_SOURCES})
  else()
      add_executable(re_cherry ${RECHERRY_SOURCES})
  endif()
  ```
- `CMakePresets.json` already ships Linux configure/build presets (`linux-base`,
  Ninja generator, clang-20/clang++-20, Debug/Release/RelWithDebInfo).
- The real dependency is the **RexGlue SDK**. `generated/rexglue.cmake`,
  `generated/re_cherry_config.h` and `generated/re_cherry_init.h` are gitignored
  and produced by `rexglue codegen re_cherry_config.toml` — they don't exist in
  this repo until the SDK is installed and codegen is run, so Linux support
  ultimately depends on the SDK, not on this repo's own sources.
- README explicitly defers Linux/Mac setup to the RexGlue SDK wiki
  (https://github.com/rexglue/rexglue-sdk/wiki) instead of the Visual Studio steps.

## Remaining steps (need a Linux machine or WSL)

1. Install RexGlue SDK on Linux per the SDK wiki.
2. Put `rexglue` in `PATH`, run `rexglue codegen re_cherry_config.toml` to
   generate the `generated/` files.
3. `cmake --preset linux-amd64-relwithdebinfo` (adjust preset name to whatever
   `cmake --list-presets` reports) then build with `ninja`.
4. Confirm no Linux-specific issues surface in the generated RexGlue runtime
   headers (`rex/...`) — unverified since they don't exist without the SDK.
5. Copy dumped game assets (`assets/`, including `default.xex`) next to the
   built binary, same as the Windows instructions.
