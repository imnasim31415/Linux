# SELinux Practical Guide for RHCSA

SELinux (Security-Enhanced Linux) is a **mandatory access control (MAC) system** that enhances the security of Linux systems. Unlike discretionary access control (DAC) where users can change permissions, SELinux enforces policies that govern how users, processes, and files interact.

---

## 1. SELinux Theory and Concepts

### 1.1 SELinux Modes

* **Enforcing:** SELinux policy is enforced; unauthorized access is blocked.
* **Permissive:** SELinux policy is not enforced but violations are logged.
* **Disabled:** SELinux is turned off.

### 1.2 SELinux Components

* **Context:** Label applied to every process and file: `user:role:type:level`

  * **User:** SELinux user (e.g., system\_u, unconfined\_u)
  * **Role:** Defines allowed domains (e.g., system\_r, object\_r)
  * **Type (Domain):** Most important; controls access (e.g., httpd\_t, sshd\_t)
  * **Level (MLS/MCS):** Security level (e.g., s0)
* **Policy:** Rules defining allowed access between domains and types.
* **Booleans:** Runtime toggles for policy features (e.g., allowing HTTP to connect to database).
* **Audit Logs:** `/var/log/audit/audit.log` contains AVC (Access Vector Cache) denials.

### 1.3 SELinux File and Process Contexts

* **Files:** Have `user:role:type:level` labels. Example: `system_u:object_r:httpd_sys_content_t:s0`
* **Processes:** Run in domains defined by SELinux type. Example: `system_u:system_r:sshd_t:s0`

---

## 2. Practical Use Cases and Real-World Scenarios

### 2.1 Managing Process Contexts

**Task:** Save the SELinux label of `sshd` to a file.

```bash
ps -eZ | grep sshd | awk '{print $1}' > /home/bob/sshd
cat /home/bob/sshd
```

**Use Case:** Verify SSHD process domain to troubleshoot access denials.

### 2.2 File Context Management

**Scenario:** Apache cannot read `/var/index.html`

* **Temporary fix:**

```bash
sudo chcon -t httpd_sys_content_t /var/index.html
ls -Z /var/index.html
```

* **Permanent fix:**

```bash
sudo semanage fcontext -a -t httpd_sys_content_t "/var/index.html"
sudo restorecon -v /var/index.html
```

**Explanation:** Ensures Apache can read and serve files without disabling SELinux.

### 2.3 Changing SELinux Modes

**Scenario:** Debugging services with SELinux enforcement.

```bash
sudo setenforce 0   # Temporarily permissive
getenforce
```

**Note:** Change does not persist across reboots. Permanent mode is set in `/etc/selinux/config`.

### 2.4 Identifying SELinux Roles

**Task:** Save roles of `xguest_u` to a file.

```bash
semanage user -l | awk '/^xguest_u/ {print $3}' > /home/bob/serole
cat /home/bob/serole
```

**Use Case:** Identify roles for user sandboxing or restricted access.

### 2.5 Real-World Scenarios with Solutions

#### Scenario 1: Apache Serving Web Pages

* **Problem:** Apache cannot serve `/srv/mywebsite/index.html`
* **Fix:**

```bash
semanage fcontext -a -t httpd_sys_content_t "/srv/mywebsite(/.*)?"
restorecon -Rv /srv/mywebsite
```

#### Scenario 2: SSH Home Directory Issue

* **Problem:** SSH login fails after moving home directory.
* **Fix:**

```bash
semanage fcontext -a -t user_home_t "/data/home(/.*)?"
restorecon -Rv /data/home
```

#### Scenario 3: Database & Web App Separation

* **Problem:** MySQL data files inaccessible.
* **Fix:**

```bash
semanage fcontext -a -t mysqld_db_t "/opt/mysql(/.*)?"
restorecon -Rv /opt/mysql
```

**Outcome:** Least privilege maintained; httpd cannot tamper with database files.

#### Scenario 4: Kernel Runtime Parameters & SELinux

* **Problem:** Server acts as router with web app.
* **Kernel fix:**

```bash
sysctl -w net.ipv4.ip_forward=1
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.d/99-sysctl.conf
```

* **SELinux fix:**

```bash
setsebool -P httpd_can_network_connect on
```

**Explanation:** Kernel manages system behavior; SELinux controls security behavior.

---

## 3. SELinux Cheat-Sheets

### 3.1 File and Process Commands

| Task                    | Command                                      | Description                             |
| ----------------------- | -------------------------------------------- | --------------------------------------- |
| Check file context      | `ls -Z /path`                                | Shows SELinux label                     |
| Restore default context | `restorecon -Rv /path`                       | Relabels according to policy            |
| Add permanent rule      | `semanage fcontext -a -t type "/path(/.*)?"` | Assign permanent type                   |
| Check process context   | `ps -eZ`                                     | Lists all processes with SELinux labels |
| Current shell context   | `id -Z`                                      | Show context of current shell           |

### 3.2 Mode & Boolean Commands

| Task           | Command                       | Description                         |
| -------------- | ----------------------------- | ----------------------------------- |
| Check mode     | `getenforce / sestatus`       | Shows SELinux mode                  |
| Temporary mode | `setenforce 0/1`              | Switch between permissive/enforcing |
| List booleans  | `getsebool -a`                | Show all SELinux booleans           |
| Toggle boolean | `setsebool -P boolean on/off` | Enable/disable persistently         |

### 3.3 Troubleshooting Commands

| Task                | Command                               | Description              |
| ------------------- | ------------------------------------- | ------------------------ |
| View recent denials | `ausearch -m avc -ts recent`          | Shows AVC denials        |
| Analyze denials     | `sealert -a /var/log/audit/audit.log` | Provides fix suggestions |

---

## 4. RHCSA Tips

* Understand temporary vs permanent changes (`chcon` vs `semanage + restorecon`).
* Always check process and file contexts when troubleshooting services.
* Use SELinux booleans to allow legitimate access without weakening security.
* Review `/var/log/audit/audit.log` for policy violations.
* Practice restoring contexts and toggling booleans to solve common RHCSA exam problems.
