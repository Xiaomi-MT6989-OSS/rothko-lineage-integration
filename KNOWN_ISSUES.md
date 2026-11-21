# Known issues and validation limits

These reports and validation gaps apply to the 23.2 work leading up to the
2026-11-14 source snapshot. Build and device checks for the exact public
source combination are recorded in [VALIDATION.md](VALIDATION.md).

| Area | Known status |
| --- | --- |
| China Telecom calls and SMS | Community testing reported both directions failing. The November 13 image declares CDMA capability. Calls, data and IMS still need testing with a China Telecom SIM. |
| Bluetooth audio | LHDC V5 at 48 kHz/24-bit worked on the November 13 image. LHDC V3/V4, higher rates, long sessions and the final LE Audio changes still need device checks. |
| Stock camera still-image effects | Watermark/AI watermark and filters appeared in preview/thumbnail but were reported missing from saved still photos. Live-photo video retained the effects. Beauty presets and current image detail need a fresh check on this source baseline. |
| Aperture Ultra HDR | Its HDR capture/display path had unresolved issues; the stock camera is the selected app. Do not infer Aperture HDR support from stock camera results. |
| Factory thermal parity | Original thermal/brightness behavior has not been demonstrated across the full factory policy and workload range. |
| 32-bit applications | This product is 64-bit-only; 32-bit app support is not provided. |

Previous user tests reported working color-mode changes, 60 Hz AOD, screen-off
fingerprint, WPA3 hotspot/client use, GPS outdoor positioning and software-path
Bluetooth media. Those results belong to their tested images. Recheck them on a
new image before treating them as validated for this exact source set.

Separate display HDR from camera Ultra HDR, and separate UI options from actual
hardware behavior. Do not include personal identifiers, carrier numbers, NV
data, calibration, biometric templates or unredacted logs in reports.
