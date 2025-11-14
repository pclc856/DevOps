# Linux Fundamentals Complete Guide for DevOps

## 1. Linux Basics

### What is Linux?
- **Open-source** Unix-like operating system
- **Kernel** developed by Linus Torvalds (1991)
- **Distributions**: Ubuntu, CentOS, RHEL, Debian, Fedora, Amazon Linux
- **Multi-user**, multi-tasking system
- Powers most servers, cloud infrastructure, and containers

### Linux File System Hierarchy

```
/                    # Root directory
├── /bin            # Essential user binaries (ls, cp, cat)
├── /boot           # Boot loader files (kernel, initrd)
├── /dev            # Device files (hard drives, USB)
├── /etc            # System configuration files
├── /home           # User home directories
├── /lib            # System libraries
├── /media          # Removable media mount points
├── /mnt            # Temporary mount points
├── /opt            # Optional software packages
├── /proc           # Process and kernel information (virtual)
├── /root           # Root user's home directory
├── /run            # Runtime data
├── /sbin           # System binaries (admin commands)
├── /srv            # Service data
├── /sys            # System information (virtual)
├── /tmp            # Temporary files (cleared on reboot)
├── /usr            # User programs and data
│   ├── /usr/bin    # User binaries
│   ├── /usr/lib    # Libraries
│   └── /usr/local  # Locally installed software
└── /var            # Variable data (logs, databases)
    ├── /var/log    # Log files
    └── /var/www    # Web server files
```

---

## 2. Basic Linux Commands

### Navigation & Directory Operations

```bash
# Print Working Directory
pwd

# List files and directories
ls                  # Basic listing
ls -l              # Long format (permissions, owner, size, date)
ls -a              # Show hidden files (starting with .)
ls -lh             # Human-readable sizes
ls -lt             # Sort by modification time
ls -ltr            # Reverse time sort (oldest first)
ls -R              # Recursive listing

# Change Directory
cd /path/to/dir    # Absolute path
cd ..              # Parent directory
cd ~               # Home directory
cd -               # Previous directory
cd                 # Home directory (shortcut)

# Create Directory
mkdir dirname           # Create single directory
mkdir -p dir1/dir2/dir3 # Create nested directories
mkdir dir1 dir2 dir3    # Create multiple directories

# Remove Directory
rmdir dirname           # Remove empty directory
rm -r dirname          # Remove directory and contents
rm -rf dirname         # Force remove (no confirmation)

# Copy Directory
cp -r source dest      # Copy recursively

# Move/Rename
mv oldname newname     # Rename
mv file /path/to/dest  # Move file
```

### File Operations

```bash
# Create Empty File
touch filename
touch file1 file2 file3

# View File Contents
cat filename           # Display entire file
cat file1 file2       # Concatenate multiple files
cat > file.txt        # Create/overwrite file (Ctrl+D to save)
cat >> file.txt       # Append to file

# View File with Pagination
less filename         # View with navigation (q to quit)
more filename         # Simple pagination
head filename         # First 10 lines
head -n 20 filename   # First 20 lines
tail filename         # Last 10 lines
tail -n 20 filename   # Last 20 lines
tail -f filename      # Follow file (real-time updates)

# Copy Files
cp source dest            # Copy file
cp file1 file2 dest/      # Copy multiple files
cp -r dir1 dir2           # Copy directory
cp -p file1 file2         # Preserve attributes
cp -i file1 file2         # Interactive (prompt before overwrite)

# Move/Rename Files
mv source dest            # Move or rename
mv file1 file2 dest/      # Move multiple files
mv -i source dest         # Interactive

# Remove Files
rm filename               # Remove file
rm file1 file2 file3      # Remove multiple files
rm -i filename            # Interactive removal
rm -f filename            # Force removal
rm -rf directory/         # Remove directory recursively

# Create Links
ln source link            # Hard link
ln -s source link         # Symbolic (soft) link

# File Information
file filename             # Determine file type
stat filename             # Detailed file information
wc filename               # Word, line, byte count
wc -l filename            # Line count only
wc -w filename            # Word count only
```

---

## 3. File Permissions & Ownership

### Understanding Permissions

```
-rwxrwxrwx
│││││││││└─ Others execute
││││││││└── Others write
│││││││└─── Others read
││││││└──── Group execute
│││││└───── Group write
││││└────── Group read
│││└─────── Owner execute
││└──────── Owner write
│└───────── Owner read
└────────── File type (- file, d directory, l link)
```

### Permission Values

```
r (read)    = 4
w (write)   = 2
x (execute) = 1

rwx = 4+2+1 = 7
rw- = 4+2+0 = 6
r-x = 4+0+1 = 5
r-- = 4+0+0 = 4
```

### Permission Commands

