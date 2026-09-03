
# Introduction

![[Pasted image 20260729113532.png]]

# Cheat Sheet

##  Basics

###  Basic Script Structure

```bash
#!/bin/bash
# This script creates a new Linux user 
echo "Please enter user name:" 
read USERNAME 
echo "Congratulations $USERNAME user is created"
```

**Key Elements:**

- #!/bin/bash - Shebang line (required)
- echo - Print messages
- read - Get user input

###  File Permissions

```bash 
# Make script executable 
chmod a+x welcome.sh # Create file and set permissions in one line touch user_add.sh; chmod a+x user_add.sh; vi user_add.sh
```

**Permission Options:**

- a+x - Execute permissions to all users
- ; - Executes commands sequentially
- Different from pipe | which passes output

###  Password Encryption

``` bash
# Problem: useradd -p needs encrypted password, not plain text # Solution: Use openssl to encrypt 
echo "mypassword" | openssl passwd -6 -stdin 
# In script: 
ENCRYPTED_PASSWORD=$(echo "$PASSWORD" | openssl passwd -6 -stdin)
```

**Important:** useradd -p does NOT accept plain text passwords. It expects an encrypted hash.
طريقة الـ `openssl` قوية، بس الكيرنال موفرلك أداة أذكى اسمها `chpasswd`. الأداة دي بتاخد منك (اسم اليوزر : الباسورد العادي)، وهي من جوه بتاخد الباسورد، تولدله Salt، تشفره، وترميه في `/etc/shadow` من غير ما إنت تتدخل في أي تفاصيل معمارية!

تعالى نكتب السكريبت بالطريقة النظيفة والآمنة:
```shell
#!/bin/bash
# This Script for Creating new user and set password cleanly.

# 1. Input variables (with prompts on the same line)
read -p "Please Enter username: " username

# استخدمنا -s عشان الباسورد ميظهرش على الشاشة
read -s -p "Please Enter Password: " pass

# الـ echo دي بس عشان تنزل سطر بعد ما اليوزر يكتب الباسورد المخفي
echo "" 

# 2. Create User (بدون باسورد في الخطوة دي)
sudo useradd -md /home/$username -s /bin/bash "$username"

# 3. The Automation Magic (chpasswd)
# بنبعت اليوزر والباسورد كـ نص واحد للأداة في الباك جراوند
echo "$username:$pass" | sudo chpasswd

# 4. Success Message
echo "User $username Created successfully"
```


## Variables

###  Variables & Input

``` Shell
# Method A: Using environment variable 
USERNAME=$LOGNAME 
echo "Welcome script $USERNAME" 
# Method B: Using command substitution 
USERNAME=$(whoami) 
echo "Welcome script $USERNAME" 
# User input 
echo "Please enter user name:" 
read USERNAME 
# Silent password input 
echo "Please enter password:" 
read -s PASSWORD
```

**Quote Behavior:**  
• "Welcome script $USERNAME" - Variables are substituted  
• 'Welcome script $USERNAME' - Prints literally: $USERNAME

###  Exit Status

``` Shell
# Check exit status 
echo $? 
# Use in conditionals 
if [ $? -eq 0 ] 
then 
	echo "Last command succeeded" 
else 
	echo "Last command failed" 
fi
```

**0** - Success

**1** - General error

**Non-zero** - Error occurred

**For grep command:**

- Exit status 0 = Match found
- Exit status 1 = No match found

##  User Management

###  User Management

``` Shell
# Basic user creation 
useradd -m -d /home/$USERNAME $USERNAME 
# User creation with password 
ENCRYPTED_PASSWORD=$(echo "$PASSWORD" | openssl passwd -6 -stdin) 
useradd -m -d /home/$USERNAME -p "$ENCRYPTED_PASSWORD" $USERNAME 
# User deletion 
userdel -r $USERNAME
```

**useradd flags:**

- -m - Creates home directory
- -d /home/$USERNAME - Specifies home directory path
- -p "$ENCRYPTED_PASSWORD" - Sets encrypted password

**userdel flags:**

