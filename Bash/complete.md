# 🚀 Complete Bash Scripting Course for DevOps Engineers

> **From Zero to Hero** - A comprehensive, hands-on guide with examples and outputs

---

## 📚 Table of Contents

1. [Introduction to Bash](#1-introduction-to-bash)
2. [Basic Commands & File Operations](#2-basic-commands--file-operations)
3. [Variables & Data Types](#3-variables--data-types)
4. [Input/Output Operations](#4-inputoutput-operations)
5. [Conditional Statements](#5-conditional-statements)
6. [Loops & Iterations](#6-loops--iterations)
7. [Functions](#7-functions)
8. [Arrays & String Manipulation](#8-arrays--string-manipulation)
9. [Text Processing Tools](#9-text-processing-tools)
10. [File & Directory Management](#10-file--directory-management)
11. [Process Management](#11-process-management)
12. [System Monitoring](#12-system-monitoring)
13. [Network Commands](#13-network-commands)
14. [Permissions & Ownership](#14-permissions--ownership)
15. [Error Handling & Debugging](#15-error-handling--debugging)
16. [Advanced Topics](#16-advanced-topics)
17. [Real-World DevOps Scripts](#17-real-world-devops-scripts)

---

## 1. Introduction to Bash

### What is Bash?

**Bash** (Bourne Again Shell) is a command-line interface and scripting language for Unix/Linux systems.

### Your First Script

```bash
#!/bin/bash
# This is a comment
echo "Hello, DevOps World!"
```

**How to run:**
```bash
chmod +x script.sh    # Make executable
./script.sh           # Run script
```

**Output:**
```
Hello, DevOps World!
```

### Shebang Line Variations

```bash
#!/bin/bash           # Most common
#!/usr/bin/env bash   # Portable (finds bash in PATH)
#!/bin/sh             # POSIX shell
```

---

## 2. Basic Commands & File Operations

### 2.1 Navigation Commands

#### `pwd` - Print Working Directory

```bash
pwd
```

**Output:**
```
/home/username/projects
```

**Flags:**
- `-L` : Logical path (default, shows symlinks)
- `-P` : Physical path (resolves symlinks)

```bash
pwd -P
```

---

#### `ls` - List Directory Contents

```bash
ls
```

**Output:**
```
file1.txt  file2.sh  directory1  directory2
```

**Common Flags:**

```bash
ls -l                 # Long format with details
```

**Output:**
```
-rw-r--r-- 1 user group  4096 Nov 18 10:30 file1.txt
drwxr-xr-x 2 user group  4096 Nov 18 11:00 directory1
```

```bash
ls -lh                # Human-readable sizes
```

**Output:**
```
-rw-r--r-- 1 user group  4.0K Nov 18 10:30 file1.txt
-rw-r--r-- 1 user group  2.5M Nov 18 10:35 large_file.zip
```

```bash
ls -la                # Show hidden files (starting with .)
```

**Output:**
```
drwxr-xr-x 5 user group  4096 Nov 18 10:30 .
drwxr-xr-x 3 user group  4096 Nov 17 09:00 ..
-rw-r--r-- 1 user group   220 Nov 15 08:00 .bashrc
-rw-r--r-- 1 user group  4096 Nov 18 10:30 file1.txt
```

```bash
ls -lt                # Sort by modification time
ls -lS                # Sort by size
ls -lr                # Reverse order
ls -R                 # Recursive listing
```

**Practical Example:**
```bash
ls -lhSr              # List files, human-readable, sorted by size, reversed
```

**Output:**
```
-rw-r--r-- 1 user group  1.2K Nov 18 10:30 small.txt
-rw-r--r-- 1 user group  256K Nov 18 10:35 medium.pdf
-rw-r--r-- 1 user group  2.5M Nov 18 10:40 large.zip
```

---

#### `cd` - Change Directory

```bash
cd /path/to/directory     # Absolute path
cd documents              # Relative path
cd ..                     # Go up one level
cd ~                      # Go to home directory
cd -                      # Go to previous directory
cd                        # Also goes to home
```

**Example Session:**
```bash
pwd
# Output: /home/user

cd /var/log
pwd
# Output: /var/log

cd -
pwd
# Output: /home/user
```

---

### 2.2 File Operations

#### `touch` - Create Empty Files or Update Timestamps

```bash
touch newfile.txt
```

**Flags:**
- `-a` : Change only access time
- `-m` : Change only modification time
- `-t` : Use specific timestamp

```bash
touch -t 202311180900.00 file.txt    # Set specific time
```

---

#### `cat` - Concatenate and Display Files

```bash
cat file.txt
```

**Output:**
```
This is line 1
This is line 2
This is line 3
```

**Advanced Usage:**

```bash
cat file1.txt file2.txt              # Display multiple files
cat file1.txt file2.txt > merged.txt # Merge files
cat > newfile.txt                    # Create file (Ctrl+D to save)
cat -n file.txt                      # Show line numbers
```

**Output with `-n`:**
```
     1  This is line 1
     2  This is line 2
     3  This is line 3
```

```bash
cat -b file.txt                      # Number non-empty lines only
cat -E file.txt                      # Show $ at end of lines
cat -T file.txt                      # Show tabs as ^I
cat -A file.txt                      # Show all non-printing characters
```

---

#### `cp` - Copy Files and Directories

```bash
cp source.txt destination.txt        # Copy file
```

**Flags:**

```bash
cp -r directory1 directory2          # Copy directory recursively
cp -i file.txt backup/               # Interactive (prompt before overwrite)
cp -v file.txt backup/               # Verbose (show what's being copied)
cp -p file.txt backup/               # Preserve attributes (permissions, timestamps)
cp -u source.txt dest.txt            # Update (copy only if source is newer)
```

**Output with `-v`:**
```
'file.txt' -> 'backup/file.txt'
```

**Practical Example:**
```bash
cp -rpv /var/log/app/*.log /backup/logs/
```

**Output:**
```
'/var/log/app/app.log' -> '/backup/logs/app.log'
'/var/log/app/error.log' -> '/backup/logs/error.log'
```

---

#### `mv` - Move or Rename Files

```bash
mv oldname.txt newname.txt           # Rename file
mv file.txt /path/to/directory/      # Move file
```

**Flags:**
```bash
mv -i file.txt destination/          # Interactive
mv -v file.txt destination/          # Verbose
mv -n file.txt destination/          # No overwrite
mv -u source.txt dest.txt            # Update only
```

---

#### `rm` - Remove Files and Directories

```bash
rm file.txt                          # Remove file
```

**Flags:**

```bash
rm -r directory/                     # Remove directory recursively
rm -f file.txt                       # Force removal (no prompt)
rm -rf directory/                    # Force recursive removal (DANGEROUS!)
rm -i file.txt                       # Interactive (ask before deletion)
rm -v file.txt                       # Verbose
```

**⚠️ WARNING:** Never run `rm -rf /` (will delete everything!)

**Safe Practice:**
```bash
# Always use -i for important deletions
alias rm='rm -i'

# Or use trash/move to trash instead
mv file.txt ~/.trash/
```

---

#### `mkdir` - Make Directories

```bash
mkdir newfolder                      # Create single directory
```

**Flags:**

```bash
mkdir -p parent/child/grandchild     # Create nested directories
mkdir -m 755 folder                  # Set permissions while creating
mkdir -v folder                      # Verbose output
```

**Output with `-pv`:**
```bash
mkdir -pv project/src/components
```

**Output:**
```
mkdir: created directory 'project'
mkdir: created directory 'project/src'
mkdir: created directory 'project/src/components'
```

---

#### `rmdir` - Remove Empty Directories

```bash
rmdir emptyfolder                    # Remove empty directory
```

**Flags:**
```bash
rmdir -p parent/child/grandchild     # Remove nested empty directories
rmdir -v folder                      # Verbose
```

**Note:** Only works on empty directories. Use `rm -r` for non-empty ones.

---

### 2.3 File Viewing Commands

#### `more` - View File Page by Page

```bash
more largefile.txt
```

**Controls:**
- `Space` : Next page
- `Enter` : Next line
- `q` : Quit
- `/pattern` : Search forward
- `b` : Previous page

---

#### `less` - Better File Viewer (More Features)

```bash
less largefile.txt
```

**Controls:**
- `Space` / `f` : Next page
- `b` : Previous page
- `g` : Go to beginning
- `G` : Go to end
- `/pattern` : Search forward
- `?pattern` : Search backward
- `n` : Next search result
- `N` : Previous search result
- `q` : Quit

**Flags:**
```bash
less -N file.txt                     # Show line numbers
less -S file.txt                     # Truncate long lines (no wrap)
less +F file.txt                     # Follow mode (like tail -f)
```

---

#### `head` - View First Lines of File

```bash
head file.txt                        # First 10 lines (default)
```

**Output:**
```
Line 1
Line 2
Line 3
Line 4
Line 5
Line 6
Line 7
Line 8
Line 9
Line 10
```

**Flags:**

```bash
head -n 5 file.txt                   # First 5 lines
head -5 file.txt                     # Same as above
head -c 100 file.txt                 # First 100 bytes
head -n -5 file.txt                  # All except last 5 lines
```

**Multiple Files:**
```bash
head -n 3 file1.txt file2.txt
```

**Output:**
```
==> file1.txt <==
Line 1 of file1
Line 2 of file1
Line 3 of file1

==> file2.txt <==
Line 1 of file2
Line 2 of file2
Line 3 of file2
```

---

#### `tail` - View Last Lines of File

```bash
tail file.txt                        # Last 10 lines (default)
```

**Flags:**

```bash
tail -n 5 file.txt                   # Last 5 lines
tail -5 file.txt                     # Same as above
tail -c 100 file.txt                 # Last 100 bytes
tail -n +5 file.txt                  # From line 5 to end
```

**Most Important Flag:**
```bash
tail -f /var/log/syslog              # Follow mode (real-time monitoring)
```

**Output:** (continuously updates)
```
Nov 18 10:30:01 server systemd[1]: Starting Daily apt activities...
Nov 18 10:30:02 server CRON[1234]: (root) CMD (run-parts /etc/cron.daily)
Nov 18 10:30:05 server systemd[1]: Started Daily apt activities.
[keeps showing new lines as they're added]
```

**Practical DevOps Usage:**
```bash
tail -f /var/log/application.log | grep ERROR     # Monitor errors in real-time
tail -n 100 -f /var/log/nginx/access.log          # Monitor last 100 lines and follow
```

---

## 3. Variables & Data Types

### 3.1 Variable Declaration

**Rules:**
- No spaces around `=`
- Variable names: letters, numbers, underscores (can't start with number)
- Case-sensitive

```bash
#!/bin/bash

# String variables
name="John Doe"
email="john@example.com"

# Numeric variables (treated as strings by default)
age=25
count=100

# Use variables
echo "Name: $name"
echo "Age: $age"
```

**Output:**
```
Name: John Doe
Age: 25
```

### 3.2 Variable Usage

```bash
# Correct usage
echo "$name"              # Recommended (handles spaces)
echo "${name}"            # With braces (safer)
echo "Hello, $name!"      # In strings

# Concatenation
full_name="${first_name} ${last_name}"
path="/home/${username}/documents"
```

**Example:**
```bash
#!/bin/bash

first_name="John"
last_name="Doe"
full_name="${first_name} ${last_name}"

echo "Full Name: $full_name"
```

**Output:**
```
Full Name: John Doe
```

---

### 3.3 Command Substitution

```bash
# Modern syntax (recommended)
current_date=$(date)
file_count=$(ls | wc -l)
current_dir=$(pwd)

# Old syntax (deprecated but still works)
current_date=`date`
```

**Example:**
```bash
#!/bin/bash

current_time=$(date '+%Y-%m-%d %H:%M:%S')
files_in_dir=$(ls -1 | wc -l)
disk_usage=$(df -h / | tail -1 | awk '{print $5}')

echo "Current Time: $current_time"
echo "Files in directory: $files_in_dir"
echo "Disk Usage: $disk_usage"
```

**Output:**
```
Current Time: 2025-11-18 10:30:45
Files in directory: 15
Disk Usage: 45%
```

---

### 3.4 Special Variables

```bash
#!/bin/bash

echo "Script name: $0"
echo "First argument: $1"
echo "Second argument: $2"
echo "All arguments: $@"
echo "Number of arguments: $#"
echo "Exit status of last command: $?"
echo "Process ID: $$"
echo "Last background process ID: $!"
```

**Run with:**
```bash
./script.sh arg1 arg2 arg3
```

**Output:**
```
Script name: ./script.sh
First argument: arg1
Second argument: arg2
All arguments: arg1 arg2 arg3
Number of arguments: 3
Exit status of last command: 0
Process ID: 12345
Last background process ID: 
```

### 3.5 Environment Variables

```bash
# View all environment variables
env
printenv

# Common environment variables
echo $HOME                # User's home directory
echo $USER                # Current username
echo $PATH                # Executable search path
echo $PWD                 # Current directory
echo $SHELL               # Current shell
echo $HOSTNAME            # Computer name

# Set environment variable
export MY_VAR="value"

# Make variable available to child processes
export DATABASE_URL="postgresql://localhost/mydb"
```

**Example:**
```bash
#!/bin/bash

echo "User: $USER"
echo "Home: $HOME"
echo "Shell: $SHELL"
echo "Path: $PATH"
```

**Output:**
```
User: devops_engineer
Home: /home/devops_engineer
Shell: /bin/bash
Path: /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

---

### 3.6 Variable Scope

```bash
#!/bin/bash

# Global variable
global_var="I am global"

my_function() {
    # Local variable (only in function)
    local local_var="I am local"
    
    echo "Inside function:"
    echo "  Global: $global_var"
    echo "  Local: $local_var"
}

my_function

echo "Outside function:"
echo "  Global: $global_var"
echo "  Local: $local_var"    # Empty (not accessible)
```

**Output:**
```
Inside function:
  Global: I am global
  Local: I am local
Outside function:
  Global: I am global
  Local: 
```

---

### 3.7 Readonly Variables

```bash
#!/bin/bash

# Declare readonly variable
readonly PI=3.14159
readonly CONFIG_FILE="/etc/myapp/config.conf"

echo "PI: $PI"

# Try to change (will fail)
PI=3.14    # Error: PI: readonly variable
```

---

### 3.8 Arithmetic Operations

```bash
#!/bin/bash

# Method 1: Using $(( ))
a=10
b=5

sum=$((a + b))
diff=$((a - b))
product=$((a * b))
quotient=$((a / b))
remainder=$((a % b))

echo "Sum: $sum"
echo "Difference: $diff"
echo "Product: $product"
echo "Quotient: $quotient"
echo "Remainder: $remainder"

# Method 2: Using let
let result=10+5
echo "Result: $result"

# Method 3: Using expr (old style)
result=$(expr 10 + 5)
echo "Result: $result"

# Increment/Decrement
counter=0
((counter++))        # counter = 1
((counter+=5))       # counter = 6
((counter--))        # counter = 5

echo "Counter: $counter"
```

**Output:**
```
Sum: 15
Difference: 5
Product: 50
Quotient: 2
Remainder: 0
Result: 15
Result: 15
Counter: 5
```

**Floating Point with bc:**
```bash
#!/bin/bash

# Bash only does integer arithmetic
# For floating point, use bc

result=$(echo "scale=2; 10 / 3" | bc)
echo "Result: $result"

result=$(echo "scale=4; 22 / 7" | bc)
echo "PI approximation: $result"
```

**Output:**
```
Result: 3.33
PI approximation: 3.1428
```

---

## 4. Input/Output Operations

### 4.1 Echo Command

```bash
#!/bin/bash

# Basic echo
echo "Hello World"

# Without newline
echo -n "Enter your name: "

# Interpret backslash escapes
echo -e "Line 1\nLine 2\nLine 3"
echo -e "Tab\tseparated\tvalues"

# Colors (ANSI escape codes)
echo -e "\e[31mRed text\e[0m"
echo -e "\e[32mGreen text\e[0m"
echo -e "\e[33mYellow text\e[0m"
echo -e "\e[34mBlue text\e[0m"
```

**Output:**
```
Hello World
Enter your name: Line 1
Line 2
Line 3
Tab    separated    values
Red text (in red)
Green text (in green)
Yellow text (in yellow)
Blue text (in blue)
```

---

### 4.2 Printf Command (Formatted Output)

```bash
#!/bin/bash

# Basic usage
printf "Hello, %s!\n" "World"

# Format specifiers
name="John"
age=25
printf "Name: %s, Age: %d\n" "$name" "$age"

# Floating point
price=19.99
printf "Price: $%.2f\n" "$price"

# Padding and alignment
printf "%-20s %10s\n" "Name" "Score"
printf "%-20s %10d\n" "Alice" 95
printf "%-20s %10d\n" "Bob" 87
printf "%-20s %10d\n" "Charlie" 92
```

**Output:**
```
Hello, World!
Name: John, Age: 25
Price: $19.99
Name                      Score
Alice                        95
Bob                          87
Charlie                      92
```

---

### 4.3 Read Command (User Input)

```bash
#!/bin/bash

# Basic input
echo "Enter your name:"
read name
echo "Hello, $name!"

# Prompt in same line
read -p "Enter your age: " age
echo "You are $age years old"

# Silent input (passwords)
read -sp "Enter password: " password
echo
echo "Password saved (hidden)"

# Read multiple variables
read -p "Enter first and last name: " first last
echo "First: $first, Last: $last"

# Read with timeout
if read -t 5 -p "Enter input (5 seconds): " input; then
    echo "You entered: $input"
else
    echo -e "\nTimeout!"
fi

# Read from file
while read line; do
    echo "Line: $line"
done < input.txt
```

**Interactive Example:**
```
Enter your name:
John                    # User types this
Hello, John!
Enter your age: 25      # User types 25
You are 25 years old
Enter password:         # Input hidden
Password saved (hidden)
Enter first and last name: John Doe
First: John, Last: Doe
```

---

### 4.4 Redirection Operators

```bash
# Output redirection
echo "Hello" > file.txt              # Overwrite file
echo "World" >> file.txt             # Append to file

# Input redirection
wc -l < file.txt                     # Read from file

# Error redirection
command 2> error.log                 # Redirect stderr
command > output.log 2>&1            # Redirect both stdout and stderr
command &> all.log                   # Same as above (shorter)

# Discard output
command > /dev/null                  # Discard stdout
command 2> /dev/null                 # Discard stderr
command &> /dev/null                 # Discard everything
```

**Example:**
```bash
#!/bin/bash

# Create log with both output and errors
{
    echo "Starting process..."
    ls /existing/directory
    ls /nonexistent/directory
    echo "Process complete"
} > process.log 2>&1

cat process.log
```

**Output in process.log:**
```
Starting process...
file1.txt
file2.sh
directory1
ls: cannot access '/nonexistent/directory': No such file or directory
Process complete
```

---

### 4.5 Here Documents

```bash
#!/bin/bash

# Here document (multi-line input)
cat << EOF
This is a multi-line
text block that will
be displayed as is.
Variables work too: $USER
EOF

# Write to file
cat << 'EOF' > config.txt
server_name=localhost
port=8080
database=mydb
EOF

# With variable substitution disabled (quoted delimiter)
cat << 'EOF'
Use $variable literally (no substitution)
EOF
```

**Output:**
```
This is a multi-line
text block that will
be displayed as is.
Variables work too: devops_engineer
```

---

## 5. Conditional Statements

### 5.1 If Statement Basics

```bash
#!/bin/bash

# Basic if
if [[ condition ]]; then
    commands
fi

# If-else
if [[ condition ]]; then
    commands
else
    other_commands
fi

# If-elif-else
if [[ condition1 ]]; then
    commands1
elif [[ condition2 ]]; then
    commands2
else
    commands3
fi
```

---

### 5.2 File Test Operators

```bash
#!/bin/bash

file="test.txt"

# File existence and type
if [[ -e "$file" ]]; then
    echo "File exists"
fi

if [[ -f "$file" ]]; then
    echo "Regular file exists"
fi

if [[ -d "$file" ]]; then
    echo "Directory exists"
fi

if [[ -L "$file" ]]; then
    echo "Symbolic link"
fi

# Permissions
if [[ -r "$file" ]]; then
    echo "File is readable"
fi

if [[ -w "$file" ]]; then
    echo "File is writable"
fi

if [[ -x "$file" ]]; then
    echo "File is executable"
fi

# Size and timestamps
if [[ -s "$file" ]]; then
    echo "File is not empty"
fi

if [[ file1.txt -nt file2.txt ]]; then
    echo "file1.txt is newer than file2.txt"
fi

if [[ file1.txt -ot file2.txt ]]; then
    echo "file1.txt is older than file2.txt"
fi
```

**Complete File Test Operators:**
```
-e : exists
-f : regular file
-d : directory
-L : symbolic link
-b : block device
-c : character device
-p : named pipe
-S : socket
-r : readable
-w : writable
-x : executable
-s : not empty (size > 0)
-nt : newer than
-ot : older than
-ef : same file (hard link)
```

---

### 5.3 Numeric Comparisons

```bash
#!/bin/bash

num1=10
num2=20

if [[ $num1 -eq $num2 ]]; then
    echo "Numbers are equal"
fi

if [[ $num1 -ne $num2 ]]; then
    echo "Numbers are not equal"
fi

if [[ $num1 -lt $num2 ]]; then
    echo "$num1 is less than $num2"
fi

if [[ $num1 -le $num2 ]]; then
    echo "$num1 is less than or equal to $num2"
fi

if [[ $num1 -gt $num2 ]]; then
    echo "$num1 is greater than $num2"
fi

if [[ $num1 -ge $num2 ]]; then
    echo "$num1 is greater than or equal to $num2"
fi
```

**Output:**
```
Numbers are not equal
10 is less than 20
10 is less than or equal to 20
```

**Numeric Operators:**
```
-eq : equal
-ne : not equal
-lt : less than
-le : less than or equal
-gt : greater than
-ge : greater than or equal
```

---

### 5.4 String Comparisons

```bash
#!/bin/bash

str1="hello"
str2="world"
str3=""

# Equality
if [[ "$str1" == "$str2" ]]; then
    echo "Strings are equal"
fi

if [[ "$str1" != "$str2" ]]; then
    echo "Strings are different"
fi

# Empty/non-empty
if [[ -z "$str3" ]]; then
    echo "String is empty"
fi

if [[ -n "$str1" ]]; then
    echo "String is not empty"
fi

# Pattern matching
if [[ "$str1" == h* ]]; then
    echo "String starts with 'h'"
fi

if [[ "$str1" == *lo ]]; then
    echo "String ends with 'lo'"
fi

if [[ "$str1" == *ll* ]]; then
    echo "String contains 'll'"
fi

# Lexicographic comparison
if [[ "$str1" < "$str2" ]]; then
    echo "'$str1' comes before '$str2' alphabetically"
fi
```

**Output:**
```
Strings are different
String is empty
String is not empty
String starts with 'h'
String ends with 'lo'
String contains 'll'
'hello' comes before 'world' alphabetically
```

**String Operators:**
```
== : equal
!= : not equal
< : less than (alphabetically)
> : greater than (alphabetically)
-z : empty string
-n : non-empty string
```

---

### 5.5 Logical Operators

```bash
#!/bin/bash

age=25
country="USA"

# AND operator
if [[ $age -ge 18 && "$country" == "USA" ]]; then
    echo "Eligible to vote in USA"
fi

# OR operator
if [[ $age -lt 13 || $age -gt 65 ]]; then
    echo "Eligible for discount"
fi

# NOT operator
if [[ ! -f "config.txt" ]]; then
    echo "Config file not found"
fi

# Complex conditions
if [[ ($age -ge 18 && $age -le 30) && "$country" == "USA" ]]; then
    echo "Young adult in USA"
fi
```

**Output:**
```
Eligible to vote in USA
```

**Logical Operators:**
```
&& : AND
|| : OR
! : NOT
```

---

### 5.6 Case Statements

```bash
#!/bin/bash

read -p "Enter a day (mon/tue/wed/thu/fri/sat/sun): " day

case $day in
    mon|Mon|MON)
        echo "Monday - Start of work week"
        ;;
    tue|Tue|TUE)
        echo "Tuesday"
        ;;
    wed|Wed|WED)
        echo "Wednesday - Midweek"
        ;;
    thu|Thu|THU)
        echo "Thursday"
        ;;
    fri|Fri|FRI)
        echo "Friday - TGIF!"
        ;;
    sat|Sat|SAT|sun|Sun|SUN)
        echo "Weekend!"
        ;;
    *)
        echo "Invalid day"
        ;;
esac
```

**Real-World Example (Menu System):**
```bash
#!/bin/bash

echo "=== System Management ==="
echo "1. Check disk usage"
echo "2. Check memory usage"
echo "3. List processes"
echo "4. Exit"
read -p "Enter choice: " choice

case $choice in
    1)
        df -h
        ;;
    2)
        free -h
        ;;
    3)
        ps aux | head -10
        ;;
    4)
        echo "Exiting..."
        exit 0
        ;;
    *)
        echo "Invalid choice!"
        exit 1
        ;;
esac
```

---

## 6. Loops & Iterations

### 6.1 For Loop

#### Basic For Loop

```bash
#!/bin/bash

# Loop through list
for item in apple banana cherry; do
    echo "Fruit: $item"
done
```

**Output:**
```
Fruit: apple
Fruit: banana
Fruit: cherry
```

#### For Loop with Range

```bash
#!/bin/bash

# Loop through numbers
for i in {1..5}; do
    echo "Number: $i"
done

# With step
for i in {0..10..2}; do
    echo "Even number: $i"
done

# C-style for loop
for ((i=1; i<=5; i++)); do
    echo "Count: $i"
done
```

**Output:**
```
Number: 1
Number: 2
Number: 3
Number: 4
Number: 5
Even number: 0
Even number: 2
Even number: 4
Even number: 6
Even number: 8
Even number: 10
Count: 1
Count: 2
Count: 3
Count: 4
Count: 5
```

#### For Loop with Files

```bash
#!/bin/bash

# Loop through files in directory
for file in *.txt; do
    echo "Processing: $file"
    wc -l "$file"
done

# Loop through files recursively
for file in $(find . -name "*.log"); do
    echo "Log file: $file"
done
```

**Output:**
```
Processing: file1.txt
25 file1.txt
Processing: file2.txt
50 file2.txt
Processing: file3.txt
100 file3.txt
```

#### For Loop with Command Output

```bash
#!/bin/bash

# Loop through users
for user in $(cat /etc/passwd | cut -d: -f1); do
    echo "User: $user"
done

# Loop through directories
for dir in $(ls -d */); do
    echo "Directory: $dir"
    ls -l "$dir" | wc -l
done
```

---

### 6.2 While Loop

```bash
#!/bin/bash

# Basic while loop
counter=1
while [[ $counter -le 5 ]]; do
    echo "Counter: $counter"
    ((counter++))
done
```

**Output:**
```
Counter: 1
Counter: 2
Counter: 3
Counter: 4
Counter: 5
```

#### While Loop - Reading Files

```bash
#!/bin/bash

# Read file line by line
while IFS= read -r line; do
    echo "Line: $line"
done < input.txt

# Read with field separator
while IFS=: read -r username password uid gid info home shell; do
    echo "User: $username, Home: $home, Shell: $shell"
done < /etc/passwd
```

**Output:**
```
User: root, Home: /root, Shell: /bin/bash
User: daemon, Home: /usr/sbin, Shell: /usr/sbin/nologin
User: bin, Home: /bin, Shell: /usr/sbin/nologin
...
```

#### Infinite While Loop

```bash
#!/bin/bash

# Infinite loop (use Ctrl+C to stop)
while true; do
    echo "Monitoring system..."
    date
    df -h | grep "/$"
    sleep 5
done

# Infinite loop with condition to break
counter=0
while true; do
    echo "Iteration: $counter"
    ((counter++))
    
    if [[ $counter -ge 10 ]]; then
        echo "Reached 10, breaking..."
        break
    fi
    
    sleep 1
done
```

---

### 6.3 Until Loop

```bash
#!/bin/bash

# Until loop (opposite of while)
counter=1
until [[ $counter -gt 5 ]]; do
    echo "Counter: $counter"
    ((counter++))
done
```

**Output:**
```
Counter: 1
Counter: 2
Counter: 3
Counter: 4
Counter: 5
```

**Real-World Example (Wait for Service):**
```bash
#!/bin/bash

# Wait until service is up
until curl -s http://localhost:8080/health > /dev/null; do
    echo "Waiting for service to start..."
    sleep 2
done

echo "Service is up!"
```

---

### 6.4 Loop Control (break, continue)

```bash
#!/bin/bash

# Break example
echo "=== Break Example ==="
for i in {1..10}; do
    if [[ $i -eq 5 ]]; then
        echo "Breaking at $i"
        break
    fi
    echo "Number: $i"
done

# Continue example
echo -e "\n=== Continue Example ==="
for i in {1..10}; do
    if [[ $((i % 2)) -eq 0 ]]; then
        continue  # Skip even numbers
    fi
    echo "Odd number: $i"
done
```

**Output:**
```
=== Break Example ===
Number: 1
Number: 2
Number: 3
Number: 4
Breaking at 5

=== Continue Example ===
Odd number: 1
Odd number: 3
Odd number: 5
Odd number: 7
Odd number: 9
```

#### Nested Loop with Break

```bash
#!/bin/bash

# Nested loops with break
for i in {1..3}; do
    echo "Outer loop: $i"
    for j in {1..3}; do
        if [[ $j -eq 2 ]]; then
            break  # Breaks inner loop only
        fi
        echo "  Inner loop: $j"
    done
done
```

**Output:**
```
Outer loop: 1
  Inner loop: 1
Outer loop: 2
  Inner loop: 1
Outer loop: 3
  Inner loop: 1
```

---

### 6.5 Select Loop (Menu Creation)

```bash
#!/bin/bash

# Select menu
echo "Choose your favorite language:"
select lang in "Python" "Bash" "JavaScript" "Go" "Quit"; do
    case $lang in
        "Python"|"Bash"|"JavaScript"|"Go")
            echo "You selected: $lang"
            break
            ;;
        "Quit")
            echo "Exiting..."
            break
            ;;
        *)
            echo "Invalid option"
            ;;
    esac
done
```

**Output:**
```
Choose your favorite language:
1) Python
2) Bash
3) JavaScript
4) Go
5) Quit
#? 2
You selected: Bash
```

---

## 7. Functions

### 7.1 Function Basics

```bash
#!/bin/bash

