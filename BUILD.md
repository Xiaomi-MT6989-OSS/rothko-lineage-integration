# Build and extraction workflow

The supported helper environment is Linux x86-64 with Python 3 and the matching
LineageOS checkout's host tools/JDK21. Provision the ordinary LineageOS build
environment separately. Stock extraction, compiling Android and flashing are
different steps; none of these helpers contacts a phone.

## 1. Clone the four real repositories

Clone the four public repositories beside one another:

```sh
git clone -b lineageos-24.0 https://github.com/Xiaomi-MT6989-OSS/rothko-lineage-integration.git
git clone -b lineageos-24.0 https://github.com/Xiaomi-MT6989-OSS/android_device_xiaomi_rothko.git
git clone -b lineageos-24.0 https://github.com/Xiaomi-MT6989-OSS/proprietary_vendor_xiaomi_rothko.git
git clone -b lineageos-24.0 https://github.com/Xiaomi-MT6989-OSS/rothko-kernel-extraction.git
```

Set `INTEGRATION` to the integration checkout and `SOURCE` to a new Android
source directory. Keep the repository names from README.md, or pass
`--repositories-parent` to the staging helper.

Read `baseline.json` and check out its recorded companion revisions before
continuing. Do not use an unrelated vendor, kernel or device branch.

## 2. Synchronize the pinned Android base

```sh
mkdir -p "$SOURCE"
cd "$SOURCE"
repo init -u "$INTEGRATION" -b lineageos-24.0 -m manifests/pinned.xml --git-lfs
repo sync -j4
python3 "$INTEGRATION/scripts/stage-sources.py" --source-root "$SOURCE"
```

`manifests/pinned.xml` is a complete manifest, **not** a local-manifest overlay.
It uses public fetch URLs and upstream bases for locally patched repositories.
The 1242-project snapshot is pinned; this does not claim that every remote SHA
has been fetched anew during preparation. A missing upstream object must be
reported with its project and SHA, not replaced by an arbitrary newer branch.
Three companion checkouts are intentionally staged separately; no unpublished
maintainer commit is requested from an unrelated upstream remote.

## 3. Apply the platform series

```sh
PATCH_INDEX="$INTEGRATION/patches/index.json"
python3 "$INTEGRATION/scripts/apply-platform-patches.py" --source-root "$SOURCE" --patch-index "$PATCH_INDEX" --check-only
python3 "$INTEGRATION/scripts/apply-platform-patches.py" --source-root "$SOURCE" --patch-index "$PATCH_INDEX"
```

This requires clean repositories at exact bases and verifies final trees. It
never resets local work; an interrupted `git am` is left for inspection. Configure
your Git committer identity first. Original patch authors/messages remain intact.
The Soong series includes host support for external OUT_DIR and optional
Soong memory limits and required Android 17 build integration. Apply the
whole series for this snapshot. First-stage probe patches are excluded and
`system/core` stays at the manifest pin.

For another reviewed integration version, pass `--patch-index /absolute/path/to/index.json`
explicitly. Its mbox files must be beside that index. Prepare the intended topic
branch before applying and retain the applied commits across incremental builds.
Use the same index with `--require-applied` in a build entrypoint to validate the
prepared sources without applying patches. This mode fails if a series is pending,
while `--check-only` accepts an applicable base and reports the pending count.

## 4. Prepare the exact stock inputs

Unpack your own OS3.0.307.0.WNNCNXM fastboot package using trusted Android tools.
The helpers below accept **already unpacked** inputs; archive/image unpacking is
not implemented or validated by these scripts. Preserve bytes and case. The
required layout is:

| Variable | Required contents |
| --- | --- |
| STOCK_DUMP | Partition filesystem roots: system, system_ext, product, vendor, odm, vendor_dlkm, system_dlkm and recovery (the files of the recovery fragment of the vendor ramdisk) |
| STOCK_DUMP/apex | Prepared com.android.vndk.v34 filesystem, including lib64; it is not the compressed .apex archive |
| STOCK_ARTIFACTS | boot/kernel, vendor_boot/dtb, vendor_boot/vendor_ramdisk00-files/lib/modules, kernel-config.txt |
| STOCK_IMAGES | Original images directory containing dtbo.img |

`boot/kernel` is the original compressed kernel returned by Android unpack_bootimg;
retain the DT table wrapper in vendor_boot/dtb. Unpack the first vendor ramdisk
without flattening its module directory. Extract IKCONFIG from that same kernel
as kernel-config.txt; it is evidence, not a kernel build input override. Extract
dynamic partitions before reading filesystem contents; do not feed super.img or
sparse images to the file-copy helpers. Hash locks reject other firmware or
incorrect extraction. No LK/preloader image is used.

