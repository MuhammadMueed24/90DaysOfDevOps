[shell_scripting_cheatsheet.md](https://github.com/user-attachments/files/26838529/shell_scripting_cheatsheet.md)
# 🐚 Shell Scripting Cheat Sheet

> A practical quick-reference guide for DevOps engineers.

---

## ⚡ Quick Reference Table

| Topic | Key Syntax | Example |
|-------|-----------|---------|
| Variable | `VAR="value"` | `NAME="DevOps"` |
| Argument | `$1`, `$2` | `./script.sh arg1` |
| If | `if [ condition ]; then` | `if [ -f file ]; then` |
| For loop | `for i in list; do` | `for i in 1 2 3; do` |
| While loop | `while [ condition ]; do` | `while [ $i -lt 10 ]; do` |
| Function | `name() { ... }` | `greet() { echo "Hi"; }` |
| Grep | `grep pattern file` | `grep -i "error" log.txt` |
| Awk | `awk '{print $1}' file` | `awk -F: '{print $1}' /etc/passwd` |
| Sed | `sed 's/old/new/g' file` | `sed -i 's/foo/bar/g' config.txt` |
| Exit code | `$?` | `echo $?  # 0 = success` |
| Trap | `trap 'fn' EXIT` | `trap 'rm -f /tmp/lock' EXIT` |

---

## 1. Basics

### Shebang
Tells the OS which interpreter to use. Must be the **first line** of the script.
```bash
#!/bin/bash        # Use bash
#!/usr/bin/env python3  # Portable: finds interpreter in PATH
```

### Running a Script
```bash
chmod +x script.sh   # Make executable (one-time)
./script.sh          # Run directly
bash script.sh       # Run without chmod (spawns subshell)
source script.sh     # Run in current shell (affects current env)
```

### Comments
```bash
# This is a single-line comment

echo "Hello"  # Inline comment — comes after the code
```

### Variables
No spaces around `=`. Always quote variables to avoid word-splitting surprises.
```bash
NAME="DevOps"           # Declare
echo $NAME              # Use (unquoted — risky with spaces)
echo "$NAME"            # Use (quoted — safe, always prefer this)
echo '$NAME'            # Literal: prints $NAME (no expansion)
AGE=30                  # Numbers don't need quotes
readonly PI=3.14        # Constant — cannot be reassigned
unset NAME              # Delete a variable
```

### Reading User Input
```bash
read NAME                        # Reads into $NAME
read -p "Enter your name: " NAME # With prompt
read -s -p "Password: " PASS    # Silent (no echo — good for passwords)
read -t 5 NAME                   # Timeout after 5 seconds
```

### Command-Line Arguments
```bash
$0    # Script name itself
$1    # First argument
$2    # Second argument
$#    # Number of arguments passed
$@    # All arguments as separate words (use in loops)
$*    # All arguments as a single string
$?    # Exit code of last command (0 = success)

# Example:
# ./deploy.sh myapp production
echo "Deploying $1 to $2"  # Deploying myapp to production
echo "Got $# arguments"    # Got 2 arguments
```

---

## 2. Operators and Conditionals

### String Comparisons
Use `[[ ]]` (preferred — safer, supports regex) or `[ ]` (POSIX).
```bash
[[ "$A" == "$B" ]]   # Equal
[[ "$A" != "$B" ]]   # Not equal
[[ -z "$A" ]]        # True if string is empty (zero length)
[[ -n "$A" ]]        # True if string is NOT empty (non-zero length)
[[ "$A" < "$B" ]]    # Alphabetically less than (inside [[ ]])
[[ "$A" =~ ^[0-9]+$ ]] # Regex match (only in [[ ]])
```

### Integer Comparisons
```bash
[ "$A" -eq "$B" ]    # Equal
[ "$A" -ne "$B" ]    # Not equal
[ "$A" -lt "$B" ]    # Less than
[ "$A" -gt "$B" ]    # Greater than
[ "$A" -le "$B" ]    # Less than or equal
[ "$A" -ge "$B" ]    # Greater than or equal
```

### File Test Operators
```bash
[ -f "$FILE" ]   # Is a regular file (exists and is not a directory)
[ -d "$DIR" ]    # Is a directory
[ -e "$PATH" ]   # Exists (file or directory)
[ -r "$FILE" ]   # Is readable
[ -w "$FILE" ]   # Is writable
[ -x "$FILE" ]   # Is executable
[ -s "$FILE" ]   # Exists and has size > 0 (non-empty)
[ -L "$FILE" ]   # Is a symbolic link
```

### if / elif / else
```bash
if [ "$ENV" == "prod" ]; then
    echo "Production!"
elif [ "$ENV" == "staging" ]; then
    echo "Staging"
else
    echo "Unknown environment"
fi
```

### Logical Operators
```bash
[[ -f "$F" && -r "$F" ]]   # AND — file exists AND is readable
[[ -z "$A" || -z "$B" ]]   # OR  — either A or B is empty
[[ ! -d "$DIR" ]]           # NOT — directory does NOT exist

# Short-circuit in commands:
mkdir -p logs && echo "Created"   # Run second only if first succeeds
rm file.tmp || echo "Delete failed"  # Run second only if first fails
```

### Case Statements
Clean alternative to long if-elif chains, especially for string matching.
```bash
case "$ENV" in
    prod | production)
        echo "Production mode"
        ;;
    staging)
        echo "Staging mode"
        ;;
    dev*)                   # Wildcard: matches dev, development, etc.
        echo "Dev mode"
        ;;
    *)
        echo "Unknown: $ENV"
        exit 1
        ;;
esac
```

---

## 3. Loops

### for Loop — List-Based
```bash
for COLOR in red green blue; do
    echo "Color: $COLOR"
done

# Over a range (brace expansion):
for i in {1..5}; do echo "Count: $i"; done

# Over array elements:
SERVERS=("web1" "web2" "db1")
for SERVER in "${SERVERS[@]}"; do
    echo "Pinging $SERVER"
done
```

### for Loop — C-Style
```bash
for (( i=0; i<5; i++ )); do
    echo "Index: $i"
done
```

### while Loop
Runs while condition is true.
```bash
COUNT=0
while [ $COUNT -lt 5 ]; do
    echo "Count: $COUNT"
    (( COUNT++ ))
done

# Infinite loop with break:
while true; do
    read -p "Continue? (y/n): " ANSWER
    [[ "$ANSWER" == "n" ]] && break
done
```

### until Loop
Runs *until* condition becomes true (opposite of while).
```bash
RETRIES=0
until ping -c1 google.com &>/dev/null; do
    echo "Waiting for network... attempt $RETRIES"
    (( RETRIES++ ))
    sleep 2
done
echo "Network is up!"
```

### Loop Control
```bash
break       # Exit the loop immediately
continue    # Skip to next iteration
break 2     # Break out of 2 nested loops at once
```

### Looping Over Files
```bash
for FILE in *.log; do
    echo "Processing: $FILE"
done

# Safely handle filenames with spaces:
for FILE in /var/log/*.log; do
    [[ -f "$FILE" ]] && wc -l "$FILE"
done
```

### Looping Over Command Output
```bash
# Read line by line from a command:
df -h | while read -r LINE; do
    echo ">>> $LINE"
done

# Read from a file line by line:
while IFS= read -r LINE; do
    echo "$LINE"
done < /etc/hosts
```

---

## 4. Functions

### Defining and Calling
```bash
greet() {
    echo "Hello, World!"
}

greet    # Call — no parentheses when calling
```

### Passing Arguments
Inside a function, `$1`, `$2` etc. refer to the function's own arguments (not the script's).
```bash
deploy() {
    local APP="$1"
    local ENV="$2"
    echo "Deploying $APP to $ENV"
}

deploy "myapp" "production"
```