# Function definition method 1
function greet() {
    echo "Hello, World!"
}

# Function definition method 2 (preferred)
greet2() {
    echo "Hello again!"
}

# Call functions
greet
greet2
```

**Output:**
```
Hello, World!
Hello again!
```

---

### 7.2 Functions with Arguments

```bash
#!/bin/bash

# Function with parameters
greet_user() {
    local name="$1"
    local age="$2"
    
    echo "Hello, $name!"
    echo "You are $age years old."
}

# Call function with arguments
greet_user "John" 25
greet_user "Alice" 30
```

**Output:**
```
Hello, John!
You are 25 years old.
Hello, Alice!
You are 30 years old.
```

#### Function with Default Parameters

```bash
#!/bin/bash

greet() {
    local name="${1:-Guest}"  # Default to "Guest" if not provided
    local greeting="${2:-Hello}"
    
    echo "$greeting, $name!"
}

greet                      # Uses defaults
greet "John"               # Uses default greeting
greet "Alice" "Welcome"    # Uses both arguments
```

**Output:**
```
Hello, Guest!
Hello, John!
Welcome, Alice!
```

---

### 7.3 Return Values

```bash
#!/bin/bash

# Return exit status (0-255)
check_file() {
    local file="$1"
    
    if [[ -f "$file" ]]; then
        return 0  # Success
    else
        return 1  # Failure
    fi
}

