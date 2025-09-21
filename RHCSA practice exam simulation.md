# RHCSA Practice Exam Simulation (RHEL9)

---

## Exam Environment (at Exam Center)

* You will work **inside a VM provided by Red Hat** (not on the host machine).
* The VM runs **RHEL 9.x** with SELinux and firewalld enabled by default.
* Bootloader (GRUB2) is installed, sometimes broken for troubleshooting tasks.
* **Partitioning**:

  * `/boot` → XFS (\~500 MB – 1 GB)
  * `/` and `swap` → on LVM logical volumes
* You may be given a **second disk** (e.g., `/dev/sdb`) for tasks like LVM, Stratis, VDO, or encryption.
* Access is usually as **root** or a provided user with `sudo`.
* No internet access; rely on `man`, `--help`, and local docs.
* Left side of screen: **task list/questions**. Right side: **console tabs to VM(s)**.

---

## Section 1: Boot & Recovery

* [ ] Break GRUB by renaming `/boot/grub2/grub.cfg` → reboot → fix it.
* [ ] Reset the root password **without knowing the old one** (using GRUB rescue).

---

## Section 2: Storage

(Add a second disk `/dev/sdb`, \~5–10 GB before starting)

* [ ] Create a new 1 GB partition on `/dev/sdb` and format it as XFS.
* [ ] Mount it at `/mnt/data` persistently using UUID.
* [ ] Create a new VG `examvg` on `/dev/sdb2`.
* [ ] Create LV `examlv` of 500 MB, format XFS, mount at `/mnt/exam`.
* [ ] Extend `examlv` to 1 GB and resize filesystem.
* [ ] Create a **Stratis pool** called `strpool` and a filesystem `strfs`.
* [ ] Mount it at `/mnt/stratis`.
* [ ] Create a **VDO volume** on `/dev/sdb3`, format, mount at `/mnt/vdo`.

---

## Section 3: User & Group Management

* [ ] Create a user `student` with UID `1200`.
* [ ] Create a group `project` and add `student` to it.
* [ ] Configure `student` to run all commands with `sudo`.
* [ ] Create `/project/data` with group ownership `project`, SGID set.
* [ ] Verify new files in `/project/data` inherit group `project`.
* [ ] Create an ACL: user `student` has read/write on `/project/data/aclfile`.

---

## Section 4: Networking

* [ ] Configure interface `eth0` with static IP `192.168.10.50/24` and gateway `192.168.10.1`.
* [ ] Set hostname to `server1.lab.example.com`.
* [ ] Ensure network settings persist across reboots.
* [ ] Test with `ping 192.168.10.1`.

---

## Section 5: Services

* [ ] Configure **chronyd** for time sync with `pool.ntp.org`.
* [ ] Install and configure **Apache HTTPD**:

  * [ ] Default page should show: `Welcome RHCSA`.
  * [ ] Service enabled at boot.
* [ ] Create a simple **systemd service** called `mytimer` that runs:

  ```
  /usr/bin/date >> /var/log/mytime.log
  ```

  every 2 minutes.

---

## Section 6: SELinux & Firewall

* [ ] Move `/var/www/html/index.html` to `/srv/web/index.html`.
* [ ] Configure SELinux so Apache can still serve it.
* [ ] Open firewall port `80/tcp`.
* [ ] Test with `curl http://localhost`.

---

# ✅ Completion Criteria

* [ ] Every mount survives reboot.
* [ ] Services persist after reboot.
* [ ] Users, groups, and passwords survive reboot.
* [ ] Apache works, SELinux is correct, firewall is correct.