### Return Values
`return` sends an **exit code** (0–255). Use `echo` to return a **string/value**.
```bash
# Return exit code (for success/fail signaling):
is_root() {
    [ "$(id -u)" -eq 0 ]
    return $?    # 0 if root, 1 otherwise
}

if is_root; then echo "Running as root"; fi

# Return a value (capture with $(...)):
get_timestamp() {
    echo "$(date +%Y%m%d_%H%M%S)"
}

TS=$(get_timestamp)
echo "Timestamp: $TS"
```

### Local Variables
`local` scopes the variable to the function — prevents polluting the global scope.
```bash
counter() {
    local COUNT=0    # Only exists inside this function
    (( COUNT++ ))
    echo "$COUNT"
}

counter
echo "${COUNT:-unset}"  # Prints "unset" — COUNT not visible here
```

---

## 5. Text Processing Commands

### grep — Search Patterns
```bash
grep "error" app.log             # Basic search
grep -i "error" app.log          # Case-insensitive
grep -r "TODO" ./src/            # Recursive through directories
grep -c "error" app.log          # Count matching lines
grep -n "error" app.log          # Show line numbers
grep -v "debug" app.log          # Invert — show NON-matching lines
grep -E "error|warn|fail" app.log  # Extended regex (alternation)
grep -l "error" *.log            # Only print filenames that match
grep -A2 -B2 "CRITICAL" app.log  # 2 lines After and Before match
```

