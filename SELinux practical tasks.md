# 🔒 SELinux Practical Guide for RHCSA

SELinux (Security-Enhanced Linux) is a **mandatory access control (MAC) system** that enhances the security of Linux systems. Unlike discretionary access control (DAC) where users can change permissions, SELinux enforces strict policies that govern how users, processes, and files interact. It is a kernel-level guardrail that provides **least privilege**, **exploit containment**, and **auditing**.

---

## 1. SELinux Theory and Concepts

### 1.1 SELinux Modes

* **Enforcing:** SELinux policy is enforced; unauthorized access is blocked.
* **Permissive:** SELinux policy is not enforced but violations are logged.
* **Disabled:** SELinux is turned off.

### 1.2 SELinux Components

* **Context (Label):** `user:role:type:level`

  * **User:** SELinux user (e.g., system_u, unconfined_u)
  * **Role:** Defines allowed domains (e.g., system_r, object_r)
  * **Type (Domain):** Most important; controls access (e.g., httpd_t, sshd_t)
  * **Level (MLS/MCS):** Security level (e.g., s0)
* **Policy:** Rules defining allowed access between domains and types.
* **Booleans:** Runtime toggles for policy features (e.g., allowing HTTP to connect to DB).
* **Audit Logs:** `/var/log/audit/audit.log` contains AVC (Access Vector Cache) denials.

### 1.3 File and Process Contexts

* **Files:** Example → `system_u:object_r:httpd_sys_content_t:s0`
* **Processes:** Example → `system_u:system_r:sshd_t:s0`

---

## 2. Practical Use Cases & Real-World Scenarios

### 2.1 Real-World Scenarios

#### Scenario 1: Apache Serving Web Pages

* **Problem:** Apache cannot serve `/srv/mywebsite/index.html`.
* **Fix:**

```bash
semanage fcontext -a -t httpd_sys_content_t "/srv/mywebsite(/.*)?"
restorecon -Rv /srv/mywebsite
```

✅ Apache restricted to safe web dirs.

#### Scenario 2: SSH Home Directory Issue

* **Problem:** SSH fails after moving home dir.
* **Fix:**

```bash
semanage fcontext -a -t user_home_t "/data/home(/.*)?"
restorecon -Rv /data/home
```

✅ Users regain access securely.

#### Scenario 3: Database & Web App Separation

* **Problem:** MySQL cannot access `/opt/mysql`.
* **Fix:**

```bash
semanage fcontext -a -t mysqld_db_t "/opt/mysql(/.*)?"
restorecon -Rv /opt/mysql
```

✅ httpd and mysqld remain isolated.

#### Scenario 4: Kernel + SELinux Runtime

* **Problem:** Web app requires routing + network access.
* **Kernel fix:**

```bash
sysctl -w net.ipv4.ip_forward=1
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.d/99-sysctl.conf
```

* **SELinux fix:**

```bash
setsebool -P httpd_can_network_connect on
```

✅ Both OS & SELinux allow secure networking.

#### Scenario 5: Zero-Day in SSHD

* **Problem:** Attacker gains root via exploit.
* **SELinux:** `sshd_t` cannot modify `/etc/shadow`.
  ✅ Exploit impact reduced.

#### Scenario 6: Multi-Tenant Hosting

* **Problem:** One container compromises another.
* **SELinux:** MLS/MCS policies keep them isolated.
  ✅ Tenant separation enforced.

#### Scenario 7: Protecting Logs

* **Problem:** Malware deletes `/var/log/audit/audit.log`.
* **SELinux:** Only `auditd_t` can write logs.
  ✅ Evidence preserved.

---

## 3. SELinux Cheat-Sheets

### 3.1 File & Process Commands

| Task                    | Command                                      | Description              |
| ----------------------- | -------------------------------------------- | ------------------------ |
| Check file context      | `ls -Z /path`                                | Show labels              |
| Restore default context | `restorecon -Rv /path`                       | Relabel files            |
| Add permanent rule      | `semanage fcontext -a -t type "/path(/.*)?"` | Persistent relabel       |
| Check process context   | `ps -eZ`                                     | Show process domains     |
| Current shell context   | `id -Z`                                      | Show current shell label |

### 3.2 Modes & Booleans

| Task           | Command                       | Description                 |
| -------------- | ----------------------------- | --------------------------- |
| Check mode     | `getenforce` / `sestatus`     | Show mode                   |
| Change mode    | `setenforce 0/1`              | Switch enforcing/permissive |
| List booleans  | `getsebool -a`                | Show all toggles            |
| Toggle boolean | `setsebool -P boolean on/off` | Change persistently         |

### 3.3 Troubleshooting

