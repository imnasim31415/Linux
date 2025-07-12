# Bash Conditional Statements

## 1. Basic `if` Statement

```bash
if CONDITION; then
    commands
fi
```

**Example:**
```bash
if [ "$name" = "nasim" ]; then
    echo "Welcome, master."
fi
```

---

## 2. `if-else` Statement

```bash
if CONDITION; then
    commands_if_true
else
    commands_if_false
fi
```

**Example:**
```bash
if [ -f "file.txt" ]; then
    echo "File exists"
else
    echo "File not found"
fi
```

---

## 3. `if-elif-else` Statement

```bash
if CONDITION1; then
    commands1
elif CONDITION2; then
    commands2
else
    commands3
fi
```

**Example:**
```bash
if [ $x -gt 100 ]; then
    echo "Greater than 100"
elif [ $x -eq 100 ]; then
    echo "Exactly 100"
else
    echo "Less than 100"
fi
```

---

## 4. Logical Operators

### `&&` (AND)
```bash
if [ -f "$file" ] && [ -s "$file" ]; then
    echo "File exists and is not empty"
fi
```

### `||` (OR)
```bash
if [ "$user" = "admin" ] || [ "$user" = "root" ]; then
    echo "Privileged user"
fi
```

### `!` (NOT)
```bash
if [ ! -f "file.txt" ]; then
    echo "File does not exist"
fi
```

---

## 5. Recommended: `[[ ... ]]` (Modern test command)

```bash
if [[ "$str" = "hello" ]]; then
    echo "Hi"
fi

if [[ "$filename" =~ \.txt$ ]]; then
    echo "It's a text file"
fi
```

---

## 6. Test Operators Summary

### Numeric Comparisons

| Operator | Meaning           |
|----------|-------------------|
| `-eq`    | Equal to          |
| `-ne`    | Not equal         |
| `-gt`    | Greater than      |
| `-lt`    | Less than         |
| `-ge`    | Greater or equal  |
| `-le`    | Less or equal     |

---

### String Comparisons

| Operator | Meaning           |
|----------|-------------------|
| `=`      | Equal             |
| `!=`     | Not equal         |
| `-z`     | Empty string      |
| `-n`     | Non-empty string  |

---

### File Tests

| Operator | Meaning                  | Example                                      |
|----------|--------------------------|----------------------------------------------|
| `-e`     | File exists              | `[ -e "file.txt" ] && echo "Exists"`         |
| `-f`     | Regular file exists      | `[ -f "file.txt" ] && echo "It's a file"`    |
| `-d`     | Directory exists         | `[ -d "/etc" ] && echo "Directory exists"`   |
| `-s`     | File is not empty        | `[ -s "file.txt" ] && echo "Not empty"`      |
| `-r`     | File is readable         | `[ -r "file.txt" ] && echo "Readable"`       |
| `-w`     | File is writable         | `[ -w "file.txt" ] && echo "Writable"`       |
| `-x`     | File is executable       | `[ -x "script.sh" ] && echo "Executable"`    |



---

## 7. Short Form Conditionals

```bash
[ -f "file.txt" ] && echo "File exists"
[ "$x" -gt 10 ] || echo "x is not greater than 10"
```

---

## 8. Using `test` Command (Same as `[ ... ]`)

```bash
if test -f "file.txt"; then
    echo "File exists"
fi
```

---

## 9. Exit Status Conditions

```bash
if command; then
    echo "Command succeeded"
else
    echo "Command failed"
fi
```

```bash
if curl -s google.com > /dev/null; then
    echo "Internet is up"
fi
```

---


## 10. Nested `if` Statements

You can nest `if` statements inside each other.

```bash
if [ "$user" = "nasim" ]; then
    if [ "$access" = "granted" ]; then
        echo "Welcome, Nasim."
    else
        echo "Access denied."
    fi
fi
```

---

## 11. Case Statements (Alternative to if-elif chains)

```bash
case "$var" in
    start)
        echo "Starting..."
        ;;
    stop)
        echo "Stopping..."
        ;;
    restart)
        echo "Restarting..."
        ;;
    *)
        echo "Unknown command"
        ;;
esac
```

---

## 12. Arithmetic Conditions using `(( ))`

```bash
if (( x > 10 )); then
    echo "x is greater than 10"
fi
```

Supports C-style syntax:
```bash
(( x = x + 1 ))
```

---

## 13. String Comparison using `[[ ]]`

```bash
if [[ "$a" == "$b" ]]; then
    echo "Strings are equal"
fi

if [[ "$a" != "$b" ]]; then
    echo "Strings are not equal"
fi
```

You can also use pattern matching:
```bash
if [[ "$file" == *.txt ]]; then
    echo "It's a text file"
fi
```

---

## 14. File Descriptors and Redirect Checks

Check if a file descriptor is open:
```bash
if [ -t 1 ]; then
    echo "Standard output is a terminal"
fi
```

Redirect output conditionally:
```bash
if [ -w "log.txt" ]; then
    echo "Writing log..." >> log.txt
fi
```

---

## 15. Function Return Status in Conditionals

```bash
my_func() {
    return 1
}

if my_func; then
    echo "Success"
else
    echo "Failure"
fi
```

---

## 16. Multiple Conditions with `[[ ]]` and `&&` / `||`

```bash
if [[ $x -gt 10 && $x -lt 20 ]]; then
    echo "x is between 10 and 20"
fi
```

```bash
if [[ -z "$input" || "$input" = "quit" ]]; then
    echo "Exiting..."
fi
```

---
