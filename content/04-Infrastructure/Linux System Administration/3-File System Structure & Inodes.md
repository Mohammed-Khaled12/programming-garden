# Storage Concepts Overview

- **Sectors:** Smallest physical units on a hard disk
- **Blocks:** Logical units created after formatting (contain multiple sectors)
- **Partitions:** Logical divisions of disk space
- **File Systems:** Organizational structure applied to partitions 
- **Mount Points:** Empty directories that act as gateways to file systems

A file system exists on a formatted partition. To access it, you need a mount point - an empty directory that serves as the entry point to that file system

# Inode (Index Node)
هو رقم مميز لكل فايل او دايركتوري 
Unique Identifier 
اي ملف بيتكريت الكيرنال بيديله Inode مميز 

الرقم ده بقي عباره عن داتا ستراكتشر متخزن في مكان مخصص في الديسك وظيفته انه يحتفظ بال Metadata

### What Inodes Store

Each inode contains metadata about the file:

- File type (regular file, directory, etc.)
- Permissions (read, write, execute)
- Ownership (user and group IDs)
- File size
- Creation and modification timestamps 
- Link count (how many names point to this inode)
- Pointers to data blocks on disk 
**Important:** Inodes do NOT store the file name!

![[Pasted image 20260719014542.png]]

![[Pasted image 20260719014918.png]]

ازاي اشوف ال inode بتاع فايل؟
```bash
ls -li
```

## Directory Entries
طالما ال Inode مبيشيلش الاسم اومال هو فين؟
موجود في ال  Directory Entries ده دايركتوري موجود في كل ال dirs جواه جدول فيه اسم الفايل و ال inode المرتبط بيه و نوع الفايل 

![[Pasted image 20260719015349.png]]

## Inode Table
كل FS ليه Inode Table فيه كل ال Inodes بتاعت ال FS ده

![[Pasted image 20260719015509.png]]

*Note* --> عادي ممكن 2 اينود يتشابهوه لكن ميكونوش علي نفس الفايل سيستم
*Note* --> عادي اسم فايلين يتشابهوا بالضبط بس ميكونوش في نفس الدايريكتوي و كل واحد هياخد اينود مختلف

FS --> `ext4`, `xfs`, `btrfs` ....