If STOCK_DUMP/apex is a symlink outside STOCK_DUMP, pass `--apex-input` explicitly
with its exact prepared target. Other external symlink escapes are rejected.
See device `DEVICE_PREBUILTS.md` for the 778 device-local inputs
and the kernel `extraction-recipe.json` for 581 precise stock
artifacts.

```sh
python3 "$INTEGRATION/scripts/prepare-local-inputs.py" \
  --source-root "$SOURCE" --stock-dump "$STOCK_DUMP" \
  --stock-artifacts "$STOCK_ARTIFACTS" --stock-images "$STOCK_IMAGES" \
  --staging-output "$NEW_PRIVATE_STAGING"
# Add --apex-input "$PREPARED_APEX" only for the documented external APEX layout.
```

Staging must be new and outside source/stock directories. The workflow verifies
778 device payloads, 3061 vendor/ODM payloads
and four generated metadata files, plus 581 kernel artifacts. Conflicting
target files are rejected;
existing byte-identical payloads are retained. All extracted payload paths are
Git-ignored. Do not `git add -f` them. The reviewed patchelf binary from
prebuilts/extract-tools is mandatory for exact device audio transforms.

The extraction contract is stock-dump based. Installed-phone extraction, arbitrary
APK signatures, OTA parsing and a source-built kernel remain separate work.

## 5. Stage the LHDC inputs

The Bluetooth APEX requires four ARM64 encoder libraries from the stock
OS3.0.307.0.WNNCNXM `system/apex/com.android.bt.apex`. Unpack its
`apex_payload.img` with the appropriate Android filesystem tools and set
`STOCK_BT_LIB64` to the resulting `lib64` directory. The four encoder
libraries stay local. Obtain the matching headers at the exact EvoX-LHDC
revision below. The archived Rothko files match these upstream header bytes.

Run the following from a shell with `INTEGRATION`, `SOURCE` and
`STOCK_BT_LIB64` set. It stages in a temporary directory inside
`$SOURCE/external` and installs the result only after all hashes agree.
If `external/liblhdc` is already present, verify its files with the same
`SHA256SUMS` before reusing it.

```sh
(
    set -eu
    test ! -e "$SOURCE/external/liblhdc"
    test ! -L "$SOURCE/external/liblhdc"
    lhdc_tmp=$(mktemp -d "$SOURCE/external/.rothko-lhdc.XXXXXXXX")
    trap 'rm -rf -- "$lhdc_tmp"' EXIT
    mkdir -p "$lhdc_tmp/staged/lib64"
    git init -q "$lhdc_tmp/headers"
    git -C "$lhdc_tmp/headers" fetch --depth=1 \
        https://github.com/EvoX-LHDC/external_liblhdc.git 7add548047836e7ee711babb8cd138751ac69654
    git -C "$lhdc_tmp/headers" archive -o "$lhdc_tmp/headers.tar" FETCH_HEAD inc include
    tar -xf "$lhdc_tmp/headers.tar" -C "$lhdc_tmp/staged"
    cp "$INTEGRATION/inputs/lhdc/Android.bp" "$lhdc_tmp/staged/"
    for codec in liblhdc liblhdcBT_enc liblhdcv5 liblhdcv5BT_enc; do
        cp "$STOCK_BT_LIB64/$codec.so" "$lhdc_tmp/staged/lib64/"
    done
    (cd "$lhdc_tmp/staged" && sha256sum -c "$INTEGRATION/inputs/lhdc/SHA256SUMS")
    mv -T --no-clobber "$lhdc_tmp/staged" "$SOURCE/external/liblhdc"
    test ! -e "$lhdc_tmp/staged"
)
```

## 6. Build a development candidate

```sh
cd "$SOURCE"
export OUT_DIR=/absolute/path/to/new-output
export SOONG_GOMEMLIMIT=24GiB
export NINJA_HIGHMEM_NUM_JOBS=4
source build/envsetup.sh
lunch lineage_rothko-cp2a-userdebug
m -j6 bootimage initbootimage vendorbootimage systemimage systemextimage \
  productimage vendorimage odmimage system_dlkmimage vendor_dlkmimage odm_dlkmimage \
  vbmetasystemimage vbmetavendorimage vbmetaimage check-vintf-all sepolicy_test
```

These host resource values describe the working development host, not device
requirements. Use appropriate resources on another machine. This is the recorded
image-oriented bring-up route; Recovery/OTA distribution, signing and installation
must be validated separately. Do not use it to overwrite a trusted release build.
The original local source combination was used for the 2026-11-19 release build.
A fresh clean build and device regression from these public repositories are pending. A successful extraction or host fixture does not imply that
those steps passed. See VALIDATION.md before proposing a release.
