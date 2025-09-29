# RHCSA – Tough Practice Q&A


* [ ] **1. Create a directory `/secure/projects` with the following:**

  * Owner: `devuser`, Group: `devteam`
  * Permissions: Owner full, Group read+execute, Others no access.
  * Inside it, create `confidential.txt` and set **SUID** on it.

<details><summary>✅ Answer</summary>

```bash
mkdir -p /secure/projects
chown devuser:devteam /secure/projects
chmod 750 /secure/projects
cd /secure/projects
touch confidential.txt
chmod 4740 confidential.txt
```

</details>

* [ ] **2. Create a shared directory `/data/shared` where:**

  * All files created inside should inherit the group of the directory.
  * Prevent users from deleting files they don’t own.

<details><summary>✅ Answer</summary>

```bash
mkdir -p /data/shared
chown :devteam /data/shared
chmod 2775 /data/shared   # SGID
chmod +t /data/shared     # Sticky bit
```

</details>

* [ ] **3. Find all files owned by user `john` under `/var` and save the list in `/root/john_files.txt`.**

<details><summary>✅ Answer</summary>

```bash
find /var -user john > /root/john_files.txt
```

</details>

* [ ] **4. Compare two files `config.old` and `config.new` line by line.**

<details><summary>✅ Answer</summary>

```bash
diff config.old config.new
```

</details>

* [ ] **5. Display lines 10 to 20 from `/etc/passwd`.**

<details><summary>✅ Answer</summary>

```bash
sed -n '10,20p' /etc/passwd
```

</details>

* [ ] **6. Create 2 users `u1` and `u2`. Set up `/collab` so:**

  * Both users can read/write inside.
  * New files are owned by group `collab`.
  * Only owners can delete their files.

<details><summary>✅ Answer</summary>

```bash
groupadd collab
useradd -G collab u1
useradd -G collab u2
mkdir /collab
chown root:collab /collab
chmod 2770 /collab
chmod +t /collab
```

</details>



* [ ] **7. Create a compressed archive of `/etc` using `bzip2` and verify contents.**

<details><summary>✅ Answer</summary>

```bash
tar cvjf etc_backup.tar.bz2 /etc
tar tvf etc_backup.tar.bz2 | less
```

</details>

* [ ] **8. Backup `/var/log` to remote host `192.168.1.100` under `/backups/logs`.**

<details><summary>✅ Answer</summary>

```bash
tar czf - /var/log | ssh user@192.168.1.100 "cat > /backups/logs/logs_backup.tar.gz"
```

</details>

* [ ] **9. Securely copy `/etc/hosts` to remote host `192.168.1.100` at `/tmp/`.**

<details><summary>✅ Answer</summary>

```bash
scp /etc/hosts user@192.168.1.100:/tmp/
```

</details>


* [ ] **10. Show all users from `/etc/passwd` that use `/bin/bash`.**

<details><summary>✅ Answer</summary>

```bash
grep "/bin/bash" /etc/passwd | cut -d: -f1
# or
cat /etc/passwd | grep /bin/bash | awk '{split($1,a,":"); print a[1]}'
```

</details>

* [ ] **11. Count how many system users have `/sbin/nologin`.**

<details><summary>✅ Answer</summary>

```bash
grep -c "/sbin/nologin" /etc/passwd
 # or
cat /etc/passwd | grep /sbin/nologin | wc -l
```

</details>

* [ ] **12. Extract only usernames of users with UID < 1000 from `/etc/passwd`.**

<details><summary>✅ Answer</summary>

```bash
awk -F: '$3 < 1000 {print $1}' /etc/passwd
```

</details>

* [ ] **13. Show all lines from `/etc/passwd` that do NOT contain `bash`.**

<details><summary>✅ Answer</summary>

```bash
grep -v bash /etc/passwd
```

</details>



* [ ] **14. Configure passwordless SSH for user `admin` from local → `192.168.1.200`.**

<details><summary>✅ Answer</summary>

```bash
ssh-keygen -t rsa
ssh-copy-id admin@192.168.1.200
ssh admin@192.168.1.200
```

</details>

* [ ] **15. Restrict SSH login for `root` and allow only `wheel` group members.**

<details><summary>✅ Answer</summary>

```bash
vi /etc/ssh/sshd_config
# PermitRootLogin no
# AllowGroups wheel
systemctl restart sshd
```

</details>



* [ ] **16. Find documentation of `tar` in `/usr/share/doc`.**