```bash
# Change Permissions
chmod 755 filename        # rwxr-xr-x
chmod 644 filename        # rw-r--r--
chmod 600 filename        # rw-------
chmod u+x filename        # Add execute for user
chmod g-w filename        # Remove write for group
chmod o+r filename        # Add read for others
chmod a+x filename        # Add execute for all
chmod -R 755 directory/   # Recursive

# Change Ownership
chown user filename           # Change owner
chown user:group filename     # Change owner and group
chown -R user:group dir/      # Recursive
chgrp groupname filename      # Change group only

# Special Permissions
chmod +t directory/       # Sticky bit (only owner can delete)
chmod u+s filename        # SUID (run as owner)
chmod g+s directory/      # SGID (inherit group)

# View Permissions
ls -l filename
stat filename
```

### Common Permission Scenarios

```bash
# Executable script
chmod 755 script.sh

# Secure file (only owner can read/write)
chmod 600 private.key

# Web files
chmod 644 *.html
chmod 755 cgi-bin/

# Shared directory
chmod 775 shared/
chmod g+s shared/  # Files inherit group
```

---

## 4. Text Processing & Searching

### grep - Search Text

```bash
# Basic Search
grep "pattern" filename
grep -i "pattern" file        # Case-insensitive
grep -v "pattern" file        # Invert match (exclude)
grep -n "pattern" file        # Show line numbers
grep -c "pattern" file        # Count matches
grep -w "word" file           # Match whole word
grep -r "pattern" directory/  # Recursive search
grep -l "pattern" *.txt       # List files with matches

# Multiple Patterns
grep -e "pattern1" -e "pattern2" file
grep "pattern1\|pattern2" file

# Extended Regex
grep -E "pattern" file
egrep "pattern" file

# Practical Examples
grep "error" /var/log/syslog
grep -i "failed" /var/log/auth.log
grep -r "TODO" ~/projects/
ps aux | grep nginx
```

### find - Search Files

```bash
# Find by Name
find /path -name "filename"
find /path -iname "filename"     # Case-insensitive
find . -name "*.txt"             # Wildcard
find . -name "*.log" -o -name "*.txt"  # OR condition

# Find by Type
find . -type f                   # Files only
find . -type d                   # Directories only
find . -type l                   # Symbolic links

# Find by Size
find . -size +100M               # Larger than 100MB
find . -size -10k                # Smaller than 10KB
find . -size 50M                 # Exactly 50MB

# Find by Time
find . -mtime -7                 # Modified in last 7 days
find . -mtime +30                # Modified more than 30 days ago
find . -atime -1                 # Accessed in last 24 hours
find . -ctime -1                 # Changed in last 24 hours

# Find by Permissions
find . -perm 777                 # Exact permissions
find . -perm -644                # At least these permissions

# Find and Execute
find . -name "*.tmp" -delete
find . -name "*.log" -exec rm {} \;
find . -type f -exec chmod 644 {} \;

# Practical Examples
find /var/log -name "*.log" -mtime +7 -delete
find ~ -type f -size +100M
find . -name "*.sh" -exec chmod +x {} \;
```

### sed - Stream Editor

```bash
# Substitute
sed 's/old/new/' file              # First occurrence per line
sed 's/old/new/g' file             # All occurrences
sed 's/old/new/gi' file            # Case-insensitive
sed '3s/old/new/' file             # Line 3 only
sed '1,5s/old/new/' file           # Lines 1-5
sed -i 's/old/new/g' file          # Edit file in-place

# Delete Lines
sed '5d' file                      # Delete line 5
sed '1,3d' file                    # Delete lines 1-3
sed '/pattern/d' file              # Delete lines matching pattern

# Print Specific Lines
sed -n '5p' file                   # Print line 5
sed -n '1,10p' file                # Print lines 1-10
sed -n '/pattern/p' file           # Print matching lines

# Practical Examples
sed 's/http/https/g' urls.txt
sed -i 's/localhost/127.0.0.1/g' config.ini
sed -n '1,100p' largefile.txt > first100.txt
```

### awk - Text Processing

```bash
# Print Columns
awk '{print $1}' file              # First column
awk '{print $1, $3}' file          # First and third columns
awk '{print $NF}' file             # Last column
awk '{print $1, $2}' file          # Multiple columns

# Pattern Matching
awk '/pattern/ {print}' file
awk '$3 > 50' file                 # Third column > 50
awk '$1 == "root"' /etc/passwd

# Field Separator
awk -F: '{print $1}' /etc/passwd   # Colon separator
awk -F',' '{print $1, $3}' file.csv

# Calculations
awk '{sum+=$1} END {print sum}' file
awk '{sum+=$1; count++} END {print sum/count}' file

# Practical Examples
awk '{print $1, $3}' access.log
awk -F: '{print $1}' /etc/passwd
ps aux | awk '{print $2, $11}'
df -h | awk '$5 > 80 {print $0}'
```

### cut - Extract Sections

