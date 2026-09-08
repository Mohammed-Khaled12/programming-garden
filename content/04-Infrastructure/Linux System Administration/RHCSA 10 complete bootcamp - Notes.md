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