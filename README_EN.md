# PixelOS 14 - Xiaomi Redmi Note 11 (spes)

> This ROM was built with the assistance of AI (Qoder) ✨

---

## ROM Info
| Item | Details |
|------|---------|
| ROM | PixelOS (AOSP 14) |
| Device | Xiaomi Redmi Note 11 (spes) |
| Android | 14 (AP2A) |
| Kernel | Linux 4.19 (sm6225) |
| Branch | `fourteen` |

---

## Available Manifests

| Manifest | Description |
|----------|-------------|
| `default.xml` | Stock ROM, no root |
| `ksu.xml` | ROM + KernelSU Next (root ready) |

---

## Setup & Build Instructions

### 1. Install Dependencies
```bash
sudo apt-get install -y bc bison build-essential ccache curl flex g++-multilib gcc-multilib git git-lfs gnupg gperf imagemagick lib32ncurses-dev lib32readline-dev lib32z1-dev liblz4-tool libncurses-dev libsdl1.2-dev libssl-dev libwxgtk3.2-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev
```

### 2. Install repo tool
```bash
mkdir -p ~/bin && curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo && chmod a+x ~/bin/repo
```

### 3. Repo Init

**Stock ROM (no root):**
```bash
repo init -u https://github.com/cyabell/pixelos_spes -b fourteen -m default.xml --git-lfs
```

**ROM + KernelSU Next:**
```bash
repo init -u https://github.com/cyabell/pixelos_spes -b fourteen -m ksu.xml --git-lfs
```
> ⚠️ `--git-lfs` is required — GMS APKs are stored in Git LFS. Without it you will get corrupt APK errors during build.

### 4. Repo Sync
```bash
repo sync -c -j$(nproc) --force-sync --no-clone-bundle --no-tags
```

### 5. Setup CCACHE (recommended)
```bash
export USE_CCACHE=1
export CCACHE_EXEC=$(which ccache)
ccache -M 50G
```

### 6. Build
```bash
source build/envsetup.sh
breakfast spes
mka bacon -j$(nproc)
```

### 7. Output
The flashable ZIP will be at:
```
out/target/product/spes/PixelOS-*.zip
```

---

## Notes
- For the full list of bugs fixed, see [BUGS_FIXED.md](BUGS_FIXED.md)
- For KernelSU integration details, see [KERNELSU.md](KERNELSU.md)
- Thai language guide: [README.md](README.md)