- -r - Remove home directory and mail spool

###  User Verification

``` Shell
# Check if user was created 
id newuser 
# Show last entry in passwd file
tail -1 /etc/passwd 
# Search for username in passwd 
grep newuser /etc/passwd 
# Most reliable method (recommended) 
grep -w "^newuser" /etc/passwd
```

**Best Practice:** The combination of -w (word match) and ^ (line start) is the most reliable way to confirm if a user exists.

##  Conditions

###  User Existence Check

``` Shell
# Check if user exists 
grep -w "^$USERNAME" /etc/passwd > /dev/null 2>&1 
# Check the exit status 
if [ $? -eq 0 ]
then 
	echo "User $USERNAME already exists." 
else 
	echo "User $USERNAME does not exist." 
fi
```

**Explanation:**

- grep -w - Exact word match
- "^$USERNAME" - Line starts with username
- > /dev/null 2>&1 - Suppress output
- $? - Exit status of last command

### Basic Conditional Logic
``` Shell
# Basic if statement 
if [ $? -eq 0 ]
then 
	# Commands if true 
else 
	# Commands if false 
fi
```

**Important:** There must be **spaces** around the square brackets [ ] and around comparison operators like -eq.

**Note:** fi closes the if block. In shell scripting, constructs that open (like if) have reversed closing keywords.

### 1. String Operators

Used for string comparison or to verify if a user actually provided input.

- `[ -z "$var" ]`: Returns `True` if the string is **empty** (Zero length).
    
- `[ -n "$var" ]`: Returns `True` if the string is **not empty** (contains any text).
    
- `[ "$var1" == "$var2" ]`: Returns `True` if the strings are exactly identical.
    
- `[ "$var1" != "$var2" ]`: Returns `True` if the strings are different.
    

> **Golden Rule:** Always enclose string variables in double quotes `""` to prevent syntax errors or kernel hangs if the variable is empty or contains spaces.

### 2. Integer Operators

Bash does not recognize `<` and `>` symbols for evaluating integers inside single brackets `[ ]`. You must use the following flags:

- **`-eq`** (Equal): Equal to.
    
- **`-ne`** (Not Equal): Not equal to.
    
- **`-gt`** (Greater Than): Greater than.
    
- **`-ge`** (Greater or Equal): Greater than or equal to.
    
- **`-lt`** (Less Than): Less than.
    
- **`-le`** (Less or Equal): Less than or equal to.
    
- _Example:_ `[ "$age" -ge 18 ]`
    

### 3. File Operators 

These are crucial for automation. They allow you to verify the status of files or directories before executing commands like deletion or moving:

- `[ -e /path/file ]`: Returns `True` if the file or directory **exists**.
    
- `[ -f /path/file ]`: Returns `True` if it is a **regular file** (not a directory).
    
- `[ -d /path/dir ]`: Returns `True` if it is a **directory**.
    
- `[ -r /path/file ]`: Returns `True` if you have **read** permissions.
    
- `[ -w /path/file ]`: Returns `True` if you have **write** permissions.
    
- `[ -x /path/file ]`: Returns `True` if the file is **executable** (can be run as a script).
    

### 4. Logical Operators (AND / OR)

Used to evaluate multiple conditions simultaneously within a single bracket:

- **AND (`-a`)**: Both conditions must be true.
    
    - _Example:_ `[ "$age" -gt 18 -a -n "$username" ]`
        
- **OR (`-o`)**: At least one condition must be true.
    
    - _Example:_ `[ "$user" == "root" -o "$user" == "admin" ]`


##  Advanced Concepts

###  Advanced Foundations

```
# Script execution types
- Interpreted (line by line) - Shell scripts
- Compiled (pre-checked) - C, Go programs

# Script components hierarchy
Shell → Script → Commands → Functions → Conditions → Loops
```

**Interpreted vs Compiled:**

- **Interpreted** - Slower, immediate feedback on errors
- **Compiled** - Faster execution, errors caught early
- **Python** - Can be both interpreted and compiled

###  Advanced Execution Methods