# Use function return value
if check_file "test.txt"; then
    echo "File exists"
else
    echo "File does not exist"
fi

# Capture return value
check_file "test.txt"
result=$?
echo "Return value: $result"
```

#### Return Data (Using Echo)

```bash
#!/bin/bash

# Return string data using echo
get_user_info() {
    local username="$1"
    
    # Simulate database lookup
    if [[ "$username" == "john" ]]; then
        echo "John Doe:john@example.com:25"
    else
        echo "Unknown:unknown@example.com:0"
    fi
}

# Capture function output
user_info=$(get_user_info "john")
echo "User info: $user_info"

# Parse the output
IFS=: read -r name email age <<< "$user_info"
echo "Name: $name"
echo "Email: $email"
echo "Age: $age"
```

**Output:**
```
User info: John Doe:john@example.com:25
Name: John Doe
Email: john@example.com
Age: 25
```

---

### 7.4 Local vs Global Variables

```bash
#!/bin/bash

# Global variable
global_var="I am global"

test_scope() {
    local local_var="I am local"
    global_var="Modified global"
    
    echo "Inside function:"
    echo "  Global: $global_var"
    echo "  Local: $local_var"
}

echo "Before function:"
echo "  Global: $global_var"

test_scope

echo "After function:"
echo "  Global: $global_var"
echo "  Local: $local_var"
```

**Output:**
```
Before function:
  Global: I am global
Inside function:
  Global: Modified global
  Local: I am local
After function:
  Global: Modified global
  Local: 
```

---

### 7.5 Function with Validation

```bash
#!/bin/bash

