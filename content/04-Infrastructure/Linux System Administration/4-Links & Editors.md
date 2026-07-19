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

