# About this branch

`target-dir-size` is Cargo at `ae8f06eb4` (the Cargo shipped in `nightly-2026-11-25`)
plus pull requests related to the Rust project goal
[Target directory size reduction](https://github.com/rust-lang/goals/issues/830).

## Build it as a nightly Cargo

```sh
CFG_RELEASE_CHANNEL=nightly cargo build --release --locked
```

`CFG_RELEASE_CHANNEL` is read at compile time (`src/version.rs`). Without it, a locally
built Cargo reports no release channel, so `features::is_nightly_toolchain` is false and
the nightly-only defaults are switched off, as on a stable Cargo.

Measured on a ~120-crate workspace (dev profile, Linux x86_64), before
rust-lang/cargo#17533 was applied: a Cargo built without the variable produced a target
directory 12.8% larger than the nightly one (510 MB instead of 452 MB), because every
`.rlib` then embeds a second copy of the crate metadata. With #17533 on this branch,
`embed-metadata=no` no longer depends on the channel; the other nightly-only defaults
still do.

`cargo --version` does not print the channel, so it cannot be used to check the build.

## What is on top of the base

| PR | What it does |
|---|---|
| rust-lang/cargo#17796, #17797, #17798 | Follow-ups to the `line-tables-only` dev profile (merged upstream after the base) |
| rust-lang/cargo#17533 | `embed-metadata=no` by default, via the stable `-C` flag |
| rust-lang/cargo#17715 | Stop uplifting rlibs |
| rust-lang/cargo#17524 | `cargo clean gc --max-target-age` (`-Zgc`) |
| rust-lang/cargo#17512 | Shared object storage across workspaces (`-Zshared-object-storage`) |

Local changes: #17512 adapted to the `home_path` field name of this base, and the
`cargo clean gc` options of #17524 and #17512 kept side by side where they conflicted.

Status: builds; the Cargo test suite has not been run on this branch.