# Function with input validation
divide() {
    local num1="$1"
    local num2="$2"
    
    # Validate arguments
    if [[ $# -ne 2 ]]; then
        echo "Error: Two arguments required"
        return 1
    fi
    
    # Check if numbers
    if ! [[ "$num1" =~ ^[0-9]+$ ]] || ! [[ "$num2" =~ ^[0-9]+$ ]]; then
        echo "Error: Arguments must be numbers"
        return 1
    fi
    
    # Check for division by zero
    if [[ $num2 -eq 0 ]]; then
        echo "Error: Cannot divide by zero"
        return 1
    fi
    
    # Perform division
    local result=$((num1 / num2))
    echo "Result: $result"
    return 0
}

# Test function
divide 10 2
divide 10 0
divide 10 "abc"
divide 10
```

**Output:**
```
Result: 5
Error: Cannot divide by zero
Error: Arguments must be numbers
Error: Two arguments required
```

---

### 7.6 Recursive Functions

```bash
#!/bin/bash

# Factorial using recursion
factorial() {
    local n=$1
    
    if [[ $n -le 1 ]]; then
        echo 1
    else
        local temp=$(factorial $((n - 1)))
        echo $((n * temp))
    fi
}

# Calculate factorials
for i in {1..5}; do
    result=$(factorial $i)
    echo "$i! = $result"
done
```

**Output:**
```
1! = 1
2! = 2
3! = 6
4! = 24
5! = 120
```

---

## 8. Arrays & String Manipulation

### 8.1 Array Basics

```bash
#!/bin/bash

# Declare array
fruits=("apple" "banana" "cherry" "date")

# Access elements
echo "First fruit: ${fruits[0]}"
echo "Second fruit: ${fruits[1]}"

# All elements
echo "All fruits: ${fruits[@]}"
echo "All fruits (alternative): ${fruits[*]}"

# Array length
echo "Number of fruits: ${#fruits[@]}"

# Specific element length
echo "Length of first fruit: ${#fruits[0]}"
```

**Output:**
```
First fruit: apple
Second fruit: banana
All fruits: apple banana cherry date
All fruits (alternative): apple banana cherry date
Number of fruits: 4
Length of first fruit: 5
```

---

### 8.2 Array Operations

```bash
#!/bin/bash

# Initialize array
numbers=(1 2 3 4 5)

# Add elements
numbers+=(6)
numbers+=(7 8 9)

echo "Array: ${numbers[@]}"

# Remove element (unset)
unset numbers[2]
echo "After removing index 2: ${numbers[@]}"

# Array slice
echo "Slice [1:3]: ${numbers[@]:1:3}"

# Get indices
echo "Indices: ${!numbers[@]}"
```

**Output:**
```
Array: 1 2 3 4 5 6 7 8 9
After removing index 2: 1 2 4 5 6 7 8 9
Slice [1:3]: 2 4 5
Indices: 0 1 3 4 5 6 7 8
```

---

### 8.3 Loop Through Arrays

```bash
#!/bin/bash

servers=("web1" "web2" "db1" "cache1")

# Method 1: Loop through values
echo "=== Servers ==="
for server in "${servers[@]}"; do
    echo "Server: $server"
done

# Method 2: Loop through indices
echo -e "\n=== With Indices ==="
for i in "${!servers[@]}"; do
    echo "Server $i: ${servers[$i]}"
done

# Method 3: C-style loop
echo -e "\n=== C-style Loop ==="
for ((i=0; i<${#servers[@]}; i++)); do
    echo "Server $((i+1)): ${servers[$i]}"
done
```

**Output:**
```
=== Servers ===
Server: web1
Server: web2
Server: db1
Server: cache1

=== With Indices ===
Server 0: web1
Server 1: web2
Server 2: db1
Server 3: cache1

=== C-style Loop ===
Server 1: web1
Server 2: web2
Server 3: db1
Server 4: cache1
```

---

### 8.4 Associative Arrays (Hash Maps)

```bash
#!/bin/bash

# Declare associative array
declare -A user_ages

# Add elements
user_ages["john"]=25
user_ages["alice"]=30
user_ages["bob"]=28

# Access elements
echo "John's age: ${user_ages["john"]}"

# All keys
echo "All users: ${!user_ages[@]}"

# All values
echo "All ages: ${user_ages[@]}"

# Check if key exists
if [[ -v user_ages["alice"] ]]; then
    echo "Alice exists in array"
fi

# Loop through associative array
for name in "${!user_ages[@]}"; do
    echo "$name is ${user_ages[$name]} years old"
done
```

**Output:**
```
John's age: 25
All users: john alice bob
All ages: 25 30 28
Alice exists in array
john is 25 years old
alice is 30 years old
bob is 28 years old
```

---

### 8.5 String Manipulation

#### String Length

```bash
#!/bin/bash

text="Hello, World!"
echo "Length: ${#text}"
```

**Output:**
```
Length: 13
```

#### Substring Extraction

```bash
#!/bin/bash

text="Hello, World!"

# Extract substring
echo "${text:0:5}"      # From position 0, length 5
echo "${text:7}"        # From position 7 to end
echo "${text:7:5}"      # From position 7, length 5
echo "${text: -6}"      # Last 6 characters
```

**Output:**
```
Hello
World!
World
World!
```

#### String Replacement

```bash
#!/bin/bash

text="apple banana apple cherry apple"

# Replace first occurrence
echo "${text/apple/orange}"

# Replace all occurrences
echo "${text//apple/orange}"

# Replace at beginning
echo "${text/#apple/orange}"

# Replace at end
echo "${text/%apple/orange}"
```

**Output:**
```
orange banana apple cherry apple
orange banana orange cherry orange
orange banana apple cherry apple
apple banana apple cherry orange
```

#### Remove Pattern from String

```bash
#!/bin/bash

filename="document.backup.tar.gz"

# Remove shortest match from beginning
echo "${filename#*.}"           # backup.tar.gz

# Remove longest match from beginning
echo "${filename##*.}"          # gz

# Remove shortest match from end
echo "${filename%.*}"           # document.backup.tar

# Remove longest match from end
echo "${filename%%.*}"          # document
```

**Output:**
```
backup.tar.gz
gz
document.backup.tar
document
```

#### Case Conversion

```bash
#!/bin/bash

text="Hello World"

# Uppercase
echo "${text^^}"                # HELLO WORLD
echo "${text^^[aeiou]}"         # HEllO WOrld (vowels only)

# Lowercase
echo "${text,,}"                # hello world
echo "${text,,[HW]}"            # hello world (H and W only)

# Capitalize first letter
echo "${text^}"                 # Hello world

# Lowercase first letter
echo "${text,}"                 # hello World
```

**Output:**
```
HELLO WORLD
HEllO WOrld
hello world
hello world
Hello world
hello World
```

---

### 8.6 String Validation

```bash
#!/bin/bash

# Check if string is empty
str=""
if [[ -z "$str" ]]; then
    echo "String is empty"
fi

# Check if string is not empty
str="hello"
if [[ -n "$str" ]]; then
    echo "String is not empty"
fi

# Check if string contains substring
text="Hello, World!"
if [[ "$text" == *"World"* ]]; then
    echo "Contains 'World'"
fi

# Regex matching
email="user@example.com"
if [[ "$email" =~ ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$ ]]; then
    echo "Valid email format"
fi

# Check if string is a number
input="12345"
if [[ "$input" =~ ^[0-9]+$ ]]; then
    echo "String is a number"
fi
```

**Output:**
```
String is empty
String is not empty
Contains 'World'
Valid email format
String is a number
```

---

## 9. Text Processing Tools

### 9.1 grep - Search Text

#### Basic Usage

```bash
# Search for pattern in file
grep "error" logfile.txt

# Case-insensitive search
grep -i "error" logfile.txt

# Show line numbers
grep -n "error" logfile.txt

# Count matches
grep -c "error" logfile.txt

# Invert match (lines NOT containing pattern)
grep -v "error" logfile.txt
```

**Example File (logfile.txt):**
```
2025-11-18 10:00:01 INFO Application started
2025-11-18 10:00:15 ERROR Connection failed
2025-11-18 10:00:20 INFO Retrying connection
2025-11-18 10:00:25 ERROR Connection timeout
2025-11-18 10:00:30 INFO Connection successful
```

**Output:**
```bash
$ grep "ERROR" logfile.txt
2025-11-18 10:00:15 ERROR Connection failed
2025-11-18 10:00:25 ERROR Connection timeout

$ grep -n "ERROR" logfile.txt
2:2025-11-18 10:00:15 ERROR Connection failed
4:2025-11-18 10:00:25 ERROR Connection timeout

$ grep -c "ERROR" logfile.txt
2
```

#### Advanced grep

```bash
# Recursive search in directory
grep -r "TODO" /path/to/project/

# Search multiple files
grep "error" file1.txt file2.txt file3.txt

# Extended regex
grep -E "error|warning|critical" logfile.txt

# Show context (lines before/after match)
grep -A 2 "ERROR" logfile.txt      # 2 lines after
grep -B 2 "ERROR" logfile.txt      # 2 lines before
grep -C 2 "ERROR" logfile.txt      # 2 lines before and after

# Show only matching part
grep -o "ERROR" logfile.txt

# Match whole word only
grep -w "error" logfile.txt

# Multiple patterns from file
grep -f patterns.txt logfile.txt

# Show files with matches (not the matches themselves)
grep -l "error" *.txt

# Show files without matches
grep -L "error" *.txt
```

**Output with context:**
```bash
$ grep -C 1 "ERROR" logfile.txt
2025-11-18 10:00:01 INFO Application started
2025-11-18 10:00:15 ERROR Connection failed
2025-11-18 10:00:20 INFO Retrying connection
--
2025-11-18 10:00:20 INFO Retrying connection
2025-11-18 10:00:25 ERROR Connection timeout
2025-11-18 10:00:30 INFO Connection successful
```

---

### 9.2 sed - Stream Editor

#### Basic Substitution

```bash
# Replace first occurrence per line
sed 's/old/new/' file.txt

# Replace all occurrences (global)
sed 's/old/new/g' file.txt

# Replace on specific line
sed '3s/old/new/' file.txt

# Replace in range of lines
sed '2,5s/old/new/g' file.txt

# Case-insensitive replacement
sed 's/old/new/gi' file.txt
```

**Example:**
```bash
$ echo "hello world hello universe" | sed 's/hello/hi/'
hi world hello universe

$ echo "hello world hello universe" | sed 's/hello/hi/g'
hi world hi universe
```

#### Delete Lines

```bash
# Delete specific line
sed '3d' file.txt

# Delete range
sed '2,5d' file.txt

# Delete lines matching pattern
sed '/error/d' file.txt

# Delete empty lines
sed '/^$/d' file.txt

# Delete lines NOT matching pattern
sed '/pattern/!d' file.txt
```

**Example:**
```bash
$ cat numbers.txt
one
two
three
four
five

$ sed '2d' numbers.txt
one
three
four
five

$ sed '2,4d' numbers.txt
one
five
```

#### Insert and Append

```bash
# Insert line before match
sed '/pattern/i\New line' file.txt

# Append line after match
sed '/pattern/a\New line' file.txt

# Insert at specific line number
sed '3i\Inserted line' file.txt
```

#### In-place Editing

```bash
# Edit file in-place (dangerous!)
sed -i 's/old/new/g' file.txt

# Edit with backup
sed -i.bak 's/old/new/g' file.txt
```

#### Multiple Commands

```bash
# Multiple sed commands
sed -e 's/old/new/g' -e 's/foo/bar/g' file.txt

# Or using semicolon
sed 's/old/new/g; s/foo/bar/g' file.txt
```

---

### 9.3 awk - Pattern Scanning and Processing

#### Basic Usage

```bash
# Print entire line
awk '{print}' file.txt

# Print specific columns
awk '{print $1}' file.txt          # First column
awk '{print $1, $3}' file.txt      # First and third column
awk '{print $NF}' file.txt         # Last column
awk '{print $(NF-1)}' file.txt     # Second to last column
```

**Example File (data.txt):**
```
John Doe 25 Engineer
Jane Smith 30 Manager
Bob Johnson 28 Developer
Alice Williams 35 Director
```

**Output:**
```bash
$ awk '{print $1}' data.txt
John
Jane
Bob
Alice

$ awk '{print $1, $3}' data.txt
John 25
Jane 30
Bob 28
Alice 35

$ awk '{print $NF}' data.txt
Engineer
Manager
Developer
Director
```

#### Field Separator

```bash
# Custom field separator
awk -F':' '{print $1}' /etc/passwd

# Multiple separators
awk -F'[,:]' '{print $1, $2}' file.txt

# Tab separator
awk -F'\t' '{print $1}' file.tsv
```

**Example:**
```bash
$ cat /etc/passwd | head -3
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin

$ awk -F':' '{print $1, $7}' /etc/passwd | head -3
root /bin/bash
daemon /usr/sbin/nologin
bin /usr/sbin/nologin
```

#### Conditional Processing

```bash
# Print lines where column matches condition
awk '$3 > 25' data.txt

# Multiple conditions
awk '$3 > 25 && $4 == "Engineer"' data.txt

# Pattern matching
awk '/Manager/ {print}' data.txt
awk '$4 ~ /^Dev/' data.txt         # Regex match
```

**Output:**
```bash
$ awk '$3 > 25' data.txt
Jane Smith 30 Manager
Bob Johnson 28 Developer
Alice Williams 35 Director

$ awk '/Manager/' data.txt
Jane Smith 30 Manager
```

#### Built-in Variables

```bash
# NR: Line number
awk '{print NR, $0}' file.txt

# NF: Number of fields
awk '{print NF, $0}' file.txt

# Print with line numbers
awk '{print NR ": " $0}' file.txt
```

**Output:**
```bash
$ awk '{print NR, $0}' data.txt
1 John Doe 25 Engineer
2 Jane Smith 30 Manager
3 Bob Johnson 28 Developer
4 Alice Williams 35 Director

$ awk '{print NF}' data.txt
4
4
4
4
```

#### Calculations

```bash
# Sum column
awk '{sum += $3} END {print sum}' data.txt

# Average
awk '{sum += $3; count++} END {print sum/count}' data.txt

# Maximum
awk 'BEGIN {max = 0} {if ($3 > max) max = $3} END {print max}' data.txt

# Count lines
awk 'END {print NR}' file.txt
```

**Output:**
```bash
$ awk '{sum += $3} END {print "Total age:", sum}' data.txt
Total age: 118

$ awk '{sum += $3; count++} END {print "Average age:", sum/count}' data.txt
Average age: 29.5
```

#### BEGIN and END Blocks

```bash
# Print header and footer
awk 'BEGIN {print "=== Report ==="} {print $0} END {print "=== End ==="}' file.txt

# Initialize variables
awk 'BEGIN {FS=":"; OFS=" - "} {print $1, $7}' /etc/passwd
```

**Output:**
```bash
$ awk 'BEGIN {print "Name Age"} {print $1, $3} END {print "Total:", NR}' data.txt
Name Age
John 25
Jane 30
Bob 28
Alice 35
Total: 4
```

---

### 9.4 cut - Extract Columns

```bash
# Cut by character position
cut -c1-5 file.txt                 # Characters 1-5
cut -c1,3,5 file.txt               # Characters 1, 3, and 5

# Cut by delimiter
cut -d':' -f1 /etc/passwd          # First field, colon delimiter
cut -d':' -f1,7 /etc/passwd        # Fields 1 and 7
cut -d',' -f2-4 file.csv           # Fields 2 through 4

# Tab delimiter (default)
cut -f1,3 file.tsv
```

**Example:**
```bash
$ echo "John:Doe:25:Engineer" | cut -d':' -f1,4
John:Engineer

$ cat /etc/passwd | cut -d':' -f1,7 | head -3
root:/bin/bash
daemon:/usr/sbin/nologin
bin:/usr/sbin/nologin
```

---

### 9.5 sort - Sort Lines

```bash
# Basic sort (alphabetical)
sort file.txt

# Numeric sort
sort -n numbers.txt

# Reverse sort
sort -r file.txt

# Sort by column
sort -k2 file.txt                  # Sort by 2nd column
sort -t':' -k3 -n /etc/passwd      # Sort by 3rd field (numeric)

# Remove duplicates
sort -u file.txt

# Case-insensitive sort
sort -f file.txt

# Sort by multiple columns
sort -k1,1 -k2,2n file.txt         # First by col 1 (alpha), then col 2 (numeric)
```

**Example:**
```bash
$ cat numbers.txt
10
2
5
1
20

$ sort -n numbers.txt
1
2
5
10
20

$ sort -nr numbers.txt
20
10
5
2
1
```

---

### 9.6 uniq - Remove Duplicates

```bash
# Remove adjacent duplicates (must sort first!)
sort file.txt | uniq

# Count occurrences
sort file.txt | uniq -c

# Show only duplicates
sort file.txt | uniq -d

# Show only unique lines
sort file.txt | uniq -u

# Ignore case
sort file.txt | uniq -i
```

**Example:**
```bash
$ cat fruits.txt
apple
banana
apple
cherry
banana
apple

$ sort fruits.txt | uniq
apple
banana
cherry

$ sort fruits.txt | uniq -c
      3 apple
      2 banana
      1 cherry

$ sort fruits.txt | uniq -d
apple
banana
```

---

### 9.7 wc - Word Count

```bash
# Count lines
wc -l file.txt

# Count words
wc -w file.txt

# Count characters
wc -c file.txt

# Count bytes
wc -c file.txt

# All counts
wc file.txt
```

**Example:**
```bash
$ wc sample.txt
  10  50  250 sample.txt
# 10 lines, 50 words, 250 characters

$ wc -l *.txt
  10 file1.txt
  20 file2.txt
  15 file3.txt
  45 total
```

---

### 9.8 tr - Translate Characters

```bash
# Convert lowercase to uppercase
echo "hello" | tr 'a-z' 'A-Z'

# Delete characters
echo "hello123" | tr -d '0-9'

# Squeeze repeats
echo "hello    world" | tr -s ' '

# Replace characters
echo "hello" | tr 'aeiou' '12345'
```

**Output:**
```bash
$ echo "hello world" | tr 'a-z' 'A-Z'
HELLO WORLD

$ echo "Phone: 123-456-7890" | tr -d '0-9-'
Phone: 

$ echo "hello    world" | tr -s ' '
hello world

$ echo "hello" | tr 'l' 'L'
heLLo
```

---

# 🚀 Complete Bash Scripting Course - Part 2

## 9.9 Real-World Text Processing Examples (Continued)

### Example 1: Parse CSV File

```bash
#!/bin/bash

# Count records in CSV
csv_file="employees.csv"

echo "=== CSV Analysis ==="
echo "Total records: $(tail -n +2 "$csv_file" | wc -l)"
echo "Total fields: $(head -1 "$csv_file" | awk -F',' '{print NF}')"

# Extract specific columns
echo -e "\n=== Employee Names ==="
awk -F',' 'NR>1 {print $1, $2}' "$csv_file"

# Filter by condition
echo -e "\n=== High Earners (>80000) ==="
awk -F',' 'NR>1 && $4 > 80000 {print $1, $2, $4}' "$csv_file"

# Calculate average salary
echo -e "\n=== Average Salary ==="
awk -F',' 'NR>1 {sum+=$4; count++} END {print "$" sum/count}' "$csv_file"
```

**Sample employees.csv:**
```
FirstName,LastName,Department,Salary
John,Doe,Engineering,85000
Jane,Smith,Marketing,72000
Bob,Johnson,Engineering,95000
Alice,Williams,HR,68000
```

**Output:**
```
=== CSV Analysis ===
Total records: 4
Total fields: 4

=== Employee Names ===
John Doe
Jane Smith
Bob Johnson
Alice Williams

=== High Earners (>80000) ===
John Doe 85000
Bob Johnson 95000

=== Average Salary ===
$80000
```

---

### Example 2: Log File Analysis

```bash
#!/bin/bash

logfile="/var/log/application.log"

echo "=== Log Analysis ==="

# Count error types
echo -e "\n--- Error Summary ---"
grep -i "error\|warning\|critical" "$logfile" | \
    awk '{print $3}' | sort | uniq -c | sort -rn

# Top 10 most frequent errors
echo -e "\n--- Top 10 Error Messages ---"
grep "ERROR" "$logfile" | \
    awk -F'ERROR' '{print $2}' | \
    sort | uniq -c | sort -rn | head -10

# Errors by hour
echo -e "\n--- Errors by Hour ---"
grep "ERROR" "$logfile" | \
    awk '{print substr($2,1,2)}' | \
    sort | uniq -c

# Extract IP addresses and count
echo -e "\n--- Top IP Addresses ---"
grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" "$logfile" | \
    sort | uniq -c | sort -rn | head -10
```

---

### Example 3: System Report Generator

```bash
#!/bin/bash

generate_report() {
    local output_file="system_report_$(date +%Y%m%d_%H%M%S).txt"
    
    {
        echo "========================================="
        echo "        SYSTEM HEALTH REPORT"
        echo "========================================="
        echo "Generated: $(date)"
        echo
        
        echo "--- System Information ---"
        echo "Hostname: $(hostname)"
        echo "Uptime: $(uptime -p)"
        echo "Kernel: $(uname -r)"
        echo
        
        echo "--- CPU Usage ---"
        top -bn1 | grep "Cpu(s)" | \
            sed "s/.*, *\([0-9.]*\)%* id.*/\1/" | \
            awk '{print "CPU Usage: " 100 - $1 "%"}'
        echo
        
        echo "--- Memory Usage ---"
        free -h | awk 'NR==2 {printf "Used: %s / %s (%.2f%%)\n", $3, $2, $3/$2 * 100}'
        echo
        
        echo "--- Disk Usage ---"
        df -h | awk 'NR>1 && $5+0 > 80 {print "WARNING: " $0}'
        df -h | grep -v "tmpfs"
        echo
        
        echo "--- Top 5 Processes by Memory ---"
        ps aux --sort=-%mem | head -6
        echo
        
        echo "--- Network Connections ---"
        netstat -tuln | grep LISTEN | wc -l | \
            awk '{print "Listening ports: " $1}'
        echo
        
        echo "--- Recent Failed Login Attempts ---"
        grep "Failed password" /var/log/auth.log 2>/dev/null | \
            tail -10 || echo "No auth.log access or no failed attempts"
        
    } > "$output_file"
    
    echo "Report generated: $output_file"
}

generate_report
```

---

## 10. File & Directory Management

### 10.1 Find Command

#### Basic Find Operations

```bash
# Find by name
find /path -name "*.txt"

# Case-insensitive name search
find /path -iname "*.TXT"

# Find directories only
find /path -type d

# Find files only
find /path -type f

# Find symbolic links
find /path -type l
```

**Example:**
```bash
$ find . -name "*.sh"
./backup.sh
./deploy.sh
./scripts/monitor.sh
./scripts/cleanup.sh

$ find /home -type f -name "config.txt"
/home/user1/config.txt
/home/user2/.config/app/config.txt
```

---

#### Find by Size

```bash
# Files larger than 100MB
find /path -size +100M

# Files smaller than 10KB
find /path -size -10k

# Files exactly 50MB
find /path -size 50M

# Empty files
find /path -type f -empty

# Empty directories
find /path -type d -empty
```

**Size units:**
- `c`: bytes
- `k`: kilobytes
- `M`: megabytes
- `G`: gigabytes

**Example:**
```bash
$ find /var/log -type f -size +50M
/var/log/syslog.1
/var/log/apache2/access.log
/var/log/mysql/mysql-slow.log

$ find . -type f -empty
./empty_file.txt
./temp/placeholder.txt
```

---

#### Find by Time

```bash
# Modified in last 7 days
find /path -mtime -7

# Modified more than 30 days ago
find /path -mtime +30

# Modified exactly 10 days ago
find /path -mtime 10

# Accessed in last 24 hours
find /path -atime -1

# Changed in last hour
find /path -cmin -60

# Modified in last 30 minutes
find /path -mmin -30
```

**Time options:**
- `-mtime`: modification time (days)
- `-atime`: access time (days)
- `-ctime`: status change time (days)
- `-mmin`: modification time (minutes)
- `-amin`: access time (minutes)
- `-cmin`: status change time (minutes)

**Example:**
```bash
$ find /var/log -name "*.log" -mtime -7
/var/log/syslog
/var/log/application.log
/var/log/error.log

$ find /tmp -type f -atime +30 -delete
# Deletes files not accessed in 30+ days
```

---

#### Find by Permissions

```bash
# Find by exact permissions
find /path -perm 644

# Find with at least these permissions
find /path -perm -644

# Find SUID files
find /path -perm /4000

# Find SGID files
find /path -perm /2000

# Find world-writable files
find /path -perm -002

# Find files without specific permission
find /path ! -perm -600
```

**Example:**
```bash
$ find /var/www -type f -perm 777
/var/www/html/uploads/suspicious.php
# Security concern - files shouldn't be 777

$ find /usr/bin -perm /4000
/usr/bin/sudo
/usr/bin/passwd
# SUID binaries
```

---

#### Find with Actions

```bash
# Execute command on results
find /path -name "*.tmp" -exec rm {} \;

# More efficient (runs command once with all files)
find /path -name "*.tmp" -exec rm {} +

# Prompt before action
find /path -name "*.log" -ok rm {} \;

# Print with details
find /path -name "*.txt" -ls

# Count results
find /path -type f | wc -l
```

**Example:**
```bash
# Backup all .conf files
find /etc -name "*.conf" -exec cp {} /backup/configs/ \;

# Change ownership of all .sh files
find /opt/scripts -name "*.sh" -exec chown user:group {} \;

# Find and compress old logs
find /var/log -name "*.log" -mtime +30 -exec gzip {} \;

# Find large files and list with sizes
find /home -size +100M -exec ls -lh {} \; | awk '{print $9, $5}'
```

---

#### Complex Find Queries

```bash
# Combine conditions (AND)
find /path -name "*.txt" -size +10M -mtime -7

# OR condition
find /path \( -name "*.txt" -o -name "*.log" \)

# NOT condition
find /path -type f ! -name "*.txt"

# Multiple conditions
find /path -type f \( -name "*.log" -o -name "*.txt" \) -size +1M -mtime -30

# Exclude directories
find /path -type f -not -path "*/node_modules/*" -name "*.js"
```

**Example:**
```bash
# Find config files modified in last week, larger than 1KB, excluding backup dirs
find /etc -type f \
    \( -name "*.conf" -o -name "*.cfg" \) \
    -size +1k \
    -mtime -7 \
    ! -path "*/backup/*"

# Find setuid/setgid files (security audit)
find / -type f \( -perm -4000 -o -perm -2000 \) -ls 2>/dev/null
```

---

### 10.2 Directory Navigation & Management

#### Directory Stack (pushd/popd)

```bash
# Save current directory and change
pushd /var/log

# View directory stack
dirs -v

# Return to previous directory
popd

# Switch between two recent directories
pushd

# Clear directory stack
dirs -c
```

**Example Session:**
```bash
$ pwd
/home/user

$ pushd /var/log
/var/log ~

$ pushd /etc
/etc /var/log ~

$ dirs -v
 0  /etc
 1  /var/log
 2  ~

$ popd
/var/log ~

$ popd
~
```

---

#### Create Complex Directory Structures

```bash
#!/bin/bash

# Create project structure
create_project() {
    local project_name=$1
    
    mkdir -p "$project_name"/{src,tests,docs,config}
    mkdir -p "$project_name"/src/{components,utils,services}
    mkdir -p "$project_name"/tests/{unit,integration}
    
    touch "$project_name"/README.md
    touch "$project_name"/.gitignore
    touch "$project_name"/config/{dev,prod}.env
    
    tree "$project_name" 2>/dev/null || find "$project_name" -type f -o -type d
}

create_project "my_app"
```

**Output:**
```
my_app/
├── README.md
├── .gitignore
├── config/
│   ├── dev.env
│   └── prod.env
├── docs/
├── src/
│   ├── components/
│   ├── services/
│   └── utils/
└── tests/
    ├── integration/
    └── unit/
```

---

### 10.3 File Synchronization

#### Using rsync

```bash
# Basic sync
rsync -av source/ destination/

# Sync with deletion (make destination identical)
rsync -av --delete source/ destination/

# Dry run (preview changes)
rsync -av --dry-run source/ destination/

# Show progress
rsync -av --progress source/ destination/

# Exclude patterns
rsync -av --exclude='*.tmp' --exclude='node_modules/' source/ destination/

# Sync over SSH
rsync -avz -e ssh source/ user@remote:/path/destination/

# Sync with bandwidth limit
rsync -av --bwlimit=1000 source/ destination/  # 1000 KB/s
```

**Example:**
```bash
# Backup with exclusions
rsync -av \
    --exclude='*.log' \
    --exclude='temp/' \
    --exclude='.git/' \
    --delete \
    /home/user/projects/ \
    /backup/projects/

# Remote backup
rsync -avz \
    --progress \
    -e "ssh -p 2222" \
    /var/www/ \
    user@backup-server:/backups/www/
```

---

## 11. Process Management

### 11.1 Process Information

```bash
# List all processes
ps aux

# List processes for current user
ps -u $USER

# Process tree
ps auxf
pstree

# Detailed process info
ps -ef

# Custom format
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head -10

# Real-time process monitoring
top
htop  # If installed
```

**Example Output:**
```bash
$ ps aux | head -5
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 169416 11672 ?        Ss   Nov17   0:03 /sbin/init
root         2  0.0  0.0      0     0 ?        S    Nov17   0:00 [kthreadd]
root         3  0.0  0.0      0     0 ?        I<   Nov17   0:00 [rcu_gp]
root         4  0.0  0.0      0     0 ?        I<   Nov17   0:00 [rcu_par_gp]

$ ps -eo pid,cmd,%mem,%cpu --sort=-%mem | head -5
  PID CMD                         %MEM %CPU
 1234 /usr/bin/chrome             12.5  8.2
 5678 /usr/bin/mysql              8.3  15.1
 9012 /usr/sbin/apache2           5.2  3.4
```

---

### 11.2 Process Control

```bash
# Run in background
command &

# List background jobs
jobs

# Bring to foreground
fg %1

# Send to background
bg %1

# Kill process by PID
kill PID

# Force kill
kill -9 PID

# Kill by name
pkill process_name
killall process_name

# Send specific signal
kill -SIGTERM PID
kill -15 PID  # Same as SIGTERM
```

**Common Signals:**
```
SIGTERM (15) - Graceful termination
SIGKILL (9)  - Force kill (cannot be caught)
SIGHUP (1)   - Hangup (reload config)
SIGINT (2)   - Interrupt (Ctrl+C)
SIGSTOP (19) - Stop/pause process
SIGCONT (18) - Continue paused process
```

**Example:**
```bash
# Start long-running process in background
./long_script.sh &
[1] 12345

# Check jobs
$ jobs
[1]+  Running                 ./long_script.sh &

# Kill by job number
$ kill %1

# Kill all chrome processes
$ pkill chrome

# Gracefully restart service
$ kill -HUP $(cat /var/run/nginx.pid)
```

---

### 11.3 Process Monitoring Script

```bash
#!/bin/bash

monitor_process() {
    local process_name=$1
    local max_mem_percent=80
    
    while true; do
        # Check if process exists
        if ! pgrep -x "$process_name" > /dev/null; then
            echo "[$(date)] WARNING: $process_name is not running!"
            # Optionally restart
            # systemctl restart $process_name
        else
            # Get memory usage
            local mem_percent=$(ps aux | grep "$process_name" | grep -v grep | awk '{print $4}' | head -1)
            
            if (( $(echo "$mem_percent > $max_mem_percent" | bc -l) )); then
                echo "[$(date)] ALERT: $process_name using ${mem_percent}% memory!"
                # Send notification or take action
            else
                echo "[$(date)] OK: $process_name running normally (${mem_percent}% mem)"
            fi
        fi
        
        sleep 60  # Check every minute
    done
}

# Usage
monitor_process "nginx"
```

---

### 11.4 Nice and Renice (Process Priority)

```bash
# Start with lower priority
nice -n 10 command

# Start with higher priority (requires root)
nice -n -10 command

# Change priority of running process
renice 10 -p PID

# Change priority for all processes of user
renice 10 -u username

# View process priority
ps -el | grep process_name
```

**Priority Range:** -20 (highest) to 19 (lowest)

**Example:**
```bash
# Run backup with low priority (won't impact system)
nice -n 19 ./backup_script.sh

# Make process more important
sudo renice -5 -p 1234

# CPU-intensive task with low priority
nice -n 15 gzip large_file.tar
```

---

## 12. System Monitoring

### 12.1 CPU Monitoring

```bash
#!/bin/bash

# Get CPU usage
get_cpu_usage() {
    top -bn1 | grep "Cpu(s)" | sed "s/.*, *\([0-9.]*\)%* id.*/\1/" | awk '{print 100 - $1}'
}

# Get load average
get_load_average() {
    uptime | awk -F'load average:' '{print $2}'
}

# Monitor CPU
echo "CPU Usage: $(get_cpu_usage)%"
echo "Load Average:$(get_load_average)"

# Per-core CPU usage
mpstat -P ALL 1 1
```

**Example Output:**
```
CPU Usage: 25.3%
Load Average: 1.23, 1.45, 1.67

Linux 5.15.0 (server)     11/18/2025      _x86_64_        (4 CPU)

10:30:15 AM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
10:30:16 AM  all   15.25    0.00    5.00    2.50    0.00    0.25    0.00    0.00    0.00   77.00
10:30:16 AM    0   20.00    0.00    5.00    0.00    0.00    0.00    0.00    0.00    0.00   75.00
10:30:16 AM    1   12.00    0.00    3.00    5.00    0.00    1.00    0.00    0.00    0.00   79.00
```

---

### 12.2 Memory Monitoring

```bash
#!/bin/bash

# Memory usage
free -h

# Detailed memory info
cat /proc/meminfo

# Memory by process
ps aux --sort=-%mem | head -10

# Memory usage percentage
memory_usage() {
    free | grep Mem | awk '{printf "%.2f", $3/$2 * 100}'
}

echo "Memory Usage: $(memory_usage)%"

# Check for OOM kills
dmesg | grep -i "out of memory"
grep -i "killed process" /var/log/syslog
```

**Example:**
```bash
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           15Gi       8.2Gi       2.1Gi       324Mi       5.1Gi       6.5Gi
Swap:         2.0Gi       512Mi       1.5Gi

$ ps aux --sort=-%mem | head -5
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
www-data  1234  5.2 12.5 2048576 2097152 ?      Sl   10:00   1:23 /usr/bin/node server.js
mysql     5678 15.1  8.3 1048576 1048576 ?      Ssl  09:00   5:45 /usr/sbin/mysqld
```

---

### 12.3 Disk Monitoring

```bash
#!/bin/bash

# Disk usage
df -h

# Inode usage
df -i

# Disk usage by directory
du -h --max-depth=1 /var | sort -hr

# Find large files
find / -type f -size +100M -exec ls -lh {} \; 2>/dev/null | awk '{print $9, $5}'

# Disk I/O statistics
iostat -x 1 5

# Check disk health
smartctl -a /dev/sda
```

**Disk Alert Script:**
```bash
#!/bin/bash

THRESHOLD=80
EMAIL="admin@example.com"

check_disk_usage() {
    df -h | awk 'NR>1 {gsub(/%/,"",$5); if($5 > '$THRESHOLD') print $0}' | while read line; do
        filesystem=$(echo "$line" | awk '{print $1}')
        usage=$(echo "$line" | awk '{print $5}')
        mount=$(echo "$line" | awk '{print $6}')
        
        echo "ALERT: $filesystem mounted on $mount is ${usage}% full"
        
        # Send email (requires mailutils)
        # echo "Disk $mount is ${usage}% full" | mail -s "Disk Alert" $EMAIL
    done
}

check_disk_usage
```

---

### 12.4 Network Monitoring

```bash
#!/bin/bash

# Network interfaces
ip addr show
ifconfig  # Legacy

# Network statistics
netstat -i

# Listening ports
netstat -tuln
ss -tuln  # Modern alternative

# Active connections
netstat -ant

# Network bandwidth usage
iftop  # If installed
nload  # If installed

# Connection count by state
netstat -ant | awk '{print $6}' | sort | uniq -c | sort -rn

# Top talkers (IP addresses with most connections)
netstat -ntu | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -rn | head -10
```

**Example:**
```bash
$ netstat -tuln
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN
tcp6       0      0 :::3306                 :::*                    LISTEN

$ netstat -ant | awk '{print $6}' | sort | uniq -c | sort -rn
    142 ESTABLISHED
     25 TIME_WAIT
     12 LISTEN
      5 CLOSE_WAIT
```

---

### 12.5 Comprehensive Monitoring Script

```bash
#!/bin/bash

ALERT_EMAIL="admin@example.com"
LOG_FILE="/var/log/system_monitor.log"

# Thresholds
CPU_THRESHOLD=80
MEM_THRESHOLD=85
DISK_THRESHOLD=90

log_message() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

check_cpu() {
    cpu_usage=$(top -bn1 | grep "Cpu(s)" | sed "s/.*, *\([0-9.]*\)%* id.*/\1/" | awk '{print 100 - $1}')
    
    if (( $(echo "$cpu_usage > $CPU_THRESHOLD" | bc -l) )); then
        log_message "ALERT: CPU usage is ${cpu_usage}%"
        return 1
    else
        log_message "OK: CPU usage is ${cpu_usage}%"
        return 0
    fi
}

check_memory() {
    mem_usage=$(free | grep Mem | awk '{printf "%.2f", $3/$2 * 100}')
    
    if (( $(echo "$mem_usage > $MEM_THRESHOLD" | bc -l) )); then
        log_message "ALERT: Memory usage is ${mem_usage}%"
        # Log top memory consumers
        ps aux --sort=-%mem | head -5 >> "$LOG_FILE"
        return 1
    else
        log_message "OK: Memory usage is ${mem_usage}%"
        return 0
    fi
}

check_disk() {
    local alert=0
    
    while read line; do
        usage=$(echo "$line" | awk '{print $5}' | sed 's/%//')
        mount=$(echo "$line" | awk '{print $6}')
        
        if [[ $usage -gt $DISK_THRESHOLD ]]; then
            log_message "ALERT: Disk $mount is ${usage}% full"
            alert=1
        fi
    done < <(df -h | awk 'NR>1 && $5+0 > 0')
    
    if [[ $alert -eq 0 ]]; then
        log_message "OK: All disks within threshold"
    fi
    
    return $alert
}

check_services() {
    local services=("nginx" "mysql" "sshd")
    local failed=0
    
    for service in "${services[@]}"; do
        if systemctl is-active --quiet "$service"; then
            log_message "OK: $service is running"
        else
            log_message "ALERT: $service is not running!"
            failed=1
        fi
    done
    
    return $failed
}

# Main execution
log_message "=== Starting System Monitor ==="

check_cpu
check_memory
check_disk
check_services

log_message "=== Monitor Complete ==="
```

---

## 13. Network Commands

### 13.1 Network Configuration

```bash
# Show IP addresses
ip addr show
ip a  # Short form

# Show specific interface
ip addr show eth0

# Add IP address
sudo ip addr add 192.168.1.100/24 dev eth0

# Delete IP address
sudo ip addr del 192.168.1.100/24 dev eth0

# Bring interface up/down
sudo ip link set eth0 up
sudo ip link set eth0 down

# Show routing table
ip route show
route -n  # Legacy

# Add route
sudo ip route add 192.168.2.0/24 via 192.168.1.1

# Show ARP cache
ip neigh show
arp -a  # Legacy
```

---

### 13.2 Network Testing

```bash
# Ping host
ping -c 4 google.com

# Trace route
traceroute google.com
tracepath google.com

# DNS lookup
nslookup google.com
dig google.com
host google.com

# Test port connectivity
telnet hostname 80
nc -zv hostname 80  # netcat

# HTTP request
curl -I https://google.com
wget --spider https://google.com

# Download speed test
curl -o /dev/null https://speed.cloudflare.com/__down?bytes=100000000
```

**Example:**
```bash
$ ping -c 4 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=118 time=12.3 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=118 time=11.8 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=118 time=12.1 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=118 time=12.0 ms

--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 11.841/12.050/12.307/0.169 ms

$ dig google.com +short
142.250.185.46
```

---

### 13.3 Network Monitoring

```bash
# Monitor network traffic
tcpdump -i eth0

# Capture specific port
tcpdump -i eth0 port 80

# Save capture to file
tcpdump -i eth0 -w capture.pcap

# Monitor bandwidth
iftop -i eth0
nethogs  # By process

# Show socket statistics
ss -s

# TCP connections
ss -t

# UDP connections
ss -u

# Listening sockets
ss -l
```

---

### 13.4 Firewall (iptables/firewalld)

```bash
# List rules
sudo iptables -L -n -v

# Allow incoming port
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Block IP address
sudo iptables -A INPUT -s 192.168.1.100 -j DROP

# Save rules
sudo iptables-save > /etc/iptables/rules.v4

# With firewalld
sudo firewall-cmd --list-all
sudo firewall-cmd --add-port=8080/tcp --permanent
sudo firewall-cmd --reload
```

---

### 13.5 Network Troubleshooting Script

```bash
#!/bin/bash

network_diagnostics() {
    local target=${1:-google.com}
    
    echo "=== Network Diagnostics ==="
    echo "Target: $target"
    echo
    
    echo "--- Interface Status ---"
    ip link show | grep -E "^[0-9]|state"
    echo
    
    echo "--- IP Addresses ---"
    ip -4 addr show | grep inet
    echo
    
    echo "--- Default Gateway ---"
    ip route | grep default
    echo
    
    echo "--- DNS Resolution ---"
    if nslookup "$target" > /dev/null 2>&1; then
        echo "✓ DNS resolution successful"
        nslookup "$target" | grep -A1 "Name:"
    else
        echo "✗ DNS resolution failed"
    fi
    echo
    
    echo "--- Connectivity Test ---"
    if ping -c 3 -W 2 "$target" > /dev/null 2>&1; then
        echo "✓ Ping successful to $target"
        ping -c 3 "$target" | tail -2
    else
        echo "✗ Ping failed to $target"
    fi
    echo
    
    echo "--- Port Connectivity ---"
    if nc -zv -w 2 "$target" 80 2>&1 | grep -q succeeded; then
        echo "✓ Port 80 is reachable on $target"
    else
        echo "✗ Port 80 is not reachable on $target"
    fi
    echo
    
    echo "--- Active Connections ---"
    netstat -ant | grep ESTABLISHED | wc -l | xargs echo "Established connections:"
    echo
    
    echo "--- Network Statistics ---"
    ss -s
}

network_diagnostics "$@"
```

---

## 14. Permissions & Ownership

### 14.1 File Permissions Basics

```bash
# Permission format: rwxrwxrwx
# r = read (4), w = write (2), x = execute (1)
# First three: Owner, Next three: Group, Last three: Others

# View permissions
ls -l file.txt

# Change permissions (symbolic)
chmod u+x file.sh          # Add execute for user
chmod g-w file.txt         # Remove write for group
chmod o+r file.txt         # Add read for others
chmod a+x file.sh          # Add execute for all

# Change permissions (numeric)
chmod 644 file.txt         # rw-r--r--
chmod 755 script.sh        # rwxr-xr-x
chmod 700 private.txt      # rwx------
chmod 600 secret.txt       # rw-------
```

**Permission Numbers:**
```
7 = rwx (read + write + execute)
6 = rw- (read + write)
5 = r-x (read + execute)
4 = r-- (read only)
3 = -wx (write + execute)
2 = -w- (write only)
1 = --x (execute only)
0 = --- (no permissions)
```

**Example:**
```bash
$ ls -l
-rw-r--r-- 1 user group  1234 Nov 18 10:30 file.txt
-rwxr-xr-x 1 user group  5678 Nov 18 10:31 script.sh
drwxr-xr-x 2 user group  4096 Nov 18 10:32 directory

$ chmod 755 file.txt
$ ls -l file.txt
-rwxr-xr-x 1 user group  1234 Nov 18 10:30 file.txt

$ chmod u-x,g+w file.txt
$ ls -l file.txt
-rw-rwxr-x 1 user group  1234 Nov 18 10:30 file.txt
```

---

### 14.2 Ownership

```bash
# Change owner
chown user file.txt

# Change owner and group
chown user:group file.txt

# Change only group
chgrp group file.txt
chown :group file.txt  # Alternative

# Recursive ownership change
chown -R user:group directory/

# Change owner of symbolic link (not target)
chown -h user:group symlink
```

**Example:**
```bash
$ ls -l
-rw-r--r-- 1 olduser oldgroup  1234 Nov 18 10:30 file.txt

$ sudo chown newuser:newgroup file.txt

$ ls -l
-rw-r--r-- 1 newuser newgroup  1234 Nov 18 10:30 file.txt

# Change ownership of all files in directory
$ sudo chown -R www-data:www-data /var/www/html/
```

---

### 14.3 Special Permissions

#### SUID (Set User ID)

```bash
# Set SUID bit (file executes with owner's privileges)
chmod u+s file
chmod 4755 file

# Example: /usr/bin/passwd has SUID
ls -l /usr/bin/passwd
# -rwsr-xr-x 1 root root 59640 passwd
```

#### SGID (Set Group ID)

```bash
# Set SGID bit
chmod g+s directory
chmod 2755 directory

# Files created in directory inherit group
chmod g+s /shared/project/
```

#### Sticky Bit

```bash
# Set sticky bit (only owner can delete files)
chmod +t directory
chmod 1777 directory

# Example: /tmp has sticky bit
ls -ld /tmp
# drwxrwxrwt 15 root root 4096 /tmp
```

**Example:**
```bash
$ mkdir shared
$ chmod 1777 shared
$ ls -ld shared
drwxrwxrwt 2 user group 4096 Nov 18 10:30 shared

# Now only file owners can delete their files in this directory
```

---

### 14.4 Default Permissions (umask)

```bash
# View current umask
umask

# Set umask
umask 022    # Files: 644, Directories: 755
umask 077    # Files: 600, Directories: 700

# Explanation:
# Default file permissions: 666 (rw-rw-rw-)
# Default dir permissions:  777 (rwxrwxrwx)
# Umask subtracts from these

# umask 022:
# Files:  666 - 022 = 644 (rw-r--r--)
# Dirs:   777 - 022 = 755 (rwxr-xr-x)
```

---

### 14.5 Access Control Lists (ACL)

```bash
# View ACL
getfacl file.txt

# Set ACL for user
setfacl -m u:username:rwx file.txt

# Set ACL for group
setfacl -m g:groupname:rx file.txt

# Remove ACL
setfacl -x u:username file.txt

# Remove all ACLs
setfacl -b file.txt

# Copy ACL from one file to another
getfacl file1.txt | setfacl --set-file=- file2.txt

# Recursive ACL
setfacl -R -m u:username:rwx directory/

# Default ACL for new files in directory
setfacl -d -m u:username:rwx directory/
```

**Example:**
```bash
$ getfacl report.txt
# file: report.txt
# owner: user
# group: group
user::rw-
group::r--
other::r--

$ setfacl -m u:john:rwx report.txt
$ getfacl report.txt
# file: report.txt
# owner: user
# group: group
user::rw-
user:john:rwx
group::r--
mask::rwx
other::r--
```

---

### 14.6 Permission Troubleshooting Script

```bash
#!/bin/bash

check_permissions() {
    local file=$1
    
    if [[ ! -e "$file" ]]; then
        echo "Error: File does not exist"
        return 1
    fi
    
    echo "=== Permission Analysis for: $file ==="
    echo
    
    # Basic info
    echo "--- File Info ---"
    ls -l "$file"
    echo
    
    # Ownership
    echo "--- Ownership ---"
    stat -c "Owner: %U (%u)" "$file"
    stat -c "Group: %G (%g)" "$file"
    echo
    
    # Permissions in different formats
    echo "--- Permissions ---"
    stat -c "Symbolic: %A" "$file"
    stat -c "Octal: %a" "$file"
    echo
    
    # Special bits
    echo "--- Special Permissions ---"
    if [[ $(stat -c "%a" "$file" | cut -c1) -ge 4 ]]; then
        echo "✓ SUID bit is set"
    fi
    if [[ $(stat -c "%a" "$file" | cut -c1) -ge 2 ]]; then
        echo "✓ SGID bit is set"
    fi
    if [[ $(stat -c "%a" "$file" | cut -c1) -eq 1 ]]; then
        echo "✓ Sticky bit is set"
    fi
    echo
    
    # Access checks
    echo "--- Access Rights ---"
    [[ -r "$file" ]] && echo "✓ Readable" || echo "✗ Not readable"
    [[ -w "$file" ]] && echo "✓ Writable" || echo "✗ Not writable"
    [[ -x "$file" ]] && echo "✓ Executable" || echo "✗ Not executable"
    echo
    
    # ACL if present
    if getfacl "$file" 2>/dev/null | grep -q "^user:"; then
        echo "--- Access Control Lists ---"
        getfacl "$file" 2>/dev/null
    fi
}

check_permissions "$1"
```

---

## 15. Error Handling & Debugging

### 15.1 Exit Codes

```bash
#!/bin/bash

# Exit codes: 0 = success, 1-255 = error

# Custom exit codes
command
if [[ $? -eq 0 ]]; then
    echo "Success"
else
    echo "Failed"
    exit 1
fi

# Exit on error
set -e  # Exit immediately if command fails

# Exit on undefined variable
set -u

# Show commands being executed
set -x

# Combine options
set -euxo pipefail
```

**Example:**
```bash
#!/bin/bash

# Good practice: exit immediately on error
set -e

backup_file() {
    local source=$1
    local dest=$2
    
    if [[ ! -f "$source" ]]; then
        echo "Error: Source file not found"
        return 1
    fi
    
    cp "$source" "$dest" || {
        echo "Error: Copy failed"
        return 2
    }
    
    echo "Backup successful"
    return 0
}

# Use the function
if backup_file "important.txt" "/backup/important.txt"; then
    echo "Operation completed successfully"
else
    exit_code=$?
    echo "Operation failed with code: $exit_code"
    exit $exit_code
fi
```

---

### 15.2 Error Handling Patterns

```bash
#!/bin/bash

# Pattern 1: Check command success
if command; then
    echo "Success"
else
    echo "Failed"
fi

# Pattern 2: Short-circuit operators
command && echo "Success" || echo "Failed"

# Pattern 3: Trap errors
trap 'echo "Error on line $LINENO"' ERR

# Pattern 4: Custom error handler
error_exit() {
    echo "Error: $1" >&2
    exit 1
}

[[ -f "$file" ]] || error_exit "File not found: $file"

# Pattern 5: Try-catch style
{
    command1
    command2
    command3
} || {
    echo "One of the commands failed"
    exit 1
}
```

**Comprehensive Error Handling Example:**
```bash
#!/bin/bash

set -euo pipefail

# Error handler
error_handler() {
    local line_num=$1
    local error_code=$2
    echo "Error occurred in script at line: $line_num with exit code: $error_code" >&2
    cleanup
    exit $error_code
}

# Cleanup function
cleanup() {
    echo "Performing cleanup..."
    # Remove temp files, close connections, etc.
    [[ -f "$temp_file" ]] && rm -f "$temp_file"
}

# Set trap
trap 'error_handler ${LINENO} $?' ERR
trap cleanup EXIT

# Script logic
temp_file=$(mktemp)

echo "Processing..."
# Your commands here

echo "Script completed successfully"
```

---

### 15.3 Debugging Techniques

```bash
#!/bin/bash

# Enable debug mode
set -x  # Print commands before execution

# Selective debugging
set -x
critical_command
set +x

# Custom debug function
DEBUG=true

debug() {
    if [[ "$DEBUG" == "true" ]]; then
        echo "[DEBUG] $*" >&2
    fi
}

debug "Variable value: $var"
debug "About to execute important command"

# Verbose mode
VERBOSE=true

verbose() {
    if [[ "$VERBOSE" == "true" ]]; then
        echo "[INFO] $*"
    fi
}

verbose "Starting process..."

# Debug with file and line numbers
debug_trace() {
    echo "[${BASH_SOURCE[1]##*/}:${BASH_LINENO[0]}] ${FUNCNAME[1]}: $*" >&2
}

my_function() {
    debug_trace "Entering function"
    # Function code
    debug_trace "Exiting function"
}
```

---

### 15.4 Logging

```bash
#!/bin/bash

LOG_FILE="/var/log/myscript.log"
LOG_LEVEL="INFO"  # DEBUG, INFO, WARN, ERROR

# Logging function
log() {
    local level=$1
    shift
    local message="$*"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    
    echo "[$timestamp] [$level] $message" | tee -a "$LOG_FILE"
    
    # Also log to syslog
    logger -t myscript -p user.$level "$message"
}

# Convenience functions
log_debug() { [[ "$LOG_LEVEL" == "DEBUG" ]] && log "DEBUG" "$*"; }
log_info() { log "INFO" "$*"; }
log_warn() { log "WARN" "$*"; }
log_error() { log "ERROR" "$*"; }

# Usage
log_info "Script started"
log_debug "Debug information: variable=$var"
log_warn "Warning: disk space low"
log_error "Error: connection failed"
```

**Output:**
```
[2025-11-18 10:30:15] [INFO] Script started
[2025-11-18 10:30:15] [DEBUG] Debug information: variable=value
[2025-11-18 10:30:16] [WARN] Warning: disk space low
[2025-11-18 10:30:17] [ERROR] Error: connection failed
```

---

### 15.5 Testing Scripts

```bash
#!/bin/bash

# Dry-run mode
DRY_RUN=false

execute() {
    if [[ "$DRY_RUN" == "true" ]]; then
        echo "[DRY-RUN] Would execute: $*"
    else
        "$@"
    fi
}

# Usage
execute rm -f /important/file.txt

# Test function
test_function() {
    local test_name=$1
    local expected=$2
    local actual=$3
    
    if [[ "$expected" == "$actual" ]]; then
        echo "✓ $test_name: PASSED"
        return 0
    else
        echo "✗ $test_name: FAILED (expected: $expected, got: $actual)"
        return 1
    fi
}

# Run tests
run_tests() {
    local passed=0
    local failed=0
    
    echo "=== Running Tests ==="
    
    if test_function "Addition test" "3" "$(echo $((1 + 2)))"; then
        ((passed++))
    else
        ((failed++))
    fi
    
    if test_function "String test" "hello" "hello"; then
        ((passed++))
    else
        ((failed++))
    fi
    
    echo
    echo "Results: $passed passed, $failed failed"
    
    return $failed
}

run_tests
```

---

## 16. Advanced Topics

### 16.1 Signal Handling

```bash
#!/bin/bash

# Trap signals
trap 'echo "Interrupted!"; exit 1' INT TERM
trap 'echo "Cleaning up..."; cleanup; exit' EXIT

cleanup() {
    # Cleanup code
    rm -f /tmp/myapp.*
    echo "Cleanup complete"
}

# Ignore signal
trap '' HUP

# Reset trap
trap - INT

# Example: Graceful shutdown
shutdown_flag=false

graceful_shutdown() {
    echo "Received shutdown signal, finishing current task..."
    shutdown_flag=true
}

trap graceful_shutdown SIGTERM SIGINT

while true; do
    if [[ "$shutdown_flag" == "true" ]]; then
        echo "Shutting down gracefully"
        break
    fi
    
    # Do work
    echo "Working..."
    sleep 2
done

echo "Shutdown complete"
```

---

### 16.2 Parallel Processing

```bash
#!/bin/bash

# Background jobs
for i in {1..5}; do
    (
        echo "Task $i started"
        sleep $((RANDOM % 5 + 1))
        echo "Task $i completed"
    ) &
done

wait  # Wait for all background jobs

echo "All tasks completed"

# GNU Parallel
parallel echo "Processing: {}" ::: file1 file2 file3

# Parallel with function
process_file() {
    local file=$1
    echo "Processing $file"
    # Process file
}
export -f process_file

find . -name "*.txt" | parallel -j 4 process_file {}

# xargs parallel
find . -name "*.log" | xargs -P 4 -I {} gzip {}
```

**Example with job control:**
```bash
#!/bin/bash

MAX_JOBS=4
job_count=0

for file in *.txt; do
    # Wait if max jobs reached
    while [[ $(jobs -r | wc -l) -ge $MAX_JOBS ]]; do
        sleep 0.1
    done
    
    # Start new job
    (
        echo "Processing $file"
        gzip "$file"
    ) &
done

wait
echo "All files compressed"
```

---

### 16.3 Inter-Process Communication

#### Named Pipes (FIFOs)

```bash
#!/bin/bash

# Create named pipe
mkfifo /tmp/mypipe

# Writer (background)
(
    for i in {1..5}; do
        echo "Message $i"
        sleep 1
    done > /tmp/mypipe
) &

# Reader
while read line; do
    echo "Received: $line"
done < /tmp/mypipe

# Cleanup
rm /tmp/mypipe
```

#### Process Substitution

```bash
# Compare output of two commands
diff <(ls dir1) <(ls dir2)

# Multiple inputs
paste <(seq 1 5) <(seq 6 10)

# Output:
# 1    6
# 2    7
# 3    8
# 4    9
# 5    10
```

---

### 16.4 Coproces (Bash 4+)

```bash
#!/bin/bash

# Start coprocess
coproc CAT { cat; }

# Write to coprocess
echo "Hello" >&${CAT[1]}

# Read from coprocess
read -u ${CAT[0]} response
echo "Response: $response"

# Close coprocess
exec {CAT[1]}>&-
exec {CAT[0]}<&-
wait $CAT_PID
```

---

### 16.5 Regular Expressions

```bash
#!/bin/bash

# Basic regex matching
if [[ "$string" =~ ^[0-9]+$ ]]; then
    echo "String is numeric"
fi

# Capture groups
if [[ "$email" =~ ^([a-zA-Z0-9._%+-]+)@([a-zA-Z0-9.-]+)\.([a-zA-Z]{2,})$ ]]; then
    username="${BASH_REMATCH[1]}"
    domain="${BASH_REMATCH[2]}"
    tld="${BASH_REMATCH[3]}"
    
    echo "Username: $username"
    echo "Domain: $domain"
    echo "TLD: $tld"
fi

# Validate patterns
validate_ip() {
    local ip=$1
    local pattern='^([0-9]{1,3}\.){3}[0-9]{1,3}
    
    if [[ $ip =~ $pattern ]]; then
        # Check ranges
        local IFS='.'
        local -a octets=($ip)
        for octet in "${octets[@]}"; do
            if ((octet > 255)); then
                return 1
            fi
        done
        return 0
    fi
    return 1
}

if validate_ip "192.168.1.1"; then
    echo "Valid IP"
else
    echo "Invalid IP"
fi
```

---

### 16.6 Command Line Argument Parsing

```bash
#!/bin/bash

# Simple argument parsing
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            echo "Usage: $0 [-h] [-v] [-f file]"
            exit 0
            ;;
        -v|--verbose)
            VERBOSE=true
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

# Using getopts (POSIX compliant)
while getopts ":hvf:" opt; do
    case $opt in
        h)
            echo "Usage: $0 [-h] [-v] [-f file]"
            exit 0
            ;;
        v)
            VERBOSE=true
            ;;
        f)
            FILE="$OPTARG"
            ;;
        \?)
            echo "Invalid option: -$OPTARG" >&2
            exit 1
            ;;
        :)
            echo "Option -$OPTARG requires an argument" >&2
            exit 1
            ;;
    esac
