# RHCSA Command Cheatsheet: Essential Text Processing & File Tools

## Table of Contents
- [find](#find)
- [grep](#grep)
- [sed](#sed)
- [awk](#awk)
- [cut](#cut)
- [sort](#sort)
- [uniq](#uniq)
- [tr](#tr)
- [wc](#wc)
- [xargs](#xargs)
- [tee](#tee)

---

## find

Search for files and directories in a hierarchy.

### Basic Syntax
```bash
find [path] [options] [expression]
```

### Common Use Cases

#### Find by Name
```bash
# Find files by exact name
find /etc -name "hosts"

# Case-insensitive search
find /etc -iname "HOSTS"

# Find with wildcards
find /var/log -name "*.log"

# Find files NOT matching pattern
find /tmp -not -name "*.txt"
```

#### Find by Type
```bash
# Find only files
find /home -type f

# Find only directories
find /home -type d

# Find symbolic links
find /usr/bin -type l
```

#### Find by Size
```bash
# Files larger than 100MB
find /var -type f -size +100M

# Files smaller than 1KB
find /tmp -type f -size -1k

# Files exactly 512 bytes
find . -type f -size 512c

# Size units: c (bytes), k (KB), M (MB), G (GB)
```

#### Find by Permissions
```bash
# Files with exact permissions 755
find /usr/bin -type f -perm 755

# Files with at least these permissions (any of these bits set)
find /tmp -type f -perm -644

# Files with any of these permissions
find /home -type f -perm /u+x

# Find SUID files (security audit)
find / -type f -perm -4000 2>/dev/null

# Find SGID files
find / -type f -perm -2000 2>/dev/null
```

#### Find by Ownership
```bash
# Files owned by user
find /home -user john

# Files owned by group
find /var -group apache

# Files with no valid owner (deleted user)
find / -nouser

# Files with no valid group
find / -nogroup
```

#### Find by Time
```bash
# Modified in last 7 days
find /var/log -type f -mtime -7

# Modified more than 30 days ago
find /tmp -type f -mtime +30

# Accessed in last 24 hours
find /home -type f -atime 0

# Changed (metadata) in last hour
find /etc -type f -cmin -60

# Modified in last 2 hours
find /var/log -type f -mmin -120
```

#### Execute Commands on Found Files
```bash
# Delete found files (use with caution!)
find /tmp -type f -name "*.tmp" -delete

# Execute command on each file
find /var/log -type f -name "*.log" -exec gzip {} \;

# Execute with confirmation
find . -type f -name "*.bak" -ok rm {} \;

# Execute command once for all files (more efficient)
find /home -type f -name "*.txt" -exec chmod 644 {} +

# Copy found files to directory
find . -type f -name "*.conf" -exec cp {} /backup/ \;
```

#### Combine Multiple Conditions
```bash
# AND condition (both must be true)
find /var/log -type f -name "*.log" -size +10M

# OR condition
find /etc -type f \( -name "*.conf" -o -name "*.cfg" \)

# Complex example: files owned by user, larger than 1GB, older than 30 days
find /home -user john -type f -size +1G -mtime +30
```

#### Practical RHCSA Examples
```bash
# Find all files modified in last 24 hours in /etc
find /etc -type f -mtime 0

# Find and remove files older than 90 days
find /var/tmp -type f -mtime +90 -delete

# Find files with incorrect SELinux context
find /var/www/html -type f -exec ls -Z {} \;

# Find world-writable files (security issue)
find /home -type f -perm -002

# Find empty files
find /tmp -type f -empty

# Find files and show their details
find /etc -name "*.conf" -ls
```

---

## grep

Search for patterns in files or output.

### Basic Syntax
```bash
grep [options] pattern [file...]
```

### Common Options
```bash
-i    # Ignore case
-v    # Invert match (show non-matching lines)
-r    # Recursive search
-n    # Show line numbers
-c    # Count matching lines
-l    # Show only filenames
-w    # Match whole words only
-A n  # Show n lines After match
-B n  # Show n lines Before match
-C n  # Show n lines of Context (before and after)
-E    # Extended regex (same as egrep)
-o    # Show only matching part
-q    # Quiet mode (exit status only)
```

### Basic Usage
```bash
# Search for pattern in file
grep "root" /etc/passwd

# Case-insensitive search
grep -i "error" /var/log/messages

# Show line numbers
grep -n "Failed" /var/log/secure

# Invert match (lines NOT containing pattern)
grep -v "^#" /etc/ssh/sshd_config

# Count matching lines
grep -c "httpd" /var/log/messages
```

### Context Lines
```bash
# Show 3 lines after match
grep -A 3 "error" /var/log/httpd/error_log

# Show 2 lines before match
grep -B 2 "Failed password" /var/log/secure

# Show 2 lines before and after
grep -C 2 "kernel" /var/log/messages
```

### Recursive Search
```bash
# Search in all files in directory
grep -r "DocumentRoot" /etc/httpd/

# Recursive, ignore case, show filenames only
grep -ril "selinux" /etc/

# Recursive with line numbers
grep -rn "Port 22" /etc/
```

### Regular Expressions
```bash
# Lines starting with pattern
grep "^root" /etc/passwd

# Lines ending with pattern
grep "bash$" /etc/passwd

# Match whole word only
grep -w "root" /etc/passwd

# Match either pattern (basic regex)
grep "root\|admin" /etc/passwd

# Extended regex (easier OR syntax)
grep -E "root|admin" /etc/passwd

# Match any character
grep "r..t" /etc/passwd

# Match repeated characters
grep -E "o{2,}" /etc/passwd  # 'o' appears 2+ times
```

### Practical RHCSA Examples
```bash
# Find users with UID 0 (root privileges)
grep ":0:" /etc/passwd

# Find active users (non-system accounts)
grep -E ":[0-9]{4,}:" /etc/passwd

# Check for failed login attempts
grep "Failed password" /var/log/secure

# Find uncommented lines in config file
grep -v "^#" /etc/ssh/sshd_config | grep -v "^$"

# Search for IP addresses in logs
grep -E "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}" /var/log/httpd/access_log

# Find services listening on ports
ss -tuln | grep ":80"

# Find all configuration files containing specific directive
grep -r "ServerName" /etc/httpd/conf.d/

# Check systemd service status in logs
journalctl | grep -i "sshd"
```

---

## sed

Stream editor for filtering and transforming text.

### Basic Syntax
```bash
sed [options] 'command' file
```

### Common Options
```bash
-i           # Edit file in-place
-i.bak       # Edit in-place with backup
-e           # Multiple commands
-n           # Suppress automatic output (use with p command)
```

### Substitution (Most Common)
```bash
# Basic substitution (first occurrence per line)
sed 's/old/new/' file.txt

# Global substitution (all occurrences)
sed 's/old/new/g' file.txt

# Case-insensitive substitution
sed 's/old/new/gi' file.txt

# Substitute on specific line
sed '3s/old/new/' file.txt

# Substitute on line range
sed '1,5s/old/new/g' file.txt

# Edit file in-place
sed -i 's/old/new/g' file.txt

# Create backup before editing
sed -i.bak 's/old/new/g' file.txt
```

### Delete Lines
```bash
# Delete line 3
sed '3d' file.txt

# Delete lines 2 to 5
sed '2,5d' file.txt

# Delete last line
sed '$d' file.txt

# Delete blank lines
sed '/^$/d' file.txt

# Delete lines matching pattern
sed '/pattern/d' file.txt

# Delete lines NOT matching pattern
sed '/pattern/!d' file.txt
```

### Print Lines
```bash
# Print specific line (with -n to suppress other output)
sed -n '5p' file.txt

# Print line range
sed -n '10,20p' file.txt

# Print lines matching pattern
sed -n '/error/p' file.txt

# Print last line
sed -n '$p' file.txt
```

### Insert and Append
```bash
# Insert line before line 3
sed '3i\New line text' file.txt

# Append line after line 3
sed '3a\New line text' file.txt

# Insert before pattern match
sed '/pattern/i\New line' file.txt

# Append after pattern match
sed '/pattern/a\New line' file.txt
```

### Multiple Commands
```bash
# Using -e for multiple commands
sed -e 's/old/new/g' -e 's/foo/bar/g' file.txt

# Using semicolon
sed 's/old/new/g; s/foo/bar/g' file.txt

# Multiple lines with backslash
sed '
s/old/new/g
s/foo/bar/g
' file.txt
```

### Advanced Examples
```bash
# Replace only on lines matching pattern
sed '/pattern/s/old/new/g' file.txt

# Use different delimiter (useful with paths)
sed 's|/old/path|/new/path|g' file.txt

# Use matched pattern in replacement (&)
sed 's/[0-9]*/(&)/' file.txt  # Wraps numbers in parentheses

# Use capture groups
sed 's/\(pattern\)/\1_suffix/' file.txt

# Change line only if it contains pattern
sed '/pattern/c\Replacement line' file.txt
```

### Practical RHCSA Examples
```bash
# Change SSH port in config
sed -i 's/^#Port 22/Port 2222/' /etc/ssh/sshd_config

# Enable SELinux
sed -i 's/^SELINUX=.*/SELINUX=enforcing/' /etc/selinux/config

# Comment out line
sed -i 's/^PermitRootLogin yes/#PermitRootLogin yes/' /etc/ssh/sshd_config

# Uncomment line
sed -i 's/^#PermitRootLogin no/PermitRootLogin no/' /etc/ssh/sshd_config

# Add nameserver to resolv.conf
sed -i '1i\nameserver 8.8.8.8' /etc/resolv.conf

# Remove comments and blank lines
sed '/^#/d; /^$/d' /etc/ssh/sshd_config

# Change IP address in config
sed -i 's/192.168.1.100/192.168.1.200/g' /etc/httpd/conf/httpd.conf

# Replace all occurrences of user in file
sed -i 's/\bolduser\b/newuser/g' /etc/passwd

# Add line to end of file
sed -i '$a\new last line' file.txt

# Change hostname in /etc/hosts
sed -i 's/oldhost/newhost/g' /etc/hosts
```

---

## awk

Pattern scanning and text processing language.

### Basic Syntax
```bash
awk 'pattern {action}' file
```

### Basic Concepts
- AWK processes input line by line
- Each line is split into fields (default delimiter: whitespace)
- `$0` = entire line
- `$1, $2, $3...` = first, second, third field, etc.
- `NF` = number of fields
- `NR` = current record (line) number

### Print Columns
```bash
# Print first column
awk '{print $1}' file.txt

# Print multiple columns
awk '{print $1, $3}' file.txt

# Print with custom separator
awk '{print $1 ":" $3}' file.txt

# Print all columns
awk '{print $0}' file.txt

# Print last column
awk '{print $NF}' file.txt

# Print second-to-last column
awk '{print $(NF-1)}' file.txt
```

### Field Separator
```bash
# Use colon as delimiter
awk -F: '{print $1}' /etc/passwd

# Use comma as delimiter
awk -F, '{print $2}' data.csv

# Multiple character delimiter
awk -F"::" '{print $1}' file.txt

# Use tab as delimiter
awk -F'\t' '{print $1}' file.txt
```

### Pattern Matching
```bash
# Print lines matching pattern
awk '/pattern/' file.txt

# Print column from lines matching pattern
awk '/pattern/ {print $2}' file.txt

# Pattern in specific column
awk '$3 == "value"' file.txt

# Numeric comparison
awk '$3 > 100' file.txt

# Multiple conditions (AND)
awk '$3 > 100 && $4 < 200' file.txt

# Multiple conditions (OR)
awk '$1 == "root" || $1 == "admin"' file.txt

# NOT match
awk '$1 != "root"' file.txt

# Regular expression match in column
awk '$1 ~ /^root/' /etc/passwd

# Inverse regex match
awk '$1 !~ /^root/' /etc/passwd
```

### BEGIN and END Blocks
```bash
# Execute before processing (print header)
awk 'BEGIN {print "Username"} {print $1}' file.txt

# Execute after processing (print footer)
awk '{sum += $1} END {print "Total:", sum}' numbers.txt

# Both BEGIN and END
awk 'BEGIN {print "Start"} {print $1} END {print "Done"}' file.txt
```

### Calculations
```bash
# Sum of column
awk '{sum += $3} END {print sum}' file.txt

# Average
awk '{sum += $3; count++} END {print sum/count}' file.txt

# Count lines
awk 'END {print NR}' file.txt

# Max value
awk 'max < $3 {max = $3} END {print max}' file.txt

# Running total
awk '{sum += $3; print $1, sum}' file.txt
```

### Conditional Statements
```bash
# If-else
awk '{if ($3 > 100) print $1, "high"; else print $1, "low"}' file.txt

# Multiple conditions
awk '{
    if ($3 > 200) print $1, "high"
    else if ($3 > 100) print $1, "medium"
    else print $1, "low"
}' file.txt
```

### Built-in Variables
```bash
# NR = line number
awk '{print NR, $0}' file.txt

# NF = number of fields
awk '{print NF}' file.txt

# Print lines with specific number of fields
awk 'NF == 5' file.txt

# FILENAME = current filename
awk '{print FILENAME, $1}' file.txt
```

### Output Field Separator (OFS)
```bash
# Change output separator
awk -F: 'BEGIN {OFS="="} {print $1, $3}' /etc/passwd

# Format output nicely
awk 'BEGIN {OFS="\t"} {print $1, $2, $3}' file.txt
```

### Practical RHCSA Examples
```bash
# List usernames from /etc/passwd
awk -F: '{print $1}' /etc/passwd

# List users with UID >= 1000 (regular users)
awk -F: '$3 >= 1000 {print $1}' /etc/passwd

# List users and their home directories
awk -F: '{print $1, $6}' /etc/passwd

# Find users with /bin/bash shell
awk -F: '$7 == "/bin/bash" {print $1}' /etc/passwd

# Calculate total disk usage
df -h | awk 'NR > 1 {print $5}' | sed 's/%//' | awk '{sum += $1} END {print sum "%"}'

# Get process memory usage
ps aux | awk 'NR > 1 {sum += $6} END {print sum/1024 "MB"}'

# Print disk usage over 80%
df -h | awk '$5 > 80 {print $0}'

# Format /etc/passwd output
awk -F: 'BEGIN {print "User\t\tUID\tHome"} {printf "%-15s %-7s %s\n", $1, $3, $6}' /etc/passwd

# Count number of running processes
ps aux | awk 'END {print NR-1}'

# Show IP addresses from ip command
ip addr | awk '/inet / {print $2}'

# Parse log files by date
awk '/Jan 15/ {print $0}' /var/log/messages

# Sum network traffic
awk '{rx += $2; tx += $10} END {print "RX:", rx, "TX:", tx}' network_stats.txt

# Print specific fields from ss output
ss -tuln | awk 'NR > 1 {print $5}' | cut -d: -f1 | sort -u

# Filter and format systemctl output
systemctl list-units --type=service | awk '$4 == "running" {print $1}'
```

### Printf Formatting
```bash
# Formatted output
awk '{printf "%-10s %5d\n", $1, $2}' file.txt

# Format with decimals
awk '{printf "%.2f\n", $3}' file.txt

# Complex formatting
awk -F: '{printf "User: %-15s UID: %5d\n", $1, $3}' /etc/passwd
```

---

## sort

### Basic Sorting
```bash
# Alphabetical sort
sort file.txt

# Reverse sort
sort -r file.txt

# Numeric sort
sort -n numbers.txt

# Remove duplicates while sorting
sort -u file.txt

# Case-insensitive sort
sort -f file.txt

# Save output to file
sort file.txt -o sorted.txt
```

### Sort by Column
```bash
# Sort by second column (space-delimited)
sort -k2 file.txt

# Sort by second column numerically
sort -k2 -n file.txt

# Sort by third column with custom delimiter
sort -t: -k3 -n /etc/passwd

# Sort by size (handles K, M, G)
du -h | sort -h
```
---

## uniq

Report or omit repeated lines (requires sorted input).

### Basic Usage
```bash
# Remove adjacent duplicate lines
uniq file.txt

# Count occurrences
uniq -c file.txt

# Show only duplicated lines
uniq -d file.txt

# Show only unique lines (appear once)
uniq -u file.txt

# Ignore case
uniq -i file.txt
```

### With Sort (Most Common)
```bash
# Sort and remove duplicates
sort file.txt | uniq

# Sort and count occurrences
sort file.txt | uniq -c

# Sort numerically by count
sort file.txt | uniq -c | sort -rn

# Find most common entries
sort file.txt | uniq -c | sort -rn | head -10
```

---

## tr
Translate or delete characters.

### Character Replacement
```bash
# Replace characters
echo "hello" | tr 'a-z' 'A-Z'    # Uppercase

# Lowercase to uppercase
tr 'a-z' 'A-Z' < file.txt

# Replace spaces with underscores
echo "hello world" | tr ' ' '_'

# Replace multiple characters
echo "hello123" | tr 'helo' 'HELO'

# ROT13 cipher
echo "hello" | tr 'a-zA-Z' 'n-za-mN-ZA-M'
```

### Delete Characters
```bash
# Delete specific characters
echo "hello123" | tr -d '0-9'

# Delete spaces
echo "hello world" | tr -d ' '

# Delete newlines
tr -d '\n' < file.txt
```

### Practical RHCSA Examples
```bash

# Convert filename spaces to underscores
echo "my file name.txt" | tr ' ' '_'

# Convert PATH to multiline
echo $PATH | tr ':' '\n'
```

---

## wc
Word, line, character, and byte count.

### Basic Usage
```bash
# Count lines, words, and bytes
wc file.txt

# Count only lines
wc -l file.txt

# Count only words
wc -w file.txt

# Count multiple files
wc -l file1.txt file2.txt file3.txt
```

### Practical RHCSA Examples
```bash
# Count number of users
wc -l /etc/passwd

# Count running processes
ps aux | wc -l

# Count files in directory
ls /etc | wc -l

# Count error lines in log
grep "error" /var/log/messages | wc -l

# Count configuration files
find /etc -name "*.conf" | wc -l

# Count lines in specific systemd units
systemctl list-unit-files --type=service | wc -l

# Count failed login attempts
grep "Failed password" /var/log/secure | wc -l
```

---

## tee
Read from stdin and write to stdout and files simultaneously.
```bash
# Write to file and display
echo "Hello" | tee output.txt

# Append to file
echo "World" | tee -a output.txt

# Write to multiple files
echo "Data" | tee file1.txt file2.txt file3.txt
```
---

## Combining Commands

### Pipe Usage Examples
```bash
# Find and grep
find /var/log -name "*.log" -exec grep "error" {} +

# Find, then process with awk
find /home -type f -name "*.txt" | awk -F/ '{print $NF}'

# Process listing with grep and awk
ps aux | grep httpd | awk '{print $2, $11}'

# Chain sed and grep
sed 's/old/new/g' file.txt | grep "pattern"

# Use all four together
find /etc -name "*.conf" -exec grep -l "ServerName" {} \; | \
  xargs sed -n '/ServerName/p' | \
  awk '{print $2}'
```

### RHCSA Exam Scenarios
```bash
# Find large log files and compress
find /var/log -type f -size +100M -exec gzip {} \;

# Find and remove old files
find /tmp -type f -mtime +7 -delete

# Extract specific users and their shells
grep -E ":[0-9]{4,}:" /etc/passwd | awk -F: '{print $1, $7}'

# Check for failed SSH attempts and count by IP
grep "Failed password" /var/log/secure | \
  awk '{print $(NF-3)}' | sort | uniq -c | sort -rn

# Find SUID files and log them
find / -type f -perm -4000 2>/dev/null > /tmp/suid_files.txt

# Change all occurrences in multiple files
find /etc/httpd -name "*.conf" -exec sed -i 's/80/8080/g' {} \;

# Parse system logs for errors
grep -i "error" /var/log/messages | \
  awk '{print $1, $2, $3, $NF}' | \
  sort | uniq -c

# Complex pipeline: analyze access logs
cat /var/log/httpd/access_log | \
  cut -d' ' -f1 | \
  sort | uniq -c | \
  sort -rn | \
  head -10 | \
  tee top_ips.txt

# Find users, sort by UID, save and display
cut -d: -f1,3 /etc/passwd | \
  sort -t: -k2 -n | \
  tee /tmp/users_by_uid.txt | \
  column -t -s:

# Monitor logs and save important entries
tail -f /var/log/secure | \
  grep --line-buffered "Failed" | \
  tee -a /tmp/failed_attempts.log

# Process multiple files with xargs
find /var/log -name "*.log" -mtime -1 | \
  xargs grep -h "ERROR" | \
  cut -d' ' -f1-3 | \
  sort | uniq -c | \
  sort -rn
```

---

## Quick Reference Table

| Task | Command |
|------|---------|
| Find files by name | `find /path -name "*.txt"` |
| Find files > 100MB | `find /path -size +100M` |
| Search in file | `grep "pattern" file` |
| Recursive search | `grep -r "pattern" /path` |
| Replace in file | `sed -i 's/old/new/g' file` |
| Delete lines | `sed '/pattern/d' file` |
| Print column | `awk '{print $1}' file` |
| Sum column | `awk '{sum += $1} END {print sum}' file` |
| Parse /etc/passwd | `awk -F: '{print $1, $3}' /etc/passwd` |
| Extract field | `cut -d: -f1 /etc/passwd` |
| Sort numerically | `sort -n file` |
| Remove duplicates | `sort -u file` |
| Count duplicates | `sort file \| uniq -c` |
| Uppercase text | `tr 'a-z' 'A-Z'` |
| Count lines | `wc -l file` |
| Apply to multiple files | `find . -name "*.txt" \| xargs grep "pattern"` |
| Save and display | `command \| tee output.txt` |

---

## Tips for RHCSA Exam

1. **Practice without `--help` or `man` pages** - learn the most common options by heart
2. **Remember to use `-i` with sed** for in-place editing
3. **Always use quotes** around awk patterns and sed commands
4. **Test destructive commands** (like `find -delete`) by running without the destructive option first
5. **Use `-exec` carefully** - understand the difference between `\;` and `+`
6. **Combine commands** using pipes to solve complex problems
7. **Remember regex basics**: `^` (start), `# RHCSA Command Cheatsheet: Essential Text Processing & File Tools
