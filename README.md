# PixelOS 14 — Xiaomi Redmi Note 11 (spes)

> ROM นี้สร้างด้วยความช่วยเหลือของ AI (Qoder) ✨

---

## ข้อมูล ROM
| รายการ | ข้อมูล |
|--------|--------|
| ROM | PixelOS (AOSP 14) |
| Device | Xiaomi Redmi Note 11 (spes) |
| Android | 14 (AP2A) |
| Branch | `fourteen` |

---

## วิธี Setup และ Build

### 1. ติดตั้ง Dependencies
```bash
sudo apt-get install -y bc bison build-essential ccache curl flex g++-multilib gcc-multilib git git-lfs gnupg gperf imagemagick lib32ncurses5-dev lib32readline-dev lib32z1-dev liblz4-tool libncurses5 libncurses5-dev libsdl1.2-dev libssl-dev libwxgtk3.0-gtk3-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev
```

### 2. ติดตั้ง repo tool
```bash
mkdir -p ~/bin && curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo && chmod a+x ~/bin/repo
```

### 3. Repo Init
```bash
repo init -u https://github.com/cyabell/pixelos_spes -b fourteen --git-lfs
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
- ดูวิธี Setup ภาษาอังกฤษได้ที่ [README_EN.md](README_EN.md)
