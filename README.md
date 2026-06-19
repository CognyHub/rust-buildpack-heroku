# rust-buildpack

Heroku classic buildpack that builds Rust applications with [rustup][] and [cargo][], with caching between deploys.

It is a trimmed, vendored copy of [`emk/heroku-buildpack-rust`][upstream] (pinned at upstream commit [`cfa0f06`][pin]), kept under CognyHub so our Heroku apps depend on a repo we control instead of a third party.

[rustup]: https://rustup.rs/
[cargo]: https://crates.io/
[upstream]: https://github.com/emk/heroku-buildpack-rust
[pin]: https://github.com/emk/heroku-buildpack-rust/commit/cfa0f0621411f0dbb46df9d4740e119b7a459ccc

## Usage

Point an app's buildpack chain at this repo:

```bash
heroku buildpacks:set https://github.com/CognyHub/rust-buildpack --app <app>
```

To combine with another runtime buildpack, add it first in the chain:

```bash
heroku buildpacks:add --index 1 https://github.com/CognyHub/rust-buildpack --app <app>
```

The buildpack activates when the app has a `Cargo.toml`, a `rust-toolchain`, or a `RustConfig` file.

## Specifying the Rust version

By default the latest stable Rust is used. Pin a version with a [`rust-toolchain`][toolchain] file (rustup format). A `VERSION` set in `RustConfig` still works and overrides `rust-toolchain`.

[toolchain]: https://rust-lang.github.io/rustup/overrides.html#the-toolchain-file

## RustConfig options

Optional `RustConfig` file in the app root:

- `RUST_SKIP_BUILD=1` — install the toolchain but skip `cargo build` (for apps that only need Rust to build a dependency).
- `RUST_INSTALL_DIESEL=1` — install the diesel CLI at build time.
- `RUST_CARGO_BUILD_FLAGS` — flags passed to `cargo build` (default `--release`).
- `BUILD_PATH` — subdirectory to build in.

## What we changed from upstream

Trimmed to what we run on Heroku: the CI/test scaffolding (`.travis.yml`, `docker-compose-test.yml`, `test_buildpack`) was dropped. `bin/compile` is vendored verbatim. `bin/detect` is verbatim except for the framework label it prints (`cogny-rust` instead of `Rust`), which is what Heroku shows under the app's Frameworks list.

## Re-syncing from upstream

Diff our `bin/` against the upstream pin above, pull desired changes, and update the pinned commit in this README.
