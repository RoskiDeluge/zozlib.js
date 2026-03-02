# Pinekin3 C -> WASM -> JS Host Summary

## What We Clarified

- `zozlib.js` uses a lightweight host model: C is compiled to wasm and browser-side JavaScript (`raylib.js`) provides imported functions expected by the wasm module.
- The browser is the runtime environment; `raylib.js` is the host/glue layer implementing `env.*` imports.
- Missing host functions surface at runtime when gameplay reaches code paths that require those imports.

## Why This Approach Exists

- Keep builds simple (`clang --target=wasm32`) without full Emscripten runtime.
- Implement only needed API surface incrementally.
- Reuse one host shim across multiple demos/games.
- Tradeoff: less completeness out of the box, but more control and faster iteration.

## How We Reached A Working Web Build For `pinekin3`

- Ported game logic from Ada to C (`pinekin3/eepers.c`) and integrated into root `nob.c` build flow.
- Added compiler selection logic for wasm-capable clang in `nob.c`.
- Added `pinekin3` to `index.html` example selector.
- Implemented/extended host imports in `raylib.js` as runtime errors appeared (camera mode, vector math, timing, memory helpers, etc.).
- Refactored web path to avoid runtime image loading by embedding map data (`pinekin3/web_map.h`).
- Fixed web frame loop model to use `raylib_js_set_entry(...)` instead of a blocking `while` loop.
- Fixed path-pointer bug in entity spawn routines that caused wasm memory faults.
- Stabilized web storage by using static buffers for map/path/checkpoint data in web builds.

## Result

- `pinekin3` now runs in browser via the repository's wasm host pattern.
- This validated tsoding's intended extension workflow: when wasm demands a function, add/adjust the host implementation as needed.

## Nested Repo / Pointer Notes

- `pinekin3` and `raylib` are tracked as gitlinks/submodules in the top-level repo.
- Added `.gitmodules` and updated pointers so GitHub shows pointer-style entries.

