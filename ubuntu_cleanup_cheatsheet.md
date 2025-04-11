
# 🧹 Ubuntu Cleanup Commands Cheat Sheet

A quick reference to clean up disk space and remove junk from your Ubuntu system.

---

## 📦 Check Disk Usage

```bash
df -h                           # Show disk usage of mounted partitions
du -h --max-depth=1 /          # Show size of top-level directories
```

---

## 🔍 Find Large Files and Directories

```bash
sudo find / -type f -exec du -Sh {} + 2>/dev/null | sort -rh | head -n 20
find ~ -type f -exec du -Sh {} + 2>/dev/null | sort -rh | head -n 20
sudo du -ahx / | sort -rh | head -n 20
```

---

## 📦 Check Installed Packages by Size

```bash
dpkg-query -Wf='${Installed-Size}\t${Package}\n' | awk '{printf "%.2f MB\t%s\n", $1/1024, $2}' | sort -nr | head -n 20
```

---

## 🧹 Cleanup Commands

### APT Cache Cleanup

```bash
sudo apt clean
sudo apt autoclean
sudo apt autoremove --purge
```

### Clear Thumbnail Cache

```bash
rm -rf ~/.cache/thumbnails/*
```

### Clear Trash

```bash
rm -rf ~/.local/share/Trash/*
```

### Remove Snap Package Junk

```bash
sudo snap list --all | awk '/disabled/{print $1, $2}' | while read snapname version; do sudo snap remove "$snapname" --revision="$version"; done
```

---

## 🧰 Install Useful Tools

### ncdu (Disk usage analyzer)

```bash
sudo apt install ncdu
sudo ncdu /
```

### BleachBit (GUI cleaner)

```bash
sudo apt install bleachbit
sudo bleachbit
```

---
