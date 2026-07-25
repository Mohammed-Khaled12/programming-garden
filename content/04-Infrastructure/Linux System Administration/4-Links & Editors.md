# Links

![[Pasted image 20260719194023.png]]

![[Pasted image 20260719202731.png]]
## Hard Links (Multiple Names, One Inode)

A hard link is simply an additional **Directory Entry** that points to the exact same **Inode** as the original file.

When you create a hard link, you are not copying the data, nor are you creating a shortcut. You are creating a second, equally valid name for the exact same underlying data on the storage disk.

- **How it works:** Both the original file and the hard link share the exact same Inode number.
    
- **Deletion Behavior:** The kernel keeps a "link count" for every Inode. Deleting a file just removes its directory entry and drops the link count by 1. The actual data on the disk is **only** deleted when the link count reaches 0. If you delete the "original" file, the hard link continues to work perfectly because the data is still there.
    
- **Command:** 
```bash
ln target_file link_name
```
    
- **Architectural Limitations:**
    
    1. **Cannot link directories:** The system forbids this to prevent infinite loops in the file system tree.
        
    2. **Cannot cross file systems:** You cannot create a hard link from one partition (e.g., `/`) to another (e.g., `/var` on a different drive). Since each file system has its own independent Inode Table, an Inode number from one drive means absolutely nothing on another drive

```bash
─ pwd
/home/mohammed/Testat

╭─    ~/Testat ·································· ✔  2   07:51:53 PM  
╰─ touch file{1..3}

╭─    ~/Testat ·································· ✔  2   07:52:00 PM  
╰─ ls -li
total 0
1188198 -rw-rw-r-- 1 mohammed mohammed 0 Jul 19 19:52 file1
1188744 -rw-rw-r-- 1 mohammed mohammed 0 Jul 19 19:52 file2
1188829 -rw-rw-r-- 1 mohammed mohammed 0 Jul 19 19:52 file3

╭─    ~/Testat ·································· ✔  2   07:52:02 PM  
╰─ ln file1 file2 
ln: failed to create hard link 'file2': File exists

╭─    ~/Testat ································ 1 ✘  2   07:52:11 PM  
╰─ # Because File 2 already exist you can't make it a Hard Link     

╭─    ~/Testat ································ 1 ✘  2   07:54:04 PM  
╰─ ln file1 file4

╭─    ~/Testat ·································· ✔  2   07:54:23 PM  
╰─ ls -li        
total 0
1188198 -rw-rw-r-- 2 mohammed mohammed 0 Jul 19 19:52 file1
1188744 -rw-rw-r-- 1 mohammed mohammed 0 Jul 19 19:52 file2
1188829 -rw-rw-r-- 1 mohammed mohammed 0 Jul 19 19:52 file3
1188198 -rw-rw-r-- 2 mohammed mohammed 0 Jul 19 19:52 file4
```

## Soft / Symbolic Links (New File, Path Shortcut)
*Windows Shortcut*

A soft link (Symbolic link) is a completely **separate file** with its own unique **Inode**.

Instead of pointing directly to the data blocks on the disk, the data block of a soft link simply contains the **text string of the path** pointing to the target file.

- **How it works:** When you access a soft link, the operating system reads the text path stored inside it and automatically redirects you to that target path.
    
- **Deletion Behavior:** If you delete the original target file, the soft link remains intact, but it becomes a **"broken" or "dangling" link**. It is essentially pointing to a path that no longer exists, resulting in an error if you try to open it.
    
- **Command:** 
```bash
ln -s target_file link_name
```

ياريت تكتب ال target_file بال Absolute Path احسن 
لأنك لو استخدمت Relative Path وعملت للـ Soft Link ده `mv` لأي فولدر تاني، اللينك هيتكسر  (Broken Link) والسيستم مش هيلاقيه. لكن الAbsolute Path بيثبته ويخليه يشاور على الداتا الصح مهما مكان اللينك نفسه اتغير.

