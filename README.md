# Electron Media Patch

Electron patches for HEVC, AC3 and E-AC3 codec support.

> [!NOTE]
> Work in progress

## Versions

- [x] Electron [v36.2.1](https://releases.electronjs.org/release/compare/v36.2.0/v36.2.1)
  - [x] Tested on macOS
  - [x] Tested on Windows
  - [ ] Tested on Linux

## Guide

Source: [Electron Build Instructions](https://www.electronjs.org/docs/latest/development/build-instructions-gn)

Install [depot_tools](https://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools_tutorial.html#_setting_up)

Also, on Windows, you'll need to set the environment variable `DEPOT_TOOLS_WIN_TOOLCHAIN=0`. 
To do so, open `Control Panel → System and Security → System → Advanced system settings` and add a system variable `DEPOT_TOOLS_WIN_TOOLCHAIN` with value `0`.
This tells depot_tools to use your locally installed version of Visual Studio (by default, depot_tools will try to download a Google-internal version that only Googlers have access to).

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

📝 gclient works by checking a file called DEPS inside the src/electron folder for dependencies (like Chromium or Node.js). Running `gclient sync -f` ensures that all dependencies required to build Electron match that file.

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

move `media_hevc_ac3_chromium.patch` into `src/`
move `media_hevc_ac3_electron.patch` into `src/electron/`
move `media_hevc_ac3_ffmpeg.patch` into `src/third_party/ffmpeg/`

```bash
cd src
git apply media_hevc_ac3_chromium.patch
```

```bash
cd src/electron
git apply media_hevc_ac3_electron.patch
```

```bash
cd src/third_party/ffmpeg
git apply media_hevc_ac3_ffmpeg.patch
```

Download [this file](https://github.com/StaZhu/enable-chromium-hevc-hardware-decoding/blob/main/add-hevc-ffmpeg-decoder-parser.js)
and place it in the `src/third_party/ffmpeg` directory.

Then, run the following command to apply the patch:
```bash
node ./add-hevc-ffmpeg-decoder-parser.js
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

Build Electron

For the release configuration (This will take a while):

```
ninja -C out/Release electron
```

Packaging

On linux, first strip the debugging and symbol information:

```bash
electron/script/strip-binaries.py -d out/Release
```

To package the electron build as a distributable zip file:

```bash
ninja -C out/Release electron:electron_dist_zip
```


## Acknowledgements

- [enable-chromium-hevc-hardware-decoding](https://github.com/StaZhu/enable-chromium-hevc-hardware-decoding) - HEVC patches