done

shift $((OPTIND-1))
```

---

## 17. Real-World DevOps Scripts

### 17.1 Automated Backup Script

```bash
#!/bin/bash

set -euo pipefail

# Configuration
BACKUP_SOURCE="/var/www"
BACKUP_DEST="/backup"
RETENTION_DAYS=7
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_NAME="backup_${TIMESTAMP}.tar.gz"
LOG_FILE="/var/log/backup.log"

# Logging
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"
}

# Notification function
send_notification() {
    local status=$1
    local message=$2
    
    # Send email (requires mailutils)
    # echo "$message" | mail -s "Backup $status" admin@example.com
    
    log "$status: $message"
}

# Create backup
create_backup() {
    log "Starting backup of $BACKUP_SOURCE"
    
    cd "$BACKUP_SOURCE" || {
        send_notification "FAILED" "Cannot access source directory"
        exit 1
    }
    
    tar -czf "${BACKUP_DEST}/${BACKUP_NAME}" . || {
        send_notification "FAILED" "Backup creation failed"
        exit 1
    }
    
    local backup_size=$(du -h "${BACKUP_DEST}/${BACKUP_NAME}" | cut -f1)
    log "Backup created: ${BACKUP_NAME} (${backup_size})"
}

# Verify backup
verify_backup() {
    log "Verifying backup integrity"
    
    if tar -tzf "${BACKUP_DEST}/${BACKUP_NAME}" > /dev/null; then
        log "Backup verification successful"
    else
        send_notification "FAILED" "Backup verification failed"
        exit 1
    fi
}

