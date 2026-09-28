# build-toolchain

Native C/C++/Rust compilation toolchain for OpenCharly builder images.

The `build-toolchain` candy installs the generic native-compilation stack — the
GNU C/C++ compiler, `make`, CMake, the Rust/cargo toolchain, the nasm assembler,
ccache, gdb, git, and a wide set of `-devel`/`-dev` headers — so multi-stage
builder images can compile native software from source. Every consuming builder
stage (`fedora-builder`, `arch-builder`) depends on these binaries being on
`PATH`. The layer is package-only: no service, no daemon.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `build-toolchain` |
| Packages | `gcc`, `make`, `cmake`, `cargo`, `nasm`, `ccache`, `gdb`, `git`, `autoconf`, `automake`, `libtool`, `binutils`, plus per-distro `-devel`/`-dev` headers |
| Environment | `CCACHE_DISABLE=1` |
| Service / port | none |

The per-distro package sections cover Fedora (`rpm:`), Arch (`pac:`), Debian and
Ubuntu (`deb:`) — full parity. The Debian/Ubuntu sections map Fedora's `-devel`
packages to their `-dev` equivalents and drop the RPM-only tooling
(`redhat-rpm-config`, `rpm-build`, `rpm-sign`, `pkgconf-m4`).

## How to use it

Compose the layer by pinning this repo in a builder box's `candy:` list:

```yaml
my-builder:
  candy:
    base: fedora-builder
    candy:
      - '@github.com/opencharly/layer-build-toolchain:v2026.239.1636'
```

Then, inside the built image:

```bash
gcc --version
cmake --version
cargo --version
nasm --version
```

## Layout

- `charly.yml` — the candy manifest: the package list with per-distro sections,
  the `CCACHE_DISABLE` environment, an ordered `plan:` of build-time `check:`
  steps, and the embedded `skill:` entity.
- `.github/workflows/deploy.yml` — builds the pinned charly and runs
  `charly box validate` on the manifest (the merge gate).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-coder:build-toolchain`
- Consumed by: `/charly-distros:fedora-builder`, `/charly-distros:arch-builder`
- Companion builder layers: `/charly-languages:pixi`, `/charly-coder:nodejs`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
