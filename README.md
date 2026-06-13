# wrc-x3200gst3

OpenWrt v25.12.4 custom firmware builder for **ELECOM WRC-X3200GST3** with expanded UBI partition (19 MB → 109 MB).

## Features

- **Expanded overlay storage**: merges unused `tm_pattern`, `tm_key`, `user_data`, `reserved` partitions into `ubi`, giving ~100 MB for `rootfs_data`
- **Auto-resize on first boot**: `resize-rootfs_data` init script (START=09) expands the UBI volume before `mount_root` runs
- **Full-featured wpad-mbedtls**: no basic `wpad-basic-mbedtls` fallback

## Build

```bash
# Trigger manually
gh workflow run build.yml

# Or push to main – scheduled every Friday 22:00 UTC
```

The built artifact (`openwrt-*-sysupgrade.itb`) is uploaded to GitHub Actions.

## Install

```bash
scp openwrt-*-sysupgrade.itb root@192.168.1.1:/tmp/
ssh root@192.168.1.1 'sysupgrade -F /tmp/sysupgrade.itb'
```

The `-F` flag bypasses firmware compatibility checks. This is required because the NAND partition layout differs from the stock OpenWrt layout.

After reboot, `resize-rootfs_data` automatically expands the overlay. Verify with:

```bash
df -h /overlay
```

## Upgrades

To upgrade to a later build, simply build a new image with the same config and flash via `sysupgrade -F`. The `resize-rootfs_data` script skips running if already resized (`/etc/.resize-rootfs_data-done`).

## Credits

Based on https://qiita.com/Bacalhau/items/6ce9c30da2b17f64cbe3
