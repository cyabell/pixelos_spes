# PixelOS 14 - Xiaomi Redmi Note 11 (spes)

> ROM นี้สร้างด้วยความช่วยเหลือของ AI (Qoder) ✨

---

## ข้อมูล ROM
| รายการ | ข้อมูล |
|--------|--------|
| ROM | PixelOS (AOSP 14) |
| Device | Xiaomi Redmi Note 11 (spes) |
| Android | 14 (AP2A) |
| Kernel | Linux 4.19 (sm6225) |
| Branch | `fourteen` |

---

## Manifest ที่ให้เลือก

| Manifest | คำอธิบาย |
|----------|-----------|
| `default.xml` | ROM ต้นฉบับ ไม่มี root |
| `ksu.xml` | ROM + KernelSU Next (root พร้อมใช้) |

---

## วิธี Setup และ Build

### 1. ติดตั้ง Dependencies
```bash
sudo apt-get install -y bc bison build-essential ccache curl flex g++-multilib gcc-multilib git git-lfs gnupg gperf imagemagick lib32ncurses-dev lib32readline-dev lib32z1-dev liblz4-tool libncurses-dev libsdl1.2-dev libssl-dev libwxgtk3.2-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev
```

### 2. ติดตั้ง repo tool
```bash
mkdir -p ~/bin && curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo && chmod a+x ~/bin/repo
```

### 3. Repo Init

**Original ROM (ไม่มี root):**
```bash
repo init -u https://github.com/cyabell/pixelos_spes -b fourteen -m default.xml --git-lfs
```

**ROM + KernelSU Next:**
```bash
repo init -u https://github.com/cyabell/pixelos_spes -b fourteen -m ksu.xml --git-lfs
```
> ⚠️ ต้องใส่ `--git-lfs` เพื่อให้ GMS APKs โหลดครบ

### 4. Repo Sync
```bash
repo sync -c -j$(nproc) --force-sync --no-clone-bundle --no-tags
```

### 5. Setup CCACHE (แนะนำ)
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
ไฟล์ ZIP จะอยู่ที่:
```
out/target/product/spes/PixelOS-*.zip
```

---

## หมายเหตุ
- ดูรายการ Bug ที่แก้ไขไปแล้วได้ที่ [BUGS_FIXED.md](BUGS_FIXED.md)
- ดูรายละเอียด KernelSU Integration ได้ที่ [KERNELSU.md](KERNELSU.md)
- English version: [README_EN.md](README_EN.md)
