# Validation of the LineageOS 23.2 source snapshot

The source snapshot was recorded before the LineageOS 24 migration on
2026-11-14. The checks here apply to the filtered publication candidate
prepared on 2026-11-21.

## Source and host checks

- The six device commits between the previous source export and the snapshot
  were applied to the public device tree in order. Their modified files
  match the frozen source byte for byte. Vendor metadata and the kernel
  recipe use the same revisions as the previous public baseline.
- The 1,167 Android manifest pins match the previous public manifest.
  All 15 platform series (83 commits) replayed with the build preparation
  script against the pinned bases in isolated repositories. Their final
  trees match the snapshot and the replayed author identities and author
  dates match the source. Readiness passed after application and reapplying
  skipped all 15. See [the replay record](history/2026-11-14-replay.json).
- The LHDC staging instructions fetched the 16 pinned headers. The staged
  headers, four locally supplied stock libraries and Soong definition
  passed the 21-file hash check. The seven existing input-tool fixtures
  passed.
- Bluetooth replay preserves CRLF in 16 imported LHDC source files so their
  Git blobs match the snapshot. Git reports existing whitespace in the
  source patches.

## Earlier device evidence

On the 2026-11-13 development image, the user confirmed LHDC V5 software
audio at 48 kHz/24-bit, USB mode switching, fast-charging indication and
stock camera photo/video capture. ISP service registration, thermal sensor
reporting and the CDMA capability declaration were checked on that build.
The final source snapshot also includes later Bluetooth settings and LE Audio
changes whose exact combination has not been flashed.

## Pending

Clean synchronization of all pinned repositories, a fresh full ROM build,
flashing and device acceptance for this exact source combination are pending.
See [KNOWN_ISSUES.md](KNOWN_ISSUES.md) for the 23.2 validation gaps.
