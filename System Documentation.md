
| Command / Option       | Purpose                                           | Example (RHCSA context)                         | When to Use                                                  |
| ---------------------- | ------------------------------------------------- | ----------------------------------------------- | ------------------------------------------------------------ |
| `man <command>`        | Show manual page for a command                    | `man useradd` → see options like `-m`, `-s`     | Need exact syntax before adding a user                       |
| `man -k <keyword>`     | Search man **database** for keyword (≈ `apropos`) | `man -k quota` → lists `quota`, `edquota`, etc. | When you *know the topic* but not the command name           |
| `man -K <string>`      | Search inside **all man pages** for a string      | `man -K "SELinux context"`                      | When keyword search fails; exhaustive fallback               |
| `mandb`                | Update man **index database**                     | `sudo mandb`                                    | Run after installing packages so `man -k` finds new commands |
| `catman`               | Pre-format man pages into “cat pages” for speed   | `sudo catman -w`                                | Rarely needed now; ignore unless asked in theory             |
| `info <command>`       | GNU “info” docs (sometimes longer than man)       | `info coreutils 'ls invocation'`                | When `man ls` is too short; deeper dive                      |
| `--help`               | Quick one-screen help                             | `tar --help` → lists all archive options        | For fast reminders in the exam (less scrolling)              |
| `/usr/share/doc/<pkg>` | Package documentation, config samples, README     | `ls /usr/share/doc/openssh*/` → sample configs  | Use when you need config file examples                       |
| `rpm -qd <pkg>`        | List doc files installed with package             | `rpm -qd bash`                                  | To find docs if unsure where they are                        |
| `apropos <keyword>`    | Same as `man -k`                                  | `apropos partition`                             | Find related commands (`fdisk`, `partprobe`, etc.)           |

---

## ⚡ RHCSA-Specific Use Cases

1. **User Management**

   * `man useradd` → confirm `-m` creates home dir, `-s` sets shell.
   * `man passwd` → check password expiry options.

2. **SELinux**

   * `man -k selinux` → find commands like `getenforce`, `setenforce`.
   * `man semanage-fcontext` → check syntax for labeling dirs.

3. **Storage & Partitions**

   * `man mount` → check `-t` for filesystem type.
   * `man fstab` → confirm column meanings for permanent mounts.
   * `apropos partition` → discover `partx`, `partprobe`.

4. **Networking**

   * `man nmcli-examples` → pre-written network config commands.
   * `man hostnamectl` → set/verify hostname.

5. **Systemctl & Services**

   * `man systemctl` → check unit types, enabling/disabling syntax.
   * `man journalctl` → filtering logs by unit or time.

6. **Archiving & Compression**

   * `tar --help` → instant reminder of `-czf`, `-xvf`.
   * `man rsync` → check common flags `-a`, `-v`, `-z`.

---

⚠️ **Exam Hack:**
If you’re blanking out →
- 👉 Start with `--help`
- 👉 If not enough, `man <cmd>`
- 👉 If you can’t remember command, `man -k <topic>`
- 👉 For SELinux or oddball topics, `/usr/share/doc/<pkg>`
