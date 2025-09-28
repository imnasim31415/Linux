# Linux Essentials – Practice Q&A

This guide contains **practice questions** with checkboxes for tracking and collapsible answers.  
Try solving before expanding the answer!

---

## 🔹 Basics (Simple)

- [ ] **1. Which command shows the current working directory?**  
  <details><summary>Answer</summary>  
  `pwd`  
  </details>

- [ ] **2. How do you list all hidden files in a directory?**  
  <details><summary>Answer</summary>  
  `ls -a`  
  </details>

- [ ] **3. What does `cd -` do?**  
  <details><summary>Answer</summary>  
  Switches to the previous directory.  
  </details>

- [ ] **4. Which command shows the username of the current shell session?**  
  <details><summary>Answer</summary>  
  `whoami`  
  </details>

- [ ] **5. What is the difference between `who` and `whoami`?**  
  <details><summary>Answer</summary>  
  `who` shows logged-in users, `whoami` shows only the current user.  
  </details>

- [ ] **6. How do you clear the terminal screen?**  
  <details><summary>Answer</summary>  
  `clear`  
  </details>

- [ ] **7. Which command shows the system uptime?**  
  <details><summary>Answer</summary>  
  `uptime`  
  </details>

- [ ] **8. How do you show all groups the current user belongs to?**  
  <details><summary>Answer</summary>  
  `groups`  
  </details>

- [ ] **9. What’s the difference between `last` and `lastb`?**  
  <details><summary>Answer</summary>  
  `last` shows successful logins/reboots, `lastb` shows failed attempts.  
  </details>

- [ ] **10. What does `uname -a` display?**  
  <details><summary>Answer</summary>  
  System + kernel information.  
  </details>

---

## 🔹 Intermediate

- [ ] **11. View the hostname and then change it to `server1`.**  
  <details><summary>Answer</summary>  
  `hostnamectl` → `hostnamectl set-hostname server1`  
  </details>

- [ ] **12. Which command lists all available time zones?**  
  <details><summary>Answer</summary>  
  `timedatectl list-timezones`  
  </details>

- [ ] **13. Set the system timezone to `Asia/Dhaka`.**  
  <details><summary>Answer</summary>  
  `timedatectl set-timezone Asia/Dhaka`  
  </details>

- [ ] **14. How do you check the path of a command (e.g., `ls`)?**  
  <details><summary>Answer</summary>  
  `which ls`  
  </details>

- [ ] **15. Which command shows details about USB devices?**  
  <details><summary>Answer</summary>  
  `lsusb`  
  </details>

- [ ] **16. Which command lists CPU details?**  
  <details><summary>Answer</summary>  
  `lscpu`  
  </details>

- [ ] **17. Difference between `gzip file.txt` and `bzip2 file.txt`?**  
  <details><summary>Answer</summary>  
  Both compress, gzip → `.gz`, bzip2 → `.bz2` (slower but smaller).  
  </details>

- [ ] **18. Create an archive `backup.tar` of `/home/user/`.**  
  <details><summary>Answer</summary>  
  `tar cvf backup.tar /home/user/`  
  </details>

- [ ] **19. Extract the archive `backup.tar.gz`.**  
  <details><summary>Answer</summary>  
  `tar xvfz backup.tar.gz`  
  </details>

- [ ] **20. Difference between `tar -cvf` and `tar -rvf`?**  
  <details><summary>Answer</summary>  
  `-c` creates new archive, `-r` appends to existing archive.  
  </details>

---

## 🔹 I/O Redirection

- [ ] **21. Redirect the output of `ls -l` to `files.txt`.**  
  <details><summary>Answer</summary>  
  `ls -l > files.txt`  
  </details>

- [ ] **22. Append `pwd` output to `files.txt`.**  
  <details><summary>Answer</summary>  
  `pwd >> files.txt`  
  </details>

- [ ] **23. Redirect only errors of `ls /no/such/dir` into `error.txt`.**  
  <details><summary>Answer</summary>  
  `ls /no/such/dir 2> error.txt`  
  </details>

- [ ] **24. Redirect both stdout & stderr of `echo hello` into `all.txt`.**  
  <details><summary>Answer</summary>  
  `echo hello >> all.txt 2>&1`  
  </details>

- [ ] **25. Read `/etc/passwd` using input redirection.**  
  <details><summary>Answer</summary>  
  `cat < /etc/passwd`  
  </details>

---

## 🔹 Text Searching & Regex

- [ ] **26. Find lines containing `root` in `/etc/passwd`.**  
  <details><summary>Answer</summary>  
  `grep root /etc/passwd`  
  </details>

- [ ] **27. Find lines starting with `user100` in `/etc/passwd`.**  
  <details><summary>Answer</summary>  
  `grep ^user100 /etc/passwd`  
  </details>

