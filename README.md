# rothko LineageOS 23.2 collaboration sources

[中文入口](README.zh-CN.md)

Unofficial, source-only collaboration for Redmi K70 Ultra (`rothko`). The
Android framework is Android 16; the selected stock vendor is Android 14 / API
34 from **OS3.0.307.0.WNNCNXM**. This repository is an integration guide and pinned
source manifest, not a ROM release or an official LineageOS submission.

The collaboration set has four repositories:

| Repository | Android checkout path | Contents |
| --- | --- | --- |
| `android_device_xiaomi_rothko` | `device/xiaomi/rothko` | Device configuration, services, overlays, policy and extraction tools |
| `proprietary_vendor_xiaomi_rothko` | `vendor/xiaomi/rothko` | Build metadata and portable source ledger; no proprietary payloads |
| `rothko-kernel-extraction` | `device/xiaomi/rothko-kernel` | Stock kernel material extraction recipe; no kernel source or binaries |
| `rothko-lineage-integration` | integration | Pinned manifest, platform patch series, extraction/build instructions and validation notes |

The device tree alone is insufficient. Follow [BUILD.md](BUILD.md) and use the
companion commits recorded in `baseline.json`. The kernel repository is an
extraction recipe, not a kernel source release.

`KNOWN_ISSUES.md` lists remaining reports and limits. User tests from earlier
images do not automatically validate this exact source set; consult
`VALIDATION.md` for the scope of checks performed on these repositories.

No firmware archive, factory calibration, modem NV, device identity, biometric
template, signing key or phone log is included. Obtain matching stock firmware
yourself and reconstruct declared private inputs locally. Do not commit them.
The source preparation tools do not flash devices or manipulate the bootloader.

Source publication does not grant proprietary redistribution rights or claim a
clean full-ROM build, runtime acceptance, complete hardware parity or official
device status.
