# BUGS FIXED — PixelOS 14 spes

> รายการปัญหาทั้งหมดที่แก้ไขเพื่อให้ Build ผ่าน 100%
> All issues fixed to achieve a successful build and working sideload.

---

## 1. `qti_kernel_headers` — Duplicate Soong Module
- **File:** `vendor/aosp/build/soong/Android.bp`
- **Cause:** Module defined in both `kernel/xiaomi/sm6225/Android.bp` and `vendor/aosp/build/soong/Android.bp`
- **Fix:** Removed duplicate definition from `vendor/aosp/build/soong/Android.bp`

---

## 2. `qcom_libfmjni_defaults` — Missing Soong Config Module
- **File:** `vendor/aosp/build/soong/Android.bp`
- **Cause:** `vendor/qcom/opensource/libfmjni/Android.bp` referenced `qcom_libfmjni_defaults` which did not exist
- **Fix:** Added `soong_config_module_type` definition for `qcom_libfmjni_defaults` to `vendor/aosp/build/soong/Android.bp`

---

## 3. `libfmjni` — Soong Namespace Blocking Make Layer Resolution
- **Files:** `packages/apps/FMRadio/jni/fmr/Android.bp`, `vendor/qcom/opensource/libfmjni/Android.bp`
- **Cause:** `soong_namespace {}` in `jni/fmr/Android.bp` isolated `libfmjni` from Make-layer `jni_libs` lookups; `vendor/qcom/opensource/libfmjni/Android.bp` had broken `cc_defaults` references
- **Fix:** Removed `soong_namespace {}` from `jni/fmr/Android.bp`; deleted broken `vendor/qcom/opensource/libfmjni/Android.bp`

---

## 4. `custom-sdk_org.lsposed.hiddenapibypass_hiddenapibypass` — Missing Static Library
- **File:** `packages/apps/BtHelper/Android.bp`
- **Cause:** BtHelper depends on LSPosed HiddenApiBypass library not present in tree
- **Fix:** Removed the dependency from BtHelper's `static_libs`

---

## 5. `libmegface` — Duplicate Module (ckati)
- **File:** `hardware/xiaomi/megvii/Android.bp`
- **Cause:** Both `packages/apps/ParanoidSense/Android.bp` and `hardware/xiaomi/megvii/Android.bp` defined `libmegface`
- **Fix:** Renamed megvii module to `libmegface.megvii` with `stem: "libmegface"` to avoid collision

---

## 6. `IntentForwarderActivity.java` — AOSP 14 API Mismatch
- **File:** `packages/modules/IntentResolver/java/src/com/android/intentresolver/IntentForwarderActivity.java`
- **Cause:** Called `resolveActivityAsUser(Intent, String, int, int)` (4-arg) which was removed in AOSP 14
- **Fix:** Changed to `resolveActivityAsUser(intent, flags, userId)` (3-arg version)

---

## 7. `CarrierConfigLoader.java` — `TelephonyPermissions.isShell()` Removed
- **File:** `packages/services/Telephony/src/com/android/phone/CarrierConfigLoader.java`
- **Cause:** `TelephonyPermissions.isShell(int)` does not exist in AOSP 14
- **Fix:** Replaced with `getCallingUid() == android.os.Process.SHELL_UID`

---

## 8. `BtHelper` — `Theme.SubSettingsBase.Expressive` Not Found
- **File:** `packages/apps/BtHelper/AndroidManifest.xml`
- **Cause:** Theme referenced in manifest does not exist in PixelOS/AOSP tree
- **Fix:** Changed to `@style/Theme.Material3.DynamicColors.DayNight`

---

## 9. `BtHelper` — Kotlin Compile Errors
- **Files:** `packages/apps/BtHelper/` (multiple Kotlin sources)
- **Cause:** `SettingsBasePreferenceFragment` unresolved (not in SettingsLib); `HiddenApiBypass` unresolved (lib removed in fix #4)
- **Fix:** Removed BtHelper entirely from `PRODUCT_PACKAGES` in `vendor/aosp/config/common.mk`

---

## 10. `hardware/google/pixel` — Duplicate `kernel_headers/Android.bp`
- **File:** `hardware/google/pixel/kernel_headers/Android.bp`
- **Cause:** `cc_library_headers` module conflicted with another definition in tree
- **Fix:** Deleted `kernel_headers/Android.bp` from `hardware/google/pixel`

---

## 11. `hardware/lineage/compat` — Duplicate `Android.bp`
- **File:** `hardware/lineage/compat/Android.bp`
- **Cause:** Module definition conflicted with another source in tree
- **Fix:** Deleted `Android.bp` from `hardware/lineage/compat`

---

## 12. GMS APKs — Git LFS Stubs (132-byte pointer files)
- **Path:** `vendor/gms/` (85 APKs affected)
- **Cause:** `repo init` was run without `--git-lfs`, so all GMS prebuilt APKs were Git LFS pointer stubs (~132 bytes each). This caused `ZipException: zip END header not found` at 91% into the build.
- **Fix:** `cd vendor/gms && git lfs pull` — fetched all real APKs (0 stubs remaining)
- **Prevention:** Always use `repo init ... --git-lfs`
