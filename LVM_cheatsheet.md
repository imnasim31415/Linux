## LVM Tech
| Term                     | Description                                |
| ------------------------ | ------------------------------------------ |
| **PV (Physical Volume)** | A physical disk or partition used in LVM   |
| **VG (Volume Group)**    | Pool of storage combining one or more PVs  |
| **LV (Logical Volume)**  | "Virtual partitions" created from VGs      |
| **PE (Physical Extent)** | Smallest chunk of data in VG (default 4MB) |

## LVM General Workflow

| Step | Description                   | Command Example                             |
| ---- | ----------------------------- | ------------------------------------------- |
| 1    | Create a disk or partition    | `lsblk`<br>`sudo cfdisk /dev/sdb`           |
| 2    | Create a Physical Volume (PV) | `sudo pvcreate /dev/sdb1`                   |
| 3    | Create a Volume Group (VG)    | `sudo vgcreate my_vg /dev/sdb1`             |
| 4    | Create a Logical Volume (LV)  | `sudo lvcreate -n my_lv -L 500M my_vg`      |
| 5    | Create a Filesystem           | `sudo mkfs.xfs /dev/my_vg/my_lv`            |
| 6    | Mount the Filesystem          | `sudo mount /dev/my_vg/my_lv /mnt/my_mount` |

---

## PV → VG → LV Lifecycle

* **PV (Physical Volume)**

  * Create: `pvcreate /dev/sdX1`
  * Uses an **existing block device** (`/dev/sdX1`). `pvcreate` writes LVM metadata to the device. it does **not** create a new `/dev/*` file.
  * ✅ Shows up in `/dev` only because the disk/partition already existed.

* **VG (Volume Group)**

  * Create: `vgcreate my_vg /dev/sdX1`
  * A **metadata container** that aggregates PVs into a pool.
  * ❌ **Does not** create a block device under `/dev` (VG is not a device itself).

* **LV (Logical Volume)**

  * Create: `lvcreate -n my_lv -L 500M my_vg`
  * **Creates real virtual block devices** used for filesystems and mounting.
  * Paths created:

    * `/dev/my_vg/my_lv` → **symlink** (convenience path)
    * `/dev/mapper/my_vg-my_lv` → **actual device node** created by device-mapper

> May use **either** `/dev/my_vg/my_lv` or `/dev/mapper/my_vg-my_lv` for `mkfs`, `mount`, `fsck`, etc.
```bash
mount /dev/my_vg/my_lv /mnt/docs
mount /dev/mapper/my_vg-my_lv /mnt/docs
```


---
## /etc/fstab — Persistent Mounts

* **Purpose:** Automatically mount filesystems at boot.
* **Basic syntax:**

```text
<file system> <mount point> <type> <options> <dump> <pass>
```

| Field           | Description                                            |
| --------------- | ------------------------------------------------------ |
| `<file system>` | Device path, LV, or UUID (recommended)                 |
| `<mount point>` | Directory to mount the filesystem                      |
| `<type>`        | Filesystem type (e.g., ext4, xfs, btrfs)               |
| `<options>`     | Mount options (e.g., defaults, noatime, ro, rw, user)  |
| `<dump>`        | Backup utility flag (0 = skip, 1 = backup)             |
| `<pass>`        | fsck order at boot (0 = skip, 1 = root fs, 2 = others) |

* **Recommended:** Use UUID for persistent mounts, because device names can change:

```bash
blkid /dev/vg_storage/lv_docs
# Example output: /dev/vg_storage/lv_docs: UUID="abcd-1234" TYPE="ext4"
```

```text
UUID=abcd-1234 /mnt/docs ext4 defaults 0 0
```

* **Options examples:**

  * `defaults` → standard read/write mount with typical options
  * `ro` → read-only mount
  * `noatime` → do not update access times, improves performance
  * `user` → allow non-root user to mount
  * `rw,relatime` → read/write with relative atime updates

* **Example with LVM:**

```text
# Using LV UUID for persistent mount
UUID=abcd-1234 /mnt/docs ext4 defaults 0 0
```

* **Network filesystems:**

```text
# NFS example
server:/export/data /mnt/data nfs defaults,_netdev 0 0
```

* Ensures the filesystem mounts correctly even if `/dev/vg_storage/lv_docs` is not ready at boot.
* LVM volumes are normally activated automatically at boot with systemd (`lvm2-monitor.service`).
* Troubleshooting tip: Use `mount -a` after editing `/etc/fstab` to check for errors before rebooting.
---



