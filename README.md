# AMLA Kernel — Lenovo Tab 4 10 (TB-X304L)

AMLA is a custom kernel for the Lenovo Tab 4 10 (TB-X304L). This branch
(`main`) provides the base kernel only, with **no** root integration.

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
- No KernelSU or other root integration is present on this branch.

## Status

- A KernelSU build exists and has been boot-tested separately (see the `ksu`
  branch). That result does not apply to this branch.

## Build requirements

- AArch64 GCC 4.9 (`aarch64-linux-android-4.9`) — the toolchain used for the
  tested build. Other compilers are unverified.
- GNU make, `bc`, and standard kernel host build tools.

## Building

Defconfig: `neternels_tbx304_defconfig`.
Output: `out/arch/arm64/boot/Image.gz-dtb`.

The following is the **exact invocation used to produce the tested image**
(substitute your own toolchain directory):

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

> **Unverified:** flashing and post-boot behavior were not tested for this
> branch. AVB/verified-boot compatibility was not analyzed.

## Known limitations

- Legacy downstream 3.18 tree; bound by the constraints of that base.
- Not portable to unrelated devices or mismatched ramdisks.

## Recovery and testing caveats

- Back up your stock `boot.img` before flashing anything.
- Flashing requires an unlocked bootloader and is at your own risk.
- Static verification does not guarantee the image boots on your device.

## License and attribution

- Released under GPL-2.0; see `COPYING`.
- Upstream Linux, CAF, and all existing copyright/attribution notices are
  preserved.
