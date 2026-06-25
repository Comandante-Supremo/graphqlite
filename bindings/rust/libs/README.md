# Vendored graphqlite SQLite extension binaries

This directory holds pre-built graphqlite SQLite extension binaries per
target triple. They are consumed by `bindings/rust/src/platform.rs` via
`include_bytes!()` when the crate's `bundled-extension` feature is on
(default).

## Why this directory exists on this branch

Upstream's `bindings/rust/Cargo.toml` declares
`include = ["src/**/*", "libs/**/*", "Cargo.toml", "README.md", "LICENSE"]`
so a crates.io publish ships the binaries to consumers. Until upstream
publishes, git-dep consumers hit
`couldn't read .../libs/graphqlite-<platform>.<ext>: No such file or
directory` at compile time.

This branch (`bonseye-vendored-libs`) carries pre-built binaries
vendored for the BonsEye project (https://git.dominancelogistics.com/AustrianPainter/BonsEye)
so that BonsEye's `crates/spike/Cargo.toml` can declare
`graphqlite = { git = "https://github.com/Comandante-Supremo/graphqlite", branch = "bonseye-vendored-libs" }`
and have `cargo build` resolve the `include_bytes!()` invocation.

## Provenance

Each binary is the exact byte-for-byte file from the corresponding
upstream Homebrew formula install. Build process: upstream's
`make extension RELEASE=1` (same as Homebrew's bottle pipeline).

| File | Source machine | Source path | Engine version |
|---|---|---|---|
| `graphqlite-macos-aarch64.dylib` | Dominances-Mac-mini (Apple Silicon) | `/opt/homebrew/opt/graphqlite/lib/sqlite/graphqlite.dylib` | 0.6.0 |

Other target triples (linux-x86_64, linux-aarch64, macos-x86_64,
windows-x86_64) are not yet vendored on this branch; add as needed when
BonsEye targets the platform.

## Sync from upstream

```bash
git fetch upstream main
git rebase upstream/main
git push --force-with-lease
```

The vendored binaries are NOT in upstream's git tree — rebasing keeps
them as our own commits on top of upstream's history. When upstream
publishes 0.6.0+ to crates.io, drop this branch + retarget the BonsEye
Cargo.toml at the crates.io version.
