# LineageOS 24.0 validation

## Reference build

The November 19 local `lineage_rothko-cp2a-user` image build exited successfully
and reported VINTF `COMPATIBLE`. It produced
`rothko-lineage-24.0-user-20261119.zip`. The user confirmed on November 21
that it booted and the features they tried worked. The report did not itemize
their release-image tests. LC3 / LE Audio remains open. Review
[KNOWN_ISSUES.md](KNOWN_ISSUES.md) for scenario-level coverage.

## Checks on this public source snapshot

- The build record's 17 repository HEADs and trees match the local release
  sources. The manifest locks 1,242 upstream projects. All 16 platform
  series replayed with `git am` from the pinned bases and reproduced their
  expected trees. The 31 author identities, dates and subjects matched
  their exported messages. The readiness check passed.
- The source-only device tree reproduces the Android 17 IMS binary fixup
  from stock using the pinned tool. The vendor repository includes the
  matching rules and extraction ledger. OEM libraries and Google APKs
  are supplied by the build host from the exact stock firmware.
- Fresh stock replay verified 778 device payloads, 3,061 vendor payloads, four generated vendor metadata files and 581 kernel materials. The LHDC definitions, headers and four stock encoders matched their hashes.
- The input-tool fixtures and public Git history checks passed.

The public-source conversion preserves the recorded platform trees. A fresh
clean sync, complete ROM build and flash of the public source combination
remain pending. See the [recorded revisions and replay results](history/2026-11-19-lineageos-24.0.json).
