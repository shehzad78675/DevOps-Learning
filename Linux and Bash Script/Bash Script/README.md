<div align="center">

# 🐚 Complete Bash Scripting Mastery Guide

### _From Beginner to Advanced: Master Shell Scripting Like a Pro_

[![Bash](https://img.shields.io/badge/Bash-5.0+-4EAA25?style=for-the-badge&logo=gnu-bash&logoColor=white)](https://www.gnu.org/software/bash/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=for-the-badge)](http://makeapullrequest.com)

<img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/bash/bash-original.svg" width="200" alt="Bash Logo">

**[Getting Started](#-getting-started)** •
**[Debug Mode](#-debug-mode--script-safety)** •
**[Pipes](#-understanding-pipes--stdinstdout)** •
**[Conditionals](#-conditional-statements)** •
**[Loops](#-loops)** •
**[Functions](#-functions)** •
**[Examples](#-complete-script-examples)**

---

</div>

## 📋 Table of Contents

- [🚀 Getting Started](#-getting-started)
- [🔍 Debug Mode & Script Safety](#-debug-mode--script-safety)
- [🔄 Understanding Pipes & Stdin/Stdout](#-understanding-pipes--stdinstdout)
- [✅ Conditional Statements](#-conditional-statements)
- [🔁 Loops](#-loops)
- [📦 Functions](#-functions)
- [🔢 Arrays](#-arrays)
- [📝 String Manipulation](#-string-manipulation)
- [🛡️ Signal Handling with Trap](#️-signal-handling-with-trap)
- [🎨 Colors & Formatting](#-colors--formatting)
- [⚙️ Advanced Features](#️-advanced-features)
- [🚀 Complete Script Examples](#-complete-script-examples)
- [💡 Best Practices](#-best-practices)
- [🎯 Quick Reference](#-quick-reference)

---

<div align="center">

## 🚀 Getting Started

### Prerequisites

</div>

```bash
# Check Bash version
bash --version

# Make script executable
chmod +x script.sh

# Run script
./script.sh

# Or
bash script.sh
```

<div align="center">

### 📁 Basic Script Structure

</div>

```bash
#!/bin/bash
# Shebang: Tells system to use bash interpreter

# Script metadata
# Author: Your Name
# Date: 2026-01-06
# Description: What this script does

set -euo pipefail  # Enable strict mode

# Your code here
echo "Hello, World!"
```

---

<div align="center">

## 🔍 Debug Mode & Script Safety

### _The Three Essential Set Commands_

</div>

```bash
#!/bin/bash

set -x  # Debug mode: Print each command before executing
set -e  # Exit immediately if any command fails
set -o pipefail  # Return exit status of the last failed command in a pipe
```

<div align="center">

| Command           | Purpose         | What It Does                                         |
| ----------------- | --------------- | ---------------------------------------------------- |
| `set -x`          | Debug Mode      | Shows every command with `+` prefix before execution |
| `set -e`          | Exit on Error   | Stops script immediately on any error                |
| `set -o pipefail` | Pipe Safety     | Returns failure status from any command in pipe      |
| `set -u`          | Undefined Check | Exits if undefined variable is used                  |

</div>

### 🎯 Combined Usage

```bash
#!/bin/bash
set -euxo pipefail  # Ultimate robust script mode

# -e: Exit on error
# -u: Exit if undefined variable is used
# -x: Debug mode (print commands)
# -o pipefail: Pipe failure detection

echo "Script starts here..."
```

### 🔧 Controlling Debug Mode Dynamically

```bash
#!/bin/bash

# Enable debug for specific section
set -x
complex_operation
set +x  # Disable debug

# Debug only if DEBUG env var is set
if [ "${DEBUG:-0}" = "1" ]; then
    set -x
fi
```

### 🛠️ Advanced Error Handling

```bash
#!/bin/bash
set -euo pipefail

# Custom error handler
error_exit() {
    echo "❌ Error on line $1" >&2
    exit 1
}

trap 'error_exit $LINENO' ERR

# Ignore errors for specific command
some_command || true

# Temporarily disable exit on error
set +e
risky_command
status=$?
set -e

if [ $status -ne 0 ]; then
    echo "Command failed but continuing..."
fi
```

---

<div align="center">

## 🔄 Understanding Pipes & Stdin/Stdout

### _The Mystery of Lost Data Solved!_

</div>

### ❌ Your Original Problem

```bash
date | echo "today is"
# Output: today is
# ❌ Date output is lost!
```

<div align="center">

### 🔍 Why This Happens

</div>

<div align="center">

| Step | What Happens                                         | Stream                         |         |
| ---- | ---------------------------------------------------- | ------------------------------ | ------- |
| 1️⃣   | `date` outputs: `Tue Jan 6 14:30:00 UTC 2026`        | stdout                         |         |
| 2️⃣   | Pipe `                                               | ` sends output to next command | stdin → |
| 3️⃣   | `echo` **doesn't read stdin**, only prints arguments | ignored                        |         |
| 4️⃣   | Result: Only "today is" appears                      | stdout                         |         |

</div>

### ✅ The Correct Ways

```bash
# Method 1: Command Substitution (Best)
echo "today is $(date)"

# Method 2: Using xargs
date | xargs -I {} echo "today is {}"

# Method 3: Variable assignment
TODAY=$(date)
echo "today is $TODAY"

# Method 4: Using read with pipe (advanced)
date | { read timestamp; echo "today is $timestamp"; }
```

### 📊 Understanding the Three Streams

```bash
# Stream 0: stdin  (Input)
# Stream 1: stdout (Output)
# Stream 2: stderr (Errors)

# Redirect stderr to stdout
command 2>&1

# Redirect both to file
command > output.txt 2>&1
# Or modern syntax:
command &> output.txt

# Redirect stdout to file, stderr to another
command > output.txt 2> errors.txt

# Discard output
command > /dev/null 2>&1

# Read from file
command < input.txt

# Append to file
command >> output.txt

# Here document
cat << EOF
Multiple lines
of text
EOF

# Here string
grep "pattern" <<< "search this string"
```

### 🔗 Advanced Pipe Examples

```bash
# Count files
ls -l | wc -l

# Filter and extract
ps aux | grep "python" | awk '{print $2, $11}'

# Complex pipeline
cat access.log | \
    grep "ERROR" | \
    awk '{print $1}' | \
    sort | \
    uniq -c | \
    sort -rn | \
    head -10

# Named pipes (FIFO)
mkfifo my_pipe
echo "data" > my_pipe &
cat my_pipe

# Process substitution
diff <(ls dir1) <(ls dir2)

# Tee: Write to file AND stdout
echo "Important" | tee important.txt | grep "port"
```

---

<div align="center">

## ✅ Conditional Statements

### _Make Decisions in Your Scripts_

</div>

### 🎯 Basic If Statement

```bash
#!/bin/bash

if [ condition ]; then
    echo "Condition is true"
else
    echo "Condition is false"
fi
```

### 🔧 Your Original Example (Fixed)

```bash
#!/bin/bash

a=4
b=10

# ✅ Correct: Use -gt for numeric comparison
if [ $a -gt $b ]; then
    echo "a is greater than b"
else
    echo "b is greater than a"
fi

# Output: b is greater than a
```

<div align="center">

### 📊 Comparison Operators

#### Numeric Comparisons

</div>

<div align="center">

| Operator | Meaning               | Example         |
| -------- | --------------------- | --------------- |
| `-eq`    | Equal to              | `[ $a -eq $b ]` |
| `-ne`    | Not equal to          | `[ $a -ne $b ]` |
| `-gt`    | Greater than          | `[ $a -gt $b ]` |
| `-lt`    | Less than             | `[ $a -lt $b ]` |
| `-ge`    | Greater than or equal | `[ $a -ge $b ]` |
| `-le`    | Less than or equal    | `[ $a -le $b ]` |

</div>

```bash
# Examples
age=25

if [ $age -eq 25 ]; then
    echo "Age is exactly 25"
fi

if [ $age -ge 18 ]; then
    echo "You are an adult"
fi

if [ $age -lt 65 ]; then
    echo "Not a senior yet"
fi
```

<div align="center">

#### String Comparisons

</div>

<div align="center">

| Operator    | Meaning                       | Example             |
| ----------- | ----------------------------- | ------------------- |
| `=` or `==` | Equal                         | `[ "$a" = "$b" ]`   |
| `!=`        | Not equal                     | `[ "$a" != "$b" ]`  |
| `<`         | Less than (alphabetically)    | `[[ "$a" < "$b" ]]` |
| `>`         | Greater than (alphabetically) | `[[ "$a" > "$b" ]]` |
| `-z`        | String is empty               | `[ -z "$str" ]`     |
| `-n`        | String is not empty           | `[ -n "$str" ]`     |

</div>

```bash
# Examples
name="Alice"

if [ "$name" = "Alice" ]; then
    echo "Hello Alice!"
fi

if [ -z "$name" ]; then
    echo "Name is empty"
else
    echo "Name is: $name"
fi

# Check if variable is set
if [ -n "${VAR:-}" ]; then
    echo "VAR is set"
fi
```

<div align="center">

#### File Test Operators

</div>

<div align="center">

| Operator          | Test                   | Example            |
| ----------------- | ---------------------- | ------------------ |
| `-e`              | File exists            | `[ -e file.txt ]`  |
| `-f`              | Is a regular file      | `[ -f file.txt ]`  |
| `-d`              | Is a directory         | `[ -d /path ]`     |
| `-s`              | File not empty         | `[ -s file.txt ]`  |
| `-r`              | File is readable       | `[ -r file.txt ]`  |
| `-w`              | File is writable       | `[ -w file.txt ]`  |
| `-x`              | File is executable     | `[ -x script.sh ]` |
| `-L`              | Is a symbolic link     | `[ -L link ]`      |
| `-b`              | Is a block device      | `[ -b /dev/sda ]`  |
| `-c`              | Is a character device  | `[ -c /dev/tty ]`  |
| `file1 -nt file2` | file1 newer than file2 | `[ f1 -nt f2 ]`    |
| `file1 -ot file2` | file1 older than file2 | `[ f1 -ot f2 ]`    |

</div>

```bash
# Examples
if [ -f "/etc/passwd" ]; then
    echo "Password file exists"
fi

if [ -d "/tmp" ]; then
    echo "Temp directory exists"
fi

if [ -x "script.sh" ]; then
    echo "Script is executable"
    ./script.sh
fi

if [ ! -e "config.txt" ]; then
    echo "Config file missing!"
    exit 1
fi

# Check file age
if [ "backup.tar" -ot "data.txt" ]; then
    echo "Backup is outdated"
fi
```

### 🎨 If-Elif-Else

```bash
#!/bin/bash

score=85

if [ $score -ge 90 ]; then
    echo "Grade: A 🌟"
elif [ $score -ge 80 ]; then
    echo "Grade: B 👍"
elif [ $score -ge 70 ]; then
    echo "Grade: C 👌"
elif [ $score -ge 60 ]; then
    echo "Grade: D 😐"
else
    echo "Grade: F 😢"
fi
```

### 🆚 Modern Bash: [[]] vs [ ]

<div align="center">

| Feature           | `[ ]` (test)     | `[[ ]]` (bash)     |
| ----------------- | ---------------- | ------------------ | --- | --- |
| POSIX Compatible  | ✅ Yes           | ❌ No              |
| Regex Support     | ❌ No            | ✅ Yes             |
| Pattern Matching  | ❌ No            | ✅ Yes             |
| Logical Operators | `-a`, `-o`       | `&&`, `            |     | `   |
| String Comparison | `<` needs escape | `<` works directly |
| Word Splitting    | ⚠️ Happens       | ✅ Prevented       |

</div>

```bash
# Old style (POSIX compatible)
if [ "$var" = "value" ]; then
    echo "Match"
fi

# New style (Bash only, more features)
if [[ "$var" == "value" ]]; then
    echo "Match"
fi

# Regex matching (only in [[ ]])
if [[ $email =~ ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$ ]]; then
    echo "Valid email"
fi

# Pattern matching
if [[ $filename == *.txt ]]; then
    echo "Text file"
fi

# Logical operators
if [[ $age -ge 18 && $age -le 65 ]]; then
    echo "Working age"
fi

if [[ $status == "active" || $status == "pending" ]]; then
    echo "Account is accessible"
fi

# No word splitting issues
var="hello world"
if [[ $var == "hello world" ]]; then  # Works!
    echo "Match"
fi

if [ $var == "hello world" ]; then  # Error! Word splitting
    echo "This fails"
fi
```

### 🔀 Case Statements

```bash
#!/bin/bash

read -p "Enter a command (start|stop|restart|status): " cmd

case $cmd in
    start)
        echo "Starting service..."
        ;;
    stop)
        echo "Stopping service..."
        ;;
    restart)
        echo "Restarting service..."
        ;;
    status)
        echo "Checking status..."
        ;;
    *)
        echo "❌ Invalid command"
        echo "Usage: start|stop|restart|status"
        exit 1
        ;;
esac

# Pattern matching in case
case $filename in
    *.txt)
        echo "Text file"
        ;;
    *.jpg|*.png|*.gif)
        echo "Image file"
        ;;
    *.sh)
        echo "Shell script"
        ;;
    [0-9]*)
        echo "Starts with number"
        ;;
    *)
        echo "Unknown file type"
        ;;
esac
```

### 🔗 Logical Operators

```bash
# AND: Both conditions must be true
if [ $age -ge 18 ] && [ $age -le 65 ]; then
    echo "Working age"
fi

# OR: At least one condition must be true
if [ $status = "active" ] || [ $status = "pending" ]; then
    echo "Valid status"
fi

# NOT: Negate condition
if [ ! -f "config.txt" ]; then
    echo "Config missing"
fi

# Multiple conditions with [[ ]]
if [[ $age -ge 18 && $has_license == "yes" ]]; then
    echo "Can drive"
fi

# Complex conditions
if [[ ($age -ge 18 || $has_permission == "yes") && $is_banned != "yes" ]]; then
    echo "Access granted"
fi
```

### 🎪 One-Liners

```bash
# Ternary-like operator
[ $age -ge 18 ] && echo "Adult" || echo "Minor"

# Inline if
test -f file.txt && echo "File exists"

# Short-circuit evaluation
[ -f config.txt ] || { echo "Config missing!"; exit 1; }

# Multiple commands
[ $status = "ok" ] && { echo "Success"; exit 0; }
```

---

<div align="center">

## 🔁 Loops

### _Iterate Like a Pro_

</div>

### 🎯 For Loop

```bash
#!/bin/bash

# Range loop (brace expansion)
for i in {1..10}; do
    echo "Number: $i"
done

# Step increment
for i in {0..100..10}; do  # 0, 10, 20, ..., 100
    echo $i
done

# Iterate over array
fruits=("apple" "banana" "orange" "grape")
for fruit in "${fruits[@]}"; do
    echo "🍎 I like $fruit"
done

# Iterate with index
for i in "${!fruits[@]}"; do
    echo "Index $i: ${fruits[$i]}"
done

# C-style for loop
for ((i=0; i<10; i++)); do
    echo "Count: $i"
done

# Iterate over files
for file in *.txt; do
    echo "Processing: $file"
done

# Iterate over command output
for user in $(cat users.txt); do
    echo "User: $user"
done

# Multiple variables
for item in file1:10 file2:20 file3:30; do
    filename="${item%:*}"
    size="${item#*:}"
    echo "$filename is $size MB"
done
```

### 🔄 While Loop

```bash
#!/bin/bash

# Basic while loop
counter=0
while [ $counter -lt 5 ]; do
    echo "Counter: $counter"
    ((counter++))
done

# Read file line by line (best practice)
while IFS= read -r line; do
    echo "Line: $line"
done < input.txt

# With process substitution
while IFS= read -r line; do
    echo "$line"
done < <(command)

# Multiple variables from CSV
while IFS=, read -r name age city; do
    echo "Name: $name, Age: $age, City: $city"
done < data.csv

# Infinite loop
while true; do
    echo "Running... (Ctrl+C to stop)"
    sleep 1
done

# Wait for condition
while [ ! -f "ready.txt" ]; do
    echo "Waiting..."
    sleep 2
done

# Read user input
while true; do
    read -p "Continue? (y/n): " answer
    case $answer in
        [Yy]* ) break;;
        [Nn]* ) exit;;
        * ) echo "Please answer yes or no.";;
    esac
done
```

### 🔁 Until Loop

```bash
#!/bin/bash

# Runs until condition becomes true
counter=0
until [ $counter -ge 5 ]; do
    echo "Counter: $counter"
    ((counter++))
done

# Wait for service to start
until curl -s http://localhost:8080 > /dev/null; do
    echo "Waiting for service..."
    sleep 2
done

# Wait for file
until [ -f "complete.flag" ]; do
    echo "Process not complete..."
    sleep 5
done
```

### 🎛️ Select Loop (Menu System)

```bash
#!/bin/bash

echo "What would you like to do?"
select option in "Create File" "Delete File" "List Files" "Exit"; do
    case $option in
        "Create File")
            read -p "Enter filename: " filename
            touch "$filename"
            echo "✅ Created: $filename"
            ;;
        "Delete File")
            read -p "Enter filename: " filename
            rm -i "$filename"
            ;;
        "List Files")
            ls -lh
            ;;
        "Exit")
            echo "👋 Goodbye!"
            break
            ;;
        *)
            echo "❌ Invalid option"
            ;;
    esac
done

# Custom PS3 prompt
PS3="Select option: "
select choice in "${options[@]}"; do
    # Your code
done
```

### 🎮 Loop Control

```bash
#!/bin/bash

# BREAK: Exit the loop completely
for i in {1..10}; do
    if [ $i -eq 5 ]; then
        echo "Breaking at 5"
        break
    fi
    echo $i
done

# CONTINUE: Skip to next iteration
for i in {1..10}; do
    if [ $((i % 2)) -eq 0 ]; then
        continue  # Skip even numbers
    fi
    echo "Odd: $i"
done

# Nested loops with labels (break 2 exits 2 levels)
for outer in {1..3}; do
    for inner in {1..3}; do
        echo "Outer: $outer, Inner: $inner"
        if [ $outer -eq 2 ] && [ $inner -eq 2 ]; then
            break 2  # Break out of both loops
        fi
    done
done

# Continue with level
for outer in {1..3}; do
    for inner in {1..3}; do
        if [ $inner -eq 2 ]; then
            continue 2  # Continue outer loop
        fi
        echo "$outer-$inner"
    done
done
```

---

<div align="center">

## 📦 Functions

### _Write Reusable Code_

</div>

### 🎯 Basic Function

```bash
#!/bin/bash

# Method 1: Using 'function' keyword
function greet() {
    echo "Hello, World!"
}

# Method 2: Without 'function' keyword (POSIX)
greet() {
    echo "Hello, World!"
}

# Call the function
greet
```

### 📥 Functions with Parameters

```bash
#!/bin/bash

# Parameters: $1, $2, $3, ... $n
greet_user() {
    local name=$1  # First parameter
    local age=$2   # Second parameter
    echo "Hello, $name! You are $age years old."
}

greet_user "Alice" 25
greet_user "Bob" 30

# All parameters
print_all() {
    echo "All arguments: $@"
    echo "Number of arguments: $#"
    echo "Script name: $0"
}

print_all one two three four

# Shift parameters
process_args() {
    echo "First arg: $1"
    shift  # Remove first argument
    echo "Remaining: $@"
    shift 2  # Remove next two arguments
    echo "After shift 2: $@"
}

process_args a b c d e
```

### 📤 Return Values

```bash
#!/bin/bash

# Return exit status (0-255)
is_even() {
    local num=$1
    if [ $((num % 2)) -eq 0 ]; then
        return 0  # Success (true)
    else
        return 1  # Failure (false)
    fi
}

# Use return value
if is_even 4; then
    echo "4 is even"
fi

# Return string via echo
get_user_name() {
    local user_id=$1
    # Fetch from database or file
    echo "User_${user_id}"
}

# Capture output
username=$(get_user_name 123)
echo "Username: $username"

# Return multiple values
get_user_info() {
    local user_id=$1
    echo "John Doe"
    echo "john@example.com"
    echo "30"
}

# Read multiple values
IFS=$'\n' read -d '' -r name email age < <(get_user_info 1)
echo "Name: $name, Email: $email, Age: $age"
```

### 🔧 Local vs Global Variables

```bash
#!/bin/bash

global_var="I'm global"

my_function() {
    local local_var="I'm local"
    global_var="Modified global"  # Modifies global
    another_global="New global"   # Creates global

    echo "Inside function:"
    echo "  Local: $local_var"
    echo "  Global: $global_var"
}

my_function

echo "Outside function:"
# echo "$local_var"  # Error: not accessible
echo "  Global: $global_var"
echo "  Another: $another_global"
```

### 🎨 Advanced Function Features

```bash
#!/bin/bash

# Default parameters
greet() {
    local name=${1:-"Guest"}  # Default to "Guest"
    local greeting=${2:-"Hello"}
    echo "$greeting, $name!"
}

greet
greet "Alice"
greet "Bob" "Hi"

# Named parameters (using associative array)
create_user() {
    local -A params=("$@")
    echo "Creating user:"
    echo "  Name: ${params[name]}"
    echo "  Email: ${params[email]}"
    echo "  Age: ${params[age]}"
}

create_user [name]="Alice" [email]="alice@example.com" [age]=25

# Variable number of arguments
sum() {
    local total=0
    for num in "$@"; do
        ((total += num))
    done
    echo $total
}

result=$(sum 1 2 3 4 5)
echo "Sum: $result"

# Function recursion
factorial() {
    local n=$1
    if [ $n -le 1 ]; then
        echo 1
    else
        local prev=$(factorial $((n-1)))
        echo $((n * prev))
    fi
}

echo "5! = $(factorial 5)"

# Function with error handling
safe_divide() {
    local a=$1
    local b=$2

    if [ $b -eq 0 ]; then
        echo "Error: Division by zero" >&2
        return 1
    fi

    echo $((a / b))
    return 0
}

if result=$(safe_divide 10 2); then
    echo "Result: $result"
fi
```

---

<div align="center">

## 🔢 Arrays

### _Work with Multiple Values_

</div>

### 📝 Indexed Arrays

```bash
#!/bin/bash

# Declare array
fruits=("apple" "banana" "orange")

# Or declare explicitly
declare -a numbers=(1 2 3 4 5)

# Add elements
fruits+=("grape")
fruits[4]="mango"

# Access elements
echo "${fruits[0]}"  # First element
echo "${fruits[@]}"  # All elements
echo "${fruits[*]}"  # All elements (as single string)

# Array length
echo "${#fruits[@]}"

# Get indices
echo "${!fruits[@]}"

# Slice array
echo "${fruits[@]:1:2}"  # From index 1, 2 elements

# Loop through array
for fruit in "${fruits[@]}"; do
    echo "$fruit"
done

# Loop with index
for i in "${!fruits[@]}"; do
    echo "Index $i: ${fruits[$i]}"
done

# Delete element
unset fruits[1]

# Delete entire array
unset fruits
```

### 🗂️ Associative Arrays (Bash 4+)

```bash
#!/bin/bash

# Declare associative array
declare -A person

# Add elements
person[name]="Alice"
person[age]=25
person[city]="New York"

# Or declare with values
declare -A colors=(
    [red]="#FF0000"
    [green]="#00FF00"
    [blue]="#0000FF"
)

# Access elements
echo "${person[name]}"

# Get all keys
echo "${!person[@]}"

# Get all values
echo "${person[@]}"

# Check if key exists
if [ -n "${person[name]:-}" ]; then
    echo "Name exists"
fi

# Loop through associative array
for key in "${!person[@]}"; do
    echo "$key: ${person[$key]}"
done

# Array of arrays (workaround)
declare -A matrix
matrix[0,0]=1
matrix[0,1]=2
matrix[1,0]=3
matrix[1,1]=4

echo "${matrix[0,1]}"
```

### 🎯 Array Operations

```bash
#!/bin/bash

arr=(3 1 4 1 5 9 2 6)

# Sort array
IFS=$'\n' sorted=($(sort -n <<<"${arr[*]}"))
echo "${sorted[@]}"

# Unique elements
IFS=$'\n' unique=($(printf '%s\n' "${arr[@]}" | sort -u))
echo "${unique[@]}"

# Find element
needle=4
for i in "${!arr[@]}"; do
    if [ "${arr[$i]}" = "$needle" ]; then
        echo "Found at index $i"
        break
    fi
done

# Array concatenation
arr1=(1 2 3)
arr2=(4 5 6)
combined=("${arr1[@]}" "${arr2[@]}")

# Split string into array
IFS=',' read -ra parts <<< "apple,banana,orange"
echo "${parts[@]}"

# Join array into string
arr=(a b c d)
joined=$(IFS=,; echo "${arr[*]}")
echo "$joined"  # a,b,c,d
```

---

<div align="center">

## 📝 String Manipulation

### _Master Text Processing_

</div>

```bash
#!/bin/bash

text="Hello World"

# Length
echo "${#text}"  # 11

# Substring
echo "${text:0:5}"   # Hello
echo "${text:6}"     # World
echo "${text: -5}"   # World (from end)

# Replace
echo "${text/o/O}"   # HellO World (first)
echo "${text//o/O}"  # HellO WOrld (all)

# Remove from start
filename="backup_2024_01_06.tar.gz"
echo "${filename#backup_}"        # 2024_01_06.tar.gz
echo "${filename##*.}"            # gz (longest match)

# Remove from end
echo "${filename%.tar.gz}"        # backup_2024_01_06
echo "${filename%%_*}"            # backup (longest match)

# Case conversion
echo "${text^^}"    # HELLO WORLD (uppercase)
echo "${text,,}"    # hello world (lowercase)
echo "${text^}"     # Hello World (first char)

# Default values
echo "${VAR:-default}"    # Use default if unset
echo "${VAR:=default}"    # Set and use default
echo "${VAR:?error msg}"  # Error if unset
echo "${VAR:+alt value}"  # Use alt if set

# String comparison
str1="hello"
str2="world"

if [[ "$str1" < "$str2" ]]; then
    echo "$str1 comes before $str2"
fi

# Contains substring
if [[ "$text" == *"World"* ]]; then
    echo "Contains World"
fi

# Starts with
if [[ "$text" == "Hello"* ]]; then
    echo "Starts with Hello"
fi

# Ends with
if [[ "$text" == *"World" ]]; then
    echo "Ends with World"
fi

# Regex match
if [[ "$text" =~ ^[A-Z][a-z]+ ]]; then
    echo "Matches pattern"
fi

# Trim whitespace
trim() {
    local var="$*"
    var="${var#"${var%%[![:space:]]*}"}"
    var="${var%"${var##*[![:space:]]}"}"
    echo "$var"
}

result=$(trim "  hello world  ")
echo "[$result]"
```

---

<div align="center">

## 🛡️ Signal Handling with Trap

### _Graceful Error Handling_

</div>

### 📡 Common Signals

<div align="center">

| Signal  | Number | Description         | Can Trap? |
| ------- | ------ | ------------------- | --------- |
| SIGINT  | 2      | Ctrl+C pressed      | ✅ Yes    |
| SIGTERM | 15     | Termination request | ✅ Yes    |
| SIGKILL | 9      | Force kill          | ❌ No     |
| SIGHUP  | 1      | Terminal closed     | ✅ Yes    |
| SIGQUIT | 3      | Quit signal         | ✅ Yes    |
| EXIT    | 0      | Script exit         | ✅ Yes    |
| ERR     | -      | Command error       | ✅ Yes    |
| DEBUG   | -      | Before each command | ✅ Yes    |

</div>

### 🎯 Basic Trap Usage

```bash
#!/bin/bash

# Cleanup function
cleanup() {
    echo ""
    echo "🧹 Cleaning up temporary files..."
    rm -f /tmp/myapp.*
    echo "✅ Cleanup complete!"
}

# Set trap
trap cleanup EXIT

# Your script code
echo "Running script..."
temp_file="/tmp/myapp.$"
touch "$temp_file"
sleep 3
echo "Done!"

# cleanup() automatically runs on exit
```

### 🎨 Advanced Trap Examples

```bash
#!/bin/bash
set -euo pipefail

# Multiple signal handling
handle_signal() {
    local signal=$1
    echo ""
    echo "⚠️  Received signal: $signal"
    echo "🧹 Performing cleanup..."

    # Cleanup code
    pkill -P $  # Kill child processes
    rm -rf /tmp/myapp.*

    echo "👋 Exiting gracefully..."
    exit 0
}

trap 'handle_signal SIGINT' SIGINT
trap 'handle_signal SIGTERM' SIGTERM

# Error trap with line number
trap 'echo "❌ Error on line $LINENO"; exit 1' ERR

# Debug trap
trap 'echo "➤ Executing: $BASH_COMMAND"' DEBUG

# Disable trap temporarily
trap - SIGINT  # Disable SIGINT trap

# Restore trap
trap 'handle_signal SIGINT' SIGINT

# Long running process
echo "Running... Press Ctrl+C to stop"
while true; do
    echo "Working..."
    sleep 2
done
```

### 🔒 Resource Management

```bash
#!/bin/bash
set -euo pipefail

# File descriptor management
exec 3>/tmp/myapp.lock

# Cleanup function
cleanup() {
    echo "Releasing resources..."
    exec 3>&-  # Close file descriptor
    rm -f /tmp/myapp.lock
}

trap cleanup EXIT

# Database connection cleanup
db_cleanup() {
    if [ -n "${DB_CONN:-}" ]; then
        echo "Closing database connection..."
        # Close DB connection
    fi
}

trap db_cleanup EXIT
```

---

<div align="center">

## 🎨 Colors & Formatting

### _Make Your Scripts Beautiful_

</div>

```bash
#!/bin/bash

# Color codes
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
MAGENTA='\033[0;35m'
CYAN='\033[0;36m'
WHITE='\033[1;37m'
NC='\033[0m' # No Color

# Text formatting
BOLD='\033[1m'
DIM='\033[2m'
ITALIC='\033[3m'
UNDERLINE='\033[4m'
BLINK='\033[5m'
REVERSE='\033[7m'
HIDDEN='\033[8m'

# Background colors
BG_RED='\033[41m'
BG_GREEN='\033[42m'
BG_YELLOW='\033[43m'
BG_BLUE='\033[44m'

# Usage
echo -e "${GREEN}✅ Success!${NC}"
echo -e "${RED}❌ Error!${NC}"
echo -e "${YELLOW}⚠️  Warning!${NC}"
echo -e "${BLUE}ℹ️  Information${NC}"

# Formatted messages
success() {
    echo -e "${GREEN}✅ $1${NC}"
}

error() {
    echo -e "${RED}❌ $1${NC}" >&2
}

warning() {
    echo -e "${YELLOW}⚠️  $1${NC}"
}

info() {
    echo -e "${BLUE}ℹ️  $1${NC}"
}

# Use functions
success "Operation completed"
error "Something went wrong"
warning "Please check your input"
info "Processing data..."

# Progress bar
progress_bar() {
    local current=$1
    local total=$2
    local width=50
    local percentage=$((current * 100 / total))
    local completed=$((width * current / total))

    printf "\r["
    printf "%${completed}s" | tr ' ' '='
    printf "%$((width - completed))s" | tr ' ' '-'
    printf "] %d%%" $percentage
}

# Example usage
for i in {1..100}; do
    progress_bar $i 100
    sleep 0.05
done
echo ""

# Spinner
spinner() {
    local pid=$1
    local delay=0.1
    local spinstr='|/-\'
    while ps -p $pid > /dev/null 2>&1; do
        local temp=${spinstr#?}
        printf " [%c]  " "$spinstr"
        local spinstr=$temp${spinstr%"$temp"}
        sleep $delay
        printf "\b\b\b\b\b\b"
    done
    printf "    \b\b\b\b"
}

# Box drawing
box() {
    local text="$1"
    local length=${#text}
    local line=$(printf '%*s' $((length + 4)) '' | tr ' ' '─')

    echo "┌${line}┐"
    echo "│  ${text}  │"
    echo "└${line}┘"
}

box "Hello World"

# Clear screen
clear

# Move cursor
tput cup 10 20  # Move to row 10, column 20
echo "Text at position"

# Hide/Show cursor
tput civis  # Hide cursor
sleep 2
tput cnorm  # Show cursor
```

---

<div align="center">

## ⚙️ Advanced Features

### _Level Up Your Scripts_

</div>

### 🔧 Command Line Argument Parsing

```bash
#!/bin/bash

# Simple parsing
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            echo "Usage: $0 [OPTIONS]"
            echo "Options:"
            echo "  -h, --help     Show this help"
            echo "  -v, --verbose  Verbose output"
            echo "  -f, --file     Specify file"
            exit 0
            ;;
        -v|--verbose)
            VERBOSE=1
            shift
            ;;
        -f|--file)
            FILE="$2"
            shift 2
            ;;
        *)
            echo "Unknown option: $1"
            exit 1
            ;;
    esac
done

# Using getopts (builtin)
while getopts "hvf:" opt; do
    case $opt in
        h)
            echo "Help message"
            exit 0
            ;;
        v)
            VERBOSE=1
            ;;
        f)
            FILE="$OPTARG"
            ;;
        \?)
            echo "Invalid option: -$OPTARG" >&2
            exit 1
            ;;
    esac
done

shift $((OPTIND-1))
```

### 📊 Math Operations

```bash
#!/bin/bash

# Arithmetic expansion
result=$((5 + 3))
echo $result

# Various operations
a=10
b=3

echo $((a + b))   # 13
echo $((a - b))   # 7
echo $((a * b))   # 30
echo $((a / b))   # 3
echo $((a % b))   # 1
echo $((a ** b))  # 1000 (power)

# Increment/Decrement
((a++))
((b--))
((a += 5))
((b *= 2))

# Floating point (using bc)
result=$(echo "scale=2; 10 / 3" | bc)
echo $result  # 3.33

# More complex calculations
pi=$(echo "scale=10; 4*a(1)" | bc -l)
sqrt=$(echo "scale=2; sqrt(16)" | bc)

# Random numbers
random=$((RANDOM % 100))  # 0-99
random_range=$(shuf -i 1-100 -n 1)
```

### 🕐 Date and Time

```bash
#!/bin/bash

# Current date/time
current=$(date)
echo "$current"

# Formatted date
date "+%Y-%m-%d"              # 2026-01-06
date "+%Y-%m-%d %H:%M:%S"     # 2026-01-06 14:30:00
date "+%A, %B %d, %Y"         # Tuesday, January 06, 2026

# Unix timestamp
timestamp=$(date +%s)
echo $timestamp

# Date arithmetic
tomorrow=$(date -d "tomorrow" +%Y-%m-%d)
yesterday=$(date -d "yesterday" +%Y-%m-%d)
next_week=$(date -d "7 days" +%Y-%m-%d)

# Time difference
start=$(date +%s)
sleep 2
end=$(date +%s)
duration=$((end - start))
echo "Took $duration seconds"

# Parse date
parsed=$(date -d "2026-01-06" +%s)

# Format specific date
date -d "@$timestamp" "+%Y-%m-%d %H:%M:%S"
```

### 🌐 Network Operations

```bash
#!/bin/bash

# Check if URL is reachable
if curl -s --head http://example.com | grep "200 OK" > /dev/null; then
    echo "Site is up"
fi

# Download file
curl -O https://example.com/file.txt
wget https://example.com/file.txt

# POST request
curl -X POST -d "data=value" https://api.example.com/endpoint

# Check port
if nc -z localhost 8080; then
    echo "Port 8080 is open"
fi

# Get public IP
public_ip=$(curl -s ifconfig.me)
echo "Public IP: $public_ip"

# DNS lookup
nslookup example.com
dig example.com
```

### 💾 File Operations

```bash
#!/bin/bash

# Read file
while IFS= read -r line; do
    echo "$line"
done < file.txt

# Write to file
echo "content" > file.txt   # Overwrite
echo "more" >> file.txt     # Append

# File info
stat file.txt
du -h file.txt             # Size
wc -l file.txt            # Line count

# Find files
find . -name "*.txt"
find . -type f -mtime -7  # Modified in last 7 days
find . -size +1M          # Larger than 1MB

# Bulk operations
find . -name "*.log" -exec rm {} \;
find . -name "*.txt" -exec grep "pattern" {} +

# File permissions
chmod 755 script.sh
chmod u+x script.sh
chown user:group file.txt

# Create temp file/directory
tmpfile=$(mktemp)
tmpdir=$(mktemp -d)
```

### 🔍 Process Management

```bash
#!/bin/bash

# Check if process is running
if pgrep -x "nginx" > /dev/null; then
    echo "Nginx is running"
fi

# Get process PID
pid=$(pgrep -x "nginx")

# Kill process
pkill nginx
kill $pid
kill -9 $pid  # Force kill

# Run in background
long_process &
bg_pid=$!
echo "Background PID: $bg_pid"

# Wait for background process
wait $bg_pid

# Job control
jobs                # List background jobs
fg %1               # Bring job 1 to foreground
bg %1               # Send job 1 to background

# Parallel execution
command1 & command2 & command3 &
wait  # Wait for all

# Process substitution
diff <(sort file1) <(sort file2)

# Timeout
timeout 30s long_running_command
```

---

<div align="center">

## 🚀 Complete Script Examples

### _Real-World Production-Ready Scripts_

</div>

### 📦 Example 1: System Backup Script

```bash
#!/bin/bash
set -euo pipefail

# Configuration
readonly SCRIPT_NAME=$(basename "$0")
readonly SCRIPT_DIR=$(cd "$(dirname "$0")" && pwd)
readonly SCRIPT_VERSION="1.0.0"

#######################################
# Configuration
#######################################
readonly CONFIG_FILE="${CONFIG_FILE:-/etc/myapp.conf}"
readonly LOG_DIR="/var/log/myapp"

#######################################
# Global variables
#######################################
VERBOSE=0
DRY_RUN=0

#######################################
# Utility functions
#######################################
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*"
}

error() {
    echo "ERROR: $*" >&2
    exit 1
}

#######################################
# Business logic functions
#######################################
process_data() {
    local input=$1
    # Your logic here
}

#######################################
# Main function
#######################################
main() {
    # Parse arguments
    # Main logic
    # Cleanup
}

# Run main function
main "$@"
```

### 🔍 Example 2: Log File Analyzer

```bash
#!/bin/bash
set -euo pipefail

readonly LOGFILE="${1:-/var/log/app.log}"
readonly OUTPUT_DIR="./reports"
readonly REPORT_FILE="${OUTPUT_DIR}/log_report_$(date +%Y%m%d_%H%M%S).txt"

# Colors
readonly CYAN='\033[0;36m'
readonly GREEN='\033[0;32m'
readonly RED='\033[0;31m'
readonly YELLOW='\033[1;33m'
readonly BOLD='\033[1m'
readonly NC='\033[0m'

# Check if log file exists
if [ ! -f "$LOGFILE" ]; then
    echo -e "${RED}❌ Error: Log file not found: $LOGFILE${NC}"
    exit 1
fi

# Create output directory
mkdir -p "$OUTPUT_DIR"

# Generate report
{
    echo "════════════════════════════════════════"
    echo "         LOG ANALYSIS REPORT"
    echo "════════════════════════════════════════"
    echo "File: $LOGFILE"
    echo "Generated: $(date '+%Y-%m-%d %H:%M:%S')"
    echo "════════════════════════════════════════"
    echo ""

    echo "📊 STATISTICS"
    echo "────────────────────────────────────────"
    echo "Total lines:     $(wc -l < "$LOGFILE")"
    echo "File size:       $(du -h "$LOGFILE" | cut -f1)"
    echo "ERROR count:     $(grep -c "ERROR" "$LOGFILE" || echo 0)"
    echo "WARNING count:   $(grep -c "WARNING" "$LOGFILE" || echo 0)"
    echo "INFO count:      $(grep -c "INFO" "$LOGFILE" || echo 0)"
    echo ""

    echo "🔴 TOP 10 ERRORS"
    echo "────────────────────────────────────────"
    grep "ERROR" "$LOGFILE" | \
        sed 's/.*ERROR/ERROR/' | \
        sort | uniq -c | sort -rn | head -10
    echo ""

    echo "⚠️  TOP 10 WARNINGS"
    echo "────────────────────────────────────────"
    grep "WARNING" "$LOGFILE" | \
        sed 's/.*WARNING/WARNING/' | \
        sort | uniq -c | sort -rn | head -10
    echo ""

    echo "📅 ERRORS BY HOUR"
    echo "────────────────────────────────────────"
    grep "ERROR" "$LOGFILE" | \
        grep -oP '\d{2}:\d{2}:\d{2}' | \
        cut -d: -f1 | \
        sort | uniq -c | \
        sort -rn
    echo ""

    echo "🔍 RECENT ERRORS (Last 5)"
    echo "────────────────────────────────────────"
    grep "ERROR" "$LOGFILE" | tail -5

} | tee "$REPORT_FILE"

echo ""
echo -e "${GREEN}✅ Report saved to: $REPORT_FILE${NC}"
```

### 🌐 Example 3: Website Monitor

```bash
#!/bin/bash
set -euo pipefail

readonly CONFIG_FILE="websites.txt"
readonly LOG_FILE="monitor.log"
readonly CHECK_INTERVAL=60

# Colors
readonly GREEN='\033[0;32m'
readonly RED='\033[0;31m'
readonly NC='\033[0m'

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"
}

check_website() {
    local url=$1
    local response=$(curl -s -o /dev/null -w "%{http_code}" --max-time 10 "$url")
    local exit_code=$?

    if [ $exit_code -eq 0 ] && [ "$response" -eq 200 ]; then
        echo -e "${GREEN}✅${NC} $url - OK (HTTP $response)"
        log "OK - $url - HTTP $response"
        return 0
    else
        echo -e "${RED}❌${NC} $url - FAILED (HTTP $response, Exit: $exit_code)"
        log "FAILED - $url - HTTP $response, Exit: $exit_code"

        # Send alert (example using mail)
        echo "Website $url is down!" | mail -s "Alert: Website Down" admin@example.com
        return 1
    fi
}

main() {
    if [ ! -f "$CONFIG_FILE" ]; then
        echo "❌ Config file not found: $CONFIG_FILE"
        echo "Create a file with one URL per line"
        exit 1
    fi

    echo "🔍 Starting website monitor..."
    echo "📋 Reading websites from: $CONFIG_FILE"
    echo "⏱️  Check interval: ${CHECK_INTERVAL}s"
    echo ""

    while true; do
        echo "────────────────────────────────────────"
        echo "Checking websites at $(date '+%H:%M:%S')"
        echo "────────────────────────────────────────"

        while IFS= read -r url || [ -n "$url" ]; do
            [ -z "$url" ] && continue
            [[ "$url" =~ ^#  ]] && continue

            check_website "$url"
        done < "$CONFIG_FILE"

        echo ""
        sleep "$CHECK_INTERVAL"
    done
}

# Handle graceful shutdown
trap 'echo ""; log "Monitor stopped"; exit 0' SIGINT SIGTERM

main "$@"
```

### 🗃️ Example 4: Database Backup with Retention

```bash
#!/bin/bash
set -euo pipefail

readonly DB_NAME="myapp"
readonly DB_USER="backup_user"
readonly DB_PASS="secure_password"
readonly BACKUP_DIR="/backups/database"
readonly DATE=$(date +%Y%m%d_%H%M%S)
readonly BACKUP_FILE="${BACKUP_DIR}/${DB_NAME}_${DATE}.sql.gz"
readonly RETENTION_DAYS=30

# S3 Configuration (optional)
readonly S3_BUCKET="s3://my-backups/database"
readonly ENABLE_S3_UPLOAD=true

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*"
}

error_exit() {
    echo "❌ ERROR: $1" >&2
    exit 1
}

cleanup() {
    if [ $? -ne 0 ]; then
        log "Backup failed, cleaning up..."
        rm -f "$BACKUP_FILE"
    fi
}

trap cleanup EXIT

main() {
    log "Starting database backup for: $DB_NAME"

    # Create backup directory
    mkdir -p "$BACKUP_DIR"

    # Dump database
    log "Dumping database..."
    mysqldump -u"$DB_USER" -p"$DB_PASS" "$DB_NAME" | gzip > "$BACKUP_FILE" || \
        error_exit "Database dump failed"

    # Check backup size
    size=$(du -h "$BACKUP_FILE" | cut -f1)
    log "✅ Backup created: $BACKUP_FILE ($size)"

    # Upload to S3 (if enabled)
    if [ "$ENABLE_S3_UPLOAD" = true ]; then
        log "Uploading to S3..."
        aws s3 cp "$BACKUP_FILE" "$S3_BUCKET/" || \
            error_exit "S3 upload failed"
        log "✅ Uploaded to S3"
    fi

    # Clean old backups
    log "Cleaning backups older than $RETENTION_DAYS days..."
    find "$BACKUP_DIR" -name "${DB_NAME}_*.sql.gz" -mtime +$RETENTION_DAYS -delete

    remaining=$(find "$BACKUP_DIR" -name "${DB_NAME}_*.sql.gz" | wc -l)
    log "✅ Cleanup complete. Remaining backups: $remaining"

    log "🎉 Backup process completed successfully!"
}

main "$@"
```

### 📊 Example 5: System Health Check

```bash
#!/bin/bash
set -euo pipefail

readonly THRESHOLD_CPU=80
readonly THRESHOLD_MEM=85
readonly THRESHOLD_DISK=90
readonly ALERT_EMAIL="admin@example.com"

# Colors
readonly GREEN='\033[0;32m'
readonly YELLOW='\033[1;33m'
readonly RED='\033[0;31m'
readonly NC='\033[0m'

check_cpu() {
    local cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
    local cpu_int=${cpu_usage%.*}

    echo -n "CPU Usage: ${cpu_usage}% "

    if [ "$cpu_int" -gt "$THRESHOLD_CPU" ]; then
        echo -e "${RED}❌ HIGH${NC}"
        return 1
    elif [ "$cpu_int" -gt $((THRESHOLD_CPU - 10)) ]; then
        echo -e "${YELLOW}⚠️  WARNING${NC}"
        return 2
    else
        echo -e "${GREEN}✅ OK${NC}"
        return 0
    fi
}

check_memory() {
    local mem_usage=$(free | grep Mem | awk '{printf "%.0f", $3/$2 * 100}')

    echo -n "Memory Usage: ${mem_usage}% "

    if [ "$mem_usage" -gt "$THRESHOLD_MEM" ]; then
        echo -e "${RED}❌ HIGH${NC}"
        return 1
    elif [ "$mem_usage" -gt $((THRESHOLD_MEM - 10)) ]; then
        echo -e "${YELLOW}⚠️  WARNING${NC}"
        return 2
    else
        echo -e "${GREEN}✅ OK${NC}"
        return 0
    fi
}

check_disk() {
    local alerts=0

    while IFS= read -r line; do
        local mountpoint=$(echo "$line" | awk '{print $6}')
        local usage=$(echo "$line" | awk '{print $5}' | tr -d '%')

        echo -n "Disk $mountpoint: ${usage}% "

        if [ "$usage" -gt "$THRESHOLD_DISK" ]; then
            echo -e "${RED}❌ HIGH${NC}"
            ((alerts++))
        elif [ "$usage" -gt $((THRESHOLD_DISK - 10)) ]; then
            echo -e "${YELLOW}⚠️  WARNING${NC}"
        else
            echo -e "${GREEN}✅ OK${NC}"
        fi
    done < <(df -h | grep -vE '^Filesystem|tmpfs|cdrom')

    return $alerts
}

check_services() {
    local services=("nginx" "mysql" "redis")
    local failed=0

    for service in "${services[@]}"; do
        echo -n "Service $service: "
        if systemctl is-active --quiet "$service"; then
            echo -e "${GREEN}✅ Running${NC}"
        else
            echo -e "${RED}❌ Stopped${NC}"
            ((failed++))
        fi
    done

    return $failed
}

send_alert() {
    local message=$1
    echo "$message" | mail -s "System Health Alert" "$ALERT_EMAIL"
}

main() {
    echo "══════════════════════════════════════"
    echo "     SYSTEM HEALTH CHECK"
    echo "══════════════════════════════════════"
    echo "Time: $(date '+%Y-%m-%d %H:%M:%S')"
    echo "Host: $(hostname)"
    echo "══════════════════════════════════════"
    echo ""

    local issues=0

    check_cpu || ((issues++))
    check_memory || ((issues++))
    echo ""
    check_disk || issues=$((issues + $?))
    echo ""
    check_services || issues=$((issues + $?))

    echo ""
    echo "══════════════════════════════════════"
    if [ $issues -eq 0 ]; then
        echo -e "${GREEN}✅ All checks passed!${NC}"
    else
        echo -e "${RED}❌ Found $issues issue(s)${NC}"
        send_alert "System health check found $issues issues. Check the system immediately."
    fi
    echo "══════════════════════════════════════"
}

main "$@"
```

---

<div align="center">

## 💡 Best Practices

### _Write Production-Ready Scripts_

</div>

### ✅ Essential Best Practices

1. **Always use strict mode**

   ```bash
   set -euo pipefail
   ```

2. **Quote your variables**

   ```bash
   # ❌ Wrong
   echo $var

   # ✅ Correct
   echo "$var"
   ```

3. **Use [[]] for conditions**

   ```bash
   # ❌ Old style
   if [ "$var" = "value" ]; then

   # ✅ Modern Bash
   if [[ "$var" == "value" ]]; then
   ```

4. **Check command existence**

   ```bash
   if ! command -v docker &> /dev/null; then
       echo "Docker not installed"
       exit 1
   fi
   ```

5. **Use functions for organization**

   ```bash
   main() {
       # Your main logic
   }

   main "$@"
   ```

6. **Always add cleanup traps**

   ```bash
   cleanup() {
       rm -f /tmp/myapp.*
   }
   trap cleanup EXIT
   ```

7. **Validate inputs**

   ```bash
   if [ $# -eq 0 ]; then
       echo "Usage: $0 <argument>"
       exit 1
   fi
   ```

8. **Use readonly for constants**

   ```bash
   readonly CONFIG_FILE="/etc/myapp.conf"
   readonly MAX_RETRIES=3
   ```

9. **Proper error messages**

   ```bash
   error() {
       echo "ERROR: $1" >&2
       exit 1
   }
   ```

10. **Test scripts before deploying**

    ```bash
    # Syntax check
    bash -n script.sh

    # Use shellcheck
    shellcheck script.sh
    ```

---

<div align="center">

## 🎯 Quick Reference

### _Cheat Sheet for Common Operations_

</div>

### 🔧 Debugging

| Command                | Description              |
| ---------------------- | ------------------------ |
| `set -x`               | Enable debug mode        |
| `set +x`               | Disable debug mode       |
| `bash -x script.sh`    | Run script in debug mode |
| `bash -n script.sh`    | Syntax check only        |
| `shellcheck script.sh` | Lint script              |
| `set -v`               | Print shell input lines  |

### 📊 Exit Codes

| Code    | Meaning                     |
| ------- | --------------------------- |
| `0`     | Success                     |
| `1`     | General error               |
| `2`     | Misuse of shell builtin     |
| `126`   | Command cannot execute      |
| `127`   | Command not found           |
| `128+n` | Fatal error signal "n"      |
| `130`   | Script terminated by Ctrl+C |
| `255`   | Exit status out of range    |

```bash
# Check last command exit status
echo $?

# Exit with specific code
exit 0    # Success
exit 1    # Error
```

### 🔤 Variables

| Syntax            | Description                   |
| ----------------- | ----------------------------- |
| `${var}`          | Variable value                |
| `${var:-default}` | Use default if unset          |
| `${var:=default}` | Set and use default           |
| `${var:?error}`   | Error if unset                |
| `${var:+value}`   | Use value if set              |
| `${#var}`         | Length of variable            |
| `${var#pattern}`  | Remove shortest match (start) |
| `${var##pattern}` | Remove longest match (start)  |
| `${var%pattern}`  | Remove shortest match (end)   |
| `${var%%pattern}` | Remove longest match (end)    |
| `${var/old/new}`  | Replace first occurrence      |
| `${var//old/new}` | Replace all occurrences       |
| `${var^}`         | Uppercase first char          |
| `${var^^}`        | Uppercase all                 |
| `${var,}`         | Lowercase first char          |
| `${var,,}`        | Lowercase all                 |

### 🔢 Arithmetic

```bash
# Basic operations
$((a + b))          # Addition
$((a - b))          # Subtraction
$((a * b))          # Multiplication
$((a / b))          # Division
$((a % b))          # Modulo
$((a ** b))         # Power

# Increment/Decrement
((var++))           # Post-increment
((++var))           # Pre-increment
((var--))           # Post-decrement
((--var))           # Pre-decrement

# Compound assignment
((var += 5))        # Add and assign
((var -= 3))        # Subtract and assign
((var *= 2))        # Multiply and assign
((var /= 2))        # Divide and assign
```

### 🎨 Special Variables

| Variable        | Description                        |
| --------------- | ---------------------------------- |
| `$0`            | Script name                        |
| `$1, $2, ...`   | Positional parameters              |
| `$#`            | Number of parameters               |
| `$@`            | All parameters (as separate words) |
| `$*`            | All parameters (as single word)    |
| `$?`            | Exit status of last command        |
| `$`             | Process ID of script               |
| `$!`            | PID of last background command     |
| `$-`            | Current shell options              |
| `$_`            | Last argument of previous command  |
| `$BASH_VERSION` | Bash version                       |
| `$HOSTNAME`     | Hostname                           |
| `$RANDOM`       | Random number (0-32767)            |
| `$LINENO`       | Current line number                |
| `$SECONDS`      | Seconds since script started       |

### 📝 String Operations

```bash
# Length
${#string}

# Substring
${string:position}
${string:position:length}

# Replace
${string/substring/replacement}    # First occurrence
${string//substring/replacement}   # All occurrences

# Remove prefix
${string#prefix}     # Shortest match
${string##prefix}    # Longest match

# Remove suffix
${string%suffix}     # Shortest match
${string%%suffix}    # Longest match

# Case conversion
${string^}           # First char uppercase
${string^^}          # All uppercase
${string,}           # First char lowercase
${string,,}          # All lowercase
```

### 🔀 Redirections

```bash
command > file              # Redirect stdout to file
command >> file             # Append stdout to file
command 2> file             # Redirect stderr to file
command 2>> file            # Append stderr to file
command &> file             # Redirect both to file
command > file 2>&1         # Redirect both to file
command < file              # Read stdin from file
command << EOF              # Here document
command <<< "string"        # Here string
command 2>&1 | tee file     # Redirect and display
command > /dev/null 2>&1    # Discard all output
exec 3< file                # Open file for reading (fd 3)
exec 4> file                # Open file for writing (fd 4)
exec 3<&-                   # Close file descriptor 3
```

### 🔍 Test Operators

#### File Tests

```bash
-e file     # File exists
-f file     # Regular file
-d file     # Directory
-L file     # Symbolic link
-s file     # File not empty
-r file     # File readable
-w file     # File writable
-x file     # File executable
-b file     # Block device
-c file     # Character device
-p file     # Named pipe
-S file     # Socket
-t fd       # File descriptor is terminal
-O file     # Owned by user
-G file     # Owned by group
f1 -nt f2   # f1 newer than f2
f1 -ot f2   # f1 older than f2
f1 -ef f2   # f1 and f2 are same file
```

#### String Tests

```bash
-z string   # String is empty
-n string   # String is not empty
s1 = s2     # Strings are equal
s1 != s2    # Strings are not equal
s1 < s2     # s1 sorts before s2
s1 > s2     # s1 sorts after s2
```

#### Numeric Tests

```bash
n1 -eq n2   # Equal
n1 -ne n2   # Not equal
n1 -lt n2   # Less than
n1 -le n2   # Less than or equal
n1 -gt n2   # Greater than
n1 -ge n2   # Greater than or equal
```

### 🎪 One-Liners

```bash
# Create directory and cd into it
mkdir -p /path/to/dir && cd $_

# Backup file with timestamp
cp file.txt{,.bak.$(date +%Y%m%d)}

# Find and replace in files
find . -name "*.txt" -exec sed -i 's/old/new/g' {} +

# Count files in directory
find . -type f | wc -l

# Kill process by name
pkill -9 process_name

# Get file size in human readable format
du -sh /path/to/dir

# Monitor log file in real time
tail -f /var/log/app.log

# Run command every 2 seconds
watch -n 2 'command'

# Create tar.gz archive
tar -czf archive.tar.gz /path/to/dir

# Extract tar.gz archive
tar -xzf archive.tar.gz

# Download file with curl
curl -O https://example.com/file.zip

# Get public IP
curl -s ifconfig.me

# Generate random password
openssl rand -base64 32

# Find large files
find / -type f -size +100M

# Check disk usage
df -h

# Check memory usage
free -h

# Show process tree
pstree

# Network connections
netstat -tuln

# Replace spaces with underscores in filenames
for f in *\ *; do mv "$f" "${f// /_}"; done
```

---

<div align="center">

## 📚 Additional Resources

### _Learn More_

</div>

### 🔗 Useful Links

- **Official Documentation**: [GNU Bash Manual](https://www.gnu.org/software/bash/manual/)
- **Style Guide**: [Google Shell Style Guide](https://google.github.io/styleguide/shellguide.html)
- **Linter**: [ShellCheck](https://www.shellcheck.net/)
- **Tutorial**: [Bash Guide for Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/)
- **Advanced**: [Advanced Bash-Scripting Guide](https://tldp.org/LDP/abs/html/)

### 🛠️ Recommended Tools

| Tool              | Description                            |
| ----------------- | -------------------------------------- |
| `shellcheck`      | Static analysis tool for shell scripts |
| `shfmt`           | Shell script formatter                 |
| `bats`            | Bash Automated Testing System          |
| `bash-completion` | Programmable completion for bash       |
| `tldr`            | Simplified man pages                   |

### 📖 Books

- **"Learning the bash Shell"** by Cameron Newham
- **"Bash Cookbook"** by Carl Albing & JP Vossen
- **"Wicked Cool Shell Scripts"** by Dave Taylor

### 🎓 Practice Platforms

- [HackerRank - Linux Shell](https://www.hackerrank.com/domains/shell)
- [Exercism - Bash Track](https://exercism.org/tracks/bash)
- [LeetCode - Shell](https://leetcode.com/problemset/shell/)

---

<div align="center">

## 💭 Final Thoughts

</div>

Bash scripting is a journey, not a destination. Every script you write teaches you something new. Don't be discouraged if things don't work perfectly the first time - even experienced developers spend time debugging their scripts!

### 🌱 Growth Mindset

Remember:

- **Start Simple** - Begin with basic scripts and gradually add complexity
- **Read Others' Code** - Learn from open-source projects and community scripts
- **Practice Regularly** - The more you script, the more natural it becomes
- **Ask Questions** - The Bash community is welcoming and helpful
- **Document Everything** - Your future self will thank you

### 🎯 Key Takeaways

As you continue your Bash scripting journey, keep these principles in mind:

1. **Safety First** - Always use `set -euo pipefail`
2. **Quote Variables** - Prevent word splitting and globbing issues
3. **Handle Errors** - Plan for failure, clean up resources
4. **Write Readable Code** - Clear is better than clever
5. **Test Thoroughly** - Use shellcheck and test edge cases

### 🌟 Your Next Steps

Now that you have this comprehensive guide:

- **Bookmark it** for quick reference
- **Try the examples** - Run them, modify them, break them, fix them
- **Build something** - Automate a task you do regularly
- **Share your knowledge** - Help others learn
- **Keep learning** - Technology evolves, and so should your skills

### 💪 You've Got This!

Whether you're automating your daily tasks, managing servers, processing data, or building deployment pipelines, Bash is a powerful ally. This guide will be here whenever you need it.

Remember: **Every expert was once a beginner.** Every complex script started as a simple `echo "Hello, World!"`.

---

<div align="center">

## 🙏 Thank You

Thank you for taking the time to read this guide. We hope it serves you well in your scripting adventures.

May your scripts always execute successfully, your pipes never break, and your exit codes always be zero! 😊

</div>

---

<div align="center">

### 🌈 Keep Scripting, Keep Learning, Keep Growing

<img src="https://media.giphy.com/media/LmNwrBhejkK9EFP504/giphy.gif" width="300" alt="Happy Coding">

</div>

---

<div align="center">

## 📬 Stay Connected

</div>

- 💼 **LinkedIn**: Share your Bash projects
- 🐦 **Twitter**: Join #BashScripting discussions
- 💬 **Discord/Slack**: Join Bash communities
- 📝 **Blog**: Write about your scripting experiences
- 🎥 **YouTube**: Watch and create Bash tutorials

---

<div align="center">

## 🤝 Contributing

</div>

We welcome contributions! Here's how you can help:

1. 🐛 **Report bugs** - Open an issue
2. 💡 **Suggest features** - Share your ideas
3. 📝 **Improve documentation** - Fix typos, add examples
4. 🔧 **Submit pull requests** - Add new scripts or improvements

---

<div align="center">

### Code Style Guidelines

</div>

- Use 4 spaces for indentation
- Follow the structure shown in this guide
- Add comments for complex logic
- Include error handling
- Test your scripts before submitting

---

<div align="center">

## ⭐ Show Your Support

</div>

If you found this guide helpful, please consider:

- ⭐ **Starring** the repository
- 🐦 **Sharing** with others
- 💬 **Providing feedback**
- 🤝 **Contributing** improvements

---

<div align="center">

### ✍️ Author's Note

_"The command line is not just a tool; it's a superpower. With Bash scripting, you're not just using your computer - you're commanding it. May this guide empower you to automate the tedious, simplify the complex, and create the extraordinary."_

</div>

---

<div align="center">

## 🎊 One Last Thing...

Before you close this guide, here's a small gift - a motivational script:

</div>

```bash
#!/bin/bash

motivate() {
    local quotes=(
        "Code is like humor. When you have to explain it, it's bad."
        "First, solve the problem. Then, write the code."
        "The best error message is the one that never shows up."
        "Simplicity is the soul of efficiency."
        "Make it work, make it right, make it fast."
    )

    local random_index=$((RANDOM % ${#quotes[@]}))
    echo "💡 ${quotes[$random_index]}"
}

echo "════════════════════════════════════════════════"
echo "       🎯 You're Ready to Script! 🎯"
echo "════════════════════════════════════════════════"
motivate
echo ""
echo "Now go forth and automate! 🚀"
echo "════════════════════════════════════════════════"
```

---

<div align="center">

### 🌟 May the Source Be With You! 🌟

---

<sub>Built with 🧡 by developers, for developers</sub>

<sub>Remember: Every line of code is a step toward mastery</sub>

<sub>Happy Scripting! 🐚✨</sub>

---

<img src="https://forthebadge.com/images/badges/built-with-love.svg" alt="Built with Love">
<img src="https://forthebadge.com/images/badges/powered-by-coffee.svg" alt="Powered by Coffee">
<img src="https://forthebadge.com/images/badges/makes-people-smile.svg" alt="Makes People Smile">

</div>

---

```bash
# The journey of a thousand scripts begins with a single line
echo "#!/bin/bash"

# Now go write yours! 🚀
```

<div align="center">

### 🎭 The End... or Rather, The Beginning!

_This isn't goodbye, it's "see you in the terminal!" 😉_

</div>

---

<div align="center">

**Version 1.0.0** | **January 2026** | **Made for the Community**

**[⬆ Back to Top](#-complete-bash-scripting-mastery-guide)**

</div>

<!-- </div> BACKUP_DIR="/backup"
readonly SOURCE_DIR="/home/user/documents"
readonly LOG_FILE="/var/log/backup.log"
readonly DATE=$(date +%Y%m%d_%H%M%S)
readonly BACKUP_FILE="backup_${DATE}.tar.gz"
readonly MAX_BACKUPS=7

# Colors

readonly GREEN='\033[0;32m'
readonly RED='\033[0;31m'
readonly YELLOW='\033[1;33m'
readonly NC='\033[0m'

# Logging

log() {
echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"
}

success() {
echo -e "${GREEN}✅ $1${NC}" | tee -a "$LOG_FILE"
}

error() {
echo -e "${RED}❌ $1${NC}" | tee -a "$LOG_FILE" >&2
}

warning() {
echo -e "${YELLOW}⚠️  $1${NC}" | tee -a "$LOG_FILE"
}

# Cleanup function

cleanup() {
local exit_code=$?
    if [ $exit_code -ne 0 ]; then
        error "Backup failed with exit code: $exit_code"
        rm -f "${BACKUP_DIR}/${BACKUP_FILE}"
fi
exit $exit_code
}

trap cleanup EXIT

# Main script

main() {
log "Starting backup process..."

    # Check if source exists
    if [ ! -d "$SOURCE_DIR" ]; then
        error "Source directory not found: $SOURCE_DIR"
        exit 1
    fi

    # Create backup directory
    if [ ! -d "$BACKUP_DIR" ]; then
        log "Creating backup directory: $BACKUP_DIR"
        mkdir -p "$BACKUP_DIR"
    fi

    # Create backup
    log "Creating backup: ${BACKUP_FILE}"
    tar -czf "${BACKUP_DIR}/${BACKUP_FILE}" "$SOURCE_DIR" 2>&1 | tee -a "$LOG_FILE"

    # Get backup size
    size=$(du -h "${BACKUP_DIR}/${BACKUP_FILE}" | cut -f1)
    success "Backup completed: ${BACKUP_FILE} (${size})"

    # Rotate old backups
    log "Rotating old backups (keeping last $MAX_BACKUPS)..."
    cd "$BACKUP_DIR"
    ls -t backup_*.tar.gz | tail -n +$((MAX_BACKUPS + 1)) | xargs -r rm

    # Summary
    log "Backup summary:"
    log "  File: ${BACKUP_FILE}"
    log "  Size: ${size}"
    log "  Backups in directory: $(ls -1 backup_*.tar.gz | wc -l)"

    success "All operations completed successfully!"

}

main "$@"

````

---

<div align="center">

### 📚 Code Organization

```bash
#!/bin/bash
set -euo pipefail

#######################################
# Script metadata
#######################################
readonly SCRIPT_NAME=$(basename "$0")
readonly
```
```` -->
