<div>

[**简体中文**](README_zh_CN.md)

</div>

## FlClash

[![Downloads](https://img.shields.io/github/downloads/chen08209/FlClash/total?style=flat-square&logo=github)](https://github.com/chen08209/FlClash/releases/)[![Last Version](https://img.shields.io/github/release/chen08209/FlClash/all.svg?style=flat-square)](https://github.com/chen08209/FlClash/releases/)[![License](https://img.shields.io/github/license/chen08209/FlClash?style=flat-square)](LICENSE)

[![Channel](https://img.shields.io/badge/Telegram-Channel-blue?style=flat-square&logo=telegram)](https://t.me/FlClash)

A multi-platform proxy client based on ClashMeta, simple and easy to use, open-source and ad-free.

on Desktop:
<p style="text-align: center;">
    <img alt="desktop" src="snapshots/desktop.gif">
</p>

on Mobile:
<p style="text-align: center;">
    <img alt="mobile" src="snapshots/mobile.gif">
</p>

## Features

✈️ Multi-platform: Android, Windows, macOS and Linux

💻 Adaptive multiple screen sizes, Multiple color themes available

💡 Based on Material You Design, [Surfboard](https://github.com/getsurfboard/surfboard)-like UI

☁️ Supports data sync via WebDAV

✨ Support subscription link, Dark mode

## Use

### Linux

⚠️ Make sure to install the following dependencies before using them

   ```bash
    sudo apt-get install libayatana-appindicator3-dev
    sudo apt-get install libkeybinder-3.0-dev
   ```

### Android

Support the following actions

   ```bash
    com.follow.clash.action.START
    
    com.follow.clash.action.STOP
    
    com.follow.clash.action.TOGGLE
   ```

## Download

<a href="https://chen08209.github.io/FlClash-fdroid-repo/repo?fingerprint=789D6D32668712EF7672F9E58DEEB15FBD6DCEEC5AE7A4371EA72F2AAE8A12FD"><img alt="Get it on F-Droid" src="snapshots/get-it-on-fdroid.svg" width="200px"/></a> <a href="https://github.com/chen08209/FlClash/releases"><img alt="Get it on GitHub" src="snapshots/get-it-on-github.svg" width="200px"/></a>

## Build

1. Update submodules
   ```bash
   git submodule update --init --recursive
   ```

2. Install `Flutter` and `Golang` environment

3. Build Application

    - android

        1. Install  `Android SDK` ,  `Android NDK`

        2. Set `ANDROID_NDK` environment variables

        3. Run Build script

           ```bash
           dart .\setup.dart android
           ```

    - windows

        1. You need a windows client

        2. Install  `Gcc`，`Inno Setup`

        3. Run build script

           ```bash
           dart .\setup.dart windows --arch <arm64 | amd64>
           ```

    - linux

        1. You need a linux client

        2. Run build script

           ```bash
           dart .\setup.dart linux --arch <arm64 | amd64>
           ```

### Building a `.deb` on Ubuntu 22.04 (amd64) — detailed steps

The official CI builds run on Ubuntu 22.04. If you build on a newer system the resulting binary may require a newer GLIBC than Ubuntu 22 provides, causing the app to crash on launch.

#### 1. System dependencies

```bash
sudo apt update
sudo apt install -y curl git clang cmake ninja-build pkg-config \
    libgtk-3-dev liblzma-dev libstdc++-12-dev \
    rpm patchelf \
    libayatana-appindicator3-dev libkeybinder-3.0-dev
```

#### 2. Install Flutter via git (not snap)

The snap version of Flutter bundles an older libc that is incompatible with the system GCC 11 and will fail during compilation. Use the git clone instead:

```bash
git clone https://github.com/flutter/flutter.git -b stable ~/flutter
echo 'export PATH="$PATH:$HOME/flutter/bin"' >> ~/.zshrc   # or ~/.bashrc
source ~/.zshrc
```

Pin to the exact version this project targets:

```bash
cd ~/flutter && git checkout 3.35.7
```

Verify:

```bash
flutter --version
dart --version
```

#### 3. Install Go

```bash
# Download Go 1.24.0 (matches CI)
wget https://go.dev/dl/go1.24.0.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.24.0.linux-amd64.tar.gz
echo 'export PATH="$PATH:/usr/local/go/bin"' >> ~/.zshrc
source ~/.zshrc
go version
```

#### 4. Clone and initialize submodules

```bash
git clone https://github.com/chen08209/FlClash.git
cd FlClash
git submodule update --init --recursive
```

#### 5. Install flutter_distributor

```bash
dart pub global activate flutter_distributor
echo 'export PATH="$PATH:$HOME/.pub-cache/bin"' >> ~/.zshrc
source ~/.zshrc
```

#### 6. Fix CMake install prefix (one-time)

Flutter's Linux CMake build uses `ninja install` to stage the binary before packaging. The install target writes to `/usr/local/FlClash` by default if a stale CMake cache exists. Clearing the cache avoids the need for sudo:

```bash
rm -rf build/linux/x64/release/
```

If you ever see `Permission denied` copying to `/usr/local/FlClash`, just delete the build directory and retry — do **not** create that directory manually or it will break the RPATH patching step.

#### 7. Build the `.deb`

```bash
dart setup.dart linux --arch amd64 --targets deb
```

The output `.deb` will be in `dist/`.

#### 8. Install

```bash
sudo dpkg -i dist/FlClash-*-linux-amd64.deb
```

Verify the installed package includes all native plugin libraries:

```bash
ls /usr/share/FlClash/lib/
# Should include libsqlite3_flutter_libs_plugin.so and libflutter_js_plugin.so
```

#### Troubleshooting

| Error | Fix |
|---|---|
| `dart: command not found` | Add `~/flutter/bin` to PATH and re-source shell |
| `flutter_distributor: not found` | Add `~/.pub-cache/bin` to PATH and re-source shell |
| `GLIBC_2.33 not found` | You are using snap Flutter — remove it and use the git clone method above |
| Stale `/snap/flutter/.../ninja` path in CMake | Delete `build/linux/x64/release/` and retry |
| `clang++: not found` | `sudo apt install -y clang` |
| `cannot find -lstdc++` | `sudo apt install -y libstdc++-12-dev` |
| Dart compile errors in `super_sliver_list` | Flutter version too new — run `cd ~/flutter && git checkout 3.35.7` |
| `material_color_utilities` version conflict | Flutter 3.35.7 requires `^0.11.1` in `pubspec.yaml` — revert if bumped |
| `Permission denied` copying to `/usr/local/FlClash` | Delete `build/linux/x64/release/` — stale CMake cache has wrong install prefix |
| App crashes after install (missing `.so` files) | You may have installed an official release built on Ubuntu 24 — install your locally built `.deb` from `dist/` instead |

    - macOS

        1. You need a macOS client

        2. Run build script

           ```bash
           dart .\setup.dart macos --arch <arm64 | amd64>
           ```

## Star

The easiest way to support developers is to click on the star (⭐) at the top of the page.

<p style="text-align: center;">
    <a href="https://api.star-history.com/svg?repos=chen08209/FlClash&Date">
        <img alt="start" width=50% src="https://api.star-history.com/svg?repos=chen08209/FlClash&Date"/>
    </a>
</p>