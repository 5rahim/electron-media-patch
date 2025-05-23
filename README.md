# Electron Media Patch

Electron patches for HEVC, AC3 and E-AC3 codec support.

> [!NOTE]
> Work in progress

## Versions

- [x] Electron [v36.2.1](https://releases.electronjs.org/release/compare/v36.2.0/v36.2.1)
  - [x] Tested on macOS
  - [ ] Tested on Windows
  - [ ] Tested on Linux

## Guide

Source: [Electron Build Instructions](https://www.electronjs.org/docs/latest/development/build-instructions-gn)

Getting the code

```bash
mkdir electron && cd electron
gclient config --name "src/electron" --unmanaged https://github.com/electron/electron
gclient sync --with_branch_heads --with_tags
# This will take a while
```

Update the respective folder's origin URLs for `git pull`

```bash
cd src/electron
git remote remove origin
git remote add origin https://github.com/electron/electron
git checkout main
git branch --set-upstream-to=origin/main
cd -
```

📝 gclient works by checking a file called DEPS inside the src/electron folder for dependencies (like Chromium or Node.js). Running gclient sync -f ensures that all dependencies required to build Electron match that file.

So, in order to pull, you'd run the following commands:

```bash
cd src/electron
git pull
# Checkout the tagged version
git checkout v36.2.1
# Sync the dependencies
gclient sync -f
```

Apply the patches

```
TBA
```

Set the environment variable for chromium build tools

On Linux & MacOS:

```bash
cd src
export CHROMIUM_BUILDTOOLS_PATH=`pwd`/buildtools
```

On Windows:

```bash
# cmd
cd src
set CHROMIUM_BUILDTOOLS_PATH=%cd%\buildtools


# PowerShell
cd src
$env:CHROMIUM_BUILDTOOLS_PATH = "$(Get-Location)\buildtools"
```

Generate Release build config of Electron, this will take a while

On Linux & MacOS

```bash
gn gen out/Release --args="import(\"//electron/build/args/release.gn\")"
```

On Windows:

```bash
# cmd
gn gen out/Release --args="import(\"//electron/build/args/release.gn\")"

# PowerShell
gn gen out/Release --args="import(\`"//electron/build/args/release.gn\`")"
```

## Acknowledgements

- [enable-chromium-hevc-hardware-decoding](https://github.com/StaZhu/enable-chromium-hevc-hardware-decoding) - HEVC patches
