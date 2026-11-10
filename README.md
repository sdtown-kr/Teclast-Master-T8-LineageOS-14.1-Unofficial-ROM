# LineageOS 14.1 (Android 7.1.2) for Teclast T8 — Unofficial Port

**English** | [한국어](README.ko.md)

An unofficial LineageOS 14.1 port for the **Teclast T8 (Master T8)** tablet, MediaTek **MT8176**.

The ROM combines the LineageOS 14.1 system from the Amazon Fire HD 10 2017 (`suez`, MT8173) with the T8's own stock kernel, vendor blobs and device configuration. It is **not** an official LineageOS build and is not affiliated with LineageOS, Teclast or Amazon.

**Based on:** [[CLOSED][DISCONTINUED][ROM][unlocked][suez] Lineage-14.1 [26 SEP 2019]](https://xdaforums.com/t/closed-discontinued-rom-unlocked-suez-lineage-14-1-26-sep-2019.3973703/) by **ggow**. Huge thanks to ggow: without that work this port would not exist.

**Recovery:** TWRP 3.2.3 for the T8 by **sword900**, from the [Teclast Master T8 (E8C1) general thread](https://xdaforums.com/t/teclast-master-t8-e8c1-general-thread.3900666/). Many thanks to sword900 for making it: it is what lets this ROM be installed on the T8.

> ⚠️ **Flash at your own risk.** You are responsible for your device. Back up your NVRAM before doing anything (see below).

---

## Device

| | |
|---|---|
| Device | Teclast T8 (Master T8), board `wisky8176_tb_n`, codename `t8_e8c1_g` |
| SoC | MediaTek MT8176 (platform `mt8173`) |
| GPU | PowerVR Rogue GX6250 |
| Display | 8.4" WQXGA (2560×1600), 320 dpi |
| Stock firmware | Android 7.0 (NRD90M), build 2019-08-16 |

## ROM info

| | |
|---|---|
| Android | 7.1.2 (NJH47F) |
| LineageOS | 14.1, userdebug |
| Security patch | 2019-09-05 (inherited from the base build) |
| Kernel | T8 stock 3.18.35 (2019-08-16), unmodified binary |
| SELinux | Permissive (see Known issues) |
| Current release | **r5** |

---

## What works

| Feature | Status |
|---|---|
| Boot, display, touch | ✅ Working |
| GPU hardware acceleration (GLES 3.2, DDK 1.7) | ✅ Working |
| Wi-Fi | ✅ Working |
| Bluetooth | ✅ Working |
| Audio (speaker, wired earphones and headphones) | ✅ Working |
| Camera | ✅ Working |
| Sensors (auto-rotate, auto-brightness) | ✅ Working |
| Charging | ✅ Working |
| Sleep / wake | ✅ Working |
| Fingerprint (Goodix) | ❌ Not supported |
| Screen lock (PIN / pattern / password) | ✅ Working (software gatekeeper) |
| GPS | ❌ Not present in T8 hardware |
| SELinux enforcing | ❌ Permissive for now |

Wired headset detection may occasionally misdetect on the first insertion. Re-plugging fixes it.

## Known issues

- **SELinux is permissive.** The base build's policy does not cover all T8 vendor daemons. Enforcing mode is planned once AVC denials are collected.
- **Old security patch (2019-09).** This is an Android 7.1 based ROM; do not rely on it for sensitive data.
- **Fingerprint is not supported.** The T8 TrustZone gatekeeper does not work on this ROM, and fingerprint enrolment depends on it. The fingerprint components are removed, so no fingerprint menu is shown.
- **Screen lock uses the software gatekeeper.** PIN, pattern and password work, but they are verified in normal Android rather than in TrustZone. This is the same as the base `suez` ROM.
- Some MTK extras (MTK Engineer Mode, MTK Logger, MiraVision) are removed because they need the MediaTek framework.

---

## Requirements

- Teclast T8 with stock firmware layout (**this ROM is for the T8 only**)
- TWRP 3.2.3 for the T8 (MT8176), by sword900. Available in the [Teclast Master T8 (E8C1) general thread](https://xdaforums.com/t/teclast-master-t8-e8c1-general-thread.3900666/)
- SP Flash Tool + MediaTek USB VCOM drivers (for backup and recovery flashing)
- A PC with ADB / fastboot

---

## Installation

### 1. Back up first (mandatory)

Use **SP Flash Tool → Readback** to dump these partitions (addresses from the stock scatter file):

| Partition | Start | Length |
|---|---|---|
| proinfo | `0x80000` | `0x300000` |
| nvram | `0x380000` | `0x500000` |
| protect1 | `0x880000` | `0xA00000` |
| protect2 | `0x1280000` | `0xA00000` |
| seccfg | `0x4F80000` | `0x80000` |
| nvdata | `0x6B00000` | `0x2000000` |

These contain your Wi-Fi/BT MAC and calibration data, and cannot be recovered from any firmware package.

> ❗ Never use **Format All + Download** in SP Flash Tool. It erases NVRAM.

### 2. Install TWRP

Flash the TWRP image to the **recovery** partition only:

- SP Flash Tool: load the stock scatter, untick everything except `recovery`, select the TWRP image, **Download Only**.
- Or, with an unlocked bootloader: `fastboot flash recovery <twrp-image>.img`

Unlocking the bootloader is optional. The T8 bootloader boots this ROM while locked. Unlocking only lets you use fastboot.

### 3. Flash the ROM

1. Boot into TWRP.
2. **Wipe → Format Data** → type `yes`. This is required when coming from stock, MIUI or another ROM: stock firmware encrypts `/data`, and this ROM and TWRP use unencrypted data.
3. **Install** → `lineage-14.1-20261110-UNOFFICIAL-T8-port-r5.zip`
4. (Optional) Install GApps now (see below).
5. Reboot. The first boot can take 5–10 minutes.

The installer writes **only** the `system` and `boot` partitions. It never touches preloader, lk, tee, nvram or other device-specific partitions.

### GApps (optional)

Not included. If you want Google apps, flash an **arm64, Android 7.1** GApps package (smallest variant recommended) right after the ROM, before the first boot. Tested and working.

> If you ever re-flash the ROM zip, re-flash the same GApps package right after it. The installer rewrites `/system` and does not keep GApps.

---

## Recovery / going back to stock

1. Load the stock scatter in SP Flash Tool.
2. Use **Download Only** (not Format All).
3. If the Wi-Fi MAC or calibration is lost, restore the NVRAM backup from step 1.

---

## Downloads

| File | Notes |
|---|---|
| [`lineage-14.1-20261110-UNOFFICIAL-T8-port-r5.zip`](https://github.com/sdtown-kr/Teclast-Master-T8-LineageOS-14.1-Unofficial-ROM/releases/download/custom-rom/lineage-14.1-20261110-UNOFFICIAL-T8-port-r5.zip) | ROM. SHA-256: `189ceb93a2f6ece4af50f044cab5e238a646223ab10c17d5aa8e25836ab934cc` |

**Download:** [lineage-14.1-20261110-UNOFFICIAL-T8-port-r5.zip](https://github.com/sdtown-kr/Teclast-Master-T8-LineageOS-14.1-Unofficial-ROM/releases/download/custom-rom/lineage-14.1-20261110-UNOFFICIAL-T8-port-r5.zip)

---

## Technical notes (how this port was made)

For people who want to reproduce or improve it.

- **Base:** LineageOS 14.1 `suez` build 2019-09-26 by ggow ([XDA thread](https://xdaforums.com/t/closed-discontinued-rom-unlocked-suez-lineage-14-1-26-sep-2019.3973703/)). Device: Amazon Fire HD 10 2017, MT8173, same MT6630 combo chip as the T8.
- **Kernel:** T8 stock 3.18.35 binary with appended DTB, unmodified.
- **Ramdisk:** LineageOS `init.rc`, `init.cm.rc`, zygote, usb, sepolicy and file_contexts, combined with the T8 stock device files:
  - `init.mt8173.rc`, `init.project.rc`, `init.connectivity.rc`, `init.modem.rc`, `ueventd.mt8173.rc`, `fstab.mt8173`
  - `forceencrypt` and `verify` removed from fstab
  - legacy `cpuctl` cgroup setup added, since the kernel has no schedtune
- **Vendor:** the whole `/system/vendor` from T8 stock, including GPU DDK 1.7, HALs, MT6630 firmware and MTK daemons. Suez MTK blobs in `/system/lib(64)` that shadowed T8 vendor libraries were removed (224 files).
- **ABI fixes for Android 7.0 blobs on 7.1:**
  - `libdrm` taken from T8 stock (`drmOpenRender` for `libsrv_um`).
  - `libxml2` and `libcurl` added to vendor.
  - `libshim_libcamera.so` is `LD_PRELOAD`ed for `surfaceflinger`, `mediacodec` and `mediaserver`. It provides the Android 7.0 `GraphicBuffer(uint, uint, int, uint)` constructor.
- **SELinux labels / domains:**
  - `wmt_launcher` relabelled to `conn_launcher_exec`.
  - `seclabel`s added for `autokd` (required for the Wi-Fi SDIO auto-K), `aal`, `kisd` and `gx_fpd`.
- **Other fixes:**
  - Bluetooth default name patched from "Amazon Fire" to "T8".
  - GPS HAL and daemon removed, since the T8 has no GPS hardware.
  - Hardware gatekeeper HAL (`gatekeeper.mt8173.so`) removed. `gatekeeperd` falls back to the software GateKeeper, because the T8 TrustZone gatekeeper failed to enrol credentials.
  - Goodix fingerprint not included: enrolment requires the TrustZone gatekeeper.

### Why not Android 9 or newer?

The T8 stock kernel (3.18.35) has no `hwbinder`/`vndbinder` devices and no binder scatter-gather support (`BC_TRANSACTION_SG`). Both are required by Android 8 and later. Kernel source for the T8 is not available, so newer Android versions need a new kernel first. One option is to port the T8 board support onto an MT8173 3.18 kernel that already has these backports.

---

## Kernel source / GPL

The kernel is the **unmodified Teclast stock binary**. No kernel source was modified or built for this release. The kernel source is the responsibility of Teclast (Wisky ODM, `mt8176` 3.18 tree). If you obtain it, please share it.

---

## Credits

- **LineageOS** team and contributors
- **ggow**: [LineageOS 14.1 for Amazon Fire HD 10 2017 (`suez`)](https://xdaforums.com/t/closed-discontinued-rom-unlocked-suez-lineage-14-1-26-sep-2019.3973703/), the base of this port. Thank you for building and sharing it. This T8 port stands entirely on your work.
- **sword900**: TWRP 3.2.3 for the T8 ([Teclast Master T8 (E8C1) general thread](https://xdaforums.com/t/teclast-master-t8-e8c1-general-thread.3900666/))
- **Goayandi**: MT8173/MT8176 development resources (Mi Pad 3 `cappu` trees)
- **Teclast / Wisky**: stock firmware, kernel and vendor blobs
- Everyone at XDA working on MT8173/MT8176 devices
