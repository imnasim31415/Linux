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

  * **User:** SELinux user (e.g., system\_u, unconfined\_u)
  * **Role:** Defines allowed domains (e.g., system\_r, object\_r)
  * **Type (Domain):** Most important; controls access (e.g., httpd\_t, sshd\_t)
  * **Level (MLS/MCS):** Security level (e.g., s0)
* **Policy:** Rules defining allowed access between domains and types.
* **Booleans:** Runtime toggles for policy features (e.g., allowing HTTP to connect to DB).
* **Audit Logs:** `/var/log/audit/audit.log` contains AVC (Access Vector Cache) denials.

### 1.3 File and Process Contexts

* **Files:** Example → `system_u:object_r:httpd_sys_content_t:s0`
* **Processes:** Example → `system_u:system_r:sshd_t:s0`

---

## 2. Practical Use Cases & Real-World Scenarios

### 2.1 Managing Process Contexts

**Task:** Save SELinux label of `sshd` to a file.

```bash
ps -eZ | grep sshd | awk '{print $1}' > /home/bob/sshd
cat /home/bob/sshd
```

**Use Case:** Verify SSHD domain for troubleshooting.

### 2.2 File Context Management

**Scenario:** Apache cannot read `/var/index.html`

* **Temporary fix:**

```bash
sudo chcon -t httpd_sys_content_t /var/index.html
```

* **Permanent fix:**

```bash
sudo semanage fcontext -a -t httpd_sys_content_t "/var/index.html"
sudo restorecon -v /var/index.html
```

### 2.3 Changing Modes

```bash
sudo setenforce 0   # permissive
getenforce
```

Permanent mode is set in `/etc/selinux/config`.

### 2.4 Identifying SELinux Roles

```bash
semanage user -l | awk '/^xguest_u/ {print $3}' > /home/bob/serole
cat /home/bob/serole
```

### 2.5 Real-World Scenarios

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

# Kernel Runtime Parameters with `sysctl`

Linux exposes many kernel runtime parameters via the `/proc/sys/` interface. The `sysctl` command provides a convenient way to view and modify them.


## 1. Viewing Kernel Parameters

- **View all parameters:** `sysctl -a`

* **Filter/search specific parameter:** `sysctl -a | grep vm.swappiness`

* **Check a single parameter:** `sysctl vm.swappiness`

---

## 2. Changing Kernel Parameters (Non-Persistent)

* **Set a value at runtime:** `sudo sysctl -w vm.swappiness=50`


👉 These changes are **immediate** but **lost after reboot**.

---

## 3. Making Changes Persistent

### Method 1: `/etc/sysctl.conf`

* Edit the file: `sudo nano /etc/sysctl.conf`

* Add your parameter(s): `vm.swappiness = 50`

* Reload: `sudo sysctl -p`

### Method 2: `/etc/sysctl.d/`

* Create a custom config file: `sudo nano /etc/sysctl.d/99-custom.conf`

* Add parameters: `vm.swappiness = 50`

* Apply changes: `sudo sysctl --system`

---

## 4. Summary

| Action                   | Command/Method                                | Persistence |
| ------------------------ | --------------------------------------------- | ----------- |
| List all parameters      | `sysctl -a`                                   | N/A         |
| View specific parameter  | `sysctl vm.swappiness`                        | N/A         |
| Temporary change         | `sysctl -w key=value`                         | No          |
| Permanent change         | Add to `/etc/sysctl.conf` or `/etc/sysctl.d/` | Yes         |
| Apply config immediately | `sysctl -p` or `sysctl --system`              | Yes         |

```
```

