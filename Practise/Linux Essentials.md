# Linux Essentials – Practice Q&A (Simple → Medium → Hard)

This guide contains **practice questions and answers** based on common Linux tools and commands.  
Use it to drill yourself for exams or real-world scenarios.

---

## 🔹 Part 1: Practice Questions

### **Basics (Simple)**
1. Which command shows the current working directory?  
2. How do you list all hidden files in a directory?  
3. What does `cd -` do?  
4. Which command shows the username of the current shell session?  
5. What is the difference between `who` and `whoami`?  
6. How do you clear the terminal screen?  
7. Which command shows the system uptime?  
8. How do you show all groups the current user belongs to?  
9. What’s the difference between `last` and `lastb`?  
10. What does `uname -a` display?  

### **Intermediate**
11. Write the command to view the hostname and then change it to `server1`.  
12. Which command lists all available time zones?  
13. How do you set the system timezone to `Asia/Dhaka`?  
14. How do you check the path of a command (e.g., `ls`)?  
15. Which command shows details about USB devices?  
16. Which command lists CPU details?  
17. What’s the difference between `gzip file.txt` and `bzip2 file.txt`?  
18. Write the tar command to create an archive `backup.tar` of `/home/user/`.  
19. Extract the archive `backup.tar.gz`.  
20. What’s the difference between `tar -cvf` and `tar -rvf`?  

### **I/O Redirection**
21. Redirect the output of `ls -l` to a file `files.txt`.  
22. Append the output of `pwd` to the file `files.txt`.  
23. Redirect only error messages of `ls /no/such/dir` into `error.txt`.  
24. Redirect both stdout and stderr of `echo hello` into `all.txt`.  
25. Read the content of `/etc/passwd` using input redirection with `cat`.  

### **Text Searching & Regex**
26. Find lines containing `root` in `/etc/passwd`.  
27. Find lines starting with `user100` in `/etc/passwd`.  
28. Find lines ending with `/bin/bash` in `/etc/passwd`.  
29. Use grep to search case-insensitively for the word “nasim” in `/etc/hosts`.  
30. What does `grep -v nasim /etc/passwd` do?  

### **SSH & Remote Access**
31. Write the SSH command to log in as user `admin` to host `192.168.1.10`.  
32. How do you generate SSH keys?  
33. Which command copies your SSH key to a remote host?  
34. Where are SSH keys stored by default?  
35. Which two files are used by TCP Wrappers for host-based access control?  

### **File Management**
36. Create a new empty file `notes.txt`.  
37. Create a directory `projects/`.  
38. Copy `file1` to `backup/file1_copy`.  
39. Move `file2` to `archive/file2`.  
40. Delete a non-empty directory `testdir/`.  

### **Links**
41. Create a soft link `link1` pointing to `/etc/passwd`.  
42. Create a hard link `link2` pointing to `/etc/passwd`.  
43. What happens if the original file of a **soft link** is deleted?  
44. What happens if the original file of a **hard link** is deleted?  

### **Permissions & Ownership**
45. Convert symbolic `rwxr-x---` to octal.  
46. Give read, write, and execute permission to everyone on `file1`.  
47. What’s the default umask for normal users?  
48. If umask = 027, what will be the default permission of a new file?  
49. Change the owner of `file1` to user `nasim`.  
50. Change both owner and group of `dir1` recursively to `user1:group1`.  

### **Documentation**
51. Which command shows a short description of `ls` from the man database?  
52. Search for the keyword “time” in the man pages.  
53. What’s the difference between `man` and `info`?  
54. Which command updates the man database?  
55. Where is documentation for installed packages stored?  

---

## 🔹 Part 2: Answers

### **Basics**
1. `pwd`  
2. `ls -a`  
3. Switches to the previous directory.  
4. `whoami`  
5. `who` shows logged-in users, `whoami` shows the current user only.  
6. `clear`  
7. `uptime`  
8. `groups`  
9. `last` shows successful logins and reboots, `lastb` shows failed attempts.  
10. System + kernel information.  

### **Intermediate**
11. `hostnamectl` → `hostnamectl set-hostname server1`  
12. `timedatectl list-timezones`  
13. `timedatectl set-timezone Asia/Dhaka`  
14. `which ls`  
15. `lsusb`  
16. `lscpu`  
17. Both compress, but gzip uses `.gz`, bzip2 uses `.bz2` (usually smaller but slower).  
18. `tar cvf backup.tar /home/user/`  
19. `tar xvfz backup.tar.gz`  
20. `-c` creates a new archive, `-r` appends files to an existing one.  

### **I/O Redirection**
21. `ls -l > files.txt`  
22. `pwd >> files.txt`  
23. `ls /no/such/dir 2> error.txt`  
24. `echo hello >> all.txt 2>&1`  
25. `cat < /etc/passwd`  

### **Text Searching & Regex**
26. `grep root /etc/passwd`  
27. `grep ^user100 /etc/passwd`  
28. `grep /bin/bash$ /etc/passwd`  
29. `grep -i nasim /etc/hosts`  
30. Shows all lines **not containing** “nasim”.  

### **SSH & Remote Access**
31. `ssh admin@192.168.1.10`  
32. `ssh-keygen`  
33. `ssh-copy-id user@host`  
34. `~/.ssh/id_rsa` (private), `~/.ssh/id_rsa.pub` (public)  
35. `/etc/hosts.allow` and `/etc/hosts.deny`  

### **File Management**
36. `touch notes.txt`  
37. `mkdir projects/`  
38. `cp file1 backup/file1_copy`  
39. `mv file2 archive/file2`  
40. `rm -r testdir/`  

### **Links**
41. `ln -s /etc/passwd link1`  
42. `ln /etc/passwd link2`  
43. Soft link becomes broken (dangling).  
44. Hard link still works, data is preserved until all hard links are removed.  

### **Permissions & Ownership**
45. `750`  
46. `chmod 777 file1`  
47. `0002`  
48. New file permissions = 666 – 027 = `640` (rw-r-----)  
49. `chown nasim file1`  
50. `chown -R user1:group1 dir1`  

### **Documentation**
51. `whatis ls`  
52. `man -k time` or `apropos time`  
53. `man` gives concise usage docs, `info` gives more detailed structured docs.  
54. `mandb`  
55. `/usr/share/doc/`  