# Cleanup old backups
cleanup_old_backups() {
    log "Removing backups older than $RETENTION_DAYS days"
    
    find "$BACKUP_DEST" -name "backup_*.tar.gz" -mtime +$RETENTION_DAYS -delete
    
    local remaining=$(find "$BACKUP_DEST" -name "backup_*.tar.gz" | wc -l)
    log "Remaining backups: $remaining"
}

# Main execution
main() {
    log "=== Backup Script Started ==="
    
    # Check disk space
    local available_space=$(df "$BACKUP_DEST" | awk 'NR==2 {print $4}')
    if [[ $available_space -lt 1048576 ]]; then  # Less than 1GB
        send_notification "WARNING" "Low disk space: ${available_space}KB"
    fi
    
    create_backup
    verify_backup
    cleanup_old_backups
    
    send_notification "SUCCESS" "Backup completed successfully"
    log "=== Backup Script Completed ==="
}

main
```

---

### 17.2 Application Deployment Script

```bash
#!/bin/bash

set -euo pipefail

# Configuration
APP_NAME="myapp"
APP_DIR="/opt/myapp"
REPO_URL="https://github.com/user/myapp.git"
BRANCH="main"
SERVICE_NAME="myapp.service"
HEALTH_CHECK_URL="http://localhost:8080/health"
BACKUP_DIR="/opt/backups"