```Shell
# Source vs execute comparison
source script.sh    # Runs in current shell
./script.sh          # Runs in subshell

# Making scripts system commands
export PATH=$PATH:/path/to/scripts

# Permanent PATH modification
echo 'export PATH=$PATH:/home/user/scripts' >> ~/.bashrc
```

##  Script Execution Methods: 

####  source script.sh

Current Shell No Permissions

source script.sh  
. script.sh  
source ~/.bashrc

**Note:** Runs in current shell environment. Variables persist after execution.

####  ./script.sh

Subshell Needs +x

chmod a+x script.sh  
./script.sh

**Note:** Runs in isolated subshell. Requires execute permissions.

####  bash script.sh

Subshell No Permissions

bash script.sh  
bash -n script.sh

**Note:** Alternative execution method. Good for testing and debugging.

###  Advanced I/O Concepts

```shell
# Input types in detail
# Interactive
read -p "Username: " USERNAME

# Silent (passwords)
read -s -p "Password: " PASSWORD

# File parsing with validation
while IFS=',' read -r field1 field2 || [ -n "$field1" ]; do
    [[ -n "$field1" ]] && echo "Processing: $field1"
done < input.csv
```

**Advanced Input Features:**

- read -p - Prompt and read in one line
- read -t 10 - Timeout after 10 seconds
- read -n 1 - Read single character
- IFS - Internal Field Separator for parsing

##  Script Design

###  Script Design Philosophy

```
# Design Process (Advanced)
1. Imagine final output and user experience
2. Create detailed flowchart
3. Write pseudocode
4. Implement incrementally
5. Test each component
6. Integrate and refine

# Automation Decision Matrix
Time to build vs Time saved = ROI
Risk level vs Automation complexity = Priority
```

** Design Golden Rule:** Start with the end in mind. Design the user experience first, then build the technical solution to deliver that experience.

##  Advanced Logic

###  Case Statements

```
# Case statement (replaces long if/elif chains)
case "$CHOICE" in
    1)
        user_add_script
        ;;
    2)
        user_del_script
        ;;
    3)
        list_users_script
        ;;
    [4-9])
        echo "Feature coming soon"
        ;;
    *)
        echo "Invalid choice: $CHOICE"
        ;;
esac
```

**Case Features:**

- **Pattern matching** - [4-9], a|b, *.txt
- **Cleaner syntax** - No nested brackets
- **Better performance** - Optimized for multiple conditions
- **esac** - "case" backwards to close

###  Advanced File Parsing

```Shell
# Extract data from /etc/passwd
# Format: username:x:uid:gid:info:home:shell

# Last 10 users
tail -10 /etc/passwd | cut -d ':' -f 1

# Users with specific shell
grep '/bin/bash$' /etc/passwd | cut -d ':' -f 1

# Users with UID > 1000 (regular users)
awk -F: '$3 >= 1000 {print $1}' /etc/passwd

# Reverse field extraction
cat /etc/passwd | rev | cut -d ':' -f 2 | rev
```

**Parsing Tools:**

- cut - Extract fields by delimiter
- awk - Pattern scanning and processing
- sed - Stream editing
- grep - Pattern matching

##  Functions

###  Function Fundamentals

```Shell
# Function definition methods
# Method 1
function user_add_script {
    echo "Enter username:"
    read USERNAME
    # ... user creation logic
}

# Method 2 (preferred)
user_add_script() {
    echo "Enter username:"
    read USERNAME
    # ... user creation logic
}

# Function calls
user_add_script    # Simple call
```

**Function Benefits:**

- **Reusability** - Call multiple times
- **Modularity** - Easier debugging
- **Organization** - Cleaner main script
- **Maintenance** - Update in one place

###  External Function Sourcing

```
# Directory structure
project/
├── main.sh
└── modules/
    ├── user_add_script.sh
    ├── user_del_script.sh
    └── list_users_script.sh

# External function file (no shebang!)
# File: modules/user_add_script.sh
user_add_script() {
    echo "Enter username:"
    read USERNAME
    # ... function logic
}
```

