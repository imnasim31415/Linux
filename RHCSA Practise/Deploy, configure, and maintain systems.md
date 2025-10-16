# RHCSA Practice — Deploy, Configure, and Maintain Systems

---

## 🕒 Schedule Tasks Using `at` and `cron`

* [ ] **Task 1:** Schedule a one-time job using `at` to display the message “Backup Completed” at 11:30 PM today.

  <details>
  <summary>Show Answer</summary>

  ```bash
  at 23:30
  at> echo "Backup Completed" >> /root/backup.log
  at> <Ctrl+D>
  ```

  Verify with:

  ```bash
  atq
  ```

  </details>

* [ ] **Task 2:** Create a cron job to back up `/etc` daily at 3 AM to `/backup/etc.tar.gz`.

  <details>
  <summary>Show Answer</summary>

  ```bash
  mkdir -p /backup
  crontab -e
  # Add:
  0 3 * * * tar -czf /backup/etc.tar.gz /etc
  ```

  </details>

* [ ] **Task 3:** Schedule a recurring cron job for user `devops` to check system uptime every hour.

  <details>
  <summary>Show Answer</summary>

  ```bash
  su - devops -c 'crontab -e'
  # Add:
  0 * * * * uptime >> /home/devops/uptime.log
  ```

  </details>

* [ ] **Task 4:** Remove all pending `at` jobs.

  <details>
  <summary>Show Answer</summary>

  ```bash
  atrm $(atq | awk '{print $1}')
  ```

  </details>

* [ ] **Task 5:** Create a cron job that runs every Monday at 8:00 AM to clean `/tmp`.

  <details>
  <summary>Show Answer</summary>

  ```bash
  crontab -e
  # Add:
  0 8 * * 1 rm -rf /tmp/*
  ```

  </details>

---

## ⚙️ Start and Stop Services / Configure Autostart

* [ ] **Task 6:** Start and enable the `sshd` service permanently.

  <details>
  <summary>Show Answer</summary>

  ```bash
  systemctl start sshd
  systemctl enable sshd
  ```

  </details>

* [ ] **Task 7:** Stop and disable the `cups` service.

  <details>
  <summary>Show Answer</summary>

  ```bash
  systemctl stop cups
  systemctl disable cups
  ```

  </details>

* [ ] **Task 8:** Configure `httpd` service to start automatically at boot.

  <details>
  <summary>Show Answer</summary>

  ```bash
  systemctl enable httpd
  ```

  </details>

* [ ] **Task 9:** Mask a service (e.g. `bluetooth`) to prevent it from starting.

  <details>
  <summary>Show Answer</summary>

  ```bash
  systemctl mask bluetooth
  ```

  </details>

* [ ] **Task 10:** Verify which services are enabled at boot.

  <details>
  <summary>Show Answer</summary>

  ```bash
  systemctl list-unit-files --type=service | grep enabled
  ```

  </details>

---

## 🧭 Configure Boot Target

* [ ] **Task 11:** Set the system to boot into multi-user target by default.

  <details>
  <summary>Show Answer</summary>

  ```bash
  systemctl set-default multi-user.target
  ```

  </details>

* [ ] **Task 12:** Change the system to graphical target temporarily.

  <details>
  <summary>Show Answer</summary>

  ```bash
  systemctl isolate graphical.target
  ```

  </details>

* [ ] **Task 13:** List all available system targets.

  <details>
  <summary>Show Answer</summary>

  ```bash
  systemctl list-units --type=target
  ```

  </details>

* [ ] **Task 14:** Create a custom target named `maintenance.target` based on `multi-user.target`.

  <details>
  <summary>Show Answer</summary>

  ```bash
  cp /usr/lib/systemd/system/multi-user.target /etc/systemd/system/maintenance.target
  systemctl daemon-reload
  ```

  </details>

---

## ⏰ Configure Time Service Clients

