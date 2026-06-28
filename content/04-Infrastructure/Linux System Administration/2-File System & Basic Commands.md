# Everything is a File
## Linux File Types

1) NORMAL FILES --->  Documents, programs, data files.
2) DIRECTORIES    --->  Containers for other files "Folder in Windows".
3) SPECIAL FILES  ---> Hardware devices (/dev/sda1) , System processes.
## File Type Identification

Linux Don't Care about Extension of the File , Linux determines file type by examining the file's content and headers
`file` command tells you the file type

   `ls -l` --->  NORMAL FILES --->  Starts with "-".
	    ---> DIRECTORIES    --->  Starts with "d".
	    ---> SPECIAL FILES  --->  Block device "b" , Character "c" , Symbolic link "l".
![[Pasted image 20260628170330.png]]

![[Pasted image 20260628160034.png]]

## Linux File System Hierarchy

Unlike Windows (C:, D:, E: drives), Linux uses a **single-rooted hierarchical** file system. Everything starts from the root directory `/`.

![[Pasted image 20260628160308.png]]

![[Pasted image 20260628160352.png]]

### Important Directory Purposes:
1) `/bin` ---> Essential user commands


| **Directory** | **Purpose**                                                             | **Examples**                                                                             |
| ------------- | ----------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `/bin`        | Essential user commands                                                 | ls, cat, cp, mv                                                                          |
| `/boot`       | Boot loader/process files                                               | Linux kernel, bootloader                                                                 |
| `/dev`        | Device files                                                            | /dev/sda1 (hard drive), /dev/tty1 (terminal) الكيرنال بيستخدمه علشان يكلم الهاردوير نفسه |
| `/etc`        | *Almost* System configuration                                           | /etc/passwd, /etc/hosts                                                                  |
| `/home`       | User home directories بيشيل اليورز بس مش كلهم                           | /home/mohammed                                                                           |
| `/root`       | Personal directory for root user<br>Separate from /home (regular users) |                                                                                          |
| `/lib`        | Essential shared libraries                                              | System libraries for /bin and /sbin                                                      |
| `/media`      | Removable media "Mount Point"                                           | /media/usb, /media/cdrom                                                                 |
**Three Different "Roots":**

ROOT DIRECTORY (/)
• Top of file system tree
• Where everything begins

ROOT USER
• Superuser account (like Windows Administrator)
• Has complete system control

ROOT HOME (/root)
• Personal directory for root user
• Separate from /home (regular users)

# File System Navigation
#### Finding Your Location

```
pwd  # Print Working Directory - shows your current location
```

#### Changing Directories

```
cd /              # Go to root directory
cd /home          # Go to /home directory  
cd /home/username # Go to specific user's home
cd ~              # Go to your home directory (shortcut)
cd -              # Go back to previous directory
```
#### Understanding Paths

**Path Types Comparison:**

**Absolute Paths**: Start with `/` and specify the complete path from root
**Relative Paths**: Start from your current location
![[Pasted image 20260628165958.png]]
#### Special Directory References

- `.` (dot): Refers to current directory
- `..` (dot-dot): Refers to parent directory

```
# Demonstrate . and .. 
pwd                    # Shows current location
ls -l .               # List current directory (same as ls -l)
ls -l ..              # List parent directory
cd ..                 # Move up one level
cd ../..              # Move up two levels ترجع ورا خطوتين
cd ./subdirectory     # Enter subdirectory (explicit current dir reference)
```

#### More about Navigation

Files starting with `.` are hidden in Linux:
```
ls              # Shows visible files only
ls -a           # Shows all files including . and ..
ls -A           # Shows all hidden files except . and ..
```

Creating Files and Directories
```
ouch filename.txt        # Create empty file
touch file1 file2 file3   # Create multiple files
mkdir dirname             # Create directory
mkdir -p path/to/dir      # Create directory path (including parents)
```

Viewing File Contents
```
cat filename.txt          # Display entire file content
less filename.txt         # View file page by page (q to quit)
more filename.txt         # View file page by page (older version of less)
head filename.txt         # Show first 10 lines
tail filename.txt         # Show last 10 lines
```
