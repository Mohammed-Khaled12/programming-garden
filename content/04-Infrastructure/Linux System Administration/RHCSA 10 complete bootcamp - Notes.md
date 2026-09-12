### 1. Linux File System Hierarchy & Best Practices

- **`/var` (Variable Data):** Used primarily for system logs, databases, and dynamically changing files.
    
    - **Production Best Practice:** Always mount `/var` on a separate partition or dedicated disk. This prevents runaway log files from consuming all available space on the root partition (`/`), which would cause a system crash.
        
- **`/opt` & `/usr/local`:** Used for organizing custom or third-party software.
    
    - Use `/opt` for large, monolithic third-party applications.
        
    - Use `/usr/local` for locally compiled software, custom scripts, and company-specific tools to keep them isolated from files managed by the OS package manager (DNF/RPM).
        

### 2. File Management & Copying (`cp`)

- **Preserving Attributes:** By default, copying a file changes its ownership to the user executing the `cp` command.
    
    - Use `cp -a` (Archive) to copy files/directories while preserving original ownership, permissions, timestamps, and symlinks.
        
    - _Note:_ `cp -p` (Preserve) also maintains attributes, but `-a` is more comprehensive because it automatically implies recursion (`-r`) and ensures special files are copied correctly without modification.
        
- **Preventing Overwrites:** `cp` silently overwrites files with the same name in the destination directory.
    
    - Use `cp -i` (Interactive) to force the system to prompt for confirmation before overwriting existing files.
        
    - _Pro Tip:_ In RHEL, `cp` is typically aliased to `cp -i` by default for the `root` user to prevent catastrophic data loss, but always use `-i` explicitly in scripts or as a regular user.
        

### 3. Text Processing & Log Monitoring

- **`tee` Command:** Reads from standard input (stdin) and writes to both standard output (the screen) and one or more files simultaneously. This is highly useful in pipelines (e.g., writing output to a log file while simultaneously piping it to `wc` to count lines/words, without needing to read the file a second time).
    
- **`tail -f` (Follow):** Used to output appended data as a file grows in real-time. This is the standard tool for live log monitoring (e.g., `tail -f /var/log/secure`).

---
### 4. Bash History Expansion (The Bang `!` Operator)

The `!` (bang) operator allows for rapid re-execution of previously executed commands from the shell history.

- **`!!` (Double Bang):** Re-executes the immediate previous command. Highly useful for privilege escalation when you forget to append `sudo` (e.g., executing `sudo !!` after a "Permission denied" error).
    
- **`!N` (By Index):** Re-executes the command at the specific index number `N` in the history list (e.g., `!105`).
    
- **`!string` (By String):** Re-executes the most recent command that starts with the specified string (e.g., `!sys` will run the last command starting with "sys", like `systemctl`).
    
- **`:p` Modifier (Print Only):** Appends to a history expansion to print the command to the terminal without executing it, allowing for safe verification (e.g., `!sys:p`).
    

### 5. Argument Expansion

- **`!$` (Last Argument):** Grabs the last argument of the immediate previous command. Useful for chaining operations on the same file or directory (e.g., running `mkdir /very/long/path/name` followed directly by `cd !$`).
    

### 6. History Searching

- **Reverse Interactive Search (`CTRL + R`):** Triggers a reverse search through the shell history. Typing a substring will instantly fetch the most recent matching command. Pressing `CTRL + R` repeatedly cycles further back through matching results.
    

### 7. Bypassing Aliases

- **Backslash Escape (`\`):** Prepending a backslash to a command temporarily disables any configured alias for that specific execution, forcing the shell to execute the raw original binary (e.g., using `\cp` to copy without triggering the `cp -i` alias).