* [ ] **Task 15:** Configure NTP synchronization using `chronyd`.

  <details>
  <summary>Show Answer</summary>

  ```bash
  systemctl enable --now chronyd
  timedatectl set-ntp true
  ```

  </details>

* [ ] **Task 16:** Set timezone to Asia/Dhaka.

  <details>
  <summary>Show Answer</summary>

  ```bash
  timedatectl set-timezone Asia/Dhaka
  ```

  </details>

* [ ] **Task 17:** Configure `chrony` to use a custom NTP server `time.example.com`.

  <details>
  <summary>Show Answer</summary>

  ```bash
  echo 'server time.example.com iburst' >> /etc/chrony.conf
  systemctl restart chronyd
  ```

  </details>

* [ ] **Task 18:** Display current NTP synchronization status.

  <details>
  <summary>Show Answer</summary>

  ```bash
  chronyc tracking
  ```

  </details>

---

## 📦 Install and Update Software Packages

* [ ] **Task 19:** Install `vim` from the Red Hat repository.

  <details>
  <summary>Show Answer</summary>

  ```bash
  dnf install vim -y
  ```

  </details>

* [ ] **Task 20:** Install a package from a local RPM file.

  <details>
  <summary>Show Answer</summary>

  ```bash
  dnf localinstall /tmp/httpd.rpm -y
  ```

  </details>

* [ ] **Task 21:** Configure a local repository located at `/mnt/repo`.

  <details>
  <summary>Show Answer</summary>

  ```bash
  cat > /etc/yum.repos.d/local.repo <<EOF
  [localrepo]
  name=Local Repository
  baseurl=file:///mnt/repo
  enabled=1
  gpgcheck=0
  EOF
  ```

  </details>

* [ ] **Task 22:** Update all installed packages on the system.

  <details>
  <summary>Show Answer</summary>

  ```bash
  dnf update -y
  ```

  </details>

* [ ] **Task 23:** List all available package groups.

  <details>
  <summary>Show Answer</summary>

  ```bash
  dnf group list
  ```

  </details>

* [ ] **Task 24:** Install the “Server with GUI” package group.

  <details>
  <summary>Show Answer</summary>

  ```bash
  dnf groupinstall "Server with GUI" -y
  ```

  </details>

---

## 🧰 Modify the System Bootloader

* [ ] **Task 25:** Change the GRUB timeout to 10 seconds.

  <details>
  <summary>Show Answer</summary>

  ```bash
  vi /etc/default/grub
  GRUB_TIMEOUT=10
  grub2-mkconfig -o /boot/grub2/grub.cfg
  ```

  </details>

* [ ] **Task 26:** Add kernel parameter `net.ifnames=0` permanently.

  <details>
  <summary>Show Answer</summary>

  ```bash
  vi /etc/default/grub
  GRUB_CMDLINE_LINUX="... net.ifnames=0"
  grub2-mkconfig -o /boot/grub2/grub.cfg
  reboot
  ```

  </details>

* [ ] **Task 27:** Set a default kernel to boot from using `grubby`.

  <details>
  <summary>Show Answer</summary>

  ```bash
  grubby --set-default /boot/vmlinuz-<kernel-version>
  ```

  </details>

* [ ] **Task 28:** View the current default kernel.

  <details>
  <summary>Show Answer</summary>

  ```bash
  grubby --default-kernel
  ```

  </details>

---

## ✅ Verification Commands Summary

| Task Type     | Verification Command                   |
| ------------- | -------------------------------------- |
| `cron` jobs   | `crontab -l`, `systemctl status crond` |
| `at` jobs     | `atq`, `at -c <jobid>`                 |
| `services`    | `systemctl list-units --type=service`  |
| `boot target` | `systemctl get-default`                |
| `time sync`   | `timedatectl`, `chronyc sources`       |
| `repo`        | `dnf repolist`                         |
| `bootloader`  | `grubby --default-kernel`              |

---

🧩 **Tip:** RHCSA questions test *practical outcomes* — always verify configuration persistence after reboot.