- [ ] **28. Find lines ending with `/bin/bash` in `/etc/passwd`.**  
  <details><summary>Answer</summary>  
  `grep /bin/bash$ /etc/passwd`  
  </details>

- [ ] **29. Case-insensitive search for “nasim” in `/etc/hosts`.**  
  <details><summary>Answer</summary>  
  `grep -i nasim /etc/hosts`  
  </details>

- [ ] **30. What does `grep -v nasim /etc/passwd` do?**  
  <details><summary>Answer</summary>  
  Shows all lines **not containing** “nasim”.  
  </details>

---

## 🔹 SSH & Remote Access

- [ ] **31. SSH as user `admin` to host `192.168.1.10`.**  
  <details><summary>Answer</summary>  
  `ssh admin@192.168.1.10`  
  </details>

- [ ] **32. How do you generate SSH keys?**  
  <details><summary>Answer</summary>  
  `ssh-keygen`  
  </details>

- [ ] **33. Copy your SSH key to a remote host.**  
  <details><summary>Answer</summary>  
  `ssh-copy-id user@host`  
  </details>

- [ ] **34. Where are SSH keys stored by default?**  
  <details><summary>Answer</summary>  
  `~/.ssh/id_rsa` (private), `~/.ssh/id_rsa.pub` (public)  
  </details>

- [ ] **35. Which two files control TCP Wrappers?**  
  <details><summary>Answer</summary>  
  `/etc/hosts.allow` and `/etc/hosts.deny`  
  </details>

---

## 🔹 File Management

- [ ] **36. Create an empty file `notes.txt`.**  
  <details><summary>Answer</summary>  
  `touch notes.txt`  
  </details>

- [ ] **37. Create directory `projects/`.**  
  <details><summary>Answer</summary>  
  `mkdir projects/`  
  </details>

- [ ] **38. Copy `file1` to `backup/file1_copy`.**  
  <details><summary>Answer</summary>  
  `cp file1 backup/file1_copy`  
  </details>

- [ ] **39. Move `file2` to `archive/file2`.**  
  <details><summary>Answer</summary>  
  `mv file2 archive/file2`  
  </details>

- [ ] **40. Delete non-empty directory `testdir/`.**  
  <details><summary>Answer</summary>  
  `rm -r testdir/`  
  </details>

---

## 🔹 Links

- [ ] **41. Create a soft link `link1` → `/etc/passwd`.**  
  <details><summary>Answer</summary>  
  `ln -s /etc/passwd link1`  
  </details>

- [ ] **42. Create a hard link `link2` → `/etc/passwd`.**  
  <details><summary>Answer</summary>  
  `ln /etc/passwd link2`  
  </details>

- [ ] **43. What happens if soft link’s target is deleted?**  
  <details><summary>Answer</summary>  
  The soft link becomes broken (dangling).  
  </details>

- [ ] **44. What happens if hard link’s target is deleted?**  
  <details><summary>Answer</summary>  
  Data remains accessible via the hard link.  
  </details>

---

## 🔹 Permissions & Ownership

- [ ] **45. Convert `rwxr-x---` to octal.**  
  <details><summary>Answer</summary>  
  `750`  
  </details>

- [ ] **46. Give full permissions to everyone on `file1`.**  
  <details><summary>Answer</summary>  
  `chmod 777 file1`  
  </details>

- [ ] **47. Default umask for normal users?**  
  <details><summary>Answer</summary>  
  `0002`  
  </details>

- [ ] **48. New file perms if umask=027?**  
  <details><summary>Answer</summary>  
  666 – 027 = `640` (rw-r-----)  
  </details>

- [ ] **49. Change owner of `file1` to user `nasim`.**  
  <details><summary>Answer</summary>  
  `chown nasim file1`  
  </details>

- [ ] **50. Recursively change owner & group of `dir1` to `user1:group1`.**  
  <details><summary>Answer</summary>  
  `chown -R user1:group1 dir1`  
  </details>

---

## 🔹 Documentation

- [ ] **51. Short description of `ls` from man db?**  
  <details><summary>Answer</summary>  
  `whatis ls`  
  </details>

- [ ] **52. Search for “time” in man pages.**  
  <details><summary>Answer</summary>  
  `man -k time` or `apropos time`  
  </details>

- [ ] **53. Difference between `man` and `info`?**  
  <details><summary>Answer</summary>  
  `man` → concise usage, `info` → detailed structured docs.  
  </details>

- [ ] **54. Update the man database.**  
  <details><summary>Answer</summary>  
  `mandb`  
  </details>

- [ ] **55. Where is package documentation stored?**  
  <details><summary>Answer</summary>  
  `/usr/share/doc/`  
  </details>