```bash
# By Character Position
cut -c1-5 file                     # Characters 1-5
cut -c1,3,5 file                   # Characters 1, 3, 5

# By Field
cut -d: -f1 /etc/passwd            # Field 1, delimiter ':'
cut -d',' -f1,3 file.csv           # Fields 1 and 3
cut -d' ' -f2-4 file               # Fields 2-4

# Practical Examples
cut -d: -f1,7 /etc/passwd          # Username and shell
echo $PATH | cut -d: -f1           # First PATH entry
```

### sort & uniq

```bash
# Sort
sort file                          # Alphabetical sort
sort -r file                       # Reverse sort
sort -n file                       # Numeric sort
sort -k2 file                      # Sort by column 2
sort -t: -k3 -n /etc/passwd        # Sort passwd by UID

# Unique
uniq file                          # Remove adjacent duplicates
sort file | uniq                   # Remove all duplicates
sort file | uniq -c                # Count occurrences
sort file | uniq -d                # Show only duplicates
sort file | uniq -u                # Show only unique

# Practical Examples
cat access.log | cut -d' ' -f1 | sort | uniq -c | sort -rn
sort -k5 -rn file.txt | head -10   # Top 10 by 5th column
```

---

## 5. Process Management

### Viewing Processes

```bash
# Process Status
ps                     # Current shell processes
ps aux                 # All processes (BSD style)
ps -ef                 # All processes (System V style)
ps aux | grep nginx    # Find specific process

# Top (Interactive)
top                    # Real-time process monitor
htop                   # Enhanced top (install separately)
top -u username        # Processes for specific user

# Process Tree
pstree                 # Show process tree
pstree -p              # Show PIDs

# Process Information
pidof nginx            # Get PID of process
pgrep nginx            # Search processes by name
```

### Managing Processes

```bash
# Run in Background
command &              # Run in background
nohup command &        # Run immune to hangups

# Job Control
jobs                   # List background jobs
fg %1                  # Bring job 1 to foreground
bg %1                  # Resume job 1 in background
Ctrl+Z                 # Suspend current process
Ctrl+C                 # Kill current process

# Kill Processes
kill PID               # Graceful termination (SIGTERM)
kill -9 PID            # Force kill (SIGKILL)
kill -15 PID           # Same as kill PID
killall nginx          # Kill all nginx processes
pkill -f "pattern"     # Kill by pattern

# Process Priority
nice -n 10 command     # Start with priority 10
renice -n 5 -p PID     # Change priority of running process
```

### System Monitoring

```bash
# CPU & Memory
free -h                # Memory usage (human-readable)
free -m                # Memory in MB
vmstat 1               # Virtual memory stats (1 sec interval)
mpstat                 # CPU stats

# Disk Usage
df -h                  # Disk space usage
df -i                  # Inode usage
du -h directory/       # Directory size
du -sh directory/      # Summary only
du -h --max-depth=1    # Top-level directories only

# IO Statistics
iostat                 # IO statistics
iotop                  # IO monitoring (like top)

# Network
netstat -tulpn         # Listening ports
ss -tulpn              # Socket statistics (modern netstat)
lsof -i :80            # Processes using port 80

# System Uptime
uptime                 # System uptime and load
w                      # Who is logged in and what they're doing
last                   # Login history
```

---

## 6. User & Group Management

### User Operations

```bash
# Add User
useradd username              # Create user
useradd -m username           # Create with home directory
useradd -m -s /bin/bash user  # Specify shell
useradd -m -G sudo user       # Add to sudo group

# Modify User
usermod -aG groupname user    # Add to additional group
usermod -s /bin/zsh user      # Change shell
usermod -L username           # Lock account
usermod -U username           # Unlock account

# Delete User
userdel username              # Delete user
userdel -r username           # Delete with home directory

# Password Management
passwd username               # Set/change password
passwd -l username            # Lock password
passwd -u username            # Unlock password
passwd -e username            # Expire password (force change)

# User Information
id username                   # User ID and groups
whoami                        # Current user
who                          # Logged in users
w                            # Detailed user info
```

### Group Operations

```bash
# Create Group
groupadd groupname

# Modify Group
groupmod -n newname oldname   # Rename group

# Delete Group
groupdel groupname

# View Groups
groups username               # User's groups
cat /etc/group               # All groups
```

### Switching Users

```bash
# Switch User
su username                   # Switch to user
su -                         # Switch to root
su - username                # Switch with environment

# Sudo
sudo command                  # Run as root
sudo -u user command         # Run as specific user
sudo -i                      # Interactive root shell
sudo -s                      # Shell as root

# Sudo Configuration
visudo                       # Edit sudoers file safely
```

---

## 7. Networking Commands

### Network Configuration

```bash
# View Network Interfaces
ip addr                       # Show all interfaces
ip addr show eth0            # Specific interface
ifconfig                     # Legacy command (deprecated)

# Assign IP Address
ip addr add 192.168.1.10/24 dev eth0
ip addr del 192.168.1.10/24 dev eth0

# Interface Up/Down
ip link set eth0 up
ip link set eth0 down

# Routing
ip route                     # Show routing table
ip route add default via 192.168.1.1
ip route del default
route -n                     # Legacy command
```

