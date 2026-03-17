# Day 16 – Shell Scripting Basics

## Task Overview

Start your shell scripting journey — learn the fundamentals every script needs.

You will:
- Understand shebang (`#!/bin/bash`) and why it matters
- Work with variables, `echo`, and `read`
- Write basic if-else conditions

---

## Task 1: Your First Script

**Script: hello.sh**

```bash
#!/bin/bash
echo "Hello, DevOps!"
```

**Run Commands:**

```bash
chmod +x hello.sh
./hello.sh
```

**Output:**

```
Hello, DevOps!
```

> **Note:** Removing the shebang may cause the script to run with a different shell, which can lead to unexpected behavior.

---

## Task 2: Variables

**Script: variables.sh**

```bash
#!/bin/bash
NAME="Maddy"
ROLE="QA Automation Engineer"
echo "Hello, I am $NAME and I am a $ROLE"
```

**Single vs Double Quotes Example:**

```bash
echo 'Hello, I am $NAME'   # Output: Hello, I am $NAME
echo "Hello, I am $NAME"   # Output: Hello, I am Maddy
```

- Single quotes `' '` preserve literal text.
- Double quotes `" "` allow variable expansion.

---

## Task 3: User Input with `read`

**Script: greet.sh**

```bash
#!/bin/bash
read -p "Enter your name: " NAME
read -p "Enter your favourite tool: " TOOL
echo "Hello $NAME, your favourite tool is $TOOL"
```

**Sample Run:**

```
Enter your name: Maddy
Enter your favourite tool: Selenium
Hello Maddy, your favourite tool is Selenium
```

---

## Task 4: If-Else Conditions

**Script: check_number.sh**

```bash
#!/bin/bash
read -p "Enter a number: " NUM
if [ $NUM -gt 0 ]; then
    echo "Positive"
elif [ $NUM -lt 0 ]; then
    echo "Negative"
else
    echo "Zero"
fi
```

**Sample Run:**

```
Enter a number: -5
Negative
```

---

**Script: file_check.sh**

```bash
#!/bin/bash
read -p "Enter filename: " FILE
if [ -f "$FILE" ]; then
    echo "File exists."
else
    echo "File does not exist."
fi
```

**Sample Run:**

```
Enter filename: test.txt
File does not exist.
```

---

## Task 5: Combine It All

**Script: server_check.sh**

```bash
#!/bin/bash
SERVICE="nginx"
read -p "Do you want to check the status of $SERVICE? (y/n): " CHOICE
if [ "$CHOICE" == "y" ]; then
    STATUS=$(systemctl is-active $SERVICE)
    if [ "$STATUS" == "active" ]; then
        echo "$SERVICE is running."
    else
        echo "$SERVICE is not running."
    fi
else
    echo "Skipped."
fi
```

**Sample Run:**

```
Do you want to check the status of nginx? (y/n): y
nginx is running.
```

---

## Key Learnings

- **Shebang (`#!/bin/bash`)** tells the system which shell interpreter to use.
- **Variables** are created without spaces and can be expanded using `$VAR`.
- **Conditional statements** (`if-else`) and **user input** (`read`) allow dynamic scripting and decision-making.
