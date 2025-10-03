# RHCSA Practice Tasks (SELinux, sysctl, SSH)

## Task 1: SSH Key Authentication

* [ ] **Configure key-based SSH authentication for user `examuser` from your workstation to `server1`. Password login must be disabled for that user.**

<details>
<summary>Answer</summary>

1. Generate SSH key:

   ```bash
   ssh-keygen -t rsa -b 4096
   ```
2. Copy key to server:

   ```bash
   ssh-copy-id examuser@server1
   ```
3. Set permissions:

   ```bash
   chmod 700 ~/.ssh
   chmod 600 ~/.ssh/authorized_keys
   ```
4. Edit `/etc/ssh/sshd_config`:

   ```
   PasswordAuthentication no
   ```
5. Restart SSH:

   ```bash
   sudo systemctl restart sshd
   ```

</details>

---

## Task 2: Kernel Parameter (Temporary vs Permanent)

* [ ] **Enable IP forwarding temporarily, verify it, and make it persistent across reboot.**

<details>
<summary>Answer</summary>

1. Temporary:

   ```bash
   sudo sysctl -w net.ipv4.ip_forward=1
   ```
2. Verify:

   ```bash
   sysctl net.ipv4.ip_forward
   ```
3. Permanent:

   ```bash
   echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
   sudo sysctl -p
   ```

</details>

---

## Task 3: SELinux Contexts

* [ ] **A new directory `/webdata` must serve Apache content. Configure SELinux so that it works correctly.**

<details>
<summary>Answer</summary>

1. Add context rule:

   ```bash
   sudo semanage fcontext -a -t httpd_sys_content_t "/webdata(/.*)?"
   ```
2. Restore context:

   ```bash
   sudo restorecon -Rv /webdata
   ```
3. Verify:

   ```bash
   ls -Z /webdata
   ```

</details>

---

## Task 4: Restore Default File Contexts

* [ ] **A sysadmin mistakenly copied `/var/www/html/index.html` with `cp -a` into `/root/test.html`. It fails under SELinux. Fix it.**

<details>
<summary>Answer</summary>

1. Restore context:

   ```bash
   sudo restorecon -v /root/test.html
   ```
2. Or remove and copy fresh without `-a`.

</details>

---

## Task 5: SELinux Troubleshooting

* [ ] **Apache cannot connect to a remote database on port 3306 due to SELinux. Fix it.**

<details>
<summary>Answer</summary>

1. Check AVC denials:

   ```bash
   sudo ausearch -m avc -ts recent
   ```
2. Enable boolean:

   ```bash
   sudo setsebool -P httpd_can_network_connect on
   ```

</details>

---

## Task 6: SELinux Process Context

* [ ] **List all running processes with their SELinux contexts and filter for httpd.**

<details>
<summary>Answer</summary>

```bash
ps -eZ | grep httpd
```

</details>

---

## Task 7: Firewall + SELinux Apache Port Change

* [ ] **Change Apache’s listening port from 80 to 8080. Ensure it works with SELinux and firewall enabled.**

<details>
<summary>Answer</summary>

1. Edit Apache config:

   ```bash
   sudo vi /etc/httpd/conf/httpd.conf
   # Change: Listen 8080
   ```
2. Update SELinux:

   ```bash
   sudo semanage port -a -t http_port_t -p tcp 8080
   ```
3. Firewall:

   ```bash
   sudo firewall-cmd --permanent --add-port=8080/tcp
   sudo firewall-cmd --reload
   ```
4. Restart Apache:

   ```bash
   sudo systemctl restart httpd
   ```

</details>

---

## Task 8: Create and Apply SELinux Custom Context

* [ ] **Serve content from `/srv/myapp` directory using Apache. Ensure correct SELinux labeling.**

<details>
<summary>Answer</summary>

```bash
sudo semanage fcontext -a -t httpd_sys_content_t "/srv/myapp(/.*)?"
sudo restorecon -Rv /srv/myapp
ls -Z /srv/myapp
```

</details>

---

## Task 9: Boolean for Home Directory Content

* [ ] **Allow Apache to serve user home directories.**

<details>
<summary>Answer</summary>

```bash
sudo setsebool -P httpd_enable_homedirs on
```

</details>

---

## Task 10: Switch System Target

* [ ] **Boot the system into multi-user (non-GUI) mode permanently.**

<details>
<summary>Answer</summary>

```bash
sudo systemctl set-default multi-user.target
```

</details>

---

## Task 11: Temporary Target Change

* [ ] **Switch immediately to rescue target (single-user mode) without reboot.**

<details>
<summary>Answer</summary>

```bash
sudo systemctl isolate rescue.target
```

</details>

---

## Task 12: Network Service SELinux Denial

* [ ] **A custom service `mydaemon` running on TCP port 9999 is blocked by SELinux. Allow it.**

<details>
<summary>Answer</summary>

```bash
sudo semanage port -a -t http_port_t -p tcp 9999
sudo restorecon -Rv /etc/systemd/system/mydaemon.service
```

</details>

---

## Task 13: Modify Kernel Swappiness

* [ ] **Change the swappiness parameter to 10 temporarily and persistently.**

<details>
<summary>Answer</summary>

1. Temporary:

   ```bash
   sudo sysctl -w vm.swappiness=10
   ```
2. Permanent:

   ```bash
   echo "vm.swappiness = 10" | sudo tee -a /etc/sysctl.conf
   sudo sysctl -p
   ```

</details>

---

## Task 14: Find Recent Logs

* [ ] **Find all files under `/var/log` modified in the last 2 days and copy them into `/root/log_backup`.**

<details>
<summary>Answer</summary>

```bash
sudo mkdir -p /root/log_backup
find /var/log -type f -mtime -2 -exec cp {} /root/log_backup/ \;
```

</details>

---

## Task 15: Create Links

* [ ] **Create a hard link and a soft link to `/etc/fstab` in `/root/links/`.**

<details>
<summary>Answer</summary>

```bash
mkdir -p /root/links
ln /etc/fstab /root/links/fstab_hard
ln -s /etc/fstab /root/links/fstab_soft
```

</details>