### Network Testing

```bash
# Ping
ping google.com              # Test connectivity
ping -c 4 google.com         # 4 packets only
ping -i 0.5 host            # 0.5 second interval

# Traceroute
traceroute google.com        # Trace route to host
tracepath google.com         # Similar to traceroute

# DNS Lookup
nslookup google.com          # DNS query
dig google.com               # Detailed DNS info
dig +short google.com        # Short output
host google.com              # Simple DNS lookup

# Network Statistics
netstat -tulpn               # Listening ports
netstat -an                  # All connections
ss -tulpn                    # Modern netstat
ss -s                        # Summary statistics

# Port Testing
telnet host 80               # Test port connectivity
nc -zv host 80               # Netcat port scan
nc -l 8080                   # Listen on port 8080

# Download Files
wget URL                     # Download file
wget -O filename URL         # Save with different name
curl URL                     # Transfer data
curl -O URL                  # Save file
curl -I URL                  # Headers only
```

### Network Tools

```bash
# Monitor Network
tcpdump -i eth0              # Packet capture
tcpdump -i eth0 port 80      # Filter by port
iftop                        # Bandwidth monitoring
nethogs                      # Per-process bandwidth

# Firewall (iptables)
iptables -L                  # List rules
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -F                  # Flush all rules

# Firewall (firewalld)
firewall-cmd --list-all
firewall-cmd --add-port=80/tcp
firewall-cmd --reload

# SSH
ssh user@host                # Connect to remote host
ssh -i keyfile user@host     # Use specific key
ssh -p 2222 user@host        # Custom port
scp file user@host:/path     # Copy file to remote
scp user@host:/path file     # Copy file from remote
```

---

## 8. Package Management

### APT (Debian/Ubuntu)

```bash
# Update Package List
apt update
apt-get update

# Upgrade Packages
apt upgrade                  # Upgrade all packages
apt full-upgrade            # Upgrade with dependency handling
apt dist-upgrade            # Legacy command

# Install Package
apt install package
apt install package1 package2 package3
apt install -y package      # Skip confirmation

# Remove Package
apt remove package          # Remove but keep config
apt purge package          # Remove including config
apt autoremove             # Remove unused dependencies

# Search Package
apt search keyword
apt-cache search keyword

# Package Information
apt show package
apt-cache show package
apt list --installed       # List installed packages
apt list --upgradable      # List upgradable packages

# Clean Up
apt clean                  # Clear cache
apt autoclean             # Clear old cache
```

### YUM/DNF (RHEL/CentOS/Fedora)

```bash
# Update Package List
yum check-update
dnf check-update

# Upgrade Packages
yum update                 # Upgrade all
yum upgrade               # Same as update
dnf update                # DNF (newer)

# Install Package
yum install package
dnf install package
yum install -y package    # Skip confirmation

# Remove Package
yum remove package
dnf remove package
yum autoremove           # Remove unused

# Search Package
yum search keyword
dnf search keyword

# Package Information
yum info package
dnf info package
yum list installed       # List installed
rpm -qa                  # All installed packages

# Repository Management
yum repolist             # List repositories
yum-config-manager --add-repo URL
```

### Snap (Universal)

```bash
# Install
snap install package

# List
snap list                # Installed snaps
snap find package        # Search

# Remove
snap remove package

# Update
snap refresh             # Update all
snap refresh package     # Update specific
```

---

## 9. System Administration

### Service Management (systemd)

```bash
# Service Control
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx           # Reload config
systemctl status nginx

# Enable/Disable
systemctl enable nginx           # Start on boot
systemctl disable nginx          # Don't start on boot
systemctl is-enabled nginx

# List Services
systemctl list-units --type=service
systemctl list-units --all
systemctl list-unit-files

# View Logs
journalctl                       # All logs
journalctl -u nginx              # Service logs
journalctl -f                    # Follow logs
journalctl --since "1 hour ago"
journalctl --since today
journalctl -p err                # Error messages only
```

### Cron Jobs (Scheduled Tasks)

```bash
# Edit Crontab
crontab -e                       # Edit for current user
crontab -l                       # List cron jobs
crontab -r                       # Remove all cron jobs
crontab -u user -e               # Edit for specific user

# Crontab Format
# * * * * * command
# │ │ │ │ │
# │ │ │ │ └─ Day of week (0-7, 0=Sunday)
# │ │ │ └─── Month (1-12)
# │ │ └───── Day of month (1-31)
# │ └─────── Hour (0-23)
# └───────── Minute (0-59)

# Examples
0 2 * * * /path/to/backup.sh     # Daily at 2 AM
*/15 * * * * /path/script.sh     # Every 15 minutes
0 0 * * 0 /path/weekly.sh        # Weekly on Sunday
0 3 1 * * /path/monthly.sh       # Monthly on 1st at 3 AM
```