# Colors for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

log_info() {
    echo -e "${GREEN}[INFO]${NC} $*"
}

log_warn() {
    echo -e "${YELLOW}[WARN]${NC} $*"
}

log_error() {
    echo -e "${RED}[ERROR]${NC} $*"
}

# Pre-deployment checks
pre_deploy_checks() {
    log_info "Running pre-deployment checks"
    
    # Check if git is installed
    if ! command -v git &> /dev/null; then
        log_error "Git is not installed"
        exit 1
    fi
    
    # Check disk space
    local available_space=$(df "$APP_DIR" | awk 'NR==2 {print $4}')
    if [[ $available_space -lt 524288 ]]; then  # Less than 512MB
        log_error "Insufficient disk space"
        exit 1
    fi
    
    log_info "Pre-deployment checks passed"
}

# Backup current version
backup_current() {
    log_info "Backing up current version"
    
    if [[ -d "$APP_DIR" ]]; then
        local backup_name="backup_$(date +%Y%m%d_%H%M%S)"
        cp -r "$APP_DIR" "${BACKUP_DIR}/${backup_name}"
        log_info "Backup created: ${backup_name}"
    fi
}

# Deploy new version
deploy() {
    log_info "Deploying new version"
    
    # Stop service
    log_info "Stopping service"
    sudo systemctl stop "$SERVICE_NAME" || true
    
    # Pull latest code
    if [[ -d "$APP_DIR/.git" ]]; then
        cd "$APP_DIR"
        git fetch origin
        git checkout "$BRANCH"
        git pull origin "$BRANCH"
    else
        git clone -b "$BRANCH" "$REPO_URL" "$APP_DIR"
    fi
    
    # Install dependencies
    log_info "Installing dependencies"
    cd "$APP_DIR"
    npm install --production || true
    
    # Build application
    log_info "Building application"
    npm run build || true
    
    # Start service
    log_info "Starting service"
    sudo systemctl start "$SERVICE_NAME"
    
    log_info "Deployment complete"
}

