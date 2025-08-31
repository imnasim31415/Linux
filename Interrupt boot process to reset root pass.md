## 🔐 Recovering Forgotten Root Password in RHEL 9 Interrupting the Boot Process

### 1. Reboot the system

- Restart the VM or physical machine.
- At the very beginning, the **GRUB2 boot menu** appears (press `Esc` if it doesn’t).

### 2. Pause at GRUB

- Use arrow keys to select the default kernel.
- Press **`e`** to edit boot parameters.

### 3. Find the `linux` line

- Scroll until you see a line starting with:
  ```bash
  linux /vmlinuz-... root=UUID=...
  ```

### 4. Add rd.break

- At the end of that line, add:
  ```bash
  rd.break
  ```
- Press **Ctrl+X** (or F10) to boot.

You will land in a **minimal emergency shell**.

### 5. Check filesystem state

```bash
mount | grep /sysroot
```

It will show `/sysroot` as **read-only**.

### 6. Remount as read-write

```bash
mount -o remount,rw /sysroot
```

### 7. Switch to real system

```bash
chroot /sysroot
```

Now you are inside your actual system.

```bash
whoami
# root
```

### 8. Reset root password

```bash
passwd root
```

Enter the new password twice.

### 9. Relabel SELinux contexts

```bash
touch /.autorelabel
```

### 10. Exit and reboot

```bash
exit
reboot
```

System reboots.

### 11. Wait for SELinux relabeling

- Filesystem will relabel (may take time).

### 12. Log in with new root password

```bash
su -
```

Enter your new root password → you’re back in control.

---

## ✅ Key Notes for RHCSA Exam

- Always use `rd.break` for root password recovery.
- Remember `/sysroot` is read-only by default — remount it rw.
- Always `chroot /sysroot` to apply changes to the real system.
- Always `touch /.autorelabel` to fix SELinux contexts.

---

## 🔎 Why `chroot` is Needed

### Without chroot

Your shell is running on the `initramfs` root filesystem, not your actual `/`.
Any changes you make (like passwd root) will apply to the temporary environment, not your real system.
That means when you reboot, nothing changes — your root password on the real system stays the same.

### With chroot /sysroot

When booting with `rd.break`, you are dropped into the **initramfs environment** — a small, temporary root filesystem in RAM.

- Without `chroot`:  
  Any changes you make (like `passwd root`) affect only the temporary initramfs, not your real system.
- With `chroot /sysroot`:  
  You switch into the **real installed RHEL system**, so your changes persist.

👉 Now the password is updated in /etc/shadow of your actual RHEL installation. Think of `chroot` as “stepping into your real house so changes actually stick.”

---