### System Information

```bash
# System Info
uname -a                         # All system info
uname -r                         # Kernel version
hostname                         # System hostname
hostnamectl                      # Detailed hostname info

# OS Information
cat /etc/os-release              # OS details
lsb_release -a                   # Distribution info

# Hardware Info
lscpu                           # CPU information
lsmem                           # Memory information
lsblk                           # Block devices
lspci                           # PCI devices
lsusb                           # USB devices
dmidecode                       # Hardware details (needs root)

# Disk Information
fdisk -l                        # Disk partitions
blkid                           # Block device attributes
df -h                           # Disk usage

# Memory Information
free -h                         # Memory usage
cat /proc/meminfo               # Detailed memory info
```

### System Logs

```bash
# Log Files
/var/log/syslog                 # System log (Debian/Ubuntu)
/var/log/messages               # System log (RHEL/CentOS)
/var/log/auth.log               # Authentication log
/var/log/kern.log               # Kernel log
/var/log/boot.log               # Boot log

# View Logs
tail -f /var/log/syslog         # Follow system log
less /var/log/syslog            # View with pagination
grep "error" /var/log/syslog    # Search logs

# Log Rotation
logrotate /etc/logrotate.conf   # Manual rotation
```

---

## 10. Archive & Compression

### tar - Archiving

```bash
# Create Archive
tar -cvf archive.tar files/      # Create tar
tar -czvf archive.tar.gz files/  # Create tar.gz (gzip)
tar -cjvf archive.tar.bz2 files/ # Create tar.bz2 (bzip2)
tar -cJvf archive.tar.xz files/  # Create tar.xz (xz)

# Extract Archive
tar -xvf archive.tar             # Extract tar
tar -xzvf archive.tar.gz         # Extract tar.gz
tar -xjvf archive.tar.bz2        # Extract tar.bz2
tar -xJvf archive.tar.xz         # Extract tar.xz
tar -xvf archive.tar -C /path    # Extract to specific directory

# View Archive
tar -tvf archive.tar             # List contents
tar -tzvf archive.tar.gz

# Flags Explained
# c - create
# x - extract
# v - verbose
# f - file
# z - gzip
# j - bzip2
# J - xz
# t - list
```

### Compression

```bash
# gzip
gzip file                        # Compress (creates file.gz, removes original)
gzip -k file                     # Keep original
gunzip file.gz                   # Decompress
gzip -d file.gz                  # Same as gunzip

# bzip2
bzip2 file                       # Compress (creates file.bz2)
bunzip2 file.bz2                 # Decompress

# zip/unzip
zip archive.zip files            # Create zip
zip -r archive.zip directory/    # Recursive
unzip archive.zip                # Extract
unzip -l archive.zip             # List contents
unzip archive.zip -d /path       # Extract to path
```

---

## 11. Shell Scripting Basics

### Shebang & Basics

```bash
#!/bin/bash
# First script

# Comments start with #

# Print output
echo "Hello, World!"

# Variables
NAME="John"
AGE=30
echo "Name: $NAME, Age: $AGE"

# Read input
read -p "Enter your name: " USERNAME
echo "Hello, $USERNAME"
```

### Conditional Statements

```bash
# If statement
if [ condition ]; then
    commands
elif [ condition ]; then
    commands
else
    commands
fi

# Examples
if [ -f "/etc/passwd" ]; then
    echo "File exists"
fi

if [ $AGE -gt 18 ]; then
    echo "Adult"
else
    echo "Minor"
fi

# File Tests
-f file    # File exists
-d dir     # Directory exists
-r file    # Readable
-w file    # Writable
-x file    # Executable
-s file    # Not empty

# String Tests
-z string  # Empty string
-n string  # Not empty string
str1 = str2   # Strings equal
str1 != str2  # Strings not equal

# Numeric Tests
-eq        # Equal
-ne        # Not equal
-gt        # Greater than
-ge        # Greater or equal
-lt        # Less than
-le        # Less or equal
```

### Loops

```bash
# For loop
for i in 1 2 3 4 5; do
    echo "Number: $i"
done

for file in *.txt; do
    echo "Processing: $file"
done

for i in {1..10}; do
    echo $i
done

# While loop
counter=1
while [ $counter -le 5 ]; do
    echo "Count: $counter"
    ((counter++))
done

# Until loop
counter=1
until [ $counter -gt 5 ]; do
    echo "Count: $counter"
    ((counter++))
done
```

### Functions

```bash
# Define function
function_name() {
    echo "Inside function"
    echo "Argument 1: $1"
    echo "Argument 2: $2"
}

# Call function
function_name arg1 arg2

# Return value
add_numbers() {
    local sum=$(($1 + $2))
    echo $sum
}

result=$(add_numbers 5 3)
echo "Sum: $result"
```

### Practical Script Examples

