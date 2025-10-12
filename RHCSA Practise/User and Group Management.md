# RHCSA Exam Prep: User and Group Management (RHEL 9)

### ✅ Task Checklist

#### 🧑 User Management

* [ ] 1. Create a user `analyst` with a non-default home directory `/data/users/analyst` and default shell `/bin/bash`.
* [ ] 2. Create a user `backup` with **no login shell**, and ensure it cannot access the system interactively.
* [ ] 3. Rename the user `analyst` to `data_analyst` while preserving the UID, GID, and home directory.
* [ ] 4. Lock the `backup` account and later unlock it.
* [ ] 5. Set a password for `data_analyst` and force the user to change it at next login.

<details>
<summary>💡 Answers</summary>

```bash
# Create user with custom home
sudo useradd -m -d /data/users/analyst -s /bin/bash analyst

# Create system user with no shell
sudo useradd -r -s /sbin/nologin backup

# Rename user (preserving home)
sudo usermod -l data_analyst -d /data/users/analyst -m analyst

# Lock and unlock account
sudo passwd -l backup
sudo passwd -u backup

# Set and expire password
echo 'RedHat123' | sudo passwd --stdin data_analyst
sudo chage -d 0 data_analyst
```

</details>

---

#### 🔐 Password and Aging

* [ ] 6. Set the maximum password age for `data_analyst` to **45 days**.
* [ ] 7. Set the minimum password age to **7 days**.
* [ ] 8. Set the warning period to **10 days** before expiration.
* [ ] 9. Disable password aging for the `backup` account entirely.

<details>
<summary>💡 Answers</summary>

```bash
sudo chage -M 45 data_analyst
sudo chage -m 7 data_analyst
sudo chage -W 10 data_analyst
sudo chage -I -1 backup
sudo chage -l data_analyst
```

</details>

---

#### 👥 Group Management

* [ ] 10. Create three groups: `finance`, `support`, and `infra`.
* [ ] 11. Add `data_analyst` to `finance` and `support` as **secondary groups**.
* [ ] 12. Set `finance` as the **primary group** for `data_analyst`.
* [ ] 13. Remove `data_analyst` from `support` without affecting the other memberships.
* [ ] 14. Delete the `infra` group only if no members are present.

<details>
<summary>💡 Answers</summary>

```bash
sudo groupadd finance
sudo groupadd support
sudo groupadd infra

sudo usermod -aG finance,support data_analyst
sudo usermod -g finance data_analyst
sudo gpasswd -d data_analyst support
sudo groupdel infra
```

</details>

---

#### ⚙️ Sudo and Superuser Access

* [ ] 15. Grant `data_analyst` passwordless sudo access to run any command.
* [ ] 16. Restrict `backup` to only be able to run `/usr/bin/rsync` as root.
* [ ] 17. Create a new sudo group `opsadmin` and add `data_analyst` to it. Configure this group for full sudo privileges.

<details>
<summary>💡 Answers</summary>

```bash
# Passwordless sudo for data_analyst
echo 'data_analyst ALL=(ALL) NOPASSWD:ALL' | sudo tee /etc/sudoers.d/data_analyst

# Restrict backup to rsync only
echo 'backup ALL=(root) /usr/bin/rsync' | sudo tee /etc/sudoers.d/backup

# Create sudo group and configure
groupadd opsadmin
usermod -aG opsadmin data_analyst
echo '%opsadmin ALL=(ALL) ALL' | sudo tee /etc/sudoers.d/opsadmin
```

</details>

---

#### 🧠 Bonus Challenge (Mixed Scenario)

* [ ] 18. Create a new user `tempuser` who:

  * Belongs to the `support` and `finance` groups.
  * Has a home directory in `/data/temp`.
  * Must change password every 15 days.
  * Can use `sudo` only to restart the `httpd` service.

<details>
<summary>💡 Answers</summary>

```bash
sudo useradd -m -d /data/temp -G support,finance tempuser
echo 'TempPass1!' | sudo passwd --stdin tempuser
sudo chage -M 15 tempuser

echo 'tempuser ALL=(ALL) /bin/systemctl restart httpd' | sudo tee /etc/sudoers.d/tempuser
```

</details>

---

### 🏁 Final Verification

Use these commands to double-check your work:

```bash
id data_analyst
grep data_analyst /etc/passwd
sudo chage -l data_analyst
sudo cat /etc/sudoers.d/*
```
