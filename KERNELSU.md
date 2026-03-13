# KernelSU Next Integration - PixelOS 14 / spes (sm6225)

> Kernel branch: `fourteen-ksu` in [kernel_xiaomi_sm6225](https://github.com/cyabell/kernel_xiaomi_sm6225)

---

## KernelSU Version

| Item | Value |
|------|-------|
| KernelSU variant | KernelSU Next |
| KSU_VERSION (kernel) | **33024** |
| Compatible Manager | KernelSU Next Manager **v3.1.0** |
| Manager download | [KernelSU Next Releases](https://github.com/rifsxd/KernelSU-Next/releases) |

---

## Overview

This integration embeds [KernelSU Next](https://github.com/rifsxd/KernelSU-Next) directly into the Linux 4.19 kernel source for Xiaomi Redmi Note 11 (spes/sm6225). KernelSU operates at the kernel level — no `su` binary in PATH, no Magisk artifacts — making root inherently stealthy.

---

## What Was Done

### 1. KernelSU Next source embedded
- Copied KernelSU Next source into `drivers/kernelsu/`
- Added to `drivers/Kconfig` and `drivers/Makefile`
- Version hardcoded to **33024** in `drivers/kernelsu/Kbuild`

### 2. Kernel config (`spes-perf_defconfig`)
Enabled:
```
CONFIG_KSU=y
CONFIG_KSU_DEBUG=n
CONFIG_OVERLAY_FS=y
CONFIG_FHANDLE=y
CONFIG_FTRACE=y
CONFIG_KPROBES=y
CONFIG_KPROBE_EVENTS=y
CONFIG_HAVE_KPROBES=y
```

### 3. Linux 4.19 compatibility patches

| File | Fix |
|------|-----|
| `drivers/kernelsu/selinux/sepolicy.c` | Pre-check `flex_array` capacity before adding types; NULL check in `add_typeattribute_raw` |
| `drivers/kernelsu/selinux/rules.c` | `apply_kernelsu_rules()` returns `bool`; skips rules if type creation fails |
| `drivers/kernelsu/selinux/selinux.h` | Updated signature to `bool apply_kernelsu_rules()` |
| `drivers/kernelsu/ksud.c` | Guard `cache_sid()` and `setup_ksu_cred()` behind `apply_kernelsu_rules()` result; added `late-init` RC fallback |
| `drivers/kernelsu/file_wrapper.c` | `read_proxy` / `read_iter_proxy`: append RC after each non-zero read (Android 14 init reads in chunks, no final empty read) |
| `drivers/kernelsu/ksu.c` | `on_post_fs_data()`: ensure `/data/adb` directory exists before loading allow list |

### 4. RC injection fix
Android 14 with non-GKI kernel may miss the `post-fs-data` trigger. Added `late-init` as a fallback in `KERNEL_SU_RC`.

### 5. SELinux flex_array fix (root cause of bootloop)
Linux 4.19 allocates `type_attr_map_array` as a fixed-size `flex_array` at policy load time.
KernelSU trying to add new types (`su`, `ksu_file`) at runtime exceeded capacity — resulting in a half-registered type — causing `strlen(NULL)` crash — kernel panic — bootloop.

**Fix**: Pre-check capacity before inserting. If full, `apply_kernelsu_rules()` returns `false` and skips all dependent calls gracefully.

---

## Result

| Feature | Status |
|---------|--------|
| KernelSU Working | Built-in (GKI mode) |
| Hook mode | Kprobes |
| Root access | uid=0 |
| SELinux su domain | u:r:su:s0 |
| Zygisk (ZygiskNext) | Supported |
| Root detection | Hidden (no su binary in PATH) |
