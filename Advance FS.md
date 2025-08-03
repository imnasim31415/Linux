
# 🔐 Advanced Linux File System Permissions (ACL, chattr, lsattr)

---

## 📂 Access Control Lists (ACLs)

ACLs allow you to set **fine-grained permissions** beyond the traditional owner/group/other model.

### ✅ Check ACL support

```bash
mount | grep acl
tune2fs -l /dev/sdX | grep 'Default mount options'  # Should show 'acl'
```

---

### 📖 View ACLs

```bash
getfacl filename
```

---

### ✍️ Set ACLs

```bash
# Give user 'alice' read+write permissions
setfacl -m u:alice:rw file.txt

# Give group 'devs' read+execute permissions
setfacl -m g:devs:rx file.txt

# Multiple ACL entries
setfacl -m u:bob:rwx,g:editors:rw file.txt
```

---

### 📂 Default ACLs for Directories

```bash
# Default ACLs: new files inherit these
setfacl -d -m u:john:rwx /project

# Combine default and normal
setfacl -m u:john:rw /project/file.txt
setfacl -d -m u:john:rw /project/
```

---

### ❌ Remove ACLs

```bash
# Remove ACL entry for user 'bob'
setfacl -x u:bob file.txt

# Remove all ACL entries
setfacl -b file.txt
```

---

## 🔒 File Attributes with `chattr` and `lsattr`

File attributes go **below the permission level** and even root must force override them.

### 🔍 View Attributes

```bash
lsattr file.txt
```

Example output:

```
----i--------e-- file.txt
```

Flags:
- `i`: Immutable – file can't be modified, deleted, renamed, or linked.
- `a`: Append-only – only append operations allowed.
- `d`: No dump – file won’t be backed up by dump command.

---

### ✍️ Set Attributes with `chattr`

```bash
# Make file immutable
chattr +i file.txt

# Allow only appends (e.g., for log files)
chattr +a /var/log/mylog.log

# Remove attribute
chattr -i file.txt
```

---

## 🛠 Real-World Scenarios

### 📁 Enforcing Read-Only Files for All

```bash
chattr +i /etc/important.conf
# Even root needs to remove +i to modify
```

### 📁 Secure Audit Logs

```bash
chattr +a /var/log/secure.log
# Only appends are allowed
```

### 🔧 ACL Example: Developer Access to Logs

```bash
# Let devs read logs but not write
setfacl -m g:devs:r-- /var/log/app.log
```

---

## 🧼 Troubleshooting Tips

```bash
# Check full permission trail
namei -lx /path/to/file

# Test user access to file
sudo -u username cat /path/to/file

# View ACLs and permissions
ls -le  # BSD systems
ls -l && getfacl filename  # Linux
```

---

## 🧠 Summary Table

| Tool     | Purpose                            | Example                      |
|----------|------------------------------------|------------------------------|
| `getfacl`| View ACLs                          | `getfacl myfile`             |
| `setfacl`| Modify ACLs                        | `setfacl -m u:bob:rw myfile` |
| `chattr` | Change file attributes             | `chattr +i secret.txt`       |
| `lsattr` | View file attributes               | `lsattr secret.txt`          |
| `namei`  | Permission resolution on a path    | `namei -lx /var/log/syslog`  |

---

Use these tools carefully – they can override or extend traditional permission schemes and help enforce stricter access policies on multi-user systems.
