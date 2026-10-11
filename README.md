# AMLA Kernel — Lenovo Tab 4 10 (TB-X304L) — KernelSU

AMLA is a custom kernel for the Lenovo Tab 4 10 (TB-X304L). This branch (`ksu`)
adds a legacy KernelSU integration on top of the base kernel.

## Device compatibility

| Item | Value |
| --- | --- |
| Device | Lenovo Tab 4 10 **TB-X304L** |
| SoC | Qualcomm MSM8917 |
| Architecture | ARM64 (aarch64) |
| Android | 10 |
| Kernel base | Linux 3.18.140 (Qualcomm/CAF downstream) |
| Device tree | `tbx304-msm8917-pmi8937-qrd-sku5` |

- Other TB-X304 variants (for example F/X) are **untested** and not claimed to
  be supported.

## KernelSU integration

- **Tested KernelSU version: 11872** (upstream v0.9.5-era), ported for the
  Linux 3.18 base used here.
- **Root access confirmed** on a Lenovo TB-X304L running an Android 10 ROM.
- KernelSU sources are vendored in-tree (not a Git submodule).
  `CONFIG_OVERLAY_FS=y` selects `CONFIG_KSU` (Kconfig default `y`); because the
  tree is not a submodule, the kernel reports the pinned version
  (`KSU_VERSION := 11872`). A command-line `KSU_VERSION=...` still overrides it.
- This is a legacy port: use a KernelSU Manager from the same era. Newer
  KernelSU releases are unverified against this integration.

## Tested vs untested images

- **Tested:** the KernelSU image built from this branch, reporting version
  `11872`; root access confirmed on the TB-X304L configuration above.
- **Untested:** builds with KernelSU disabled (including the `main` branch).
  These are provided for development only and have **not** been confirmed to
  boot.

## Build requirements

- AArch64 GCC 4.9 (`aarch64-linux-android-4.9`) — the toolchain used for the
  tested build. Other compilers are unverified.
- GNU make, `bc`, and standard kernel host build tools.

## Building

Defconfig: `neternels_tbx304_defconfig`.
Output: `out/arch/arm64/boot/Image.gz-dtb`.

The following is the **exact invocation used to produce the tested image**
(substitute your own toolchain directory). Confirm `CONFIG_OVERLAY_FS=y` and
`CONFIG_KSU=y` are present in `out/.config` before building.

```sh
TC=/path/to/aarch64-linux-android-4.9/bin

make O=out ARCH=arm64 \
  CROSS_COMPILE="$TC/real-aarch64-linux-android-" \
  AS="$TC/aarch64-linux-android-as" LD="$TC/aarch64-linux-android-ld" \
  AR="$TC/aarch64-linux-android-ar" NM="$TC/aarch64-linux-android-nm" \
  STRIP="$TC/aarch64-linux-android-strip" \
  OBJCOPY="$TC/aarch64-linux-android-objcopy" OBJDUMP="$TC/aarch64-linux-android-objdump" \
  neternels_tbx304_defconfig

make O=out ARCH=arm64 \
  HOSTCFLAGS="-Wall -Wmissing-prototypes -Wstrict-prototypes -O2 -fomit-frame-pointer -std=gnu89 -fcommon" \
  CROSS_COMPILE="$TC/real-aarch64-linux-android-" \
  AS="$TC/aarch64-linux-android-as" LD="$TC/aarch64-linux-android-ld" \
  AR="$TC/aarch64-linux-android-ar" NM="$TC/aarch64-linux-android-nm" \
  STRIP="$TC/aarch64-linux-android-strip" \
  OBJCOPY="$TC/aarch64-linux-android-objcopy" OBJDUMP="$TC/aarch64-linux-android-objdump" \
  -j"$(nproc)"
```

> **Unverified / environment-dependent:** The explicit
> `AS`/`LD`/`AR`/`NM`/`STRIP`/`OBJCOPY`/`OBJDUMP` overrides and the
> `HOSTCFLAGS="... -fcommon"` flag were required in the tested environment for
> this 3.18 tree. A plain `CROSS_COMPILE` setting may work on other hosts, but
> this has not been verified.

## Repacking a boot image

- Extract the boot parameters from the boot image of **your own ROM**; do not
  guess them.
- Layout verified against the tested TB-X304L Android 10 ROM:
  - header version `0`, page size `2048`
  - kernel load `0x80008000`, ramdisk load `0x81000000`, tags load `0x80000100`
- Keep the original ramdisk byte-identical and replace only the kernel payload.

> **Unverified:** AVB/verified-boot compatibility was not analyzed. Root
> confirmation is specific to the tested TB-X304L configuration and ROM.

## Known limitations

- Legacy downstream 3.18 tree; the KernelSU integration is an older base.
- Not portable to unrelated devices or mismatched ramdisks.

## Recovery and testing caveats

- Back up your stock `boot.img` before flashing anything.
- Flashing requires an unlocked bootloader and is at your own risk.
- "Root confirmed" reflects the tested device/ROM only.

## License and attribution

- Kernel released under GPL-2.0; see `COPYING`.
- KernelSU kernel module sources in `KernelSU/kernel/LICENSE` are GPL-2.0.
- Upstream Linux, CAF, KernelSU, and all existing copyright/attribution notices
  are preserved.
