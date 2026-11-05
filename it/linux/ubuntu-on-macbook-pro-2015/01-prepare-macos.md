# 01 · Prepare macOS and partition the disk

[← Overview](README.md)

**Goal of this step:** a clean macOS on a small partition, and two empty
placeholder partitions ready for Ubuntu.

## Steps

### 1. Install a clean macOS

Erase the disk and install a clean copy of **macOS Monterey**, the latest
version this MacBook supports. I hardly use macOS, but it's worth keeping
as a reference system. It comes in handy for firmware updates and for
checking whether a problem is the hardware or Linux.

### 2. Partition the disk from macOS

In Disk Utility, select the whole disk and click **Partition**. Split it
into three partitions:

| Partition | Size | Format in Disk Utility | Later used as |
| --- | --- | --- | --- |
| macOS | 76 GB | APFS | macOS |
| boot | 4 GB | any (placeholder) | `/boot`, unencrypted |
| Ubuntu | 420 GB | any (placeholder) | encrypted `/` |

The separate `/boot` partition is needed for encryption: the bootloader
can't read the encrypted root partition, so the kernel and initrd have to
live on a small unencrypted partition. The Ubuntu installer reformats both
placeholder partitions, so their format in Disk Utility doesn't matter.

## Check

Disk Utility shows the three partitions with the sizes above, and macOS
still boots.

## Problems & Fixes

| Problem | Fix |
| --- | --- |
|  |  |

---

[Overview](README.md) · Next: [02 · Install Ubuntu with encryption →](02-install-ubuntu.md)