| Task         | Command                               | Description      |
| ------------ | ------------------------------------- | ---------------- |
| View denials | `ausearch -m avc -ts recent`          | Show AVC denials |
| Analyze logs | `sealert -a /var/log/audit/audit.log` | Suggested fixes  |

---

## 4. RHCSA Tips

* Know difference between `chcon` (temporary) vs `semanage + restorecon` (permanent).
* Always check both **process** and **file** contexts.
* Use **booleans** for runtime flexibility.
* Review audit logs for denials.
* Practice restoring contexts and toggling booleans.

---

# ✅ Key Takeaway

SELinux enforces **mandatory, fine-grained access control** beyond DAC. It:

* Restricts services to least privilege.
* Contains exploits and misconfigurations.
* Protects sensitive files & logs.
* Provides detailed audit visibility.

👉 Think of SELinux as a **kernel-level security guard** protecting your system, even when root or DAC fails.

---

## 5. Kernel Runtime Parameters with `sysctl`

The `sysctl` command lets you **view and change kernel runtime parameters** exposed via `/proc/sys/`.

| Command/Method                              | Purpose                                  | Persistence |
| ------------------------------------------- | ---------------------------------------- | ----------- |
| `sysctl -a`                                 | List all kernel parameters               | N/A         |
| `sysctl -a \| grep vm.swappiness`           | Search specific parameter                | N/A         |
| `sysctl vm.swappiness`                      | View a single parameter                  | N/A         |
| `sudo sysctl -w vm.swappiness=50`           | Temporary change at runtime              | No          |
| Edit `/etc/sysctl.conf` and add `key=value` | Make parameter persistent                | Yes         |
| Create `/etc/sysctl.d/99-custom.conf`       | Add persistent custom configuration      | Yes         |
| `sudo sysctl -p`                            | Reload `/etc/sysctl.conf`                | Yes         |
| `sudo sysctl --system`                      | Reload all configs from `/etc/sysctl.d/` | Yes         |

### Notes

* Runtime changes with `sysctl -w` are **lost after reboot**.
* Use `/etc/sysctl.conf` or `/etc/sysctl.d/` for **persistent** changes.

---

## 6. `chcon` Command (SELinux)

`chcon` changes the SELinux security context of files or directories **temporarily**.

| Command                                      | Purpose                        |
| -------------------------------------------- | ------------------------------ |
| `ls -Z file`                                 | View SELinux context           |
| `chcon -t httpd_sys_content_t index.html`    | Change type of a file          |
| `chcon -R -t httpd_sys_content_t /var/www/`  | Recursively change type        |
| `restorecon -Rv /var/www/`                   | Restore default context        |
| `chcon -u system_u file.txt`                 | Change SELinux user            |
| `chcon -r object_r file.txt`                 | Change SELinux role            |
| `chcon -t samba_share_t file.txt`            | Change SELinux type            |
| `chcon -l s0 file.txt`                       | Change SELinux level           |
| `semanage fcontext -a -t type '/path(/.*)?'` | Define persistent context rule |
| `restorecon -Rv /path`                       | Apply persistent context       |

### Notes

* `chcon` changes are **temporary**.
* Use `semanage fcontext` + `restorecon` for **persistent** changes.

---

## 7. SELinux Boot-Time Settings

SELinux behavior can be controlled at boot via **kernel parameters** or the config file.

| Mode           | Behavior                                                                      | Use Case                          |
| -------------- | ----------------------------------------------------------------------------- | --------------------------------- |
| **Enforcing**  | SELinux policies are **enforced**. Unauthorized access is blocked and logged. | Default/production systems.       |
| **Permissive** | SELinux policies are **not enforced**. Violations are only logged.            | Troubleshooting & policy testing. |
| **Disabled**   | SELinux is completely turned off. No enforcement or logging.                  | Rarely recommended; last resort.  |

### Kernel Boot Parameters (GRUB)

Edit GRUB at boot (press `e` on the kernel line) or modify `/etc/default/grub`:

| Parameter       | Purpose                                  |
| --------------- | ---------------------------------------- |
| `enforcing=0`   | Boot with SELinux in **permissive** mode |
| `selinux=0`     | **Completely disable** SELinux           |
| `autorelabel=1` | Force filesystem **relabeling** at boot  |

Update GRUB config after editing:

```bash
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

### Config File Method

Persistent mode can also be set in `/etc/selinux/config`:

```conf
SELINUX=enforcing   # or permissive/disabled
```

### Notes

* `setenforce` changes are temporary.
* Boot parameters (`selinux=0`, `enforcing=0`) override config.
* `autorelabel=1` is useful when SELinux labels are inconsistent.
