# Ubuntu on a MacBook Pro 15" (2015)

- **Date:** 2026-11-04
- **Status:** in progress
- **Tags:** linux, ubuntu, macbook, dual-boot, ssd, encryption, luks

## Goal

Dual-boot an encrypted Ubuntu next to a clean macOS on an old MacBook Pro,
and tame its grumpy original Apple SSD along the way.

> **The original Apple SSD is the troublemaker.** Almost everything tricky
> in this guide comes from the Apple SSD these MacBooks shipped with.
> It misbehaves under Linux unless you rein it in (see
> [Known issues](#known-issues) and [step 02](02-install-ubuntu.md)).

## Setup / Materials

- MacBook Pro 15" (2015) with its **original Apple SSD**
- macOS Monterey (12), the latest macOS this model supports
- Ubuntu 26.04.1 LTS, kernel 7.0
- A bootable Ubuntu USB stick
- A backup of anything you want to keep: the clean macOS install erases the disk
- **Difficulty:** Medium, mostly because of the SSD quirks
- **Time:** About an hour plus the macOS reinstall, or one evening if you find
  out the hard way like I did

## Steps

| # | Step | Status |
| --- | --- | --- |
| 01 | [Prepare macOS and partition the disk](01-prepare-macos.md) | done |
| 02 | [Install Ubuntu with encryption](02-install-ubuntu.md) | in progress |

## Known issues

### The original Apple SSD

The SSD isn't dying, but it is the cause of most problems. The **original
Apple AHCI SSD** in these MacBooks misbehaves under Linux in two ways:

1. **Queued writes (NCQ) fail** with `host bus error` on `WRITE FPDMA QUEUED`.
2. **Large writes (4 MiB) fail**, even with NCQ turned off.

The kernel resets the link, writes get lost, and the filesystem switches
itself to read-only to protect itself. The typical symptom is the installer
failing near the end with `Read-only file system: '/target/...'`. You can
confirm it in a live session with:

```bash
sudo dmesg | grep -iE "host bus error|FPDMA|remount"
```

The fix is to turn off NCQ and limit writes to 1 MiB, both in the live
session and permanently on the installed system. See
[step 02](02-install-ubuntu.md).

## Result

An encrypted Ubuntu dual-boots next to macOS on the original Apple SSD,
with no more `host bus error` messages in `dmesg`.

## Notes / Next time

- **Performance:** turning off NCQ costs some random I/O performance in
  benchmarks. In everyday use you won't notice it.
- **Trying NCQ again:** after a week or so without errors, you can remove
  `libata.force=noncq` from `/etc/default/grub`, run `sudo update-grub`, reboot,
  and check `dmesg` again. Keep the udev rule either way.
- **Replacing the original Apple SSD** with an NVMe drive and an adapter avoids
  this problem entirely, but some of those drives come with their own Linux quirks.

## References

- Trial, error, and a lot of `dmesg`
