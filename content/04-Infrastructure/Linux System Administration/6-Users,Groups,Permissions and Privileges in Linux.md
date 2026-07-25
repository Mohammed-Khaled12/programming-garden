
# Types of Users

Linux systems classify users into two main categories:

| User Type            | Description                                                                       |
| -------------------- | --------------------------------------------------------------------------------- |
| **Superuser (root)** | Has all system privileges and can perform any operation                           |
| **Normal User**      | Limited privileges, requires specific permissions or sudo for elevated operations |

Users also can be categorized based on their ability to log in interactively:

| User Type           | Can Login? | Shell                             | Purpose                                                   |
| ------------------- | ---------- | --------------------------------- | --------------------------------------------------------- |
| **Login Users**     | Yes        | /bin/bash, <br>/bin/zsh,<br>etc.  | Regular users who can authenticate and access the system  |
| **Non-Login Users** | No         | /sbin/nologin, <br>/bin/**false** | System/application users for running background processes |

# User and Group Identifiers (UID & GID)

يعني ايه جروب الاول بس؟
الجروب هو Role الكيرنال بيديه لشويه يوزرس و يديهم صلاحيات جماعيه عن طريق انه يدي الجروب
اي نظام تشغيل بيتعامل مع ارقام مش اسماء
Systems Deals With **Numbers**
Every User or Group of user "Group" Have Unique Identifier 
- Unique User Identifier --> UID
- Unique Group Identifier  --> GID
ارقام مميزه بنديها لكل يوزر او جروب 
--> root UID = 0
--> root GID = 0
اليوزر اول ما بيتكريت الكيرنال بيديله UID و لازم يبقي عضو في Primary Group واحد علي الاقل لو انت محطيتوش السيستم هيعمل جروب برايمري لليوزر ده باسمه و يديهوله اوتوماتيك 

الكيرنال مبيديش الارقام عشوائي بيبقي ليها Ranges معينه , الصوره اللي تحت موضحاها

![[Pasted image 20260725150727.png]]

# Configuration Files

## /etc/login.defs

- **Purpose:** System-wide default configurations for user and group creation.
    
- **What it contains:** Default UID/GID ranges for normal vs. system users, default `umask` values for newly created users, password hashing algorithms, and default password expiration controls.
## /etc/passwd

- **Purpose:** The primary database for user account information.
    
- **What it contains:** Username, UID (User ID), primary GID (Group ID), GECOS (comment/full name), absolute path to the user's home directory, and the default login shell (e.g., `/bin/bash`).
    
- **Security:** World-readable (everyone can read it), but only writable by `root`.

```Vim
root:x:0:0:root:/root:/bin/bash
```

![[Pasted image 20260725152352.png]]


## /etc/shadow

- **Purpose:** The secure vault for user authentication.
    
- **What it contains:** Hashed passwords (with salt) and password aging policies (expiration dates, minimum/maximum days between password changes).
    
- **Security:** Highly restricted. Readable and writable **only** by `root`.

بتبقي فيه ال Hashed Passwords و جنبه الجورييزم الهاشينج 
`$6$`--> SHA-512
طب كده لو عملت نفس الباسوردين ل 2 يوزرس هيطلعوا نفس الهاش!!!
	لا لينكس بيضيف للباسورد رقم عشوائي عشوائي قبل الهاشينج (Salt)
	 
![[Pasted image 20260725153327.png]]

## /etc/group

- **Purpose:** The database for group management.
    
- **What it contains:** Group name, GID, and a comma-separated list of secondary members     (users who belong to this group as a supplementary group).
- `group_name:x:GID:secondary_members`

# User and Group Management Commands

## User Management Commands

### Create User  (`useradd`)
```Shell
sudo useradd -md /home/username -s /bin/bash -g primary_group -G sec_group1,sec_group2 username
```
- `-m`:  Create home directory and copy skeleton files and change the ownership
    
- `-d`: Specify custom home directory
    
- `-s`:Specify default shell
    
- `-g`: Specify primary group
    
- `-G`:Specify secondary groups (comma-separated)

Always use `-m` when creating user accounts. This creates the home directory and copies essential configuration files from `/etc/skel` (like `.bashrc`, `.profile`) that are necessary for proper shell environment setup.

في كوماند تاني غير ده اسمه `adduser` جه معمول ب perl script بيسهل الدنيا عكس `useradd` اللي هو اصلا unix-standard

```shell
useradd -D
```
- `-D`: default settings for (Group,home,shell,skel) for the added user
- `/etc/skel`: template files , any new user copies it in his home directory

### Modify User  (`usermod`)

```shell
usermod [options] username
```
- `-aG`: Append Groups , `G` --> for Secondary Groups , `g` --> for primary Group
    
- `-L`: Lock user account
    
- `-U`: Unlock user account
    
- `-s`: Change default shell
	
- `-d /path -m`: Change home directory and move contents

### Delete User  (`userdel`)

```shell
sudo userdel -r username
```

- `-r`: Remove home directory and mail spool

### `id` & `passwd`

```shell
id username
# Displays UID, GID, and group memberships for specified user or current user
```

```shell
sudo passwd username
# Set or change password for specified user
```

### Switching User
The `su` command allows you to switch your current session to another user. When you execute this, you will be prompted for **the target user's password** (unless you are currently `root`, who can switch to any user without a password).

#### The Fatal Flaw: `su username` (Non-Login Shell)

If you simply type `su alice`, the system changes your UID to `alice`, **but** it keeps your original environment variables (like your `$PATH` and your current working directory).

- **The Problem:** You are technically `alice`, but the terminal is still looking for configurations and executables in your old paths. This causes unpredictable script failures. You are essentially Alice wearing your clothes.
#### The Best Practice: `su - username` (Login Shell)

The hyphen `-` (or `-l`, `--login`) is the most critical flag. It tells the Kernel to tear down your current environment and spawn a completely fresh **Login Shell**.

- It changes your current directory to the target user's `$HOME`.
    
- It resets the environment variables and executes the target user's startup scripts (`~/.bashrc`, `~/.zshrc`, or `/etc/profile`).
    
- **Execution:** `su - alice`

#### Privilege Escalation: Switching to Root

If you are performing heavy administrative tasks and need a continuous `root` shell instead of typing `sudo` before every single command, you have to switch to the root user.

Since modern distributions like Ubuntu disable the actual `root` account password for security reasons, you cannot simply type `su - root`. Instead, you must use `sudo` to authorize the switch.

##### Method A: `sudo -i` (The Ultimate Standard)

- **What it does:** Simulates an initial root login. It drops you into `/root`, loads the root's environment profile, and gives you a completely clean, isolated root shell.
    
- **Authentication:** Requires **your** password, not root's password.

##### Method B: `sudo -s` (The Environment Retainer)

- **What it does:** Gives you a root shell (UID 0) but **keeps your current environment variables** and keeps you in your current working directory.
    
- **When to use:** Useful when you are compiling software or executing scripts in your current directory and need root privileges without losing your local shell variables.

##### Method C: `sudo su -` (The Legacy Way)

- **What it does:** It literally runs the `su -` command using `sudo` privileges. It achieves the exact same result as `sudo -i`, but it is slightly less efficient because it spawns an extra process. You will see senior engineers use this constantly purely out of muscle memory.
    

####  Returning to Your Original Identity

When you switch users, the Kernel doesn't destroy your original session; it spawns a "child shell" on top of it.

- **The `exit` command:** To drop the disguise and return to your original user, simply type `exit` or press `Ctrl + D`.
    
- **Engineering Rule:** Never switch users recursively (e.g., logging in as `mohammed` ➔ `su - alice` ➔ `su - samir` ➔ `sudo -i`). This creates a deeply nested and messy process tree. Always `exit` back to your base user before switching to a new identity.

## Group Management Commands
### Create Group  (`groupadd`)
```Shell
sudo groupadd [options] groupname
```
- `-g`:  Specify custom GID

### Modify Group  (`groupmod`)

```shell
groupmod [options] groupname
```

### Delete Group  (`groupdel`)

```shell
sudo groupdel groupname
```

### Group Info  (`groups`)

```shell
groups username
```


# Permissions & Ownership

## Explaining of the `ls -l` output

![[Pasted image 20260725181104.png]]

## Permission Types and Their Meaning

| Permissions | Symbol | on File                         | on Directory                                |
| ----------- | ------ | ------------------------------- | ------------------------------------------- |
| **Read**    | r      | View file contents "cat,vim..." | List directory contents (ls)                |
| **Write**   | w      | Modify or delete file content   | Create, delete, rename files/subdirectories |
| **Execute** | x      | Run file as program/script      | Enter directory (cd) and access contents    |
Without execute permission on a directory, you cannot `cd` into it or access its contents, even if you have read/write permissions!

## Changing Permissions with `chmod`

The `chmod` command changes file and directory permissions using two methods:

### 1) Numeric Mode:
`Bitwise OP` --> [[CPP Basics#Bitwise Operator]]

**Permission Values:**
- r (read) = 4
- w (write) = 2
- x (execute) = 1

**Calculation:** Sum the values for each permission set

![[Pasted image 20260725182014.png]]

**EX:**
```shell
# Give owner full access, group read/write, others read-only
chmod 764 myfile
```

### 2) Symbolic Mode:

- **Who:** u (user/owner), g (group), o (others), a (all)
- **Operation:** + (add), - (remove), = (set exactly)
- **Permissions:** r, w, x

**EX:**

```Shell
# Add Write Permission for user and Group
chmod ug+w
# Set exact permissions (overwrites existing)
chmod u=rwx,g=rw,o=r file.txt
```


## Default Permissions and Umask

بناء علي ايه لما بنعمل فايل جديد او directory الكيرنال بيحطلهم permissions ؟
Base Permission:
dir --> 777 (rwx rwx rwx)
File --> 666 (rw- rw- rw-)
`1`
طب الكلام ده معناه ان الكيرنال بيدي ال base permission للفايل الجديد كده ؟
لا طبعا لو عمل كده السيستم هيبقي مباح لاي حد و هنا يظهر الفلتر بتاعنا `umask`
`umask` --> فلتر بيطرح او بيخفي صلاحيات معينه من البايز
`Actual Permissions = Base Permissions - Umask Value`

```Shell
umask # print your umask value
```

### change umask Value

 Temporary Change
Change for the current session only 
```shell
umask 002 # Set umask = 002 for current session
```

 Permanent umask changes
`/etc/login.defs` and edit umask there (System-wide)
`~/.bashrc` or `~/.zshrc` and edit umask there (Specific User)

## Links and Permissions

### Soft Link:
لو عملت `ls -l` لاي Soft Link هيطلعلك `Lrwxrwxrwx` 777 Full Permission 
الحقيقه ان الصلاحيات دي Fake و الكيرنال بيتجاهلها 
ال Soft Link هو في الاصل ملف بيبقي جواه ال Path بتاع الملف الاصلي , اول لما الكيرنال بيشوف ان ده SL بيسيبه من الصلاحيات بتاعت اللينك نفسه و يبص علي ال Inode بتاع الفايل الصالي و يطبق صلاحيات الفايل الاصلي 
لو عملت `chmod` ل Soft Link هيروح يغير الفايل الاصلي 

### Hard Link:
اي تغيير في ال Hard Link هيسمع مباشر في الفايل نفسه

## Change Ownership

بتستفاد ايه ك User Onwer ---> تقدر تعمل `chmod` للفايل
بتستفاد ايه ك  Onwer ---> العمل الجماعي الامن

The `chown` command allows you to change the owner and/or group owner of files or directories.

- `chown <new_owner> <file/directory>`: Changes only the user owner
- `chown :<new_group> <file/directory>`: Changes only the group owner
- `chown <new_owner>:<new_group> <file/directory>`: Changes both user and group owner

To change ownership of a directory and all its contents recursively, use the `-R` option:

```Shell
chown -R demouser:demouser test_dir
```

## Search For "ACLs& Special Permissions"

هسيب هنا المكان فاضي علشان لما اذاكر ACLs& Special Permissions احطهم هنا

# Privileges
Permission --> هل اقدر افتح ملف و اعدل فيه؟ (علاقه اليوزر بالملفات)
Privilege --> هل اقدر اعمل اكشن يغير حاله السيستم زي اني ارستر خدمه او اغير باسورد يوزر او افرمت هارد ؟ (علاقه اليوزر بالكيرنال)

`sudo` stands for "superuser do" and allows permitted users to execute commands as the superuser (root) or another specified user. This implements Role-Based Access Control (RBAC) for privilege escalation in Linux systems.

Privilege escalation refers to elevating your privileges to perform actions you normally couldn't as a regular user. This follows the "Least Privilege Principle": only grant the minimum permissions necessary for users to perform their required tasks.

The configuration for `sudo` is managed in the `/etc/sudoers` file, which controls permissions and restrictions for sudo access.

**Critical:** Always edit the sudoers file using the `visudo` command, which provides syntax checking to prevent saving a broken sudoers file that could lock you out of root access.

![[Pasted image 20260725191255.png]]

![[WhatsApp Image 2026-07-25 at 7.16.53 PM.jpeg]]