## `lsof` Cheatsheet

A quick reference guide for the `lsof` command with descriptions, usage examples, and real-world scenarios.

---

### 🔧 Command Table

| **Command**                              | **Description**                                 | **Example**                             |
|------------------------------------------|-------------------------------------------------|------------------------------------------|
| `lsof`                                   | List all open files                             | `lsof`                                   |
| `lsof -p <PID>`                          | Show files opened by a specific process         | `lsof -p 1234`                           |
| `lsof -u <user>`                         | Show files opened by a user                     | `lsof -u nasim`                          |
| `lsof <file>`                            | See who opened a specific file                  | `lsof /var/log/syslog`                   |
| `lsof -c <name>`                         | Processes starting with command name            | `lsof -c sshd`                           |
| `lsof -i`                                | Show all network connections                    | `lsof -i`                                |
| `lsof -i :<port>`                        | Who is using a specific port                    | `lsof -i :22`                            |
| `lsof -iTCP -sTCP:LISTEN`                | List all listening TCP ports                    | `lsof -iTCP -sTCP:LISTEN`                |
| `lsof -i@<IP>`                           | Show connections on a specific IP               | `lsof -i@192.168.0.101`                  |
| `lsof +D /path/dir`                      | All open files in a directory (slow)            | `lsof +D /home/nasim`                    |
| `lsof \| grep deleted`                    | Find deleted files still held open              | `lsof \| grep deleted`                    |
| `lsof /dev/sdX`                          | See which process is using a device             | `lsof /dev/sda1`                         |
| `lsof -t -i :<port>`                     | Get only the PID using a port                   | `lsof -t -i :8080`                       |
| `kill -9 $(lsof -t -i :<port>)`          | Kill process using a specific port              | `kill -9 $(lsof -t -i :8080)`            |
| `lsof -nP`                               | Disable DNS and port resolution (faster)        | `lsof -nP`                               |
| `lsof -a -u <user> -i`                   | Combine user and network filters                | `lsof -a -u nasim -i`                    |
| `lsof -d cwd`                            | Show current working directories of processes   | `lsof -d cwd`                            |
| `lsof -n \| grep TCP`                     | List open TCP connections without resolving hostnames | `lsof -n \| grep TCP`                |
| `lsof -r 2 -i :80`                       | Monitor port 80 every 2 seconds                 | `lsof -r 2 -i :80`                       |

---

### 📌 Real-World Scenarios

#### 🧯 Scenario 1: Port Already in Use
**Problem:** You try to start your app, but it says `port 3000 is already in use`.

**Solution:**
```bash
lsof -i :3000
kill -9 $(lsof -t -i :3000)
```

---

#### 📦 Scenario 2: Disk Can’t Be Unmounted
**Problem:** You're trying to unmount a USB or partition but getting `device is busy`.

**Solution:**
```bash
sudo lsof /dev/sdb1
# Kill or close the listed processes
```

---

#### 🔄 Scenario 3: Log Rotation Fails Due to Deleted Files
**Problem:** Log rotation scripts don’t free up disk space.

**Solution:**
```bash
lsof | grep deleted
# Restart processes still holding deleted logs
```

---

#### 🛡️ Scenario 4: Investigate Possible Network Backdoor
**Problem:** You notice unusual network activity and want to inspect connections.

**Solution:**
```bash
sudo lsof -i -nP | grep ESTABLISHED
# Investigate unknown or suspicious IPs
```

---

#### 🧪 Scenario 5: Monitor Live Traffic on a Port
**Problem:** You want to see if traffic is hitting your local web server.

**Solution:**
```bash
sudo lsof -r 2 -i :80
```
