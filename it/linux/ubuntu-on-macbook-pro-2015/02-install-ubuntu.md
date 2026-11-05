# 02 · Install Ubuntu with encryption

[← Overview](README.md)

**Goal of this step:** Ubuntu installed with an encrypted root partition,
with the fixes for the original Apple SSD (see
[Known issues](README.md#known-issues)) applied before, during and after
the install.

## Steps

### 1. Boot the live USB

Hold **Option** at startup and pick **EFI Boot**. On my machine it went
straight into the live session, with no GRUB menu, so the SSD fixes below are
applied from the terminal instead of as boot parameters.

### 2. Tame the SSD before installing

Open a terminal **before** starting the installer:

```bash
echo 1024 | sudo tee /sys/block/sda/queue/max_sectors_kb
echo 1 | sudo tee /sys/block/sda/device/queue_depth
```

The first line limits writes to 1 MiB. The second sets the queue depth to 1,
which effectively turns off NCQ. Check that both worked:

```bash
cat /sys/block/sda/queue/max_sectors_kb   # should print 1024
cat /sys/block/sda/device/queue_depth     # should print 1
```

### 3. Install Ubuntu with encryption

Choose **Manual partitioning**. Identify the two placeholder partitions from
[step 01](01-prepare-macos.md) by their size, then:

- **4 GB partition:** ext4, mount point `/boot`, tick **Format**.
  Leave it unencrypted.
- **420 GB partition:** encrypt it (LUKS), then use the encrypted volume as
  ext4 with mount point `/`.
- Leave the macOS partition and the EFI partition alone.

Don't reuse a partition that a failed install left behind.

TODO: add the exact installer options or `cryptsetup` commands used to set
up the encrypted root partition.

### 4. Make the SSD fixes permanent (before rebooting!)

When the installer finishes, **don't click "Restart now" yet**. In the terminal:

```bash
# Limit write size on every boot
echo 'ACTION=="add|change", KERNEL=="sda", ATTR{queue/max_sectors_kb}="1024"' \
  | sudo tee /target/etc/udev/rules.d/60-apple-ssd.rules

# Disable NCQ via kernel parameter
sudo sed -i 's/GRUB_CMDLINE_LINUX_DEFAULT="/&libata.force=noncq /' /target/etc/default/grub
for d in dev proc sys; do sudo mount --bind /$d /target/$d; done
sudo chroot /target update-grub

# Check it's there
grep CMDLINE /target/etc/default/grub
```

The `grep` output should include `libata.force=noncq`. Now you can reboot.

## Check

At startup, enter your encryption passphrase. Then:

```bash
cat /proc/cmdline                          # should contain libata.force=noncq
cat /sys/block/sda/queue/max_sectors_kb    # should print 1024
sudo dmesg | grep -i "host bus error"      # should print nothing
```

If all three look right, this step is done. 🎉

## Problems & Fixes

| Problem | Fix |
| --- | --- |
| Installer fails near the end with `Read-only file system: '/target/...'` | Caused by the original Apple SSD. Apply the SSD fixes before installing (sub-steps 2 and 4) |
| GRUB shows Ubuntu, but selecting it does nothing (`start_image() returned 0x8000000000000001`) | Boot another kernel and reinstall the broken one, or just reinstall Ubuntu |

### GRUB shows Ubuntu, but selecting it does nothing

The kernel or initrd on disk is probably damaged, usually because the install
ran while writes were failing. Try another kernel under
**Advanced options for Ubuntu**. If one boots, check and repair the broken one:

```bash
sudo dpkg -V linux-image-<version>-generic
sudo apt install --reinstall linux-image-<version>-generic
sudo update-initramfs -u -k <version>-generic
sudo update-grub
```

Honestly, if the first install ran before the SSD fixes were in place,
**just reinstall** using the steps above. It takes about 20 minutes and
leaves you with a system you can trust.

---

Previous: [← 01 · Prepare macOS and partition the disk](01-prepare-macos.md) · [Overview](README.md)
