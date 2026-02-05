
> [!ABSTRACT] What is CLI?
> **CLI (Command Line Interface)** is a text-based interface used to interact with the Operating System directly.
> * **GUI (Graphical User Interface):** You click icons (User friendly, but limited).
> * **CLI:** You type commands (Harder, but gives you **Full** control).
> * **The Shell:** The program (like `bash` or `zsh`) that interprets your text commands and sends them to the OS Kernel.

**Environment:** Ubuntu Terminal or cmder on Win

Tags: #linux #shell #bash #cli #terminal

---
# Playlist Link

https://youtube.com/playlist?list=PLDoPjvoNmBAxzNO8ixW83Sf8FnLy_MkUT&si=vgxyqnpANgNulMwz
# Introduction
## 1️⃣ Terminal vs. Shell vs. Kernel 
To understand what happens when you type a command:
1.  **Terminal:** The window/app you see (The black screen wrapper) , text i/o environment.
2.  **Shell:** The translator "Interpreter". It takes your English command (`ls`) and converts it to machine instructions.
3.  **Kernel:** The heart of the OS that actually executes the instruction on the Hardware.

## 2️⃣ CMD vs. PowerShell

- CMD --> is The Original Shell for the Microsoft DOS OS 
	
- PowerShell --> new shell for Microsoft after CMD

## 3️⃣ Why Learn CLI? (The Superpowers)
* **Speed:** Typing `mkdir folder{1..100}` creates 100 folders in 1 second. Try doing that with a mouse.
* **Automation:** Anything you type can be saved in a "Script" to run automatically.
* **Servers:** Real servers (Cloud/AWS) don't have a Mouse or Screen. CLI is the **ONLY** way to talk to them.

---

# Files And Directories

| **Command**             | **Usage**                                                  |
| ----------------------- | ---------------------------------------------------------- |
| `pwd`                   | Where am I right now?                                      |
| `cd folder_name`        | Move to a folder.                                          |
| `cd /`                  | Go to the Root (System Start).                             |
| `cd ~`                  | Go to my Home (`/home/user`).                              |
| `cd ..`                 | Go back one step (Parent).                                 |
| `ls`                    | List files (Show content).                                 |
| `ls -a`                 | List all files (including hidden `.` files).               |
| `mkdir folder`          | Make a new directory.                                      |
| `mkdir one two three`   | Make multiple directories at once.                         |
| `mkdir "My Folder"`     | Make directory with **spaces** (must use quotes).          |
| `mkdir -p parent/child` | Make directory inside another (creates parent if missing). |
| `mv old_name new_name`  | **Rename** a file or folder.                               |
| `mv source destination` | **Move** a folder into another.                            |
| `cp -r source dest`     | Copy folder and its content (Create dest if missing).      |
| `rmdir folder`          | Remove an **empty** directory (Safe).                      |
| `rm -d folder`          | Remove an **empty** directory                              |
| `rm -r folder`          | Remove directory & contents **(DANGER: Deletes Forever)**. |

# Reading & Writing Content

| **Command**                 | **Usage**                                                            |
| --------------------------- | -------------------------------------------------------------------- |
| `echo "Text"`               | Print text to screen.                                                |
| `echo "Text" > file.txt`    | **Write** text to file (Overwrites existing content).                |
| `echo "Text" >> file.txt`   | **Append** text to end of file (Keeps existing content).             |
| `cat file.txt`              | Display file content on screen.                                      |
| `cat file1 file2`           | Display contents of multiple files together.                         |
| `cat file1 file2 > new.txt` | Combine two files and save into a **new file**.                      |
| `cat *`                     | print the content of the Whole directory , (Must be only Text Files) |

# Searching Content (Grep)

| **Command**           | **Usage**                                                                                 |
| --------------------- | ----------------------------------------------------------------------------------------- |
| `grep "text" file`    | Search for "text" inside a specific file.                                                 |
| `grep -i "text" file` | Search ignoring case (e.g., finds "Text", "TEXT", "text").                                |
| `grep -r "text" .`    | Search inside **all files** in the current directory & sub-directories.                   |
| `grep -n "text" file` | Show the **Line Number** where the text was found (Crucial for coding).                   |
| `grep -w "text" file` | Search for the **Whole Word** only (e.g., "cat" won't match "cats").                      |
| `grep -v "text" file` | Invert match (Show lines that do **NOT** contain the text).                               |
| `grep -l "text" file` | Show **filenames** only (hide the matching text lines).                                   |
| `grep -rl "text" .`   | **Recursive List**: Find all files containing "text" inside current folder & sub-folders. |
| `grep -ri "text" .`   | **Recursive + Insensitive**: Search everywhere ignoring case (Combine flags).             |

# System Inspection & Control

| **Command**            | **Usage**                                                |
| ---------------------- | -------------------------------------------------------- |
| `man command`          | Show the **Manual** (Help guide) for any command.        |
| `clear,CTRL+L`         | Clean the terminal screen.                               |
| `history`              | Show list of all previously typed commands.              |
| `file filename`        | Detect the **true type** of a file (Not just extension). |
| `tree`                 | Show folder structure as a visual tree.                  |
| `ps` or `top`          | Show running processes (Programs).                       |
| `Ctrl + C`             | **Kill/Stop** the currently running process immediately. |
| `exit`                 | Close the terminal.                                      |
| `whoami`               | Print current username.                                  |
| `ip a` (or `ifconfig`) | Show Network IP Address.                                 |
| `uname -a`             | Show System & Kernel info.                               |
| `alias name='command'` | Create a shortcut (Temporary).                           |
| `cmd1 && cmd2`         | Run cmd2 **ONLY IF** cmd1 succeeds.                      |
| `cmd1 ; cmd2`          | Run cmd2 **ALWAYS** after cmd1 finishes.                 |