<details><summary>✅ Answer</summary>

```bash
ls /usr/share/doc/tar*
```

</details>

* [ ] **17. Search man pages for keyword `socket`.**

<details><summary>✅ Answer</summary>

```bash
man -k socket
```

</details>

* [ ] **18. Get detailed structured documentation about `ls`.**

<details><summary>✅ Answer</summary>

```bash
info ls
```

</details>



* [ ] **19. Create `/scripts/deploy.sh` with these requirements:**

  * Owner executes with elevated privileges (SUID).
  * Group has full access.
  * Others have no permissions.

<details><summary>✅ Answer</summary>

```bash
mkdir -p /scripts
touch /scripts/deploy.sh
chmod 4770 /scripts/deploy.sh
```

</details>

* [ ] **20. Create a directory `/tmp/public` where everyone can create files but only file owners can delete their own files.**

<details><summary>✅ Answer</summary>

```bash
mkdir /tmp/public
chmod 1777 /tmp/public
```

</details>

    
    
# RHCSA – Advance tasks


* [ ] **1. Create a directory `/secure/projects` with the following:**

  * Owner: `devuser`, Group: `devteam`
  * Permissions: Owner full, Group read+execute, Others no access.
  * Inside it, create `confidential.txt` and set **SUID** on it.

<details><summary>✅ Answer</summary>

```bash
mkdir -p /secure/projects
chown devuser:devteam /secure/projects
chmod 750 /secure/projects
cd /secure/projects
touch confidential.txt
chmod 4740 confidential.txt
```

</details>

* [ ] **2. Create a shared directory `/data/shared` where:**

  * All files created inside should inherit the group of the directory.
  * Prevent users from deleting files they don’t own.

<details><summary>✅ Answer</summary>

```bash
mkdir -p /data/shared
chown :devteam /data/shared
chmod 2775 /data/shared   # SGID
chmod +t /data/shared     # Sticky bit
```

</details>

* [ ] **3. Find all files owned by user `john` under `/var` and save the list in `/root/john_files.txt`.**

<details><summary>✅ Answer</summary>

```bash
find /var -user john > /root/john_files.txt
```

</details>

* [ ] **4. Compare two files `config.old` and `config.new` line by line.**

<details><summary>✅ Answer</summary>

```bash
diff config.old config.new
```

</details>

* [ ] **5. Display lines 10 to 20 from `/etc/passwd`.**

<details><summary>✅ Answer</summary>

```bash
sed -n '10,20p' /etc/passwd
```

</details>

* [ ] **6. Create a compressed archive of `/etc` but exclude all `.conf` files. Save it as `/root/etc-backup.tar.gz`.**

<details><summary>✅ Answer</summary>

```bash
tar --exclude='*.conf' -czf /root/etc-backup.tar.gz /etc
```

</details>

* [ ] **7. Search recursively under `/usr` for files larger than 50MB and save the output in `/root/large_files.txt`.**

<details><summary>✅ Answer</summary>

```bash
find /usr -type f -size +50M > /root/large_files.txt
```

</details>

* [ ] **8. Find files under `/var/log` modified within the last 7 days and copy them to `/root/log_backup`.**

<details><summary>✅ Answer</summary>

```bash
mkdir -p /root/log_backup
find /var/log -type f -mtime -7 -exec cp {} /root/log_backup/ \;
```

</details>

* [ ] **9. Create a symbolic link `/root/syslog` pointing to `/var/log/messages`.**

<details><summary>✅ Answer</summary>

```bash
ln -s /var/log/messages /root/syslog
```

</details>

* [ ] **10. Create a hard link `/root/passwd_link` pointing to `/etc/passwd`.**

<details><summary>✅ Answer</summary>

```bash
ln /etc/passwd /root/passwd_link
```

</details>

* [ ] **11. Set default ACLs on `/projects` so that new files get read+write permissions for group `devteam`.**

<details><summary>✅ Answer</summary>

```bash
setfacl -m g:devteam:rw /projects
setfacl -d -m g:devteam:rw /projects
```

</details>

* [ ] **12. Remove all ACLs from `/projects/confidential.txt`.**

<details><summary>✅ Answer</summary>

```bash
setfacl -b /projects/confidential.txt
```

</details>

* [ ] **13. Change the group ownership of all `.log` files under `/var/log` to `sysadmin`.**

<details><summary>✅ Answer</summary>

