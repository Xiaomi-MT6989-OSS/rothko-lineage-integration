# Validation of the source collaboration candidate

Snapshot date: 2026-11-07. This report describes source preparation and
limited host-side checks. It is not a clean Android build, a ROM release approval
or a new phone acceptance round.

## Completed for this source snapshot

- The three companion repositories were generated from the locked current
  source heads. Their filtered histories preserve 310 device,
  53 vendor-metadata and 1 kernel-recipe commits;
  filtered HEADs contain 274, 5 and 1
  tracked files respectively. Input repositories were not modified.
- The current local source manifest resolved 1167 projects.
  The public manifest uses fetchable upstream bases for local patch repositories;
  the accompanying 13 patch series contain
  74 commits and reproduce their recorded final Git trees.
- The matching stock dump reproduced 3056 selected vendor/ODM
  payloads and four generated build metadata files with exact hashes. The
  reproduction did not compile Android or test extraction from an installed ROM.
- The repository text-history scan found no configured host-path, private-key or
  token patterns within its bounded text scan. It does not scan every binary or
  establish licensing or security clearance.

## Not completed or not claimed

- A clean synchronization of all 1167 projects and a clean
  full-ROM build from the public manifest.
- A phone test of the exact filtered source commits in this candidate. Earlier
  ROM test results remain attached to their actual image baselines.
- Fresh independent reproduction of every device-local and kernel payload from
  a different stock package, installed-phone extraction, OTA or source-built
  kernel/module delivery.
- Permission to redistribute proprietary payloads, complete factory thermal
  parity, resolution of all entries in `KNOWN_ISSUES.md`, or LineageOS official
  device acceptance.

Hosted GitHub CI has not run: the repositories have been prepared locally but
not created or pushed to GitHub. Do not treat successful source filtering,
patch replay or payload reproduction as ROM build or hardware validation.