### awk — Column Processing
```bash
awk '{print $1}' file            # Print first column (whitespace-delimited)
awk '{print $1, $3}' file        # Print columns 1 and 3
awk -F: '{print $1}' /etc/passwd # Custom delimiter (colon)
awk '{print NR, $0}' file        # Print line number + full line
awk '$3 > 100 {print $1}' file   # Conditional: print col 1 where col 3 > 100
awk 'BEGIN {print "Start"} {sum+=$1} END {print "Total:", sum}' file
awk '/error/ {count++} END {print count}' app.log  # Count error lines
```

### sed — Stream Editor
```bash
sed 's/foo/bar/' file            # Replace first occurrence per line
sed 's/foo/bar/g' file           # Replace ALL occurrences per line
sed -i 's/foo/bar/g' file        # In-place edit (modifies file directly)
sed -i.bak 's/foo/bar/g' file    # In-place with backup (.bak)
sed '3d' file                    # Delete line 3
sed '/^#/d' file                 # Delete lines starting with # (comments)
sed -n '5,10p' file              # Print only lines 5–10
sed 's/^/  /' file               # Indent every line by 2 spaces
```

### cut — Extract Columns
```bash
cut -d: -f1 /etc/passwd          # Delimiter=colon, extract field 1
cut -d, -f2,4 data.csv           # CSV: extract fields 2 and 4
cut -c1-10 file                  # Extract characters 1–10 of each line
```

### sort — Sort Lines
```bash
sort file                        # Alphabetical (default)
sort -n file                     # Numerical sort
sort -r file                     # Reverse order
sort -u file                     # Unique (remove duplicates while sorting)
sort -k2 file                    # Sort by 2nd field
sort -t: -k3 -n /etc/passwd      # Sort by UID (field 3, colon-delimited)
```

### uniq — Deduplicate
`uniq` only removes **adjacent** duplicates — always `sort` first.
```bash
sort file | uniq                 # Remove duplicates
sort file | uniq -c              # Count occurrences of each line
sort file | uniq -d              # Show only duplicate lines
sort file | uniq -u              # Show only unique (non-duplicate) lines
```

### tr — Translate / Delete Characters
```bash
echo "hello" | tr 'a-z' 'A-Z'   # Lowercase to uppercase
echo "Hello World" | tr -d ' '  # Delete spaces
echo "a:b:c" | tr ':' ','        # Replace colons with commas
echo "aabbcc" | tr -s 'a-z'     # Squeeze repeated chars → "abc"
cat file | tr -cd '[:print:]\n'  # Strip non-printable characters
```

### wc — Word / Line / Char Count
```bash
wc -l file      # Count lines
wc -w file      # Count words
wc -c file      # Count bytes
wc -m file      # Count characters
wc -l *.log     # Count lines in all .log files + total
```

### head / tail
```bash
head file           # First 10 lines (default)
head -n 20 file     # First 20 lines
tail file           # Last 10 lines
tail -n 20 file     # Last 20 lines
tail -f app.log     # Follow — stream new lines as they're written (live logs)
tail -F app.log     # Follow — also handles log rotation
```

---

## 6. Useful One-Liners

```bash
# Find and delete files older than 30 days
find /var/log -name "*.log" -mtime +30 -delete

# Count total lines across all .log files
wc -l /var/log/*.log | tail -1

# Replace a string across multiple files (in-place)
grep -rl "old_domain.com" ./config/ | xargs sed -i 's/old_domain.com/new_domain.com/g'

# Check if a service is running, restart if not
systemctl is-active --quiet nginx || systemctl restart nginx

# Alert if disk usage exceeds 80%
df -h | awk 'NR>1 && int($5) > 80 {print "ALERT: "$6" is at "$5}'

# Tail a log and filter for errors in real time
tail -f /var/log/app.log | grep --line-buffered -i "error\|warn\|critical"

# Show top 10 most frequent IPs in an access log
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -10

# Parse a CSV and print specific columns (2nd and 4th)
awk -F, '{print $2, $4}' data.csv

# Find all files larger than 100MB
find / -type f -size +100M -exec ls -lh {} \; 2>/dev/null

# Extract all unique email addresses from a file
grep -Eo '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' file.txt | sort -u

# Monitor a process and log its CPU/memory every 10 seconds
while true; do ps aux | grep "[n]ginx" | awk '{print $3, $4}'; sleep 10; done

# One-liner JSON parsing with Python (when jq isn't available)
cat data.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(d['key'])"
```