```bash
# Backup script
#!/bin/bash
BACKUP_DIR="/backup"
SOURCE_DIR="/home/user/documents"
DATE=$(date +%Y%m%d)

tar -czf $BACKUP_DIR/backup_$DATE.tar.gz $SOURCE_DIR
echo "Backup completed: backup_$DATE.tar.gz"

# System monitoring
#!/bin/bash
echo "=== System Monitor ==="
echo "CPU Usage:"
top -bn1 | grep "Cpu(s)"
echo ""
echo "Memory Usage:"
free -h
echo ""
echo "Disk Usage:"
df -h /

# Log rotation
#!/bin/bash
LOG_DIR="/var/log/myapp"
ARCHIVE_DIR="/var/log/myapp/archive"

find $LOG_DIR -name "*.log" -mtime +7 -exec gzip {} \;
mv $LOG_DIR/*.gz $ARCHIVE_DIR/
```

---

## 12. Interview Questions & Answers

### Basic Questions

**Q: What is the difference between absolute and relative paths?**
- **Absolute**: Full path from root (`/home/user/file.txt`)
- **Relative**: Path from current directory (`../file.txt`, `./script.sh`)

**Q: Explain Linux file permissions**
- Three types: Read (r/4), Write (w/2), Execute (x/1)
- Three categories: User (owner), Group, Others
- Example: `rwxr-xr-x` = 755

**Q: Difference between hard link and soft link?**
- **Hard link**: Points to inode, same data, can't cross filesystems
- **Soft/Symbolic link**: Points to filename, can cross filesystems, breaks if original deleted

**Q: What is the purpose of /etc/passwd and /etc/shadow?**
- `/etc/passwd`: User account information (username, UID, GID, home, shell)
- `/etc/shadow`: Encrypted passwords and password policies (readable only by root)

**Q: How to find files modified in last 7 days?**
```bash
find /path -type f -mtime -7
```

**Q: Explain pipe (|) and redirection (>, >>)**
- **Pipe (|)**: Pass output of one command as input to another
  - `cat file.txt | grep "error"`
- **Redirect (>)**: Overwrite file with output
  - `echo "text" > file.txt`
- **Append (>>)**: Append output to file
  - `echo "text" >> file.txt`

**Q: What is the difference between /tmp and /var/tmp?**
- `/tmp`: Cleared on reboot, for temporary files
- `/var/tmp`: Persists across reboots, for files needed longer

**Q: How to check if a service is running?**
```bash
systemctl status service_name
ps aux | grep service_name
```

**Q: What is umask?**
- Default permission mask for new files/directories
- Subtracts from maximum permissions (666 for files, 777 for dirs)
- `umask 022` results in 644 for files, 755 for directories

### Advanced Questions

**Q: Explain Linux boot process**
1. **BIOS/UEFI**: Hardware initialization, POST
2. **Bootloader (GRUB)**: Loads kernel
3. **Kernel**: Initializes hardware, mounts root filesystem
4. **Init/Systemd**: First process (PID 1), starts services
5. **Runlevel/Target**: Brings system to operational state

**Q: How to troubleshoot high CPU usage?**
```bash
top                    # Identify process
ps aux --sort=-%cpu | head
htop                   # Interactive view
pidstat 1              # Per-process stats
```

**Q: How to check open files by a process?**
```bash
lsof -p PID            # By process ID
lsof -u username       # By user
lsof -i :80            # By port
lsof /var/log/syslog   # Which process has file open
```

**Q: What is inode?**
- Data structure storing metadata about files (not filename or content)
- Contains: permissions, ownership, size, timestamps, pointers to data blocks
- Check inodes: `df -i`
- Find inode: `ls -i filename`

**Q: How to recover deleted files in Linux?**
- If process still has file open: `lsof +L1`, recover from `/proc/PID/fd/`
- Use tools: `extundelete`, `testdisk`, `photorec`
- Restore from backup (best practice)

**Q: Explain swap space**
- Virtual memory on disk when RAM is full
- Check swap: `swapon --show` or `free -h`
- Create swap: `mkswap`, `swapon`
- Swappiness: Controls tendency to swap (0-100)

**Q: How to analyze system performance issues?**
```bash
top/htop              # Overall system load
vmstat 1              # Virtual memory stats
iostat -x 1           # Disk I/O
sar -u 1 10           # CPU usage over time
dmesg | tail          # Kernel messages
journalctl -xe        # System logs
```

**Q: What is SELinux?**
- **Security-Enhanced Linux**: Mandatory Access Control (MAC)
- Enforces security policies beyond standard permissions
- Modes: Enforcing, Permissive, Disabled
- Check status: `getenforce`, `sestatus`
- Set mode: `setenforce 0|1`

**Q: Explain different types of shells**
- **bash**: Bourne Again Shell (most common)
- **sh**: Original Bourne Shell
- **zsh**: Z Shell (enhanced features)
- **fish**: Friendly Interactive Shell
- **csh/tcsh**: C Shell
- Check current shell: `echo $SHELL`

