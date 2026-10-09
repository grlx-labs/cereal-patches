# cereal-patches

Shared Meson wrap overlay for [cereal](https://github.com/USCiLab/cereal).

This repository is intended to be included as a git submodule at
`subprojects/packagefiles/cereal` in grlx-labs libraries that provide a
`cereal.wrap` (currently `grlx` and `entt_ext`).

## Contents

```
cereal/
├── meson.build                     # Meson dependency definition
└── include/cereal/types/memory.hpp # Patched to avoid std::aligned_storage
```

## Why this exists

cereal v1.3.2 and the pinned development commit `22a1b369` use
`std::aligned_storage`, which is deprecated in newer GCC and Clang and will be
removed in a future C++ standard. The overlay replaces the deprecated usage
with `alignas(T) std::byte data[sizeof(T)]`, equivalent to cereal PR #830.

## How to use

In the consuming library's `subprojects/cereal.wrap`:

```ini
[wrap-git]
directory = cereal
url = https://github.com/USCiLab/cereal.git
revision = 22a1b369f39be918ca79206a83c4facd759f9105
patch_directory = cereal

[provide]
cereal = cereal_dep
```

Add this repo as a submodule:

```bash
git submodule add https://github.com/grlx-labs/cereal-patches.git subprojects/packagefiles/cereal
```

## Updating the overlay

1. Check out the new cereal revision in a temporary clone.
2. Apply the alignas replacement to `include/cereal/types/memory.hpp`.
3. Copy the patched header and `meson.build` into this repo.
4. Tag the result (e.g. `v1.3.2-grlx2`) so the bump bot can move pins.