## Useful Commands

| Command                                 | Purpose                                               |
| --------------------------------------- | ----------------------------------------------------- |
| `lsblk`                                 | List block devices and mount points                   |
| `pvs`, `vgs`, `lvs`                     | Show Physical Volumes, Volume Groups, Logical Volumes |
| `pvcreate /dev/sdX`                     | Create a physical volume                              |
| `vgcreate <vg_name> /dev/sdX1`          | Create a volume group                                 |
| `lvcreate -n <lv_name> -L <size> <vg>`  | Create a logical volume                               |
| `mkfs.xfs /dev/<vg>/<lv>`               | Format logical volume with XFS filesystem             |
| `mount /dev/<vg>/<lv> /mount/point`     | Mount the LV to a directory                           |
| `umount /mount/point`                   | Unmount a mounted filesystem                          |
| `blkid`                                 | Display UUIDs and filesystem types of devices         |
| `df -h`                                 | Show disk usage of mounted filesystems                |
| `du -sh /path`                          | Show space used by a directory                        |
| `lvextend -L +<size> /dev/<vg>/<lv>`    | Extend a logical volume                               |
| `lvextend -r -L +<size> /dev/<vg>/<lv>` | Extend LV and resize filesystem in one step           |
| `lvreduce -L <size> /dev/<vg>/<lv>`     | Reduce the size of a logical volume                   |
| `lvremove /dev/<vg>/<lv>`               | Delete a logical volume                               |
| `vgextend <vg> /dev/sdX`                | Add a physical volume to an existing volume group     |
| `vgreduce <vg> /dev/sdX`                | Remove a physical volume from a volume group          |
| `vgremove <vg>`                         | Delete a volume group                                 |
| `pvremove /dev/sdX`                     | Delete a physical volume label                        |
| `lvdisplay`, `vgdisplay`, `pvdisplay`   | Detailed information about LVM components             |
| `file -s /dev/<vg>/<lv>`                | Check if a device has a filesystem                    |
| `xfs_growfs /mount/point`               | Resize XFS filesystem after LV expansion              |
| `findmnt`                               | Show where devices are mounted                        |
| `ls -l /dev/mapper/`                    | View symlinks to LVM device-mapper files              |

### Scenario:

You added a new 1GB disk (`/dev/sdb`) to your system. You want to use it via LVM and mount it at `/mnt/data`.

```bash
sudo pvcreate /dev/sdb
sudo vgcreate data_vg /dev/sdb
sudo lvcreate -n data_lv -L 900M data_vg
sudo mkfs.xfs /dev/data_vg/data_lv
sudo mkdir /mnt/data
sudo mount /dev/data_vg/data_lv /mnt/data
echo '/dev/data_vg/data_lv /mnt/data xfs defaults 0 0' | sudo tee -a /etc/fstab
```

### Scenario: Extending Root Partition

**Problem:** Root partition full; free space exists in VG `rhel`.

```bash
# Check LV and filesystem sizes
lsblk /
df -h /

# Extend LV by 1G
sudo lvextend -L +1G /dev/rhel/root

# Check LV size (lsblk shows updated LV)
lsblk /

# Resize filesystem (XFS) to use new LV space
sudo xfs_growfs /

# Check filesystem size
df -h /
```

**Notes:**

* `lsblk` shows **LV size** (block device).
* `df -h` shows **filesystem size** and usage.
* `lvextend` enlarges the LV, `xfs_growfs` resizes the filesystem.
* Filesystem must be resized after LV extend to use additional space.


### Scenario:

You ran out of space on `/home`. You added a new disk (`/dev/sdc`) and want to extend the current logical volume.

```bash
sudo pvcreate /dev/sdc
sudo vgextend rhel /dev/sdc
sudo lvextend -L +2G /dev/rhel/root
sudo xfs_growfs /
```

### Scenario: Create Separate Mount Point for Logs

You want `/var/log` to be on its own LV to avoid filling up /.

```bash
sudo lvcreate -n lv_log -L 1G rhel
sudo mkfs.xfs /dev/rhel/lv_log
sudo mkdir /mnt/log
sudo mount /dev/rhel/lv_log /mnt/log
sudo cp -a /var/log/* /mnt/log/
sudo umount /mnt/log
sudo mount /dev/rhel/lv_log /var/log
```
