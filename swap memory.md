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

## Swap Management

### Create a Physical Swap Partition

```bash
sudo fdisk /dev/sdb 
# n -> new partition -> primary -> size
# t -> type 82 (Linux swap)
# w -> write changes
sudo mkswap /dev/sdb1
sudo swapon /dev/sdb1
```

### Create a LVM Swap Partition
```
sudo lvcreate -n lv_swap -L 1G /dev/<vg>
sudo mkswap /dev/<vg>/lv_swap
sudo swapon /dev/<vg>/lv_swap
```

### Create a Swap File

```bash
sudo fallocate -l 512M /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```
* **Persistence:** To enable swap automatically at boot, add an entry to `/etc/fstab`.  
  - **Physical or LVM swap partition:**  
    ```text
    UUID=<swap-uuid> none swap defaults 0 0
    ```
  - **Swap file:**  
    ```text
    /swapfile none swap sw 0 0
    ```
*Note:* Swap files must be referenced by their path, not UUID. Swap partitions or LVM volumes can use either device path or UUID.



## 🔹 Quick Cheatsheet

| Task                | Command                                                               |
| ------------------- | --------------------------------------------------------------------- |
| Creating Swap       | `mkswap /dev/sdX` or `mkswap /swapfile`                               |
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

* **defaults 0 0:**

  * `defaults` → standard mount options (`rw, suid, dev, exec, auto, nouser, async`).
  * `0` → do not dump.
  * `0` → do not fsck.

---
## RHCSA Scenario-Based Questions

### Question 1: Physical Swap Partition

**Task:** A physical swap partition `/dev/sdb2` of 1GB exists but is not active. Activate it immediately and make it persistent across reboots.
**Answer:**

```bash
sudo swapon /dev/sdb2
echo '/dev/sdb2 none swap sw 0 0' | sudo tee -a /etc/fstab
swapon --show   # verify active
```

> **Explanation:** Activates the physical partition and ensures persistence by adding it to `/etc/fstab`.

### Question 2: LVM-Based Swap

**Task:** Create a 2GB swap using LVM on Volume Group `vg_data` with logical volume `lv_swap`, activate it, and ensure it persists.
**Answer:**

```bash
sudo lvcreate -L 2G -n lv_swap vg_data
sudo mkswap /dev/vg_data/lv_swap
sudo swapon /dev/vg_data/lv_swap
echo '/dev/vg_data/lv_swap none swap sw 0 0' | sudo tee -a /etc/fstab
swapon --show   # verify active
```

> **Explanation:** Demonstrates LVM swap creation, activation, and persistence. LVM allows flexible resizing in the future.

### Question 3: Swap File

**Task:** System memory is low. Create a 1.5GB swap file in `/home`, activate it, and make it permanent.
**Answer:**

```bash
sudo fallocate -l 1.5G /home/swapfile
sudo chmod 600 /home/swapfile
sudo mkswap /home/swapfile
sudo swapon /home/swapfile
echo '/home/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
swapon --show   # verify active
```

> **Explanation:** Swap file creation, secure permissions, activation, and persistence.

### Question 4: Resize LVM Swap

**Task:** Increase the LVM-based swap logical volume `lv_swap` from 2GB to 3GB and ensure it is active.
**Answer:**

```bash
sudo swapoff /dev/vg_data/lv_swap
sudo lvextend -L +1G /dev/vg_data/lv_swap
sudo mkswap /dev/vg_data/lv_swap
sudo swapon /dev/vg_data/lv_swap
swapon --show   # verify active
```

> **Explanation:** LVM swap resizing requires deactivation, logical volume extension, reformatting, and reactivation.

### Question 5: Replace Swap File with Larger Size

**Task:** Replace existing 512MB swap file `/swapfile` with 2GB swap file without reboot.
**Answer:**

```bash
sudo swapoff /swapfile
sudo rm /swapfile
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
swapon --show   # verify active
```

> **Explanation:** Swap files cannot be resized in place; must deactivate, remove, recreate with new size, and reactivate.

### Question 6: Disable All Swap Except Physical Partition

**Task:** Disable all swaps except physical partition `/dev/sdb2`.
**Answer:**

```bash
sudo swapoff -a
sudo swapon /dev/sdb2
swapon --show   # verify only /dev/sdb2 is active
```

> **Explanation:** Demonstrates selective swap activation; ensures only the physical partition swap is active.

> **Note:** These six scenarios cover **all swap types** (physical partition, LVM-based, swap file) including creation, activation, persistence, resizing, replacement, and selective enabling for RHCSA exam practice.
