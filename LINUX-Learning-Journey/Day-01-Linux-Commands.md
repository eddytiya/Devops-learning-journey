# Day 2 — Linux Commands and SSH

## Overview

Today I practised essential Linux commands on an Ubuntu EC2 instance. The session covered navigation, file management, text processing, system monitoring, process management, links, and SSH.

## Commands practised

### Navigation

- `date` — display the current date and time
- `pwd` — print the current working directory
- `ls` — list visible files and directories
- `ls -l` — display detailed file information
- `ls -a` — include hidden files
- `cd` — change directories
- `clear` — clear the terminal screen

### Files and directories

- `mkdir` — create a directory
- `touch` — create an empty file
- `cp` — copy a file
- `cp -r` — recursively copy a directory
- `mv` — move or rename something
- `rm` — delete a file
- `rmdir` — delete an empty directory
- `rm -r` — recursively delete a directory

### Reading and processing text

- `cat` — display a file
- `zcat` — display a gzip-compressed file
- `head` — display the beginning of a file
- `tail` — display the end of a file
- `tail -f` — follow a changing log file
- `less` and `more` — read files page by page
- `wc` — count lines, words, and bytes
- `cut` — extract byte positions
- `sort` — sort lines
- `diff` — compare files
- `tee` — display and save output
- `vi` — edit text files

### System monitoring

- `df -h` — display filesystem usage
- `du` — display directory usage
- `ps` — list processes
- `top` — monitor processes interactively
- `free -h` — display memory usage
- `vmstat -a` — display system statistics

### Process management

- `kill PID` — request process termination
- `kill -9 PID` — forcefully terminate a process
- `nohup` — keep a command independent of terminal hangups

### Links

- `ln` — create a hard link
- `ln -s` — create a symbolic link
- `ls -ltr` — inspect links and modification times

### Remote access

- `ssh -i "key.pem" user@host` — connect to a remote Linux machine

## Key lessons

1. Absolute paths begin with `/`; relative paths depend on the current directory.
2. Linux filenames are case-sensitive.
3. Commands and arguments require spaces.
4. Symbolic links store paths and break when their target disappears.
5. Hard links refer to the same underlying file data.
6. `rm -r` should be used carefully.
7. Normal `kill` should be attempted before `kill -9`.
8. Private SSH keys must never be committed to GitHub.

## Progress

- [x] Connected to an EC2 instance through SSH
- [x] Navigated the Linux filesystem
- [x] Created, copied, moved, and removed files
- [x] Practised text-processing commands
- [x] Monitored disk, memory, and processes
- [x] Created and tested hard and symbolic links
