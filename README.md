# Teclast T8용 LineageOS 14.1 (Android 7.1.2) — 비공식 포팅

[English](README.md) | **한국어**

MediaTek **MT8176** 태블릿 **Teclast T8 (Master T8)**용 비공식 LineageOS 14.1 포팅입니다.

Amazon Fire HD 10 2017(`suez`, MT8173)용 LineageOS 14.1의 시스템에 T8 순정 커널, 하드웨어 드라이버(vendor), 기기 설정을 합쳐서 만들었습니다. LineageOS 공식 빌드가 **아니며**, LineageOS·Teclast·Amazon과 관련이 없습니다.

**원본 ROM:** **ggow**님의 [[CLOSED][DISCONTINUED][ROM][unlocked][suez] Lineage-14.1 [26 SEP 2019]](https://xdaforums.com/t/closed-discontinued-rom-unlocked-suez-lineage-14-1-26-sep-2019.3973703/)
이 ROM을 만들고 공유해 주신 ggow님께 깊이 감사드립니다. ggow님의 작업이 없었다면 이 포팅도 없었습니다.

**리커버리:** **sword900**님의 T8용 TWRP 3.2.3, [Teclast Master T8 (E8C1) general thread](https://xdaforums.com/t/teclast-master-t8-e8c1-general-thread.3900666/)
T8용 TWRP를 만들어 주신 sword900님께 감사드립니다. 이 TWRP 덕분에 T8에 이 ROM을 설치할 수 있습니다.

> ⚠️ **설치는 본인 책임입니다.** 무엇을 하든 먼저 NVRAM을 백업하세요(아래 참고).

---

## 기기 정보

| 항목 | 내용 |
|---|---|
| 기기 | Teclast T8 (Master T8), 보드 `wisky8176_tb_n`, 코드명 `t8_e8c1_g` |
| SoC | MediaTek MT8176 (플랫폼 `mt8173`) |
| GPU | PowerVR Rogue GX6250 |
| 화면 | 8.4인치 WQXGA (2560×1600), 320 dpi |
| 순정 펌웨어 | Android 7.0 (NRD90M), 2019-08-16 빌드 |

## ROM 정보

| 항목 | 내용 |
|---|---|
| Android | 7.1.2 (NJH47F) |
| LineageOS | 14.1, userdebug |
| 보안 패치 | 2019-09-05 (원본 빌드 기준) |
| 커널 | T8 순정 3.18.35 (2019-08-16), 수정하지 않은 바이너리 |
| SELinux | Permissive (알려진 문제 참고) |
| 현재 버전 | **r5** |

---

## 동작 현황

| 기능 | 상태 |
|---|---|
| 부팅, 화면, 터치 | ✅ 정상 |
| GPU 하드웨어 가속 (GLES 3.2, DDK 1.7) | ✅ 정상 |
| Wi-Fi | ✅ 정상 |
| Bluetooth | ✅ 정상 |
| 오디오 (스피커, 유선 이어폰·헤드폰) | ✅ 정상 |
| 카메라 | ✅ 정상 |
| 센서 (자동 회전, 자동 밝기) | ✅ 정상 |
| 충전 | ✅ 정상 |
| 화면 끔 후 다시 켜기 | ✅ 정상 |
| 지문 (Goodix) | ❌ 미지원 |
| 화면 잠금 (PIN / 패턴 / 비밀번호) | ✅ 정상 (소프트웨어 gatekeeper) |
| GPS | ❌ T8 하드웨어에 없음 |
| SELinux enforcing | ❌ 현재 permissive |

유선 이어폰·헤드폰을 처음 꽂을 때 가끔 잘못 인식될 수 있습니다. 다시 꽂으면 정상으로 돌아옵니다.

## 알려진 문제

- **SELinux가 permissive입니다.** 원본 빌드의 보안 정책이 T8 하드웨어 데몬을 모두 포함하지 않아서입니다. 거부 로그를 모은 뒤 enforcing으로 바꿀 예정입니다.
- **보안 패치가 오래됐습니다(2019-09).** Android 7.1 기반이므로 민감한 데이터를 다루는 용도로는 권하지 않습니다.
- **지문은 지원하지 않습니다.** 지문 등록에 필요한 T8 TrustZone gatekeeper가 이 ROM에서 동작하지 않습니다. 지문 구성 요소를 제거했기 때문에 설정에 지문 메뉴가 나오지 않습니다.
- **화면 잠금은 소프트웨어 gatekeeper를 씁니다.** PIN, 패턴, 비밀번호는 정상 동작하지만, TrustZone이 아닌 일반 Android 영역에서 검증합니다. 원본 `suez` ROM과 같은 방식입니다.
- MediaTek 전용 프레임워크가 필요한 MTK 앱(엔지니어링 모드, MTK Logger, MiraVision 등)은 제거했습니다.

---

## 준비물

- 순정 파티션 구조의 Teclast T8 (**T8 전용입니다**)
- T8(MT8176)용 TWRP 3.2.3 (sword900님 제작). [Teclast Master T8 (E8C1) general thread](https://xdaforums.com/t/teclast-master-t8-e8c1-general-thread.3900666/)에서 받을 수 있습니다.
- SP Flash Tool과 MediaTek USB VCOM 드라이버 (백업과 리커버리 설치용)
- ADB·fastboot가 설치된 PC

---

## 설치 방법

### 1. 백업 (필수)

**SP Flash Tool → Readback**으로 아래 파티션을 백업하세요. 주소는 순정 scatter 파일 기준입니다.

| 파티션 | 시작 주소 | 길이 |
|---|---|---|
| proinfo | `0x80000` | `0x300000` |
| nvram | `0x380000` | `0x500000` |
| protect1 | `0x880000` | `0xA00000` |
| protect2 | `0x1280000` | `0xA00000` |
| seccfg | `0x4F80000` | `0x80000` |
| nvdata | `0x6B00000` | `0x2000000` |

Wi-Fi·Bluetooth MAC 주소와 보정값이 들어 있어서, 어떤 펌웨어로도 복구할 수 없습니다.

> ❗ SP Flash Tool의 **Format All + Download**는 절대 쓰지 마세요. NVRAM이 지워집니다.

### 2. TWRP 설치

TWRP 이미지를 **recovery** 파티션에만 올립니다.

- **SP Flash Tool:** 순정 scatter를 불러온 뒤 `recovery`만 체크하고 TWRP 이미지를 지정해 **Download Only**로 올립니다.
- **부트로더를 잠금 해제한 경우:** `fastboot flash recovery <twrp-image>.img`

부트로더 잠금 해제는 선택 사항입니다. T8 부트로더는 잠긴 상태에서도 이 ROM을 부팅합니다. 잠금을 해제하면 fastboot를 쓸 수 있다는 점만 달라집니다.

### 3. ROM 설치

1. TWRP로 부팅합니다.
2. **Wipe → Format Data**를 누르고 `yes`를 입력합니다. 순정, MIUI, 다른 ROM에서 넘어올 때 반드시 필요합니다. 순정 펌웨어는 `/data`를 암호화하는데, 이 ROM과 TWRP는 암호화하지 않은 data를 쓰기 때문입니다.
3. **Install**에서 `lineage-14.1-20261110-UNOFFICIAL-T8-port-r5.zip`을 설치합니다.
4. (선택) 이어서 GApps를 설치합니다(아래 참고).
5. 재부팅합니다. 첫 부팅은 5~10분 걸릴 수 있습니다.

설치 파일은 **`system`과 `boot` 파티션만** 씁니다. preloader, lk, tee, nvram 등 기기 고유 파티션은 건드리지 않습니다.

### GApps (선택)

포함되어 있지 않습니다. Google 앱이 필요하면 ROM을 설치한 직후, 첫 부팅 전에 **arm64, Android 7.1용** GApps 패키지(가장 작은 구성 권장)를 설치하세요. 이 방법으로 정상 동작하는 것을 확인했습니다.

> ROM zip을 다시 설치할 때는 바로 이어서 같은 GApps도 다시 설치하세요. 설치 파일이 `/system`을 새로 쓰기 때문에 GApps가 유지되지 않습니다.

---

## 순정으로 되돌리기

1. SP Flash Tool에서 순정 scatter를 불러옵니다.
2. **Download Only**로 올립니다. Format All은 쓰지 마세요.
3. Wi-Fi MAC이나 보정값이 사라졌다면 1단계에서 만든 NVRAM 백업을 복원합니다.

---

## 다운로드

| 파일 | 비고 |
|---|---|
| [`lineage-14.1-20261110-UNOFFICIAL-T8-port-r5.zip`](https://github.com/sdtown-kr/Teclast-Master-T8-LineageOS-14.1-Unofficial-ROM/releases/download/custom-rom/lineage-14.1-20261110-UNOFFICIAL-T8-port-r5.zip) | ROM. SHA-256: `189ceb93a2f6ece4af50f044cab5e238a646223ab10c17d5aa8e25836ab934cc` |

**다운로드:** [lineage-14.1-20261110-UNOFFICIAL-T8-port-r5.zip](https://github.com/sdtown-kr/Teclast-Master-T8-LineageOS-14.1-Unofficial-ROM/releases/download/custom-rom/lineage-14.1-20261110-UNOFFICIAL-T8-port-r5.zip)

---

## 기술 노트 (포팅 방법)

다시 만들거나 개선하려는 분을 위한 내용입니다.

- **원본:** ggow님의 LineageOS 14.1 `suez` 2019-09-26 빌드 ([XDA 스레드](https://xdaforums.com/t/closed-discontinued-rom-unlocked-suez-lineage-14-1-26-sep-2019.3973703/)). 대상 기기는 Amazon Fire HD 10 2017(MT8173)이고, Wi-Fi 칩이 T8과 같은 MT6630입니다.
- **커널:** T8 순정 3.18.35 바이너리(DTB 포함)를 수정 없이 사용했습니다.
- **램디스크:** LineageOS의 `init.rc`, `init.cm.rc`, zygote, usb, sepolicy, file_contexts에 T8 순정 기기 파일을 합쳤습니다.
  - `init.mt8173.rc`, `init.project.rc`, `init.connectivity.rc`, `init.modem.rc`, `ueventd.mt8173.rc`, `fstab.mt8173`
  - fstab에서 `forceencrypt`와 `verify`를 제거했습니다.
  - 커널에 schedtune이 없어서 기존 방식의 `cpuctl` cgroup 설정을 추가했습니다.
- **vendor:** T8 순정 `/system/vendor` 전체를 넣었습니다. GPU DDK 1.7, HAL, MT6630 펌웨어, MTK 데몬이 포함됩니다. T8 vendor 라이브러리를 가리던 suez의 MTK 라이브러리(`/system/lib(64)`, 224개)는 제거했습니다.
- **Android 7.0 드라이버를 7.1에서 쓰기 위한 수정:**
  - `libdrm`을 T8 순정 것으로 교체했습니다. `libsrv_um`이 쓰는 `drmOpenRender` 때문입니다.
  - vendor에 `libxml2`와 `libcurl`을 추가했습니다.
  - `surfaceflinger`, `mediacodec`, `mediaserver`에 `libshim_libcamera.so`를 `LD_PRELOAD`했습니다. Android 7.0의 `GraphicBuffer(uint, uint, int, uint)` 생성자를 제공하는 라이브러리입니다.
- **SELinux 라벨·도메인:**
  - `wmt_launcher`의 라벨을 `conn_launcher_exec`로 바꿨습니다.
  - `autokd`(Wi-Fi SDIO auto-K에 필요), `aal`, `kisd`, `gx_fpd`에 `seclabel`을 추가했습니다.
- **기타:**
  - Bluetooth 기본 이름을 "Amazon Fire"에서 "T8"로 바꿨습니다.
  - T8에 GPS 하드웨어가 없어서 GPS HAL과 데몬을 제거했습니다.
  - 하드웨어 gatekeeper HAL(`gatekeeper.mt8173.so`)을 제거했습니다. T8 TrustZone gatekeeper가 잠금 비밀번호를 저장하지 못해서, `gatekeeperd`가 소프트웨어 GateKeeper로 전환되도록 했습니다.
  - Goodix 지문은 넣지 않았습니다. 지문 등록에 TrustZone gatekeeper가 필요하기 때문입니다.

### Android 9 이상이 안 되는 이유

T8 순정 커널(3.18.35)에는 `hwbinder`/`vndbinder` 장치와 binder scatter-gather 기능(`BC_TRANSACTION_SG`)이 없습니다. 둘 다 Android 8 이상에 필수입니다. T8 커널 소스가 공개되지 않아서, 더 높은 버전으로 가려면 새 커널이 먼저 필요합니다. 한 가지 방법은 이 기능들이 이미 들어 있는 MT8173 3.18 커널에 T8 하드웨어 지원을 옮겨 넣는 것입니다.

---

## 커널 소스 / GPL

커널은 **수정하지 않은 Teclast 순정 바이너리**입니다. 이 배포를 위해 커널 소스를 수정하거나 빌드하지 않았습니다. 커널 소스 공개 책임은 Teclast(Wisky ODM, `mt8176` 3.18 트리)에 있습니다. 소스를 구하시면 공유해 주세요.

---

## 크레딧

- **LineageOS** 팀과 기여자 여러분
- **ggow**님: 이 포팅의 원본인 [Amazon Fire HD 10 2017(`suez`)용 LineageOS 14.1](https://xdaforums.com/t/closed-discontinued-rom-unlocked-suez-lineage-14-1-26-sep-2019.3973703/). ROM을 만들고 공유해 주셔서 감사합니다. 이 T8 포팅은 전적으로 ggow님의 작업 위에 서 있습니다.
- **sword900**님: T8용 TWRP 3.2.3 ([Teclast Master T8 (E8C1) general thread](https://xdaforums.com/t/teclast-master-t8-e8c1-general-thread.3900666/))
- **Goayandi**님: MT8173/MT8176 개발 자료 (Mi Pad 3 `cappu` 트리)
- **Teclast / Wisky**: 순정 펌웨어, 커널, vendor 파일
- XDA에서 MT8173/MT8176 기기를 개발하는 모든 분들