---

## 13. Linux Security Best Practices

### User Security

```bash
# Strong password policies
vim /etc/login.defs           # Password aging
vim /etc/pam.d/common-password # Password complexity

# Disable root login via SSH
vim /etc/ssh/sshd_config
# PermitRootLogin no

# Use SSH keys instead of passwords
ssh-keygen -t rsa -b 4096
ssh-copy-id user@host

# Limit sudo access
visudo
# user ALL=(ALL) /usr/bin/systemctl, /usr/bin/docker

# Account lockout after failed attempts
vim /etc/pam.d/common-auth
# auth required pam_tally2.so deny=5 unlock_time=900
```

### Firewall Configuration

```bash
# UFW (Ubuntu)
ufw enable
ufw default deny incoming
ufw default allow outgoing
ufw allow 22/tcp
ufw allow 80/tcp
ufw allow 443/tcp
ufw status

# Firewalld (RHEL/CentOS)
firewall-cmd --state
firewall-cmd --get-default-zone
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --reload
```

### System Hardening

```bash
# Disable unnecessary services
systemctl list-unit-files --state=enabled
systemctl disable service_name

# Update system regularly
apt update && apt upgrade -y    # Ubuntu/Debian
yum update -y                   # RHEL/CentOS

# Configure automatic security updates
apt install unattended-upgrades
dpkg-reconfigure --priority=low unattended-upgrades

# File integrity monitoring
apt install aide
aideinit
aide --check

# Set secure file permissions
chmod 700 /root
chmod 600 /boot/grub/grub.cfg
chmod 644 /etc/passwd
chmod 600 /etc/shadow
```

---

## 14. Troubleshooting Scenarios

### System Won't Boot

```bash
# Boot into recovery mode
# 1. Reboot and select recovery mode from GRUB
# 2. Check filesystem
fsck /dev/sda1

# 3. Check fstab
cat /etc/fstab
# Fix any errors in mount points

# 4. Check boot logs
journalctl -xb
dmesg | less
```

### High Load Average

```bash
# Check load average
uptime
top

# Find culprit
ps aux --sort=-%cpu | head -10
ps aux --sort=-%mem | head -10

# Check I/O wait
iostat -x 1

# Check disk space
df -h

# Check for zombie processes
ps aux | awk '$8=="Z"'
```

### Out of Disk Space

```bash
# Find large files
du -ah / | sort -rh | head -20
find / -type f -size +100M

# Check disk usage
df -h
df -i                    # Check inodes

# Clear logs
journalctl --vacuum-size=100M
find /var/log -name "*.log" -mtime +30 -delete

# Clear package cache
apt clean               # Ubuntu/Debian
yum clean all          # RHEL/CentOS
```

### Network Issues

```bash
# Check connectivity
ping 8.8.8.8
ping google.com        # DNS check

# Check interface status
ip addr
ip link show

# Check routing
ip route
traceroute google.com

# Check DNS
cat /etc/resolv.conf
nslookup google.com

# Check firewall
iptables -L
ufw status

# Check listening ports
netstat -tulpn
ss -tulpn
```

### Service Won't Start

```bash
# Check service status
systemctl status service_name

# View detailed logs
journalctl -u service_name -n 50
journalctl -u service_name --since "10 minutes ago"

# Check configuration
nginx -t              # Nginx config test
apachectl configtest  # Apache config test

# Check port availability
netstat -tulpn | grep :80
lsof -i :80

# Check permissions
ls -l /var/run/service.pid
ls -l /var/log/service/
```

---

## 15. Performance Tuning

### CPU Optimization

```bash
# Check CPU info
lscpu
cat /proc/cpuinfo

# Monitor CPU
mpstat 1
sar -u 1 10

# Set process priority
nice -n 10 command     # Start with lower priority
renice -n 5 -p PID     # Adjust running process

# CPU governor (power management)
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor
echo performance | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

### Memory Optimization

```bash
# Check memory usage
free -h
vmstat 1
cat /proc/meminfo

# Clear cache (safe)
sync
echo 3 > /proc/sys/vm/drop_caches

# Tune swappiness
cat /proc/sys/vm/swappiness
sysctl vm.swappiness=10
# Permanent: add to /etc/sysctl.conf

# OOM killer settings
cat /proc/sys/vm/overcommit_memory
```

### Disk I/O Optimization

```bash
# Monitor I/O
iostat -x 1
iotop

# Check mount options
cat /etc/fstab
# Add 'noatime' to reduce writes

# Disk scheduler
cat /sys/block/sda/queue/scheduler
echo deadline > /sys/block/sda/queue/scheduler

# Read-ahead optimization
blockdev --getra /dev/sda
blockdev --setra 4096 /dev/sda
```

### Network Optimization

```bash
# Check network stats
netstat -s
ss -s

# Increase network buffers
sysctl -w net.core.rmem_max=16777216
sysctl -w net.core.wmem_max=16777216

