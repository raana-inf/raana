# raana

Rust and C LKM build orchestration for Android GKI kernels.

raana generates module projects, installs KMSDK, and builds or downloads the
matching `rust_support` runtime for GKI 5.10, 5.15, 6.1, 6.6, and 6.12.

## Requirements

- Rust 1.82 or newer
- Docker for container builds, or a host DDK at `/opt/ddk`
- `git` and `tar`

## Install

```bash
cargo install raana
```

## Quick start

```bash
raana new mymod
cd mymod
raana fetch
raana build
```

`raana new --c mymod` creates a C module project.

## Targets

| target | kernel |
|---|---|
| `android12-5.10` | 5.10 |
| `android13-5.10` | 5.10 |
| `android13-5.15` | 5.15 |
| `android14-5.15` | 5.15 |
| `android14-6.1` | 6.1 |
| `android15-6.6` | 6.6 |
| `android16-6.12` | 6.12 |

## Commands

| command | description |
|---|---|
| `raana new <name>` | create a Rust LKM project |
| `raana new --c <name>` | create a C LKM project |
| `raana fetch` | install KMSDK and fetch dependencies |
| `raana build` | build the module |
| `raana targets` | list supported targets |
| `raana doctor` | check the current project |
| `raana clean` | remove old runtime cache |
| `raana sdk <args>` | call the KMSDK sdk script |

## Manifest

```toml
[package]
name = "mymod"
rust = "src/lib.rs"
wrapper = "src/wrapper.c"
kunit = false

[sdk]
kmsdk = "<kmsdk-rev>"
rust-support = "<rust-support-rev>"
prebuilt = true
artifact-repo = "raana-labs/raana"
artifact-match = "rev"

[build]
targets = ["android16-6.12"]
cache = ".cache"
```

`artifact-match` controls how the prebuilt runtime is selected:

- `version` finds a release tag such as `rust-support-v0.1.0-<rev>`
- `rev` finds a release tag containing the configured rust_support rev
- omitted: exact `rust-support-v<raana-version>-<rust-support-rev>`

`artifact-tag` overrides the search with one exact tag.

## Runtime

raana first tries the prebuilt runtime from GitHub Releases. If prebuilt is not
available it builds `deps/rust_support` with DDK inside the container.

## License

GPL-2.0-only
