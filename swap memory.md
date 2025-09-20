# Swap Memory in RHCSA Context

## 🔹 Basics

* **Swap Memory:** Disk space used as an extension of RAM when physical RAM is full.
* **Purpose:** Prevents out-of-memory issues; allows systems to continue running.
* **Types:**

  1. **Swap Partition**
  2. **Swap File**
  3. **LVM-based Swap**
* **Commands:**

  * View swap: `swapon --show` or `free -h`
  * Enable swap: `swapon <device_or_file>`
  * Disable swap: `swapoff <device_or_file>`
* **Persistence:** Add swap entry to `/etc/fstab` to enable on boot.

## 🔹 Swap Partition Management

### Create a Swap Partition

```bash
sudo fdisk /dev/sdb
# n -> new partition -> primary -> size
# t -> type 82 (Linux swap)
# w -> write changes
sudo mkswap /dev/sdb1
sudo swapon /dev/sdb1
```

### Make Persistent

```bash
echo '/dev/sdb1 none swap sw 0 0' | sudo tee -a /etc/fstab
```

## 🔹 Swap File Management

### Create a Swap File

```bash
sudo fallocate -l 512M /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

### Make Persistent

```bash
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

## 🔹 Quick Cheatsheet

| Task                | Command                                                               |
| ------------------- | --------------------------------------------------------------------- |
| Show swap           | `swapon --show` / `free -h`                                           |
| Enable swap         | `swapon /dev/sdX` or `swapon /swapfile`                               |
| Disable swap        | `swapoff /dev/sdX` or `swapoff /swapfile`                             |
| Make swap permanent | Add entry to `/etc/fstab`                                             |
| Change swap size    | For file: recreate file; For partition: use LVM or recreate partition |

## 🔹 Difference: Physical Partition Swap vs LVM Swap vs Swap File

* **Physical Partition Swap:**

  * Created directly on a disk partition.
  * Fixed size once created.
  * Resizing requires deleting/recreating partition.
  * Simple and efficient, but inflexible.

* **LVM-based Swap:**

  * Swap created inside a logical volume.
  * Flexible: can resize using `lvextend` or `lvreduce`.
  * Managed under Volume Groups.
  * Preferred for enterprise setups and RHCSA exam tasks involving resizing.

* **Swap File:**

  * Regular file on an existing filesystem.
  * Easiest to create and remove.
  * Flexible (can recreate with different size).
  * Requires proper permissions (`chmod 600`).
  * May have slightly lower performance compared to partitions, but acceptable in most cases.

## 🔹 Understanding `/etc/fstab` Fields

Each line in `/etc/fstab` has 6 fields:

```
<Device>   <Mount Point>   <Type>   <Options>   <Dump>   <Pass>
```

* **sw 0 0:**

  * `sw` → option for swap type.
  * `0 0` → dump and fsck not required.

* **defaults 0 0:**

  * `defaults` → standard mount options (`rw, suid, dev, exec, auto, nouser, async`).
  * `0` → do not dump.
  * `0` → do not fsck.

## 🔹 Scenario-Based RHCSA Questions and Answers

### Question 1

**Task:** A system has low memory. Add a 1GB swap file and make it permanent.
**Answer:**

```bash
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

> **Explanation:** Swap file of 1GB is created, secured, formatted, activated, and made persistent.

### Question 2

**Task:** There is a swap partition `/dev/sdb2`. Disable it temporarily without reboot.
**Answer:**

```bash
sudo swapoff /dev/sdb2
```

> **Explanation:** Swapoff deactivates swap temporarily. Reboot will reactivate it if in `/etc/fstab`.

### Question 3

**Task:** Verify all swap memory currently in use and the free swap available.
**Answer:**

```bash
swapon --show
free -h
```

> **Explanation:** Shows active swap devices/files and memory usage.

### Question 4

**Task:** Replace an existing 512MB swap file with a new 1GB swap file.
**Answer:**

```bash
sudo swapoff /swapfile
sudo rm /swapfile
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

> **Explanation:** Swap file must be deactivated before removal and recreation.

### Question 5

**Task:** A newly added swap partition is not enabled after reboot. Make it active automatically on boot.
**Answer:**

```bash
echo '/dev/sdb3 none swap sw 0 0' | sudo tee -a /etc/fstab
sudo swapon -a
```

> **Explanation:** Entry in `/etc/fstab` ensures automatic activation; `swapon -a` activates all swap immediately.

### Question 6

**Task:** On a system with LVM, add a new 2GB logical volume and use it as swap.
**Answer:**

```bash
sudo lvcreate -L 2G -n lv_swap vg_name
sudo mkswap /dev/vg_name/lv_swap
sudo swapon /dev/vg_name/lv_swap
echo '/dev/vg_name/lv_swap none swap sw 0 0' | sudo tee -a /etc/fstab
```

> **Explanation:** LVM logical volume created, formatted as swap, activated, and made persistent.

### Question 7

**Task:** Temporarily add a 256MB swap file without reboot.
**Answer:**

```bash
sudo fallocate -l 256M /tempswap
sudo chmod 600 /tempswap
sudo mkswap /tempswap
sudo swapon /tempswap
```

> **Explanation:** No `/etc/fstab` entry means it will be gone after reboot.

### Question 8

**Task:** Increase swap size from 512MB to 2GB using a swap file.
**Answer:**

```bash
sudo swapoff /swapfile
sudo rm /swapfile
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

> **Explanation:** Swap files cannot be resized directly; must be recreated with new size.

### Question 9

**Task:** System admin mistakenly created a swap file with wrong permissions. Fix it.
**Answer:**

```bash
sudo chmod 600 /swapfile
```

> **Explanation:** Permissions must be strict (read/write only for root).
