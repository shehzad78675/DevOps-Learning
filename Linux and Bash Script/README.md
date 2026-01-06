# 🐧 Essential Linux Commands Guide

> Master the command line with this comprehensive guide to Linux/Unix commands for DevOps and System Administration

[![Linux](https://img.shields.io/badge/Linux-Commands-yellow.svg)](https://github.com)
[![Shell](https://img.shields.io/badge/Shell-Bash-green.svg)](https://github.com)

---

## 📋 Table of Contents

- [Introduction](#-introduction)
- [File Management](#-file-management)
- [Directory Operations](#-directory-operations)
- [File Viewing & Editing](#-file-viewing--editing)
- [Permissions & Ownership](#-permissions--ownership)
- [System Information](#-system-information)
- [Process Management](#-process-management)
- [Shell Scripting Basics](#-shell-scripting-basics)
- [Advanced Commands](#-advanced-commands)
- [Quick Reference](#-quick-reference)

---

## 🎯 Introduction

This guide covers essential Linux commands that every developer, DevOps engineer, and system administrator should know. From basic file operations to advanced system management, you'll find practical examples and explanations for each command.

---

## 📁 File Management

### Creating Files

#### `touch` - Create Empty Files

Creates a new empty file or updates the timestamp of an existing file.

```bash
# Create a single file
touch myfile.txt

# Create multiple files
touch file1.txt file2.txt file3.txt

# Create a file with a specific timestamp
touch -t 202401010000 oldfile.txt

# Update only access time
touch -a file.txt

# Update only modification time
touch -m file.txt
```

**Use Cases:**
- Creating placeholder files
- Updating file modification times
- Creating files for testing purposes

#### `cp` - Copy Files and Directories

```bash
# Copy a file
cp source.txt destination.txt

# Copy a directory recursively
cp -r /source/directory /destination/directory

# Copy with verbose output
cp -v file1.txt file2.txt

# Copy and preserve attributes (permissions, timestamps, ownership)
cp -p original.txt backup.txt

# Copy preserving all attributes (archive mode)
cp -a /source/dir /backup/dir

# Copy multiple files to a directory
cp file1.txt file2.txt file3.txt /destination/folder/

# Interactive mode (prompt before overwrite)
cp -i source.txt destination.txt

# Force copy (no prompt for overwrite)
cp -f source.txt destination.txt

# Copy only newer files
cp -u source.txt destination.txt

# Create backup of existing destination file
cp -b file.txt /destination/

# Copy and create hard link instead of copying data
cp -l file.txt hardlink.txt

# Copy symbolic link as a link (not the file it points to)
cp -d symlink newlink
```

**Common cp Options Combinations:**
```bash
cp -rp /source/ /backup/      # Recursive with preserved attributes
cp -av /source/ /backup/      # Archive mode with verbose
cp -ruv /source/ /backup/     # Update only newer files, verbose
```

#### `mv` - Move or Rename Files

```bash
# Rename a file
mv oldname.txt newname.txt

# Move a file to another directory
mv file.txt /path/to/directory/

# Move multiple files
mv file1.txt file2.txt file3.txt /destination/

# Move with interactive prompt (confirm before overwrite)
mv -i source.txt destination.txt

# Force move (no prompt for overwrite)
mv -f source.txt destination.txt

# Never overwrite existing files
mv -n source.txt destination.txt

# Create backup before overwriting
mv -b file.txt /destination/

# Move with verbose output
mv -v file.txt /destination/

# Move only if source is newer
mv -u oldfile.txt /destination/

# Rename multiple files with pattern
mv *.txt /destination/
```

**Practical Examples:**
```bash
# Rename with timestamp
mv report.txt report_$(date +%Y%m%d).txt

# Move all PDF files
mv *.pdf ~/Documents/

# Rename file extension
mv file.txt file.md

# Swap two filenames
mv file1.txt temp && mv file2.txt file1.txt && mv temp file2.txt
```

### Deleting Files

#### `rm` - Remove Files and Directories

```bash
# Remove a single file
rm file.txt

# Remove multiple files
rm file1.txt file2.txt file3.txt

# Remove directory and its contents recursively
rm -r directory_name

# Force remove without confirmation
rm -rf directory_name

# Remove with confirmation for each file
rm -i file.txt

# Remove all .log files
rm *.log

# Remove files with verbose output
rm -v file.txt

# Remove files older than 7 days
find . -type f -mtime +7 -exec rm {} \;

# Remove empty files
find . -type f -empty -delete

# Remove files except certain ones
rm !(*.txt)  # Remove all except .txt files (requires extglob)
```

⚠️ **Warning:** `rm -rf` is extremely powerful and irreversible. Always double-check before executing!

**Safe Deletion Practices:**
```bash
# Always use -i for important deletions
alias rm='rm -i'

# List files before deleting
ls *.log && rm *.log

# Move to trash instead of permanent deletion
mv file.txt ~/.trash/
```

### File Linking

#### `ln` - Create Links

```bash
# Create hard link
ln original.txt hardlink.txt

# Create symbolic (soft) link
ln -s /path/to/original.txt symlink.txt

# Create symbolic link with relative path
ln -sr ../file.txt link.txt

# Force creation (overwrite if exists)
ln -sf original.txt link.txt

# Create link to directory
ln -s /path/to/directory/ linkdir

# Create multiple links
ln -s /path/to/file.txt link1.txt link2.txt
```

**Hard Link vs Symbolic Link:**
```
Hard Link:
- Same inode number as original
- Cannot link directories
- Cannot span filesystems
- File remains accessible if original is deleted

Symbolic Link:
- Different inode number
- Can link directories
- Can span filesystems
- Breaks if original is deleted
```

### File Comparison

#### `diff` - Compare Files

```bash
# Compare two files
diff file1.txt file2.txt

# Side-by-side comparison
diff -y file1.txt file2.txt

# Unified format (like git diff)
diff -u file1.txt file2.txt

# Brief output (only show if files differ)
diff -q file1.txt file2.txt

# Ignore white space changes
diff -w file1.txt file2.txt

# Compare directories recursively
diff -r dir1/ dir2/

# Generate patch file
diff -u original.txt modified.txt > changes.patch
```

#### `cmp` - Compare Files Byte by Byte

```bash
# Compare two files
cmp file1.txt file2.txt

# Verbose output
cmp -l file1.txt file2.txt

# Silent mode (only return exit status)
cmp -s file1.txt file2.txt
```

### File Compression

#### `gzip` - Compress Files

```bash
# Compress a file
gzip file.txt  # Creates file.txt.gz and removes original

# Compress keeping original
gzip -k file.txt

# Decompress
gzip -d file.txt.gz
# or
gunzip file.txt.gz

# Compress with best compression
gzip -9 file.txt

# Compress multiple files
gzip file1.txt file2.txt file3.txt

# View compressed file without extracting
zcat file.txt.gz
zless file.txt.gz
```

#### `bzip2` - Better Compression

```bash
# Compress file
bzip2 file.txt

# Keep original file
bzip2 -k file.txt

# Decompress
bzip2 -d file.txt.bz2
# or
bunzip2 file.txt.bz2

# View compressed file
bzcat file.txt.bz2
```

#### `zip/unzip` - ZIP Archives

```bash
# Create zip archive
zip archive.zip file1.txt file2.txt

# Create zip with directory
zip -r archive.zip directory/

# Add password protection
zip -e -r secure.zip directory/

# Extract zip file
unzip archive.zip

# Extract to specific directory
unzip archive.zip -d /destination/

# List contents without extracting
unzip -l archive.zip

# Extract specific file
unzip archive.zip filename.txt
```

---

## 📂 Directory Operations

### `pwd` - Print Working Directory

Shows your current location in the filesystem.

```bash
pwd
# Output: /home/username/projects
```

### `mkdir` - Make Directory

```bash
# Create a single directory
mkdir my_folder

# Create multiple directories
mkdir folder1 folder2 folder3

# Create nested directories (parent directories too)
mkdir -p parent/child/grandchild

# Create directory with specific permissions
mkdir -m 755 my_folder
```

### `cd` - Change Directory

```bash
# Go to a specific directory
cd /var/log

# Go to home directory
cd ~
# or simply
cd

# Go up one level
cd ..

# Go up two levels
cd ../..

# Go to previous directory
cd -

# Navigate using relative path
cd documents/projects

# Go to root directory
cd /

# Navigate to user's home directory
cd ~username

# Navigate with spaces in directory name
cd "My Documents"
cd My\ Documents
```

**Pro Tips:**
```bash
# Use Tab completion for faster navigation
cd /var/lo[TAB]  # Auto-completes to /var/log

# Create and enter directory in one command
mkdir new_dir && cd new_dir

# Go back multiple directories
cd ../../..  # Go up 3 levels

# Bookmark directories with variables
export PROJ="/home/user/projects"
cd $PROJ
```

### Advanced Directory Operations

#### `pushd` / `popd` / `dirs` - Directory Stack

```bash
# Push current directory and change to new one
pushd /var/log

# Push another directory
pushd /etc

# Show directory stack
dirs -v

# Pop back to previous directory
popd

# Clear directory stack
dirs -c

# Rotate the stack
pushd +2  # Move to 3rd directory in stack
```

#### `tree` - Display Directory Tree

```bash
# Show directory tree (may need installation)
tree

# Limit depth
tree -L 2

# Show hidden files
tree -a

# Show only directories
tree -d

# Show file sizes
tree -h

# Colorize output
tree -C

# Output to file
tree > directory_structure.txt
```

### `rmdir` - Remove Empty Directory

```bash
# Remove an empty directory
rmdir empty_folder

# Remove multiple empty directories
rmdir folder1 folder2 folder3

# Remove parent directories if empty
rmdir -p parent/child/grandchild
```

### `ls` - List Directory Contents

```bash
# List files in current directory
ls

# List with detailed information
ls -l

# List all files including hidden ones
ls -a

# List with human-readable file sizes
ls -lh

# List sorted by modification time (newest first)
ls -lt

# List sorted by modification time (oldest first)
ls -ltr

# List with detailed info, sorted by time, all files
ls -ltra

# List only directories
ls -d */

# Recursive listing
ls -R

# List with inode numbers
ls -i

# List sorted by size
ls -lS

# List sorted by extension
ls -lX

# List with color coding
ls --color=auto

# List one file per line
ls -1

# List with full time information
ls -l --full-time

# List by modification time (full timestamp)
ls -l --time-style=full-iso

# Show file type indicators
ls -F
# / for directories, * for executables, @ for symlinks

# Reverse order
ls -lr
```

**Understanding `ls -l` output:**
```
-rw-r--r-- 1 user group 1234 Jan 01 12:00 file.txt
│││││││││  │ │    │     │    │           │
│││││││││  │ │    │     │    │           └─ filename
│││││││││  │ │    │     │    └─ modification time
│││││││││  │ │    │     └─ file size
│││││││││  │ │    └─ group
│││││││││  │ └─ owner
│││││││││  └─ number of hard links
└────────── permissions

File type indicators (first character):
- : regular file
d : directory
l : symbolic link
b : block device
c : character device
p : named pipe
s : socket
```

### File and Directory Information

#### `file` - Determine File Type

```bash
# Identify file type
file document.pdf
file image.jpg
file script.sh
file unknown_file

# Check multiple files
file *

# Brief output
file -b file.txt

# MIME type
file -i file.txt
```

#### `stat` - Display File Status

```bash
# Show detailed file information
stat file.txt

# Show filesystem status
stat -f /

# Custom format
stat -c "%n %s %y" file.txt
# %n = filename, %s = size, %y = modification time

# Display in terse mode
stat -t file.txt
```

#### `wc` - Word Count

```bash
# Count lines, words, and characters
wc file.txt

# Count only lines
wc -l file.txt

# Count only words
wc -w file.txt

# Count only characters
wc -c file.txt

# Count only bytes
wc -c file.txt

# Count multiple files
wc *.txt

# Count files in directory
ls | wc -l
```

### File Sorting

#### `sort` - Sort Lines

```bash
# Sort lines alphabetically
sort file.txt

# Sort in reverse order
sort -r file.txt

# Sort numerically
sort -n numbers.txt

# Sort by specific field/column
sort -k 2 file.txt

# Sort and remove duplicates
sort -u file.txt

# Sort ignoring case
sort -f file.txt

# Sort by month names
sort -M dates.txt

# Sort by human-readable numbers (1K, 1M, 1G)
sort -h sizes.txt
```

#### `uniq` - Remove Duplicate Lines

```bash
# Remove adjacent duplicate lines (must be sorted first)
sort file.txt | uniq

# Count occurrences
sort file.txt | uniq -c

# Show only duplicates
sort file.txt | uniq -d

# Show only unique lines
sort file.txt | uniq -u

# Ignore first N fields
uniq -f 1 file.txt

# Ignore first N characters
uniq -s 5 file.txt
```

### Text Manipulation

#### `cut` - Extract Sections from Lines

```bash
# Cut by character position
cut -c 1-10 file.txt

# Cut by field (default delimiter is tab)
cut -f 1,3 file.txt

# Cut by field with custom delimiter
cut -d ',' -f 1,2 data.csv

# Cut multiple fields
cut -d ':' -f 1,3,5 /etc/passwd

# Cut complement (everything except specified fields)
cut -d ',' --complement -f 2 data.csv
```

#### `paste` - Merge Lines of Files

```bash
# Merge two files side by side
paste file1.txt file2.txt

# Use custom delimiter
paste -d ',' file1.txt file2.txt

# Merge all lines from one file
paste -s file.txt

# Merge with custom delimiter for serial mode
paste -s -d ',' file.txt
```

#### `tr` - Translate or Delete Characters

```bash
# Convert lowercase to uppercase
tr 'a-z' 'A-Z' < file.txt

# Delete characters
tr -d '0-9' < file.txt

# Replace spaces with underscores
tr ' ' '_' < file.txt

# Squeeze repeated characters
tr -s ' ' < file.txt

# Delete and squeeze
echo "hello    world" | tr -ds ' ' ''

# Replace newlines with spaces
tr '\n' ' ' < file.txt
```

#### `tee` - Read from Input and Write to Output and Files

```bash
# Write to file and stdout
echo "test" | tee output.txt

# Append to file
echo "test" | tee -a output.txt

# Write to multiple files
echo "test" | tee file1.txt file2.txt file3.txt

# Use in pipeline
ls -l | tee listing.txt | grep "txt"

# Combine with sudo
echo "config" | sudo tee /etc/config.txt
```

---

## 👁️ File Viewing & Editing

### `cat` - Concatenate and Display Files

```bash
# Display file contents
cat file.txt

# Display multiple files
cat file1.txt file2.txt

# Combine files into a new file
cat file1.txt file2.txt > combined.txt

# Append to a file
cat file1.txt >> existing_file.txt

# Display with line numbers
cat -n file.txt

# Display non-printing characters
cat -v file.txt
```

### `less` - View File Contents (Paginated)

```bash
# View file with navigation
less largefile.txt

# Navigate:
# Space - next page
# b - previous page
# / - search forward
# ? - search backward
# q - quit
```

### `head` - Display Beginning of File

```bash
# Show first 10 lines (default)
head file.txt

# Show first 20 lines
head -n 20 file.txt

# Show first 5 lines of multiple files
head -n 5 file1.txt file2.txt
```

### `tail` - Display End of File

```bash
# Show last 10 lines (default)
tail file.txt

# Show last 20 lines
tail -n 20 file.txt

# Follow file in real-time (for logs)
tail -f /var/log/syslog

# Follow with retry (keeps trying if file doesn't exist)
tail -F logfile.log
```

### `vi/vim` - Text Editor

Vim is a powerful text editor with multiple modes.

```bash
# Open or create a file
vim filename.txt
```

**Vim Modes and Commands:**

<div align="center">

| Mode | Key | Purpose |
|------|-----|---------|
| **Command Mode** | `Esc` | Default mode for navigation |
| **Insert Mode** | `i` | Insert text before cursor |
| **Insert Mode** | `a` | Insert text after cursor |
| **Insert Mode** | `o` | Open new line below |
| **Insert Mode** | `O` | Open new line above |

</div>

**Essential Vim Commands:**

```
Navigation:
  h, j, k, l  - left, down, up, right
  w          - next word
  b          - previous word
  0          - beginning of line
  $          - end of line
  gg         - first line
  G          - last line

Editing:
  x          - delete character
  dd         - delete line
  yy         - copy line
  p          - paste
  u          - undo
  Ctrl+r     - redo

Saving and Quitting:
  :w         - save (write)
  :q         - quit
  :wq        - save and quit
  :wq!       - force save and quit
  :q!        - quit without saving
  :x         - save and quit (if changes made)

Search:
  /pattern   - search forward
  ?pattern   - search backward
  n          - next match
  N          - previous match
```

### `nano` - Simple Text Editor

User-friendly text editor for beginners.

```bash
# Open or create a file
nano filename.txt

# Common shortcuts:
# Ctrl+O - Save (Write Out)
# Ctrl+X - Exit
# Ctrl+K - Cut line
# Ctrl+U - Paste
# Ctrl+W - Search
# Ctrl+G - Help
```

### `grep` - Search Text Patterns

```bash
# Search for a pattern in a file
grep "error" logfile.txt

# Case-insensitive search
grep -i "error" logfile.txt

# Search recursively in directories
grep -r "TODO" /project/

# Show line numbers
grep -n "error" logfile.txt

# Search for whole word only
grep -w "error" logfile.txt

# Invert match (show lines NOT matching)
grep -v "error" logfile.txt

# Count matching lines
grep -c "error" logfile.txt

# Show only matching part
grep -o "error" logfile.txt

# Show files that match
grep -l "error" *.txt

# Show files that don't match
grep -L "error" *.txt

# Search with context (lines before and after)
grep -C 3 "error" logfile.txt  # 3 lines before and after
grep -A 3 "error" logfile.txt  # 3 lines after
grep -B 3 "error" logfile.txt  # 3 lines before

# Search with regular expressions
grep "^error" logfile.txt      # Lines starting with "error"
grep "error$" logfile.txt      # Lines ending with "error"
grep "erro[r|R]" logfile.txt   # error or erroR

# Extended regex
grep -E "error|warning" logfile.txt

# Search multiple patterns
grep -e "error" -e "warning" logfile.txt

# Search from file containing patterns
grep -f patterns.txt logfile.txt

# Exclude certain files
grep -r "error" --exclude="*.log" /path/

# Exclude directories
grep -r "error" --exclude-dir="node_modules" /path/

# Show matches with colors
grep --color=auto "error" logfile.txt

# Binary files as text
grep -a "pattern" binaryfile

# Quiet mode (only exit status)
grep -q "error" logfile.txt
if [ $? -eq 0 ]; then
    echo "Error found"
fi
```

### Advanced Text Processing

#### `awk` - Pattern Scanning and Processing

```bash
# Print specific column
awk '{print $1}' file.txt

# Print multiple columns
awk '{print $1, $3}' file.txt

# Print with custom separator
awk -F ',' '{print $1, $2}' data.csv

# Print lines matching condition
awk '$3 > 100' file.txt

# Print lines matching pattern
awk '/error/ {print $0}' logfile.txt

# Sum values in column
awk '{sum += $2} END {print sum}' file.txt

# Calculate average
awk '{sum+=$1} END {print sum/NR}' numbers.txt

# Print line numbers
awk '{print NR, $0}' file.txt

# Print last column
awk '{print $NF}' file.txt

# Print with custom output
awk '{print "Name:", $1, "Age:", $2}' data.txt

# Multiple conditions
awk '$3 > 100 && $4 < 200' file.txt

# Use BEGIN and END blocks
awk 'BEGIN {print "Start"} {print $1} END {print "End"}' file.txt
```

#### `sed` - Stream Editor

```bash
# Replace text (first occurrence in each line)
sed 's/old/new/' file.txt

# Replace globally (all occurrences)
sed 's/old/new/g' file.txt

# Replace in file (in-place editing)
sed -i 's/old/new/g' file.txt

# Replace with backup
sed -i.bak 's/old/new/g' file.txt

# Delete lines matching pattern
sed '/pattern/d' file.txt

# Delete specific line
sed '3d' file.txt

# Delete range of lines
sed '2,5d' file.txt

# Print only matching lines
sed -n '/pattern/p' file.txt

# Print specific line
sed -n '5p' file.txt

# Insert line before match
sed '/pattern/i\New line' file.txt

# Append line after match
sed '/pattern/a\New line' file.txt

# Replace on specific line
sed '3s/old/new/' file.txt

# Multiple commands
sed -e 's/old/new/g' -e 's/foo/bar/g' file.txt

# Replace with regex
sed 's/[0-9]\+/NUMBER/g' file.txt

# Delete empty lines
sed '/^$/d' file.txt

# Remove leading whitespace
sed 's/^[ \t]*//' file.txt
```

---

## 🔐 Permissions & Ownership

### Understanding Linux Permissions

Linux uses a permission system with three categories: **User**, **Group**, and **Others**.

<div align="center">

| Permission | Binary | Decimal | Meaning |
|------------|--------|---------|---------|
| `---` | 000 | 0 | No permissions |
| `--x` | 001 | 1 | Execute only |
| `-w-` | 010 | 2 | Write only |
| `-wx` | 011 | 3 | Write and execute |
| `r--` | 100 | 4 | Read only |
| `r-x` | 101 | 5 | Read and execute |
| `rw-` | 110 | 6 | Read and write |
| `rwx` | 111 | 7 | Read, write, and execute |

</div>

### `chmod` - Change File Permissions

```bash
# Numeric method (recommended)
chmod 755 script.sh    # rwxr-xr-x (owner: rwx, group: r-x, others: r-x)
chmod 644 file.txt     # rw-r--r-- (owner: rw-, group: r--, others: r--)
chmod 600 private.txt  # rw------- (owner: rw-, group: ---, others: ---)
chmod 777 public.sh    # rwxrwxrwx (everyone: rwx) [Use carefully!]
chmod 444 readonly.txt # r--r--r-- (everyone: read-only)

# Symbolic method
chmod u+x script.sh    # Add execute for user (owner)
chmod g+w file.txt     # Add write for group
chmod o-r secret.txt   # Remove read for others
chmod a+x script.sh    # Add execute for all (user, group, others)
chmod u=rw,g=r,o=r file.txt  # Set specific permissions

# Recursive permission change
chmod -R 755 /directory/
```

**Common Permission Patterns:**

<div align="center">

| Permissions | Numeric | Use Case |
|-------------|---------|----------|
| `rwx------` | 700 | Private scripts/directories |
| `rwxr-xr-x` | 755 | Public scripts/directories |
| `rw-r--r--` | 644 | Public files (documents) |
| `rw-------` | 600 | Private files (config, keys) |
| `r--r--r--` | 444 | Read-only files |

</div>

### `chown` - Change File Owner

```bash
# Change owner
sudo chown newuser file.txt

# Change owner and group
sudo chown newuser:newgroup file.txt

# Change only group
sudo chown :newgroup file.txt

# Recursive change
sudo chown -R user:group /directory/
```

### `chgrp` - Change Group Ownership

```bash
# Change group
chgrp developers file.txt

# Recursive group change
chgrp -R developers /project/
```

---

## 💻 System Information

### User Information

#### `whoami` - Current User

```bash
# Display current username
whoami

# Display current user ID and groups
id

# Display user ID only
id -u

# Display group ID
id -g

# Display all groups
id -G

# Display user and group names
id -un
id -gn
```

#### `who` - Who is Logged In

```bash
# Show who is logged in
who

# Show all information
who -a

# Show boot time
who -b

# Show dead processes
who -d

# Show login process
who -l

# Show current runlevel
who -r

# Show only username and terminal
who -q
```

#### `w` - Show Who is Logged In and What They're Doing

```bash
# Show logged in users and their activity
w

# Short format
w -s

# Don't show header
w -h

# Show specific user
w username
```

#### `last` - Show Login History

```bash
# Show last logins
last

# Show last 10 logins
last -n 10

# Show last logins for specific user
last username

# Show last reboots
last reboot

# Show failed login attempts
lastb
```

#### `users` - Show Logged In Users

```bash
# Show currently logged in users
users
```

### System Information Commands

#### `uname` - System Information

```bash
# Show all system information
uname -a

# Show kernel name
uname -s

# Show kernel version
uname -r

# Show kernel release
uname -v

# Show machine hardware name
uname -m

# Show processor type
uname -p

# Show hardware platform
uname -i

# Show operating system
uname -o
```

#### `hostname` - System Hostname

```bash
# Display hostname
hostname

# Display IP address
hostname -I

# Display all IP addresses
hostname -i

# Display FQDN (Fully Qualified Domain Name)
hostname -f

# Display short hostname
hostname -s

# Display domain name
hostname -d

# Set hostname (requires root)
sudo hostname newhostname
```

#### `hostnamectl` - Control System Hostname

```bash
# Show hostname details
hostnamectl

# Set hostname
sudo hostnamectl set-hostname newname

# Set pretty hostname
sudo hostnamectl set-hostname "My Server" --pretty

# Set static hostname
sudo hostnamectl set-hostname myserver --static
```

#### `uptime` - System Uptime and Load

```bash
# Show system uptime and load
uptime

# Show only uptime
uptime -p

# Show since when system is running
uptime -s
```

#### `date` - Display or Set Date and Time

```bash
# Display current date and time
date

# Display in specific format
date +"%Y-%m-%d %H:%M:%S"

# Display date only
date +"%Y-%m-%d"

# Display time only
date +"%H:%M:%S"

# Display day of week
date +"%A"

# Display Unix timestamp
date +%s

# Convert Unix timestamp to date
date -d @1609459200

# Display date in UTC
date -u

# Set date (requires root)
sudo date -s "2024-01-01 12:00:00"

# Display date from different timezone
TZ='America/New_York' date
```

#### `cal` - Display Calendar

```bash
# Display current month calendar
cal

# Display specific month and year
cal 12 2024

# Display entire year
cal 2024

# Display three months (previous, current, next)
cal -3

# Display with week numbers
cal -w

# Display Monday as first day
cal -m
```

#### `timedatectl` - Control System Time and Date

```bash
# Show current time settings
timedatectl

# List available timezones
timedatectl list-timezones

# Set timezone
sudo timedatectl set-timezone America/New_York

# Set time
sudo timedatectl set-time "2024-01-01 12:00:00"

# Enable NTP
sudo timedatectl set-ntp true
```

### Hardware and System Resources

#### `lscpu` - Display CPU Information

```bash
# Show detailed CPU information
lscpu

# Show CPU information in parseable format
lscpu -p
```

` - List Block Devices

```bash
# List all block devices
lsblk

# Show filesystem type
lsblk -f

# Show additional info (UUID, permissions)
lsblk -o NAME,SIZE,TYPE,MOUNTPOINT,UUID

# Show in ASCII format
lsblk -a
```

#### `lsusb` - List USB Devices

```bash
# List all USB devices
lsusb

# Verbose output
lsusb -v

# Show device tree
lsusb -t
```

#### `lspci` - List PCI Devices

```bash
# List all PCI devices
lspci

# Verbose output
lspci -v

# Very verbose output
lspci -vv

# Show kernel drivers
lspci -k

# Show device tree
lspci -t
```

#### `dmidecode` - DMI/SMBIOS Information

```bash
# Show all hardware information (requires root)
sudo dmidecode

# Show specific type (BIOS)
sudo dmidecode -t bios

# Show system information
sudo dmidecode -t system

# Show memory information
sudo dmidecode -t memory

# Show processor information
sudo dmidecode -t processor
```

#### `hwinfo` - Hardware Information

```bash
# Show all hardware info (requires installation)
sudo hwinfo

# Show CPU info
sudo hwinfo --cpu

# Show network interface info
sudo hwinfo --netcard

# Show disk info
sudo hwinfo --disk

# Short output
sudo hwinfo --short
```

### `man` - Manual Pages

Display the manual (documentation) for any command.

```bash
# View manual for a command
man ls
man grep
man chmod

# Search for commands related to a topic
man -k network

# Show brief description
man -f ls
# or use
whatis ls

# Show all manual pages for a command
man -a intro

# Show manual in specific section
man 5 passwd  # Section 5: File formats

# Search manual pages
apropos "search term"
```

**Manual Sections:**
```
1: User commands
2: System calls
3: Library functions
4: Special files
5: File formats
6: Games
7: Miscellaneous
8: System administration
```

**Navigation in man pages:**
- `Space` - Next page
- `b` - Previous page
- `/pattern` - Search forward
- `n` - Next match
- `N` - Previous match
- `q` - Quit
- `h` - Help

### Memory and CPU Monitoring

#### `nproc` - Number of Processing Units

```bash
# Display number of CPU cores
nproc

# Display all available cores (including offline)
nproc --all

# Ignore N cores
nproc --ignore=2
```

#### `free` - Memory Usage

Displays system memory (RAM) usage.

```bash
# Display memory in bytes
free

# Display in human-readable format (MB/GB)
free -h

# Display in megabytes
free -m

# Display in gigabytes
free -g

# Show total line
free -h -t

# Update every 2 seconds
free -h -s 2

# Display wide format
free -w

# Count buffers/cache as free
free -h --si
```

**Understanding the output:**
```
              total        used        free      shared  buff/cache   available
Mem:           15Gi       5.2Gi       8.1Gi       234Mi       2.1Gi        9.5Gi
Swap:         2.0Gi          0B       2.0Gi

Explanation:
- total: Total installed memory
- used: Memory in use by applications
- free: Completely unused memory
- shared: Memory used by tmpfs
- buff/cache: Memory used for disk caching
- available: Memory available for new applications
- Swap: Disk space used as virtual memory
```

#### `vmstat` - Virtual Memory Statistics

```bash
# Display virtual memory statistics
vmstat

# Update every 2 seconds
vmstat 2

# Update 5 times with 2 second interval
vmstat 2 5

# Display in megabytes
vmstat -S M

# Show detailed statistics
vmstat -s

# Show disk statistics
vmstat -d

# Show active/inactive memory
vmstat -a
```

### Disk Management

#### `df` - Disk Free Space

Shows disk space usage of file systems.

```bash
# Display disk usage
df

# Human-readable format
df -h

# Show inode usage
df -i

# Display specific filesystem
df -h /dev/sda1

# Show filesystem type
df -T

# Show all filesystems (including pseudo)
df -a

# Display in MB
df -BM

# Display in GB
df -BG

# Exclude specific filesystem type
df -x tmpfs
```

#### `du` - Disk Usage

Shows disk usage of files and directories.

```bash
# Show size of current directory
du -sh .

# Show size of specific directory
du -sh /var/log

# Show sizes of all subdirectories
du -h --max-depth=1

# Sort by size
du -h | sort -h

# Show total size
du -ch /directory/

# Show all files (not just directories)
du -ah /directory/

# Exclude certain files
du -h --exclude="*.log" /directory/

# Display apparent size (not disk usage)
du --apparent-size -h /directory/

# Show sizes in MB
du -BM /directory/

# Display one filesystem only
du -x /directory/

# Count only from certain depth
du -h --max-depth=2 /directory/

# Show time of last modification
du -h --time /directory/
```

#### `fdisk` - Partition Table Manipulator

```bash
# List all partitions
sudo fdisk -l

# List specific disk partitions
sudo fdisk -l /dev/sda

# Start fdisk on a disk
sudo fdisk /dev/sda

# Commands inside fdisk:
# m - help
# p - print partition table
# n - new partition
# d - delete partition
# w - write changes
# q - quit without saving
# t - change partition type
```

#### `parted` - Advanced Partition Editor

```bash
# Show partition information
sudo parted -l

# Start parted on a disk
sudo parted /dev/sda

# Non-interactive mode
sudo parted /dev/sda print

# Create partition table
sudo parted /dev/sda mklabel gpt

# Create partition
sudo parted /dev/sda mkpart primary ext4 0% 50%

# Resize partition
sudo parted /dev/sda resizepart 1 100%

# Remove partition
sudo parted /dev/sda rm 1
```

#### `mkfs` - Make Filesystem

```bash
# Create ext4 filesystem
sudo mkfs.ext4 /dev/sda1

# Create ext3 filesystem
sudo mkfs.ext3 /dev/sda1

# Create XFS filesystem
sudo mkfs.xfs /dev/sda1

# Create NTFS filesystem
sudo mkfs.ntfs /dev/sda1

# Create FAT32 filesystem
sudo mkfs.vfat -F 32 /dev/sda1

# Create with label
sudo mkfs.ext4 -L "MyDisk" /dev/sda1
```

#### `mount` - Mount Filesystems

```bash
# Mount a filesystem
sudo mount /dev/sda1 /mnt

# Mount with specific filesystem type
sudo mount -t ext4 /dev/sda1 /mnt

# Mount read-only
sudo mount -o ro /dev/sda1 /mnt

# Mount with specific options
sudo mount -o noexec,nodev /dev/sda1 /mnt

# Show all mounted filesystems
mount

# Show specific filesystem
mount | grep sda1

# Remount with different options
sudo mount -o remount,rw /dev/sda1

# Mount ISO file
sudo mount -o loop image.iso /mnt
```

#### `umount` - Unmount Filesystems

```bash
# Unmount by device
sudo umount /dev/sda1

# Unmount by mount point
sudo umount /mnt

# Force unmount
sudo umount -f /mnt

# Lazy unmount (detach filesystem)
sudo umount -l /mnt

# Unmount all
sudo umount -a
```

#### `fsck` - Filesystem Check and Repair

```bash
# Check filesystem (must be unmounted)
sudo fsck /dev/sda1

# Auto repair
sudo fsck -y /dev/sda1

# Check all filesystems
sudo fsck -A

# Check specific filesystem type
sudo fsck -t ext4 /dev/sda1

# Verbose output
sudo fsck -V /dev/sda1

# Force check even if filesystem is clean
sudo fsck -f /dev/sda1
```

#### `dd` - Convert and Copy Files

```bash
# Create bootable USB
sudo dd if=ubuntu.iso of=/dev/sdb bs=4M status=progress

# Backup partition
sudo dd if=/dev/sda1 of=~/partition_backup.img bs=4M status=progress

# Restore partition
sudo dd if=~/partition_backup.img of=/dev/sda1 bs=4M status=progress

# Clone entire disk
sudo dd if=/dev/sda of=/dev/sdb bs=64K conv=noerror,sync status=progress

# Create file with random data
dd if=/dev/urandom of=random_file bs=1M count=100

# Create file with zeros
dd if=/dev/zero of=zero_file bs=1M count=100

# Wipe disk (secure delete)
sudo dd if=/dev/zero of=/dev/sda bs=4M status=progress
```

⚠️ **Warning:** `dd` can destroy data instantly if used incorrectly. Always double-check device names!

### Network Commands

#### `ping` - Test Network Connectivity

```bash
# Ping a host
ping google.com

# Ping specific number of times
ping -c 4 google.com

# Ping with specific interval
ping -i 2 google.com

# Ping with specific packet size
ping -s 1000 google.com

# Flood ping (requires root)
sudo ping -f google.com

# Ping IPv6
ping6 google.com
```

#### `ifconfig` - Network Interface Configuration (Legacy)

```bash
# Show all network interfaces
ifconfig

# Show specific interface
ifconfig eth0

# Bring interface up
sudo ifconfig eth0 up

# Bring interface down
sudo ifconfig eth0 down

# Set IP address
sudo ifconfig eth0 192.168.1.100

# Set netmask
sudo ifconfig eth0 netmask 255.255.255.0
```

#### `ip` - Modern Network Configuration

```bash
# Show all network interfaces
ip addr
ip a

# Show specific interface
ip addr show eth0

# Show routing table
ip route
ip r

# Add IP address
sudo ip addr add 192.168.1.100/24 dev eth0

# Delete IP address
sudo ip addr del 192.168.1.100/24 dev eth0

# Bring interface up/down
sudo ip link set eth0 up
sudo ip link set eth0 down

# Show link statistics
ip -s link

# Show neighbors (ARP table)
ip neigh

# Add static route
sudo ip route add 10.0.0.0/24 via 192.168.1.1

# Delete route
sudo ip route del 10.0.0.0/24
```

#### `netstat` - Network Statistics (Legacy)

```bash
# Show all connections
netstat -a

# Show listening ports
netstat -l

# Show TCP connections
netstat -t

# Show UDP connections
netstat -u

# Show listening TCP ports
netstat -lt

# Show with program names
netstat -p

# Show numerical addresses
netstat -n

# Show routing table
netstat -r

# Show interface statistics
netstat -i

# Combined common options
netstat -tuln  # TCP, UDP, listening, numerical
```

#### `ss` - Socket Statistics (Modern replacement for netstat)

```bash
# Show all sockets
ss -a

# Show listening sockets
ss -l

# Show TCP sockets
ss -t

# Show UDP sockets
ss -u

# Show listening TCP ports
ss -lt

# Show process using sockets
ss -p

# Show numerical addresses
ss -n

# Show summary statistics
ss -s

# Combined options
ss -tuln  # TCP, UDP, listening, numerical

# Show established connections
ss -o state established

# Filter by port
ss -at '( dport = :22 or sport = :22 )'
```

#### `nslookup` - DNS Lookup

```bash
# Query DNS
nslookup google.com

# Query specific DNS server
nslookup google.com 8.8.8.8

# Reverse DNS lookup
nslookup 8.8.8.8

# Query specific record type
nslookup -type=MX google.com
nslookup -type=NS google.com
nslookup -type=A google.com
```

#### `dig` - DNS Lookup (More detailed)

```bash
# Basic DNS query
dig google.com

# Query specific DNS server
dig @8.8.8.8 google.com

# Short output
dig google.com +short

# Query specific record type
dig google.com MX
dig google.com NS
dig google.com TXT

# Reverse DNS lookup
dig -x 8.8.8.8

# Trace DNS resolution path
dig google.com +trace

# Query all records
dig google.com ANY
```

#### `host` - DNS Lookup (Simple)

```bash
# Simple DNS lookup
host google.com

# Reverse lookup
host 8.8.8.8

# Query specific record type
host -t MX google.com
host -t NS google.com

# Verbose output
host -v google.com
```

#### `curl` - Transfer Data from URLs

```bash
# Download file
curl -O https://example.com/file.zip

# Download and save with different name
curl -o newname.zip https://example.com/file.zip

# Follow redirects
curl -L https://example.com

# Show headers
curl -I https://example.com

# Include headers in output
curl -i https://example.com

# Send POST request
curl -X POST https://api.example.com/data

# Send POST with data
curl -X POST -d "param1=value1&param2=value2" https://api.example.com

# Send JSON data
curl -X POST -H "Content-Type: application/json" -d '{"key":"value"}' https://api.example.com

# Basic authentication
curl -u username:password https://example.com

# Download with progress bar
curl -# -O https://example.com/file.zip

# Resume download
curl -C - -O https://example.com/largefile.iso

# Set user agent
curl -A "Mozilla/5.0" https://example.com

# Save cookies
curl -c cookies.txt https://example.com

# Send cookies
curl -b cookies.txt https://example.com

# Verbose output
curl -v https://example.com
```

#### `wget` - Download Files

```bash
# Download file
wget https://example.com/file.zip

# Download with different name
wget -O newname.zip https://example.com/file.zip

# Resume interrupted download
wget -c https://example.com/largefile.iso

# Download in background
wget -b https://example.com/file.zip

# Download multiple files
wget -i urls.txt

# Download recursively
wget -r https://example.com

# Limit download rate
wget --limit-rate=200k https://example.com/file.zip

# Download with user agent
wget --user-agent="Mozilla/5.0" https://example.com

# HTTP authentication
wget --user=username --password=password https://example.com

# Download with timeout
wget --timeout=30 https://example.com

# Try specific number of times
wget --tries=5 https://example.com

# Mirror a website
wget --mirror --convert-links https://example.com
```

#### `traceroute` - Trace Network Path

```bash
# Trace route to host
traceroute google.com

# Use ICMP instead of UDP
traceroute -I google.com

# Use TCP SYN
traceroute -T google.com

# Set maximum hops
traceroute -m 15 google.com

# Don't resolve hostnames
traceroute -n google.com
```

#### `route` - Show/Manipulate IP Routing Table

```bash
# Display routing table
route

# Display in numerical format
route -n

# Add default gateway
sudo route add default gw 192.168.1.1

# Add specific route
sudo route add -net 10.0.0.0/24 gw 192.168.1.1

# Delete route
sudo route del -net 10.0.0.0/24
```

---

## ⚙️ Process Management

### `top` - Task Manager

Interactive process viewer showing real-time system resource usage.

```bash
# Launch top
top

# Key commands inside top:
# q - quit
# k - kill a process (enter PID)
# M - sort by memory usage
# P - sort by CPU usage
# 1 - toggle individual CPU cores
# h - help
```

**Understanding top output:**
```
top - 10:30:15 up 5 days,  3:45,  2 users,  load average: 0.15, 0.10, 0.05
Tasks: 245 total,   1 running, 244 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.3 us,  0.7 sy,  0.0 ni, 96.8 id,  0.2 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :  15876.0 total,   8234.5 free,   5321.2 used,   2320.3 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   9654.8 avail Mem
```

### `htop` - Enhanced Process Viewer

More user-friendly alternative to `top` (may need installation).

```bash
# Install htop
sudo apt install htop    # Debian/Ubuntu
sudo yum install htop    # RHEL/CentOS

# Run htop
htop
```

### `ps` - Process Status

Shows currently running processes.

```bash
# Show processes for current user
ps

# Show all processes with details
ps aux

# Show process tree
ps auxf

# Show processes for specific user
ps -u username

# Find specific process
ps aux | grep nginx

# Show process by PID
ps -p 1234
```

### `kill` - Terminate Process

```bash
# Terminate process gracefully (SIGTERM)
kill 1234

# Force kill process (SIGKILL)
kill -9 1234

# Kill process by name
killall process_name

# Kill all processes matching a pattern
pkill -f pattern
```

### `bg` / `fg` - Background/Foreground Jobs

```bash
# Run command in background
command &

# List background jobs
jobs

# Bring job to foreground
fg %1

# Send job to background
bg %1

# Suspend current process
Ctrl+Z
```

---

## 🔧 Shell Scripting Basics

### Shebang (`#!/bin/bash`)

The **shebang** (hash-bang) is the first line of a shell script that tells the system which interpreter to use.

```bash
#!/bin/bash
# This tells the system to execute this script using bash

echo "Hello, World!"
```

**Common shebangs:**
```bash
#!/bin/bash           # Bash shell
#!/bin/sh             # Bourne shell (POSIX compatible)
#!/usr/bin/env bash   # Bash (portable, finds bash in PATH)
#!/usr/bin/python3    # Python 3
#!/usr/bin/env node   # Node.js
```

### Comments in Shell Scripts

```bash
#!/bin/bash

# This is a single-line comment
# Use # at the beginning of the line

echo "Hello"  # Inline comment

: '
This is a multi-line comment
Everything between these markers is ignored
Useful for longer explanations
'
```

### Basic Shell Script Example

```bash
#!/bin/bash

# Author: Your Name
# Description: Simple backup script
# Date: 2024-01-01

# Variables
SOURCE_DIR="/home/user/documents"
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d)

# Create backup
echo "Starting backup..."
tar -czf "${BACKUP_DIR}/backup_${DATE}.tar.gz" "${SOURCE_DIR}"

# Check if backup was successful
if [ $? -eq 0 ]; then
    echo "Backup completed successfully!"
else
    echo "Backup failed!"
    exit 1
fi
```

### Making Scripts Executable

```bash
# Make script executable
chmod +x script.sh

# Run the script
./script.sh

# Or run without execute permission
bash script.sh
```

### `echo` - Output Text

```bash
# Simple output
echo "Hello, World!"

# Output without newline
echo -n "Loading..."

# Output with variables
NAME="John"
echo "Hello, $NAME"

# Output to a file
echo "Log entry" >> logfile.txt

# Output with escape sequences
echo -e "Line 1\nLine 2\tTabbed"
```

### Variables in Shell

```bash
#!/bin/bash

# Define variables (no spaces around =)
NAME="John Doe"
AGE=30
PATH_TO_FILE="/home/user/file.txt"

# Use variables
echo "Name: $NAME"
echo "Age: ${AGE} years old"

# Command output to variable
CURRENT_DATE=$(date)
NUM_FILES=$(ls | wc -l)

echo "Today is: $CURRENT_DATE"
echo "Number of files: $NUM_FILES"

# Read user input
read -p "Enter your name: " USER_NAME
echo "Hello, $USER_NAME!"
```

### User and Group Management

#### `useradd` - Add User

```bash
# Add a user
sudo useradd john

# Add user with home directory
sudo useradd -m john

# Add user with specific shell
sudo useradd -s /bin/bash john

# Add user with specific UID
sudo useradd -u 1500 john

# Add user to specific group
sudo useradd -g developers john

# Add user to multiple groups
sudo useradd -G sudo,docker john

# Add user with expiry date
sudo useradd -e 2025-12-31 john

# Add user with comment
sudo useradd -c "John Doe" john

# Add user with custom home directory
sudo useradd -d /custom/home/john john
```

#### `usermod` - Modify User

```bash
# Add user to group
sudo usermod -aG sudo john

# Change user's shell
sudo usermod -s /bin/zsh john

# Change user's home directory
sudo usermod -d /new/home john

# Lock user account
sudo usermod -L john

# Unlock user account
sudo usermod -U john

# Change username
sudo usermod -l newname oldname

# Set account expiry date
sudo usermod -e 2025-12-31 john
```

#### `userdel` - Delete User

```bash
# Delete user
sudo userdel john

# Delete user and home directory
sudo userdel -r john

# Force delete (even if logged in)
sudo userdel -f john
```

#### `passwd` - Change Password

```bash
# Change your own password
passwd

# Change another user's password (requires root)
sudo passwd john

# Lock user account
sudo passwd -l john

# Unlock user account
sudo passwd -u john

# Delete user password (passwordless login)
sudo passwd -d john

# Force password change on next login
sudo passwd -e john

# Show password status
sudo passwd -S john
```

#### `groupadd` - Add Group

```bash
# Add a group
sudo groupadd developers

# Add group with specific GID
sudo groupadd -g 2000 developers

# Add system group
sudo groupadd -r sysgroup
```

#### `groupmod` - Modify Group

```bash
# Rename group
sudo groupmod -n newname oldname

# Change GID
sudo groupmod -g 3000 developers
```

#### `groupdel` - Delete Group

```bash
# Delete group
sudo groupdel developers
```

#### `groups` - Show User Groups

```bash
# Show groups for current user
groups

# Show groups for specific user
groups john
```

#### `sudo` - Execute Command as Another User

```bash
# Run command as root
sudo command

# Run command as specific user
sudo -u john command

# Switch to root shell
sudo -i

# Run command with current directory
sudo -E command

# List sudo privileges
sudo -l

# Edit file safely
sudoedit /etc/config.conf

# Run command with specific group
sudo -g developers command

# Validate sudo credentials
sudo -v

# Clear sudo credentials
sudo -k
```

#### `su` - Switch User

```bash
# Switch to root
su

# Switch to specific user
su john

# Switch with user's environment
su - john

# Run single command as user
su -c "command" john

# Switch and run shell
su -s /bin/bash john
```

### Package Management

#### APT (Debian/Ubuntu)

```bash
# Update package list
sudo apt update

# Upgrade all packages
sudo apt upgrade

# Full upgrade (handle dependencies)
sudo apt full-upgrade

# Install package
sudo apt install package_name

# Install multiple packages
sudo apt install package1 package2 package3

# Install without confirmation
sudo apt install -y package_name

# Remove package
sudo apt remove package_name

# Remove package and config files
sudo apt purge package_name

# Remove unused dependencies
sudo apt autoremove

# Search for package
apt search package_name

# Show package information
apt show package_name

# List installed packages
apt list --installed

# List upgradable packages
apt list --upgradable

# Clean package cache
sudo apt clean

# Download package without installing
apt download package_name
```

#### YUM/DNF (RHEL/CentOS/Fedora)

```bash
# Update package list
sudo yum check-update
sudo dnf check-update

# Update all packages
sudo yum update
sudo dnf upgrade

# Install package
sudo yum install package_name
sudo dnf install package_name

# Remove package
sudo yum remove package_name
sudo dnf remove package_name

# Search package
yum search package_name
dnf search package_name

# Show package info
yum info package_name
dnf info package_name

# List installed packages
yum list installed
dnf list installed

# Clean cache
sudo yum clean all
sudo dnf clean all

# Install local RPM
sudo yum localinstall package.rpm
sudo dnf install package.rpm
```

#### Snap (Universal)

```bash
# Install snap package
sudo snap install package_name

# List installed snaps
snap list

# Update all snaps
sudo snap refresh

# Update specific snap
sudo snap refresh package_name

# Remove snap
sudo snap remove package_name

# Search for snap
snap find package_name

# Show snap info
snap info package_name
```

#### Flatpak (Universal)

```bash
# Install flatpak
sudo flatpak install flathub package_name

# List installed flatpaks
flatpak list

# Update all flatpaks
sudo flatpak update

# Remove flatpak
sudo flatpak uninstall package_name

# Search flatpak
flatpak search package_name

# Add flathub repository
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

### Service Management (systemd)

#### `systemctl` - Control systemd Services

```bash
# Start service
sudo systemctl start service_name

# Stop service
sudo systemctl stop service_name

# Restart service
sudo systemctl restart service_name

# Reload service configuration
sudo systemctl reload service_name

# Enable service at boot
sudo systemctl enable service_name

# Disable service at boot
sudo systemctl disable service_name

# Check service status
sudo systemctl status service_name

# Check if service is active
systemctl is-active service_name

# Check if service is enabled
systemctl is-enabled service_name

# List all services
systemctl list-units --type=service

# List all active services
systemctl list-units --type=service --state=active

# List failed services
systemctl --failed

# Show service logs
sudo journalctl -u service_name

# Follow service logs
sudo journalctl -u service_name -f

# Reload systemd daemon
sudo systemctl daemon-reload

# List all available services
systemctl list-unit-files --type=service
```

---

## 🚀 Advanced Commands

### `find` - Search for Files

```bash
# Find files by name
find /path -name "*.txt"

# Find files by type
find /path -type f          # files
find /path -type d          # directories

# Find files modified in last 7 days
find /path -mtime -7

# Find files larger than 100MB
find /path -size +100M

# Find and execute command
find /path -name "*.log" -exec rm {} \;

# Find with multiple conditions
find /path -name "*.sh" -type f -perm 755
```

### `tar` - Archive Files

```bash
# Create archive
tar -czf archive.tar.gz /directory/

# Create archive (verbose)
tar -czvf archive.tar.gz /directory/

# Extract archive
tar -xzf archive.tar.gz

# Extract to specific directory
tar -xzf archive.tar.gz -C /destination/

# List archive contents
tar -tzf archive.tar.gz

# Create bzip2 archive
tar -cjf archive.tar.bz2 /directory/

# Extract bzip2 archive
tar -xjf archive.tar.bz2

# Create uncompressed archive
tar -cf archive.tar /directory/

# Add file to existing archive
tar -rf archive.tar newfile.txt

# Extract specific file
tar -xzf archive.tar.gz path/to/file

# Exclude files while creating archive
tar -czf archive.tar.gz --exclude='*.log' /directory/

# Create archive with timestamp
tar -czf backup_$(date +%Y%m%d).tar.gz /directory/

# Verify archive integrity
tar -tzf archive.tar.gz > /dev/null
```

**Tar flags explained:**
- `c` - create archive
- `x` - extract archive
- `t` - list contents
- `z` - compress with gzip
- `j` - compress with bzip2
- `v` - verbose output
- `f` - file name
- `r` - append to archive
- `C` - change directory

### `ssh` - Secure Shell

```bash
# Connect to remote server
ssh username@hostname

# Connect with specific port
ssh -p 2222 username@hostname

# Copy file to remote server
scp file.txt username@hostname:/path/

# Copy file from remote server
scp username@hostname:/path/file.txt .

# Copy directory recursively
scp -r /local/dir username@hostname:/remote/dir
```

### `rsync` - Remote Sync

```bash
# Sync directories
rsync -avz /source/ /destination/

# Sync to remote server
rsync -avz /local/ username@host:/remote/

# Sync with delete (mirror)
rsync -avz --delete /source/ /destination/

# Dry run (see what would be copied)
rsync -avzn /source/ /destination/
```

### `pipe` (|) - Chain Commands

```bash
# List files and search
ls -l | grep ".txt"

# Count files
ls | wc -l

# Sort and display unique values
cat file.txt | sort | uniq

# Find and count
ps aux | grep nginx | wc -l

# Multiple pipes
cat file.txt | grep "error" | sort | uniq -c
```

### `redirects` - Input/Output Redirection

```bash
# Redirect output to file (overwrite)
command > output.txt

# Redirect output to file (append)
command >> output.txt

# Redirect error messages
command 2> error.log

# Redirect both output and errors
command > output.txt 2>&1

```

## 📝 Quick Reference

### File Operations Cheat Sheet
```bash
# Create
touch file.txt              # Create empty file
mkdir directory             # Create directory
mkdir -p path/to/dir        # Create nested directories

# View
cat file.txt                # Display entire file
less file.txt               # View with pagination
head -n 10 file.txt         # First 10 lines
tail -n 10 file.txt         # Last 10 lines
tail -f log.txt             # Follow file updates

# Copy/Move
cp source dest              # Copy file
cp -r dir1 dir2             # Copy directory
cp -p file backup           # Copy preserving attributes
mv old new                  # Rename/move
mv -i file dest             # Interactive move

# Delete
rm file.txt                 # Remove file
rm -r directory             # Remove directory
rm -rf directory            # Force remove
rm -i file.txt              # Interactive remove

# Link
ln file hardlink            # Create hard link
ln -s file symlink          # Create symbolic link

# Search
find / -name "file.txt"     # Find file
find / -type f -size +100M  # Find large files
grep "text" file.txt        # Search in file
grep -r "text" /dir         # Recursive search
grep -i "text" file         # Case insensitive

# Compare
diff file1 file2            # Compare files
diff -u file1 file2         # Unified format
cmp file1 file2             # Binary compare

# Compress
gzip file                   # Compress with gzip
bzip2 file                  # Compress with bzip2
zip archive.zip files       # Create zip
tar -czf arch.tar.gz dir/   # Create tar.gz
tar -xzf arch.tar.gz        # Extract tar.gz
```

### Directory Navigation
```bash
pwd                         # Print working directory
cd /path                    # Change directory
cd ~                        # Go home
cd ..                       # Go up one level
cd -                        # Go to previous directory
ls                          # List files
ls -lah                     # Detailed list with hidden
ls -ltr                     # Sort by time (oldest first)
tree                        # Display directory tree
pushd /path                 # Push directory to stack
popd                        # Pop directory from stack
```

### Permission Quick Reference
```bash
# Read = 4, Write = 2, Execute = 1

chmod 777 file  # rwxrwxrwx (all permissions)
chmod 755 file  # rwxr-xr-x (standard script)
chmod 644 file  # rw-r--r-- (standard file)
chmod 600 file  # rw------- (private file)
chmod 400 file  # r-------- (read-only)

# Symbolic
chmod u+x file  # Add execute for user
chmod g-w file  # Remove write for group
chmod o=r file  # Set others to read-only
chmod a+x file  # Add execute for all

# Ownership
chown user file             # Change owner
chown user:group file       # Change owner and group
chown -R user:group dir/    # Recursive change
```

### System Monitoring Commands
```bash
top                 # Process viewer
htop                # Better process viewer
ps aux              # All processes
ps -ef              # Full format listing
free -h             # Memory usage
df -h               # Disk usage
du -sh /path        # Directory size
uptime              # System uptime
vmstat              # Virtual memory stats
iostat              # I/O statistics
lscpu               # CPU information
lsblk               # Block devices
lsusb               # USB devices
lspci               # PCI devices
```

### Network Commands
```bash
ping host               # Test connectivity
ip addr                 # Show IP addresses
ip route                # Show routing table
ss -tuln                # Show listening ports
netstat -tuln           # Legacy port listing
nslookup domain         # DNS lookup
dig domain              # Detailed DNS query
host domain             # Simple DNS lookup
curl url                # Transfer data
wget url                # Download file
traceroute host         # Trace network path
ifconfig                # Network interfaces (legacy)
```

### Process Management
```bash
ps aux                  # List all processes
ps aux | grep name      # Find process
kill PID                # Kill process
kill -9 PID             # Force kill
killall name            # Kill by name
pkill pattern           # Kill by pattern
jobs                    # List background jobs
fg %n                   # Foreground job
bg %n                   # Background job
nohup command &         # Run immune to hangups
command &               # Run in background
```

### User Management
```bash
whoami                  # Current user
id                      # User/group IDs
who                     # Logged in users
w                       # Who and what they're doing
last                    # Login history
sudo command            # Run as root
su - user               # Switch user
passwd                  # Change password
useradd user            # Add user
usermod -aG group user  # Add to group
userdel -r user         # Delete user
groupadd group          # Add group
```

### Package Management
```bash
# Debian/Ubuntu
apt update              # Update package list
apt upgrade             # Upgrade packages
apt install pkg         # Install package
apt remove pkg          # Remove package
apt search pkg          # Search package

# RHEL/CentOS/Fedora
yum update              # Update packages
yum install pkg         # Install package
dnf install pkg         # Install (newer)
yum remove pkg          # Remove package

# Universal
snap install pkg        # Install snap
flatpak install pkg     # Install flatpak
```

### Service Management
```bash
systemctl start srv     # Start service
systemctl stop srv      # Stop service
systemctl restart srv   # Restart service
systemctl status srv    # Check status
systemctl enable srv    # Enable at boot
systemctl disable srv   # Disable at boot
journalctl -u srv       # View logs
systemctl list-units    # List services
```

### Text Processing
```bash
cat file                # Display file
grep pattern file       # Search pattern
sed 's/old/new/g' file  # Replace text
awk '{print $1}' file   # Print column
cut -d: -f1 file        # Cut fields
sort file               # Sort lines
uniq file               # Remove duplicates
wc -l file              # Count lines
head -n 10 file         # First 10 lines
tail -n 10 file         # Last 10 lines
tr 'a-z' 'A-Z' < file   # Transform text
```

### Disk Management
```bash
fdisk -l                # List partitions
parted -l               # List partitions
mkfs.ext4 /dev/sdX      # Create filesystem
mount /dev/sdX /mnt     # Mount filesystem
umount /mnt             # Unmount
fsck /dev/sdX           # Check filesystem
dd if=in of=out         # Copy/convert
```

### Input/Output Redirection
```bash
# Redirect output to file (overwrite)
command > file.txt

# Redirect output to file (append)
command >> file.txt

# Redirect errors to file
command 2> error.txt

# Redirect all output (stdout + stderr)
command &> all.txt

# Redirect to nowhere (discard output)
command > /dev/null

# Redirect input from file
command < input.txt
```

### Shortcuts and Tricks
```bash
# Command line shortcuts
Ctrl+C          # Kill current process
Ctrl+Z          # Suspend process
Ctrl+D          # Exit shell/EOF
Ctrl+L          # Clear screen
Ctrl+A          # Go to line start
Ctrl+E          # Go to line end
Ctrl+U          # Cut from cursor to start
Ctrl+K          # Cut from cursor to end
Ctrl+W          # Cut previous word
Ctrl+Y          # Paste
Ctrl+R          # Reverse search history
!!              # Repeat last command
!$              # Last argument of previous command
!^              # First argument of previous command
!*              # All arguments of previous command

# Command combinations
command1 && command2    # Run command2 if command1 succeeds
command1 || command2    # Run command2 if command1 fails
command1 ; command2     # Run both regardless
command > file          # Redirect output
command >> file         # Append output
command 2> file         # Redirect errors
command &> file         # Redirect all output
command1 | command2     # Pipe output

# Useful aliases
alias ll='ls -lah'
alias ..='cd ..'
alias grep='grep --color=auto'
alias update='sudo apt update && sudo apt upgrade'
```

---

## 🎓 Practice Exercises

Try these exercises to reinforce your learning:

1. **File Management**
   - Create a directory structure: `project/src/main`
   - Create 5 text files in the `main` directory
   - Move 2 files to `src` directory
   - Delete one file

2. **Permissions**
   - Create a shell script
   - Make it executable only for you (700)
   - Change to read/write for you, read for group (640)

3. **Text Processing**
   - Create a log file with 100 lines
   - Use `grep` to find lines containing "ERROR"
   - Use `tail -f` to monitor a log file in real-time

4. **Process Management**
   - Find all running bash processes
   - Monitor system resources with `top`
   - Kill a process by name using `killall`

---

## 📚 Additional Resources

### Learning Platforms
- [Linux Journey](https://linuxjourney.com/) - Interactive Linux tutorial
- [OverTheWire: Bandit](https://overthewire.org/wargames/bandit/) - Linux command line game
- [The Linux Command Line Book](http://linuxcommand.org/tlcl.php) - Free comprehensive guide

### Cheat Sheets
- [Linux Command Cheat Sheet](https://www.linuxtrainingacademy.com/linux-commands-cheat-sheet/)
- [Vim Cheat Sheet](https://vim.rtorr.com/)
- [Bash Scripting Cheat Sheet](https://devhints.io/bash)

### Documentation
- `man` command - Built-in manual pages
- [GNU Coreutils Manual](https://www.gnu.org/software/coreutils/manual/)
- [Bash Reference Manual](https://www.gnu.org/software/bash/manual/)

---

## 🤝 Contributing

Found an error or want to add more commands? Contributions are welcome!

1. Fork this repository
2. Create your feature branch (`git checkout -b feature/new-commands`)
3. Commit your changes (`git commit -am 'Add new commands'`)
4. Push to the branch (`git push origin feature/new-commands`)
5. Open a Pull Request

---

<div align="center">

**Master the Command Line! 💪**

Made with ❤️ by the DevOps Community

⭐ Star this repo if you find it helpful!

[⬆ Back to Top](#-essential-linux-commands-guide)

</div>
