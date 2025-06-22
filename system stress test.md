
# 🧪 System Stress Testing – No External Tools

This cheat sheet helps you simulate system load (CPU, RAM, Disk, and Network) without installing any tools like `stress` or `stress-ng`.

---

## 🚀 CPU

```bash
yes > /dev/null &
```

Launches a CPU-hogging process.  
Repeat for more cores:

```bash
for i in {1..4}; do yes > /dev/null & done
```

Stop all:

```bash
killall yes
```

---

## 🧠 Memory (RAM)

```bash
python3 -c "a = ' ' * 500 * 1024 * 1024; input('500MB allocated, press enter to free...')"
```

Allocates 500MB of RAM in Python. Adjust the number for more.

---

## 💽 Disk I/O

```bash
dd if=/dev/zero of=tempfile bs=1M count=1000 oflag=direct
```

Writes 1GB to disk using raw I/O.

Clean up:

```bash
rm tempfile
```

---

## 🌐 Network

### 🔁 Local Ping Flood

```bash
ping -f 127.0.0.1
```

### ↔️ Between VMs

From VM1 to VM2:

```bash
ping -f 192.168.123.102
```

> ⚠️ Can overwhelm network stack – use carefully!

---

## 🔁 Combined (CPU + RAM + Disk)

Run in parallel:

```bash
yes > /dev/null &
python3 -c "a = ' ' * 1000 * 1024 * 1024; input('1GB RAM allocated...')"
dd if=/dev/zero of=tempfile bs=1M count=1000 oflag=direct
```

---

🧼 Cleanup:

```bash
killall yes
rm tempfile
```

---
