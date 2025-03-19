# Build fish-shell on MSYS2

The latest nightly Rust supports `x86_64-pc-cygwin` target. Make sure you are using the latest version.

This repo contains some patches, but we won't need them in the future.

Tracking: [fish-shell/fish-shell#11238](https://github.com/fish-shell/fish-shell/pull/11238)

## 1. Set linker
Add following lines to `~/.cargo/config.toml`:
```toml
[target.x86_64-pc-cygwin]
linker = "x86_64-pc-msys-gcc.exe"
```

## 2. Patch lib
Add stub lib to MSYS2:
```bash
$ echo "INPUT(libmsys-2.0.a)" > /usr/lib/libcygwin.a
```

## 3. Package
Clone this repo and `cd` into it.
```bash
$ export PATH="$PATH:<path-for-cargo>"
$ makepkg -si
```

## 4. Enjoy!
```
~> status buildinfo
Build system: CMake
Version: 4.0.1-Berrysoft-1
Target: x86_64-pc-cygwin
Host: x86_64-pc-windows-msvc
Profile: release
Features: gettext
```