---

## 7. Error Handling and Debugging

### Exit Codes
`0` means success. Any non-zero value means failure.
```bash
echo $?           # Check exit code of last command
exit 0            # Exit script with success
exit 1            # Exit script with generic error
exit 2            # Exit with specific error code (your convention)
```

### Strict Mode — Catch Errors Early
Put this at the top of every production script:
```bash
set -euo pipefail

# set -e     → Exit immediately if any command fails
# set -u     → Treat unset variables as errors (avoids silent $TYPO bugs)
# set -o pipefail → Catch failures in pipelines (e.g., false | true → fails)
```

### Debug Mode
```bash
set -x           # Enable trace — prints each command before executing
set +x           # Disable trace

# Or run a script in debug mode without editing it:
bash -x script.sh

# Debug only a section:
set -x
some_tricky_command
set +x
```

### Handling Errors Manually
```bash
# Check exit code explicitly:
cp source.txt dest.txt
if [ $? -ne 0 ]; then
    echo "ERROR: Copy failed!" >&2   # >&2 sends to stderr
    exit 1
fi

# Inline error handling with ||:
mkdir -p /tmp/myapp || { echo "Cannot create dir" >&2; exit 1; }
```

### Trap — Cleanup on Exit
Runs a command or function when the script exits (normally or on error).
```bash
cleanup() {
    echo "Cleaning up temp files..."
    rm -f /tmp/myapp_lock
}
trap cleanup EXIT      # Always run on exit

trap 'echo "Error on line $LINENO"; exit 1' ERR  # Run on any error

# Common signals to trap:
# EXIT  — script exits (any reason)
# ERR   — any command fails (with set -e)
# INT   — Ctrl+C pressed
# TERM  — kill signal received
trap 'echo "Interrupted!"; exit 130' INT TERM
```

### Defensive Variable Defaults
```bash
# Use default value if variable is unset or empty:
ENV="${1:-development}"          # Default to "development" if $1 not given
LOG_DIR="${LOG_DIR:-/var/log}"   # Use env var or fallback

# Exit with message if variable is required but unset:
: "${DB_PASSWORD:?ERROR: DB_PASSWORD must be set}"
```

---

## 8. Extras Worth Knowing

### Arrays
```bash
FRUITS=("apple" "banana" "cherry")
echo "${FRUITS[0]}"          # First element
echo "${FRUITS[@]}"          # All elements
echo "${#FRUITS[@]}"         # Length
FRUITS+=("date")             # Append
for F in "${FRUITS[@]}"; do echo "$F"; done
```

### String Manipulation
```bash
STR="Hello, World!"
echo "${#STR}"               # Length: 13
echo "${STR:7:5}"            # Substring: World
echo "${STR,,}"              # Lowercase: hello, world!
echo "${STR^^}"              # Uppercase: HELLO, WORLD!
echo "${STR/World/Bash}"     # Replace first: Hello, Bash!
echo "${STR//l/L}"           # Replace all: HeLLo, WorLd!
FILE="report.tar.gz"
echo "${FILE%.gz}"           # Strip shortest suffix: report.tar
echo "${FILE%%.*}"           # Strip longest suffix: report
echo "${FILE#*.}"            # Strip shortest prefix: tar.gz
```

### Here Documents (heredoc)
```bash
cat <<EOF > config.txt
HOST=localhost
PORT=5432
DB=myapp
EOF
```

### Process Substitution
```bash
diff <(sort file1) <(sort file2)   # Compare outputs without temp files
```

### Arithmetic
```bash
(( RESULT = 5 * 3 + 2 ))     # C-style arithmetic
echo $(( 10 / 3 ))           # Integer division: 3
SUM=$(echo "3.14 * 2" | bc)  # Floating point with bc
```

---

*Keep this close. Ship with confidence.* 🚀
