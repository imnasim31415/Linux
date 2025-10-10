- [ ] **Task 1: Directory Check and Creation**

Write a script that checks if a directory given as an argument exists. If not, create it and print a confirmation message.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
DIR=$1
if [ -d "$DIR" ]; then
  echo "Directory $DIR already exists."
else
  mkdir -p "$DIR"
  echo "Directory $DIR created successfully."
fi
```

</details>

---

- [ ] **Task 2: Number Comparison**

Accept two numbers and print which one is greater or if they are equal.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
if [ $# -ne 2 ]; then
  echo "Usage: $0 num1 num2"
  exit 1
fi

if [ $1 -gt $2 ]; then
  echo "$1 is greater than $2"
elif [ $1 -lt $2 ]; then
  echo "$2 is greater than $1"
else
  echo "Both numbers are equal"
fi
```

</details>

---

- [ ] **Task 3: File Existence Loop**

Loop through `/tmp/filelist.txt` and check if each file exists.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash

for line in $(cat filelist.txt); do
	if [[ -f $line ]]; then
		echo "$line exists"
	else
		echo "$line not found"
	fi
done

```

</details>

---

- [ ] **Task 4: Count Lines in Log Files**

Count number of lines in every `.log` file under `/var/log/`.

<details><summary>💡 Answer</summary>

```bash
#/bin/bash

for file in /var/log/*.log;do
	lines="${#file}"
	echo "$file - $lines"
done
```

</details>

---

- [ ] **Task 5: User, Date, Uptime**

Print current user, system date, and uptime using command substitution.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
echo "User: $(whoami)"
echo "Date: $(date)"
echo "Uptime: $(uptime -p)"
```

</details>

---

- [ ] **Task 6: Count Running Services**

Display number of currently running services using `systemctl`.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash

services=$(systemctl list-units --type service --state running | grep 'loaded active' | wc -l)
echo $services
```

</details>

---

- [ ] **Task 7: Basic Calculator**

Perform arithmetic operations using three arguments.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash

read a b c
if [[ $b = '+' ]]; then
	ans=$((a+c))
elif [[ $b = '-' ]] ; then
	ans=$((a-c))
elif [[ $b = '*' ]] ; then
	ans=$((a*c))
else 
	ans=$((a/c))
fi
echo $ans

```

</details>

---

- [ ] **Task 8: Process Count by User**

Take username as input and display number of processes owned by them.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
USER=$1
if [ -z "$USER" ]; then
  echo "Usage: $0 username"
  exit 1
fi
COUNT=$(ps -u $USER --no-headers | wc -l)
echo "$USER has $COUNT running processes."
```

</details>

---

- [ ] **Task 9: Large File Finder**

List all files larger than 100MB in a given directory.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash

read path

files=$(find $path -type f -size +100M 2>/dev/null) 

for file in $files; do
	size=$(du -h $file | awk '{print $1}')
	echo "$file - $size"
done
    
# With files sorted
#!/bin/bash
read path

find "$path" -type f -size +100M -exec du -h {} + 2>/dev/null  | sort -hr

```

</details>

---

- [ ] **Task 10: Print Arguments with Position**

Display each argument and its position number.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
COUNT=1
for arg in "$@"; do
  echo "Argument $COUNT: $arg"
  ((COUNT++))
done
```

</details>

---

- [ ] **Task 11: Disk Usage Warning**

Show all filesystems over 80% usage.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
df -h | awk 'NR>1 {if($5+0 > 80) print $0}'
```

</details>

---

- [ ] **Task 12: Backup Script**

Create a script that copies `/etc` to `/backup/etc_YYYYMMDD`.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
DATE=$(date +%Y%m%d)
DEST=/backup/etc_$DATE
mkdir -p $DEST
cp -r /etc/* $DEST
echo "Backup created at $DEST"
```

</details>

---

- [ ] **Task 13: Service Check Loop**

Check if `sshd`, `firewalld`, and `chronyd` are active. Print status.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
for svc in sshd firewalld chronyd; do
  systemctl is-active --quiet $svc && echo "$svc is running" || echo "$svc is not running"
done
```

</details>

---

- [ ] **Task 14: File Ownership Report**

List files owned by a specific user under `/home`.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
find /home -user $1 -type f -print
```

</details>

---

- [ ] **Task 15: Check Internet Connectivity**

Ping a site 3 times, report if online or offline.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
ping -c3 8.8.8.8 &>/dev/null && echo "Online" || echo "Offline"
```

</details>

---

- [ ] **Task 18: Directory Space Usage**

Show disk usage of each subdirectory under `/var`.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
du -sh /var/* 2>/dev/null
```

</details>

---

- [ ] **Task 19: Log Cleanup Script**

Delete `.log` files older than 7 days in `/var/log/custom/`.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
find /var/log/custom/ -name "*.log" -type f -mtime +7 -delete
echo "Old logs deleted."
```

</details>

---

- [ ] **Task 20: Count Failed SSH Logins**

Print number of failed SSH login attempts from `/var/log/secure`.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
grep -i 'failed password' /var/log/secure | wc -l
```

</details>

---

- [ ] **Task 21: User Home Size Check**

Show top 5 users consuming most space in `/home`.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
du -sh /home/* 2>/dev/null | sort -hr | head -5
```

</details>

---

- [ ] **Task 22: File Extension Counter**

Count how many `.sh`, `.txt`, and `.log` files are in `/scripts/`.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
echo ".sh files: $(ls /scripts/*.sh 2>/dev/null | wc -l)"
echo ".txt files: $(ls /scripts/*.txt 2>/dev/null | wc -l)"
echo ".log files: $(ls /scripts/*.log 2>/dev/null | wc -l)"
```

</details>

---

- [ ] **Task 23: Memory Usage Alert**

Warn if memory usage exceeds 90%.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
USED=$(free | awk '/Mem/{printf("%d", $3/$2*100)}')
if [ $USED -gt 90 ]; then
  echo "Memory usage high: $USED%"
else
  echo "Memory OK: $USED%"
fi
```

</details>

---

- [ ] **Task 25: System Summary Script**

Display hostname, IP, uptime, memory, and disk info.

<details><summary>💡 Answer</summary>

```bash
#!/bin/bash
echo "Hostname: $(hostname)"
echo "IP Address: $(hostname -I | awk '{print $1}')"
echo "Uptime: $(uptime -p)"
echo "Memory: $(free -h | awk '/Mem/{print $3 "/" $2}')"
echo "Disk Usage: $(df -h / | awk 'NR==2{print $5}')"
```

</details>

---

**Pro Tip for RHCSA Candidates:**
```markdown

## Variables
var=5       # no spaces; access with $var; quote when needed "$var"

## Arrays
arr=(1 2 3); echo "${arr[@]}"   # access with ${arr[idx]}

## Arithmetic
((sum=a+b)); echo $sum; ((i++))  # or sum=$(expr $a + $b)

## quote vars, use [[ ]] for complex conditions, check $? for status
```