```
# Main script sourcing
#!/bin/bash
# Enable aliases and set module path
shopt -s expand_aliases
alias include='source'
MODULES_PATH="$(pwd)/modules"

# Source external functions
include "$MODULES_PATH/user_add_script.sh"
include "$MODULES_PATH/user_del_script.sh"
include "$MODULES_PATH/list_users_script.sh"
```

**Best Practices:**

- Remove #!/bin/bash from sourced files
- Use descriptive function names
- Group related functions in modules
- Use relative paths with variables

###  Function Parameters & Return Values

```
# Function with parameters
create_user_with_group() {
    local username="$1"
    local groupname="$2"
    
    if [[ -z "$username" || -z "$groupname" ]]; then
        echo "Usage: create_user_with_group <username> <group>"
        return 1
    fi
    
    groupadd "$groupname" 2>/dev/null
    useradd -m -g "$groupname" "$username"
    return $?
}

# Function call with parameters
create_user_with_group "john" "developers"
```

**Parameter Variables:**

- $1, $2, $3... - Positional parameters
- $# - Number of parameters
- $@ - All parameters as separate words
- local - Local variable scope

##  Interactive Menus

###  Interactive Select Menus

```shell
# select creates automatic numbered menus with loops
PS3="Please select from the menu: "

select CHOICE in "User Add" "User Delete" "List Users" "Exit"; do
    case "$CHOICE" in
        "User Add")
            user_add_script
            ;;
        "User Delete")  
            user_del_script
            ;;
        "List Users")
            list_users_script
            ;;
        "Exit")
            echo "Thank you for using the utility!"
            break
            ;;
        *)
            echo "Invalid option: $REPLY. Please try again."
            ;;
    esac
    echo # Empty line for readability
done
```

**Select Features:**

- PS3 - Custom prompt variable
- $CHOICE - Selected item text
- $REPLY - Selected item number
- break - Exit the select loop


# Bash Scripting: Silent Mode, CLI Utilities, & Man Pages

## 1. Script Execution Modes

When writing shell scripts, you can execute them in different modes based on how they handle input:

- **Interactive Mode:** The script pauses to prompt the user for input (e.g., using `read`).
    
- **Silent Mode (Scripting Mode):** The script runs entirely without user interaction. Inputs are provided via:
    
    - **Constants:** Hardcoded values inside the script (e.g., routine backups).
        
    - **Variables:** Inputs passed dynamically from **Files** (CSV, JSON, txt) or **Arguments** (command-line parameters).
        

## 2. Parsing Data from Files (CSV Scenario)

To create a silent script that adds users based on a CSV file, we must extract data systematically.

### The CSV Structure (`user_list.csv`)

Plaintext

```
┌─────────────────────────────────────┐
│ Header Row → username,group_name    │ ← Skip this line
├─────────────────────────────────────┤
│ Data Row 1 → user1,admin_group      │ ← Process this
│ Data Row 2 → user2,dev              │ ← Process this  
└─────────────────────────────────────┘
```

### Core Commands for Parsing

1. **Filtering Headers (`grep -v`):** Use `grep -v "^username,"` to invert the match and skip the header row.
    
2. **Extracting Fields (`cut`):**
    
    - `cut -d','`: Sets the delimiter to a comma.
        
    - `cut -f1`: Extracts the first column (Username).
        
    - `cut -f2`: Extracts the second column (Group name).
        
3. **Cleaning Hidden Characters (`tr`):** CSV files from Windows often contain hidden carriage returns (`\r`). Use `tr -d '\r'` to delete them so they don't break system commands.
    

## 3. The Complete Silent Script

Here is the robust script (`user_add_silent.sh`) utilizing a `for` loop to parse the CSV silently.

> **Note:** The `for` loop splits on _all_ whitespaces. If your CSV data contains spaces, a `while read` loop is highly recommended for production environments.


