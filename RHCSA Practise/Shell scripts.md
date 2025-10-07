

* [ ] **Task 1: Directory Check and Creation**
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

* [ ] **Task 2: Number Comparison**
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

````
</details>

---

- [ ] **Task 3: File Existence Loop**  
Loop through `/tmp/filelist.txt` and check if each file exists.

<details><summary>💡 Answer</summary>
```bash
#!/bin/bash
while read FILE; do
  if [ -f "$FILE" ]; then
    echo "$FILE exists."
  else
    echo "$FILE not found."
  fi
done < /tmp/filelist.txt
````

</details>

---

* [ ] **Task 4: Count Lines in Log Files**
  Count number of lines in every `.log` file under `/var/log/`.

<details><summary>💡 Answer</summary>
```bash
#!/bin/bash
for f in /var/log/*.log; do
  [ -f "$f" ] || continue
  echo "$f : $(wc -l < "$f") lines"
done
```
</details>

---

* [ ] **Task 5: User, Date, Uptime**
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

* [ ] **Task 6: Count Running Services**
  Display number of currently running services using `systemctl`.

<details><summary>💡 Answer</summary>
```bash
#!/bin/bash
COUNT=$(systemctl list-units --type=service --state=running | grep -vE 'LOAD|LIST' | wc -l)
echo "Active services: $COUNT"
```
</details>

---

* [ ] **Task 7: Basic Calculator**
  Perform arithmetic operations using three arguments.

<details><summary>💡 Answer</summary>
```bash
#!/bin/bash
if [ $# -ne 3 ]; then
  echo "Usage: $0 num1 operator num2"
  exit 1
fi
case $2 in
  +) echo $(($1 + $3)); ;;
  -) echo $(($1 - $3)); ;;
  \*) echo $(($1 * $3)); ;;
  /) echo $(($1 / $3)); ;;
  *) echo "Invalid operator." ;;
esac
```
</details>

---

* [ ] **Task 8: Process Count by User**
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

* [ ] **Task 9: Large File Finder**
  List all files larger than 100MB in a given directory.

<details><summary>💡 Answer</summary>
```bash
#!/bin/bash
find "$1" -type f -size +100M -exec ls -lh {} \; | awk '{print $9, $5}'
```
</details>

---

* [ ] **Task 10: Print Arguments with Position**
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

*(You can continue in the same style for Tasks 11–25 to make all 25+ tasks markable with `- [ ]` checkboxes.)*
