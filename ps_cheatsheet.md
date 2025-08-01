# RHEL9 Process Management Cheat Sheet

A compact guide for managing processes in Red Hat Enterprise Linux 9.

---

## Viewing Processes

| Command | Description |
|--------|-------------|
| `ps aux` | List all running processes |
| `ps -ef` | List processes with full-format listing |
| `top` | Real-time system monitoring |
| `htop` | Interactive process viewer (requires install) |
| `pgrep <name>` | Find PID of process by name |
| `pidof <program>` | Find PID(s) of a running program |

---

## Filtering & Monitoring

| Command | Description |
|--------|-------------|
| `ps -C <name>` | Show process info by command name |
| `ps -p <PID>` | Show specific process by PID |
| `watch -n 1 'ps -ef \| grep <pattern>'` | Live monitor matching processes |
| `strace -p <PID>` | Trace system calls of a process |
| `lsof -p <PID>` | List open files by process |
| `pstree` | Show process hierarchy tree |

---

## Starting Processes

| Command | Description |
|--------|-------------|
| `&` | Run process in background (`./script.sh &`) |
| `nohup <cmd> &` | Run immune to hangups/logouts |
| `disown` | Remove job from shell job table |
| `setsid <cmd>` | Run in new session (detached) |
| `at <time>` | Schedule one-time job |
| `cron` | Schedule repeating jobs |

---

## Foreground & Background Jobs

| Command | Description |
|--------|-------------|
| `jobs` | List background jobs |
| `bg %<jobid>` | Resume job in background |
| `fg %<jobid>` | Bring background job to foreground |
| `Ctrl+Z` | Suspend foreground job |
| `kill -SIGCONT <PID>` | Resume stopped job |

---

## Killing Processes

| Command | Description |
|--------|-------------|
| `kill <PID>` | Send SIGTERM (graceful) |
| `kill -9 <PID>` | Send SIGKILL (forceful) |
| `pkill <name>` | Kill process by name |
| `pkill -f <pattern>` | Kill by full command line |
| `killall <program>` | Kill all instances of a program |

---

## CPU/Nice Management

| Command | Description |
|--------|-------------|
| `nice -n <value> <cmd>` | Run with specified priority (-20 to 19) |
| `renice <value> -p <PID>` | Change priority of a running process |
| `top` → `r` | Change priority interactively |

---

## Process Security / Isolation

| Tool | Description |
|------|-------------|
| `semanage` | Set SELinux policies on process |
| `ps -Z` | Show SELinux context of processes |
| `systemd-run --scope` | Run command in its own cgroup |
| `cgroups` / `cpuset` | Limit CPU/memory usage (advanced) |

---

## systemd & Services

| Command | Description |
|--------|-------------|
| `systemctl status <service>` | View service status |
| `systemctl start <service>` | Start service |
| `systemctl stop <service>` | Stop service |
| `systemctl restart <service>` | Restart service |
| `systemctl list-units --type=service` | List active services |

---

##  Bonus Tips

- Always prefer `SIGTERM` over `SIGKILL` — it's graceful.
- Use `top` or `htop` to interactively renice or kill.
- Log process behavior with `strace`, `lsof`, and `journalctl`.
- Long-running detached processes? Use `tmux` or `screen`.

---

## Useful Files

| File | Purpose |
|------|---------|
| `/proc/<PID>/` | Process info, memory maps, cmdline |
| `/etc/security/limits.conf` | Control user process limits |
| `/var/log/messages` | System messages (includes process crash logs) |

---

## Process States (via `ps`)

| Code | Meaning |
|------|---------|
| `R` | Running |
| `S` | Sleeping |
| `D` | Uninterruptible sleep (I/O) |
| `T` | Stopped |
| `Z` | Zombie |
| `X` | Dead |

---

## 🧩 Common Signals

| Signal | Description |
|--------|-------------|
| `1` (SIGHUP) | Reload config |
| `2` (SIGINT) | Interrupt (Ctrl+C) |
| `9` (SIGKILL) | Kill immediately |
| `15` (SIGTERM) | Graceful termination |
| `18` (SIGCONT) | Continue if stopped |
| `19` (SIGSTOP) | Pause process |



---

## 🧨 Scenario 1: Apache is Eating 100% CPU

### 🔍 Investigate

```bash
top -o %CPU
```

Find the offending process. Assume PID is `3421`.

```bash
ps -p 3421 -o pid,ppid,cmd,%cpu,%mem
```

### 🛠️ Solution 1: Lower Its Priority

```bash
sudo renice +10 -p 3421
```

### 🛠️ Solution 2: Restart the Service

```bash
sudo systemctl restart httpd
```

---

## 🧟 Scenario 2: Zombie Processes Hanging Around

### 🔍 Detect Zombies

```bash
ps aux | awk '{ if ($8 == "Z") print $0; }'
```

### 🧠 What it Means

Zombies are dead processes not properly cleaned up by their parent.

### 🛠️ Solution: Restart the Parent Process

Find the zombie’s PPID:

```bash
ps -o ppid= -p <zombie_pid>
```

Kill or restart the parent:

```bash
sudo kill -HUP <ppid>
```

If it's a service, restart the service instead.

---

## ⏳ Scenario 3: Background Job Stuck Too Long

### 🧪 Example

```bash
./long_script.sh &
```

### 🛠️ Bring it to Foreground

```bash
jobs
fg %1
```

### 🛠️ Kill it if Unresponsive

```bash
kill %1
```

Or use the PID directly:

```bash
pkill -f long_script.sh
```

---

## 🧯 Scenario 4: Service Keeps Crashing

### 🔍 Check Logs

```bash
journalctl -u myservice --since "10 minutes ago"
```

### 🛠️ Restart and Monitor

```bash
systemctl restart myservice
systemctl status myservice
```

### 🛡️ Enable Auto-Restart

```bash
systemctl edit myservice
```

Add:

```ini
[Service]
Restart=always
```

---

## 🧹 Scenario 5: Limit Memory or CPU of a Process

### 🔧 Use systemd-run with limits

```bash
systemd-run --scope -p MemoryMax=500M -p CPUQuota=20% ./heavy_script.sh
```

---

## 🧰 Scenario 6: Monitor File Usage of a Process

```bash
lsof -p <PID>
```

Or to track file IO live:

```bash
strace -p <PID> -e trace=file
```

---

## 🧊 Scenario 7: Gracefully Kill a Misbehaving App

```bash
pkill -15 firefox
```

If that fails:

```bash
pkill -9 firefox
```

---

## 📊 Scenario 8: See Process Tree for Troubleshooting

```bash
pstree -p
```

Or filter by parent process:

```bash
pstree -p <PID>
```