# TCP tuning
sysctl -w net.ipv4.tcp_window_scaling=1
sysctl -w net.ipv4.tcp_timestamps=1

# Connection tracking
sysctl -w net.netfilter.nf_conntrack_max=262144

# Make permanent
vim /etc/sysctl.conf
sysctl -p
```

---

## 16. Essential Linux Tools

### System Monitoring Tools

| Tool | Purpose |
|------|---------|
| **top/htop** | Process monitoring |
| **vmstat** | Virtual memory stats |
| **iostat** | I/O statistics |
| **sar** | System activity report |
| **netstat/ss** | Network statistics |
| **iftop** | Network bandwidth |
| **iotop** | I/O by process |
| **glances** | All-in-one monitoring |

### Text Editors

```bash
# Vim
vim filename
# i - insert mode
# Esc - command mode
# :w - save
# :q - quit
# :wq - save and quit
# :q! - quit without saving

# Nano
nano filename
# Ctrl+O - save
# Ctrl+X - exit
# Ctrl+K - cut line
# Ctrl+U - paste

# Basic editing
sed -i 's/old/new/g' file
```

### File Transfer Tools

```bash
# SCP (Secure Copy)
scp file.txt user@host:/path/
scp -r directory/ user@host:/path/
scp user@host:/path/file.txt .

# RSYNC
rsync -avz source/ user@host:/dest/
rsync -avz --delete source/ dest/  # Mirror
rsync -avzP --exclude='*.log' source/ dest/

# FTP/SFTP
sftp user@host
get remote_file
put local_file
```

---

## 17. Quick Reference Cheat Sheet

### File Operations
```bash
ls -la              # List all files
cd /path            # Change directory
mkdir dir           # Create directory
rm -rf dir          # Remove directory
cp -r src dst       # Copy recursively
mv src dst          # Move/rename
chmod 755 file      # Change permissions
chown user:group    # Change ownership
find / -name file   # Find file
grep pattern file   # Search in file
```

### Process Management
```bash
ps aux              # List processes
top                 # Monitor processes
kill PID            # Kill process
pkill name          # Kill by name
bg                  # Background job
fg                  # Foreground job
nohup cmd &         # Run detached
```

### System Information
```bash
uname -a            # System info
hostname            # Host name
uptime              # System uptime
df -h               # Disk space
free -h             # Memory usage
lscpu               # CPU info
```

### Network
```bash
ip addr             # IP addresses
ping host           # Test connectivity
netstat -tulpn      # Listening ports
ss -tulpn           # Socket stats
curl URL            # HTTP request
wget URL            # Download file
```

### Package Management
```bash
# Ubuntu/Debian
apt update
apt install pkg
apt remove pkg

# RHEL/CentOS
yum update
yum install pkg
yum remove pkg
```

### Services
```bash
systemctl start svc     # Start service
systemctl stop svc      # Stop service
systemctl status svc    # Check status
systemctl enable svc    # Enable on boot
journalctl -u svc       # View logs
```

---

## 18. Practice Exercises

### Exercise 1: User Management
1. Create user "devops" with home directory
2. Set password for user
3. Add user to sudo group
4. Create SSH key for user
5. Configure SSH key-based authentication

### Exercise 2: System Monitoring Script
Create a script that:
1. Checks CPU usage
2. Checks memory usage
3. Checks disk usage
4. Sends alert if any metric exceeds threshold
5. Logs results to file

### Exercise 3: Log Analysis
1. Find all error messages in /var/log/syslog
2. Count unique IP addresses in access log
3. Find top 10 most accessed URLs
4. Identify failed login attempts
5. Create summary report

### Exercise 4: Backup Automation
1. Write script to backup /home directory
2. Compress backup with timestamp
3. Keep only last 7 days of backups
4. Schedule with cron (daily at 2 AM)
5. Email notification on completion

---

## Resources for Learning

- **Linux Journey**: linuxjourney.com
- **OverTheWire Wargames**: overthewire.org/wargames/
- **Linux Command Line Book**: Free PDF online
- **Man Pages**: `man command` for any command
- **TLDR Pages**: tldr.sh - Simplified man pages
- **Linux Academy/A Cloud Guru**: Video courses

Good luck with Linux mastery! 🐧 to check disk space?**
- `df -h` for filesystem usage
- `du -sh directory/` for directory size

### Intermediate Questions

**Q: Explain process states in Linux**
- **Running (R)**: Executing or ready to execute
- **Sleeping (S)**: Waiting for event
- **Stopped (T)**: Stopped by signal
- **Zombie (Z)**: Terminated but parent hasn't read status
- **Uninterruptible Sleep (D)**: Waiting for I/O

**Q: Difference between kill and killall?**
- `kill PID`: Terminates specific process by PID
- `killall name`: Terminates all processes by name
- `kill -9`: Force kill (SIGKILL)
- `kill -15`: Graceful termination (SIGTERM, default)

**Q: How
