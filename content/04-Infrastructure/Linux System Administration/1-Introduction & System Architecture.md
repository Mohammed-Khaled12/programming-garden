
# OS Architecture 

![[Pasted image 20260624104240.png]]

## Hardware
Actual HW that will perform Tasks 
CPU,RAM,GPU.... (understands 0's and 1's only!!)

## Kernel
The heart of the OS that actually executes the instruction on the Hardware.
is a Software residing in memory that tells the CPU where to look for it's next task

## UI
**GUI (Graphical User Interface):** You click icons (User friendly, but limited).
**CLI:** You type commands (Harder, but gives you **Full** control).
**The Shell:** The program (like `bash` or `zsh`) that interprets your text commands and sends them to the OS Kernel.

## utilities
Apps and Programs


# Command
## Command Structure

```
command [options] [arguments]
```
- **Command**: Action to perform (`ls`, `cp`, `mv`)
- **Options**: Modify behavior (prefix: `-` or `+`)
- **Arguments**: Targets to operate on
- **Separator**: Spaces between components

## Command Type

 ***Built-in vs External:***
- **Built-in**: Part of shell (`cd`, `echo`, `type`)
- **External**: Separate files (`ls`, `cp`, `mv`)

```
type command_name    # Shows if built-in or external
type ls             # External command
type echo           # May show built-in
```


External Commands could be found in variable names `PATH` , `whereis` command tells you the full path for a command 
```
echo $PATH          # Display current PATH
whereis ls          # Find command location
/usr/bin/ls         # Run with full path
```

The PATH variable contains a colon-separated list of directory paths where the system searches for executable commands:

![[Pasted image 20260628141348.png]]