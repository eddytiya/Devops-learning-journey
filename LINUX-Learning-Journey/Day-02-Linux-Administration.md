# Day 2 — Linux Administration

## Overview

Today I practised Linux administration on an Ubuntu AWS EC2 instance.

The session covered:

- System-information commands
- APT package management
- User management
- Group management
- File permissions
- Ownership
- Compression
- Troubleshooting command errors

> Sensitive information such as server addresses, private-key names, and credentials has been removed.

## 1. Connecting to the server

I connected to an Ubuntu EC2 instance using SSH:

```bash
ssh -i "private-key.pem" ubuntu@server-hostname



# Linux for DevOps — Day 2

Day 2 focused on Linux administration, file permissions, user and group management, compression, secure file transfers, and directory synchronization on an AWS EC2 Ubuntu server.

## Topics Covered

- System information commands
- Package management with APT
- User and group administration
- File permissions and ownership
- ZIP, gzip, and tar archives
- Symbolic and hard links
- File transfers using SCP
- Directory synchronization using rsync
- Troubleshooting permission, path, and broken-link errors

---

## SCP: Windows to AWS

SCP commands are executed from Windows CMD or PowerShell.

### Upload one file

```cmd
scp -i "D:\path\to\key.pem" "C:\path\to\notes.txt" ubuntu@SERVER:/home/ubuntu/
```

### Upload into a remote directory

```cmd
scp -i "D:\path\to\key.pem" "C:\path\to\notes.txt" ubuntu@SERVER:/home/ubuntu/transfer/
```

### Upload a file containing spaces

```cmd
scp -i "D:\path\to\key.pem" "C:\path\to\secure copy.txt" ubuntu@SERVER:/home/ubuntu/transfer/
```

### Upload an entire folder

```cmd
scp -r -i "D:\path\to\key.pem" "C:\path\to\linux_for_devops" ubuntu@SERVER:/home/ubuntu/
```

---

## SCP: AWS to Windows

### Download one file

```cmd
scp -i "D:\path\to\key.pem" ubuntu@SERVER:/home/ubuntu/demo.txt "C:\destination"
```

### Download an entire folder

```cmd
scp -r -i "D:\path\to\key.pem" ubuntu@SERVER:/home/ubuntu/linux_for_devops "C:\destination"
```

### Verify the download on Windows

```cmd
dir "C:\destination\linux_for_devops"
```

---

## Archive Method for Complex Directories

Creating an archive is useful when a directory contains permission restrictions, hard links, symbolic links, or many small files.

Run on AWS:

```bash
cd /home/ubuntu
sudo tar -czf linux_for_devops.tar.gz linux_for_devops
sudo chown ubuntu:ubuntu linux_for_devops.tar.gz
ls -lh linux_for_devops.tar.gz
```

Download it from Windows:

```cmd
scp -i "D:\path\to\key.pem" ubuntu@SERVER:/home/ubuntu/linux_for_devops.tar.gz "C:\destination"
```

Extract it on Windows:

```cmd
mkdir "C:\destination\linux_download"
tar -xzf "C:\destination\linux_for_devops.tar.gz" -C "C:\destination\linux_download"
```

---

## Rsync on the AWS Ubuntu Server

In this setup:

- `scp` is used between Windows and AWS.
- `rsync` is used inside the AWS Linux server to synchronize directories.
- WSL is not required for these AWS-side rsync exercises.

### Check rsync

```bash
rsync --version
```

### Install rsync

```bash
sudo apt update
sudo apt install -y rsync
```

### Create a backup directory

```bash
mkdir -p ~/backup_linux_for_devops
```

### Preview synchronization

```bash
rsync -avh --dry-run ~/linux_for_devops/ ~/backup_linux_for_devops/
```

`--dry-run` displays the planned changes without copying anything.

### Synchronize the directories

```bash
rsync -avh --progress ~/linux_for_devops/ ~/backup_linux_for_devops/
```

### Resume partial transfers and display progress

```bash
rsync -avhP ~/linux_for_devops/ ~/backup_linux_for_devops/
```

Options:

- `-a` — archive mode
- `-v` — verbose output
- `-h` — human-readable sizes
- `-P` — progress plus partial-transfer support

### Exclude a broken symbolic link

```bash
rsync -avhP \
  --exclude="cloud/softlink_file" \
  ~/linux_for_devops/ \
  ~/backup_linux_for_devops/
```

### Exclude development directories

```bash
rsync -avhP \
  --exclude=".git/" \
  --exclude="node_modules/" \
  ~/linux_for_devops/ \
  ~/backup_linux_for_devops/
```

### Preview an exact mirror

```bash
rsync -avh \
  --delete \
  --dry-run \
  ~/linux_for_devops/ \
  ~/backup_linux_for_devops/
```

### Create an exact mirror

```bash
rsync -avh \
  --delete \
  ~/linux_for_devops/ \
  ~/backup_linux_for_devops/
```

> Warning: `--delete` removes destination files that do not exist in the source. Always run with `--dry-run` first.

### Verify synchronization

```bash
ls -lah ~/backup_linux_for_devops
```

Compare directory sizes:

```bash
du -sh ~/linux_for_devops ~/backup_linux_for_devops
```

---

## Understanding the Trailing Slash

These commands behave differently:

```bash
rsync -avh source destination/
```

This copies the `source` directory into `destination`.

```bash
rsync -avh source/ destination/
```

This copies only the contents of `source` into `destination`.

The trailing slash is an important part of rsync syntax.

---

## SCP vs Rsync

| Tool | Usage in this setup | Best suited for |
|---|---|---|
| SCP | Windows ↔ AWS Ubuntu | Straightforward file and directory transfers |
| Rsync | Directory ↔ directory inside AWS Ubuntu | Repeated synchronization, changed files, progress, exclusions, and mirrors |

---

## Errors and Lessons

### Incorrect local file path

Error:

```text
scp: stat local "...": No such file or directory
```

Lesson: verify whether the file is located on the normal Desktop or OneDrive Desktop.

### Filename containing spaces

Incorrect:

```bash
cat secure copy.txt
```

Correct:

```bash
cat "secure copy.txt"
```

### Broken symbolic link

A symbolic link fails after its original target is deleted.

Inspect it:

```bash
ls -l softlink_file
readlink softlink_file
```

Remove it if no longer needed:

```bash
rm softlink_file
```

### Permission denied during download

Check ownership and permissions:

```bash
ls -l
```

For a controlled practice directory, ownership can be restored with:

```bash
sudo chown -R ubuntu:ubuntu ~/linux_for_devops
```

### Important SCP syntax corrections

Incorrect:

```text
ubuntu\@SERVER
```

Correct:

```text
ubuntu@SERVER
```

Do not escape underscores in paths or filenames.

---

## Key Learnings

- SCP securely transfers files through SSH.
- The `-r` option is required when copying directories with SCP.
- Quotation marks protect paths containing spaces.
- Rsync efficiently synchronizes only new or changed content.
- `--dry-run` should be used before risky synchronization operations.
- `--delete` can remove destination data and must be used carefully.
- A trailing slash changes rsync’s source-directory behaviour.
- Symbolic links depend on their target paths.
- Hard links continue referencing the underlying data.
- Permissions and ownership affect whether files can be transferred.

---

## Day 2 Progress

- [x] Linux system-information commands
- [x] Package management
- [x] User management
- [x] Group management
- [x] Permissions and ownership
- [x] Compression and extraction
- [x] Symbolic and hard links
- [x] SCP uploads and downloads
- [x] Rsync directory synchronization
- [x] Error diagnosis and correction