```Shell
#!/bin/bash
CSV_FILE="user_list.csv"

# Loop through CSV, excluding header
for list in $(cat "$CSV_FILE" | grep -v "^username,"); do
    
    # Extract data & clean hidden carriage returns
    username=$(echo "$list" | cut -f1 -d',')
    group_name=$(echo "$list" | cut -f2 -d',' | tr -d '\r')

    # 1. Check & Create Group
    grep -q "^$group_name:" /etc/group &> /dev/null
    if [ $? -ne 0 ]; then
        groupadd "$group_name"
    fi

    # 2. Add User & Assign to Group
    useradd -m -d "/home/$username" "$username" -G "$group_name"
    echo "User '$username' added to '$group_name'."
done
```

## 4. Converting Scripts to System Commands

To run your script from anywhere just like `ls` or `cd`, you need to add its directory to your system's `$PATH`.

### Permanent `$PATH` Modification

1. Create a dedicated directory for your custom commands: `mkdir ~/comm`
    
2. Move your script there and make it executable: `chmod +x ~/comm/user_utility`
    
3. Edit your bash configuration: `nano ~/.bashrc`
    
4. Add this line at the bottom: `export PATH=$PATH:/home/youruser/comm`
    
5. Apply changes: `source ~/.bashrc`
    

```Plaintex
Shell Search Order:
1. /usr/local/bin  ─── No user_utility
2. /usr/bin        ─── No user_utility  
3. ~/comm          ─── ✓ Found user_utility!
```

## 5. Handling Arguments & Options

To make the utility professional, it should accept dynamic inputs from the terminal.

### Positional Parameters

Inputs typed after the command are stored in numbered variables:

- `$1` = First argument
    
- `$2` = Second argument

```Plaintext
user_utility Muhammad 38 developer
                 │     │      │
                 ▼     ▼      ▼
                $1    $2     $3
```

### Options (`getopts`)

`getopts` processes single-character flags (like `-h` or `-a`).

- **Syntax:** `while getopts "ha:" opt; do`
    
- `h`: Option requires NO argument.
    
- `a:`: Option REQUIRES an argument (indicated by the colon `:`). The argument is stored in `$OPTARG`.
``` bash
while getopts "ha:" opt; do
    case $opt in
        h)
            show_help
            exit 0
            ;;
        a)
            USERNAME="$OPTARG" 
            echo "Adding user: $USERNAME"
            ;;
        \?) 
            echo "Error: Invalid option" >&2 
            exit 1
            ;;
    esac
done
```

## 6. Modular Architecture

To keep your main script clean, separate the core logic into modular functions and "source" them.

**Directory Structure:**

```
~/comm/
├── user_utility              ◄── Main executable (Handles getopts)
└── modules/
    └── user_add_function.sh  ◄── Function module
```

**Inside `user_utility`:**
```Bash
#!/bin/bash
# Load the external module
source ~/comm/modules/user_add_function.sh

while getopts "a:" opt; do
    case $opt in
        a)
            user_add_function "$OPTARG" # Call function from module
            ;;
    esac
done
```

## 7. Professional Documentation: Man Pages

Creating a manual page registers your script in the Linux `man` system.

### Steps to Create a Man Page:

1. **Category:** Admin commands belong in Section 8 (`man8`).
    
2. **Template:** Copy an existing `.gz` file from `/usr/share/man/man8/`, unzip it (`gunzip`), and rename it to `user_utility.8`.
    
3. **Edit Macros (`nano user_utility.8`):**
    
    - `.TH`: Title Header (e.g., `.TH USER_UTILITY 8 "System Config"`)
        
    - `.SH NAME`: Command name and short description.
        
    - `.SH SYNOPSIS`: Usage syntax (e.g., `.B user_utility [\fB-h\fR]`).
        
    - `.SH DESCRIPTION`: Full explanation.
        
    - `.SH OPTIONS`: List and explain flags.
        
4. **Install:**
    
    - Compress it: `gzip user_utility.8`
        
    - Move it: `sudo cp user_utility.8.gz /usr/share/man/man8/`
        
    - Update DB: `sudo mandb`
        
5. **Test:** `man user_utility`
```Plaintext
Final System Integration:
• Run command:    user_utility -a newuser
• Get help:       user_utility -h
• Read manual:    man user_utility
```