# Health check
health_check() {
    log_info "Performing health check"
    
    local max_attempts=30
    local attempt=0
    
    while [[ $attempt -lt $max_attempts ]]; do
        if curl -sf "$HEALTH_CHECK_URL" > /dev/null; then
            log_info "Health check passed"
            return 0
        fi
        
        ((attempt++))
        log_warn "Health check attempt $attempt/$max_attempts failed, retrying..."
        sleep 2
    done
    
    log_error "Health check failed after $max_attempts attempts"
    return 1
}

# Rollback
rollback() {
    log_warn "Rolling back to previous version"
    
    local latest_backup=$(ls -t "$BACKUP_DIR" | head -1)
    
    if [[ -n "$latest_backup" ]]; then
        sudo systemctl stop "$SERVICE_NAME"
        rm -rf "$APP_DIR"
        cp -r "${BACKUP_DIR}/${latest_backup}" "$APP_DIR"
        sudo systemctl start "$SERVICE_NAME"
        log_info "Rollback complete"
    else
        log_error "No backup found for rollback"
        exit 1
    fi
}

# Main execution
main() {
    log_info "=== Starting Deployment ==="
    
    pre_deploy_checks
    backup_current
    deploy
    
    if health_check; then
        log_info "=== Deployment Successful ==="
    else
        log_error "=== Deployment Failed ==="
        rollback
        
        if health_check; then
            log_info "=== Rollback Successful ==="
        else
            log_error "=== Rollback Failed - Manual Intervention Required ==="
            exit 1
        fi
    fi
}

main
```

---

### 17.3 Docker Container Management Script

```bash
#!/bin/bash

set -euo pipefail

# Configuration
CONTAINER_NAME="webapp"
IMAGE_NAME="webapp:latest"
CONTAINER_PORT=8080
HOST_PORT=80
VOLUME_PATH="/data/webapp"

# Container operations
container_status() {
    docker ps -a --filter "name=${CONTAINER_NAME}" --format "{{.Status}}"
}

is_running() {
    [[ $(container_status) =~ ^Up ]]
}

start_container() {
    echo "Starting container: $CONTAINER_NAME"
    
    if is_running; then
        echo "Container is already running"
        return 0
    fi
    
    # Check if container exists but stopped
    if docker ps -a --filter "name=${CONTAINER_NAME}" | grep -q "$CONTAINER_NAME"; then
        docker start "$CONTAINER_NAME"
    else
        # Create and start new container
        docker run -d \
            --name "$CONTAINER_NAME" \
            -p "${HOST_PORT}:${CONTAINER_PORT}" \
            -v "${VOLUME_PATH}:/app/data" \
            --restart unless-stopped \
            "$IMAGE_NAME"
    fi
    
    echo "Container started successfully"
}

stop_container() {
    echo "Stopping container: $CONTAINER_NAME"
    
    if ! is_running; then
        echo "Container is not running"
        return 0
    fi
    
    docker stop "$CONTAINER_NAME"
    echo "Container stopped"
}

restart_container() {
    echo "Restarting container: $CONTAINER_NAME"
    stop_container
    sleep 2
    start_container
}