```bash
find /var/log -type f -name "*.log" -exec chgrp sysadmin {} +
```

</details>

* [ ] **14. Recursively set all directories under `/secure` to 750 and all files to 640.**

<details><summary>✅ Answer</summary>

```bash
find /secure -type d -exec chmod 750 {} +
find /secure -type f -exec chmod 640 {} +
```

</details>

* [ ] **15. Archive `/home` into `/root/home-backup.tar.gz`, but only include files owned by `student`.**

<details><summary>✅ Answer</summary>

```bash
tar --owner=student --group=student --create --gzip --file=/root/home-backup.tar.gz /home
```

</details>

* [ ] **16. Replace all occurrences of `http` with `https` in `/etc/httpd/conf/httpd.conf` and save changes in-place.**

<details><summary>✅ Answer</summary>

```bash
sed -i 's/http/https/g' /etc/httpd/conf/httpd.conf
```

</details>

* [ ] **17. Display only the usernames from `/etc/passwd`.**

<details><summary>✅ Answer</summary>

```bash
cut -d: -f1 /etc/passwd
```

</details>

* [ ] **18. Find the top 5 largest files in `/var`.**

<details><summary>✅ Answer</summary>

```bash
du -ah /var | sort -rh | head -5
```

</details>

* [ ] **19. Verify if `/root/etc-backup.tar.gz` is a valid tar archive without extracting it.**

<details><summary>✅ Answer</summary>

```bash
tar -tzf /root/etc-backup.tar.gz > /dev/null
```

</details>

* [ ] **20. Extract only `sshd_config` file from `/root/etc-backup.tar.gz` into `/tmp/`.**

<details><summary>✅ Answer</summary>

```bash
tar -xzf /root/etc-backup.tar.gz -C /tmp etc/ssh/sshd_config
```

</details>

* [ ] **21. Find files in `/etc` that are not regular files (like sockets, devices, symlinks) and save results to `/root/etc_special.txt`.**

<details><summary>✅ Answer</summary>

```bash
find /etc ! -type f > /root/etc_special.txt
```

</details>

* [ ] **22. Create `/opt/scripts/cleanup.sh` that deletes files in `/tmp` older than 10 days.**

<details><summary>✅ Answer</summary>

```bash
mkdir -p /opt/scripts
cat <<'EOF' > /opt/scripts/cleanup.sh
#!/bin/bash
find /tmp -type f -mtime +10 -delete
EOF
chmod +x /opt/scripts/cleanup.sh
```

</details>

* [ ] **23. Schedule the above script to run every day at midnight using cron.**

<details><summary>✅ Answer</summary>

```bash
echo "0 0 * * * /opt/scripts/cleanup.sh" >> /var/spool/cron/root
```

</details>

* [ ] **24. Copy `/etc/passwd` to `/root/passwd_copy.txt` but preserve permissions, timestamps, and SELinux context.**

<details><summary>✅ Answer</summary>

```bash
cp -a /etc/passwd /root/passwd_copy.txt
```

</details>

* [ ] **25. Create a tarball `/root/scripts-backup.tar.gz` that includes all `.sh` files under `/opt/scripts`.**

<details><summary>✅ Answer</summary>

```bash
tar -czf /root/scripts-backup.tar.gz /opt/scripts/*.sh
```

</details>

* [ ] **26. Extract `/root/scripts-backup.tar.gz` into `/srv/scripts` but avoid overwriting existing files.**

<details><summary>✅ Answer</summary>

```bash
mkdir -p /srv/scripts
tar --keep-old-files -xzf /root/scripts-backup.tar.gz -C /srv/scripts
```

</details>

* [ ] **27. List all files in `/etc` with their SELinux contexts.**

<details><summary>✅ Answer</summary>

```bash
ls -Z /etc
```

</details>

* [ ] **28. Restore the default SELinux context for `/var/www/html/index.html`.**

<details><summary>✅ Answer</summary>

```bash
restorecon -v /var/www/html/index.html
```

</details>

* [ ] **29. Change the SELinux context of `/srv/data` so it can be served by Apache.**

<details><summary>✅ Answer</summary>

```bash
semanage fcontext -a -t httpd_sys_content_t "/srv/data(/.*)?"
restorecon -Rv /srv/data
```

</details>

* [ ] **30. Search for all files with the `httpd_sys_content_t` context.**

<details><summary>✅ Answer</summary>

```bash
find / -context *:httpd_sys_content_t:* 2>/dev/null
```

</details>
