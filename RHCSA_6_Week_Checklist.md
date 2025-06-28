# 🧾 RHCSA 6-Week Study Plan (Detailed Checklist)

## ✅ Week 1: Shell Basics, Users & Permissions
### 🔹 Shell & Navigation
- [ ] Use `cd`, `ls`, `pwd`, `tree` to navigate
- [ ] Use `cat`, `less`, `head`, `tail` to read files
- [ ] Search with `grep`, `find`, `locate`
- [ ] Understand `man`, `--help`, and `info`
- [ ] Understand tab completion, history, and command chaining (`&&`, `;`, `|`)

### 🔹 Users & Groups
- [ ] Add users with `useradd`, set password with `passwd`
- [ ] Modify user with `usermod`, rename/delete with `usermod -l`, `userdel`
- [ ] Set password aging: `chage`, `passwd -x`, `-n`, `-w`, `-i`
- [ ] Add/delete groups: `groupadd`, `groupdel`
- [ ] Add user to group: `usermod -aG`, `gpasswd -a`

### 🔹 File Permissions & Ownership
- [ ] Use `ls -l` to view permissions
- [ ] Change permissions using `chmod` (symbolic & numeric)
- [ ] Change ownership using `chown`, `chgrp`
- [ ] Practice default permissions using `umask`
- [ ] Test SetUID (`chmod u+s`), SetGID (`chmod g+s`), sticky bit (`chmod +t`)

## ✅ Week 2: Disks, Partitions, LVM, Filesystems
### 🔹 Partitioning & Filesystems
- [ ] Use `lsblk`, `blkid`, `df`, `du`, `mount`, `umount`
- [ ] Create partitions with `fdisk`, `parted`
- [ ] Create filesystems: `mkfs.xfs`, `mkfs.ext4`
- [ ] Label FS with `e2label`, `xfs_admin`
- [ ] Explore UUIDs with `blkid` and label-based mounting

### 🔹 Mounting & Persistence
- [ ] Mount manually using `mount`
- [ ] Make persistent using `/etc/fstab`
- [ ] Test mounting at boot and handling failures
- [ ] Use `mount -a` to check fstab syntax

### 🔹 LVM
- [ ] Create PV with `pvcreate`, check with `pvs`
- [ ] Create VG with `vgcreate`, check with `vgs`
- [ ] Create LV with `lvcreate`, check with `lvs`
- [ ] Format and mount LV
- [ ] Extend LV and filesystem: `lvextend`, `xfs_growfs`, `resize2fs`
- [ ] Take snapshot: `lvcreate --snapshot`, mount and test restore

## ✅ Week 3: Software & Boot Process
### 🔹 Package Management
- [ ] Use `dnf`, `yum` to install, remove, update, list packages
- [ ] Use `rpm -ivh`, `-e`, `-qf`, `-ql`, `-qc`, etc.
- [ ] Extract `.rpm` with `rpm2cpio`, `cpio`
- [ ] Troubleshoot dependency issues

### 🔹 Repositories
- [ ] Mount ISO and use it as a local repo
- [ ] Create a local HTTP repo with `createrepo`
- [ ] Add custom repo to `/etc/yum.repos.d/`
- [ ] Use GPG keys to verify repo security

### 🔹 Boot & GRUB2
- [ ] List boot targets with `systemctl list-units --type=target`
- [ ] Switch targets using `systemctl isolate`
- [ ] Modify GRUB2 via `/etc/default/grub` and regenerate with `grub2-mkconfig`
- [ ] Reset root password using GRUB2 rescue mode

## ✅ Week 4: Networking, Firewall & SELinux
### 🔹 Networking
- [ ] Configure IP with `nmcli`, `nmtui`
- [ ] Make persistent configs in `/etc/sysconfig/network-scripts/`
- [ ] Set hostname with `hostnamectl`
- [ ] Configure DNS in `/etc/resolv.conf` or NetworkManager
- [ ] Test with `ping`, `dig`, `getent hosts`, `ss`, `ip a`, `ip r`

### 🔹 Firewalld
- [ ] View zones and interfaces: `firewall-cmd --get-active-zones`
- [ ] Add/remove ports & services: `--add-port=`, `--add-service=`
- [ ] Make rules permanent with `--permanent`
- [ ] Reload rules: `firewall-cmd --reload`
- [ ] Test with `nc`, `telnet`, `curl`

### 🔹 SELinux
- [ ] Check current mode with `getenforce`, set with `setenforce`
- [ ] Modify permanent mode in `/etc/selinux/config`
- [ ] Troubleshoot denials with `audit2why`, `audit2allow`
- [ ] Fix contexts with `restorecon`, `chcon`, `semanage fcontext`

## ✅ Week 5: Scheduled Tasks, Logs & Processes
### 🔹 Scheduled Jobs
- [ ] Use `crontab -e`, `/etc/crontab` for user/system cron jobs
- [ ] Create one-time jobs with `at`, `batch`
- [ ] Check jobs with `atq`, `atrm`

### 🔹 systemd Timers
- [ ] Write a simple `.service` file
- [ ] Write a matching `.timer` file
- [ ] Enable and start timer with `systemctl`
- [ ] Use `systemctl list-timers` to verify

### 🔹 Logging
- [ ] Explore `journalctl` (with `--since`, `-u`, `-p`, etc.)
- [ ] Filter logs using `grep`, `less`, or by time
- [ ] Inspect logs in `/var/log/` (e.g., `messages`, `secure`, `cron`)

### 🔹 Process Management
- [ ] List processes: `ps aux`, `top`, `htop`
- [ ] Kill with `kill`, `killall`
- [ ] Use `nice`, `renice` for priorities
- [ ] Set ulimits with `ulimit` and `/etc/security/limits.conf`

## ✅ Week 6: Containers, Bash & Review
### 🔹 Podman & Containers
- [ ] Run containers: `podman run`, `ps`, `exec`, `logs`
- [ ] Bind mount: `-v /host:/container`
- [ ] Build image: `podman build -t myimage .` with `Containerfile`
- [ ] Inspect images: `podman images`, `podman inspect`

### 🔹 Bash Scripting
- [ ] Use variables, `if`, `for`, `while`, `case`
- [ ] Accept and use command-line args (`$1`, `$2`, etc.)
- [ ] Use `read`, `echo`, and redirections (`>`, `>>`, `2>`)
- [ ] Automate user creation, backups, monitoring tasks

### 🔹 Final Review
- [ ] Revisit all RHCSA objectives (official checklist)
- [ ] Take at least **2 full mock exams** (timed)
- [ ] Practice recovery tasks: LVM restore, root reset, SELinux bug
- [ ] List important config files and their use cases
- [ ] Build a cheat sheet of commonly used commands