- **Architectural Advantages:**
    
    1. **Can link directories:** Widely used to redirect folders like logs or configurations to different locations without breaking applications.
        
    2. **Can cross file systems:** Because it relies on a text-based path (e.g., `/var/log/syslog`) rather than an Inode number, a soft link can easily point to files on completely different hard drives or network mounts.

![[Pasted image 20260719202945.png]]

*لاحظ ان فايل 5 بقي بيبدا ب L اللي هي Soft Link*
*لاحظ ان الاينودز مختلفه*

# Editors: Vi & VIM

![[Pasted image 20260720002005.png]]
### Basic Commands (Command Mode)

- **i**: Insert text before the cursor.
- **a**: Append text after the cursor.
- **A**: Append text at the end of the line.
- **o**: Open a new line below the current line.
- **O**: Open a new line above the current line.

### Navigation (Command Mode)

- **h**: Move cursor left.
- **j**: Move cursor down.
- **k**: Move cursor up.
- **l**: Move cursor right.
- **w**: Move to the beginning of the next word.
- **b**: Move to the beginning of the previous word.
- **0**: Move to the beginning of the line.
- **$**: Move to the end of the line.
- **G**: Move to the end of the file.
- **gg**: Move to the beginning of the file.

### Editing (Command Mode)

- **x**: Delete the character under the cursor.
- **dd**: Delete the current line.
- **yy**: Yank (copy) the current line.
- **p**: Paste the yanked line below the current line.
- **P:** Paste the yanked line above the current line.
- **u**: Undo the last change.
- **Ctrl-r**: Redo the undone change.

### Searching and Replacing

- **/pattern**: Search for `pattern` in the file.
- **n**: Move to the next occurrence of the search pattern.
- **N**: Move to the previous occurrence of the search pattern.
- **:s/old/new**: Replace the first occurrence of `old` with `new` in the current line.
- **:s/old/new/g**: Replace all occurrences of `old` with `new` in the current line.
- **:%s/old/new/g**: Replace all occurrences of `old` with `new` in the entire file.

### Saving and Exiting

- **:w**: Save the file.
- **:w file name:** Save as to specific file name
- **:q**: Quit `vi`.
- **:wq**: Save the file and quit `vi`.
- **:q!**: Quit `vi` without saving changes.

### Advanced Commands

- **:r filename**: Read the contents of `filename` and insert it after the current line.
- **:!command**: Execute an external command and display the output in `vi`.
- **:set number**: Show line numbers.
- **:set nonumber**: Hide line numbers.

### The `.vimrc` File (Vim Configuration)

The `.vimrc` (Vim Run Commands) is a hidden configuration file located in your home directory (`~/.vimrc`). It acts as the "brain" of the editor. Every time you launch `vim`, the system reads this file first and executes its instructions before rendering the interface.

 Core Architectural Rules:
1. **Persistence:** Running a command like `:set number` inside a live Vim session is temporary; it disappears when you close the file. To make configurations permanent across all future sessions, they must be declared in `.vimrc`.
    
2. **Hidden File:** The dot prefix (`.`) makes it a hidden file. It won't appear with a standard `ls` command; you must use `ls -a` to view it.
    
3. **Language:** The commands inside this file are not Bash commands. They are written in Vim's own dedicated scripting language called **Vimscript**.

```vim
" This is how you write a comment in Vimscript

" 1. Visual & UI Settings
syntax on           " Enable Syntax Highlighting (Crucial for programming)
set number          " Show absolute line numbers on the left
set relativenumber  " Show relative line numbers (For rapid vertical movement)
set cursorline      " Highlight the current line you are on

" 2. Indentation & Tabs (C++ / Backend Standard)
set autoindent      " Copy the indentation from the previous line
set tabstop=4       " A TAB character equals 4 spaces
set shiftwidth=4    " Indent size for auto-indent is 4 spaces
set expandtab       " Convert TABs to actual spaces (Prevents formatting breakage across different editors)

" 3. Usability Tweaks
set mouse=a         " Enable mouse support (Allows scrolling and clicking if needed)
set ignorecase      " Ignore case when searching
set smartcase       " ...unless you type a Capital letter in the search query
```