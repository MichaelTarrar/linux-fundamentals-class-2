# Linux Fundamentals: Class 2 (10 Practical Tasks)

Assignment for Class 2 (19 Nov 2026) of the IT & Japanese Language program by **Plus W** and **Pak-Japan Centre**, Lahore batch.

All 10 practical tasks from the Class 2 lab document are complete. Every command and its real terminal output is in this repo, with the checkpoint answer for each task below.

> **AI assistance:** these labs were completed with the help of Claude, an AI assistant. AI tools are allowed in our program. The output files are the unedited terminal output from the lab machine, apart from colour codes and progress-bar redraws being removed.

## Lab environment

| Item | Value |
| --- | --- |
| Operating system | Ubuntu 24.04.5 LTS (Noble Numbat), based on Debian |
| Kernel | 6.18.44, x86_64 |
| Hardware | 2 vCPUs (Intel Xeon @ 2.10 GHz), 7.8 GiB RAM |
| User | `ubuntu` with sudo rights |

The lab document is written for Ubuntu 22.04 on EC2 and also allows "any Ubuntu virtual machine". These labs ran on an Ubuntu 24.04 virtual machine, so versions, sizes, dates and IDs differ from the document's examples.

## Files in this repo

| File | What it is |
| --- | --- |
| `class2_commands.txt` | Every command run in the 10 tasks, in order (299 commands) |
| `lab-output/task-01-...txt` to `task-10-...txt` | The full terminal session for each task: each command with its output |

## Checkpoint answers

### Task 1: Linux system, distribution and architecture
- **Distribution:** Ubuntu 24.04.5 LTS. `ID_LIKE=debian` shows Ubuntu is built on Debian.
- **Kernel:** `6.18.44` (`uname -r`).
- **Evidence for each architecture layer:**

| Layer | Evidence |
| --- | --- |
| User applications | `/usr/bin/nano` |
| System utilities | `/usr/bin/ls` |
| Shell | `/bin/bash` (GNU bash 5.2.21) |
| System libraries | `libc.so.6` (from `ldd /bin/ls`) |
| Kernel | `6.18.44` |
| Hardware | 2 CPUs, 7.8 GiB RAM, 256 GB disk (`lscpu`, `free -h`, `lsblk`) |

- Only `apt` exists (`which apt dnf yum`), because Ubuntu is a Debian-family distribution. RHEL and Fedora use `dnf`.

### Task 2: File system hierarchy
- **Read logs:** `/var/log`
- **Edit a configuration file:** `/etc`
- **Find my own files:** `/home/ubuntu` (my `$HOME`)
- Linux has one tree starting at `/`, with no drive letters. `/tmp` shows `drwxrwxrwt`: the final `t` (sticky bit) means anyone can create files there, but no one can delete other users' files.

### Task 3: Terminal, shell, CLI and command structure
- **Terminal:** the window or device I type in (`tty` → `/dev/pts/1`).
- **Shell:** the program inside it that reads and runs my commands (`echo $0` → `-bash`).
- **CLI:** the text-based way of working, typing commands instead of clicking.
- In `ls -l /home`: **command** = `ls`, **option** = `-l` (detailed listing), **argument** = `/home` (the target).
- Commands are case-sensitive: `LS` gave `-bash: LS: command not found`.

### Task 4: CLI navigation and file paths
- **Absolute path** from anywhere: `cd /var/log`
- **Relative path** from home: `cd ../../var/log` (`pwd` → `/var/log`)
- **`ls` options:**
  - `ls` shows names only.
  - `-l` adds permissions, owner, size and date.
  - `-a` adds hidden files (names starting with a dot).
  - `-lh` shows sizes as K, M or G.
  - `-la` combines the detailed listing and hidden files.
- Special symbols: `/` root, `.` current, `..` parent, `~` home, `-` previous directory.

### Task 5: Create, copy, move, rename and delete files
- `ls ~/linux_lab` shows `backup  file1.txt  projects  renamed.txt`.
- `ls backup` shows `file1.txt  file1_copy.txt`. Both match the checkpoint.
- `rmdir` refused a non-empty directory. `rm` needed `-r` for a directory. `rm -i` asked for confirmation before deleting.

### Task 6: View, edit and search files
- Opened `logfile.txt` with `less` and quit with `q`. Used `head -n 5` and `tail -n 3` for the start and end of the file.
- `tail -f /var/log/syslog` showed `Test message from Class 2` the moment `logger` wrote it.
- **Nano:** saved `welcome.txt` (3 lines) with Ctrl + O and exited with Ctrl + X.
- **Vim:** saved with `:w`, quit with `:q`, and discarded the word "temporary" with `:q!`.
- **find vs locate:** `locate brandnew.txt` found nothing until `sudo updatedb` refreshed its database. Then it showed `/home/ubuntu/brandnew.txt`.
  - Use `find` when you need up-to-date results: it searches the disk directly.
  - Use `locate` for fast searches when a slightly old database is fine.

### Task 7: Account types, user files, sudo and su
- **/etc/shadow is restricted** (`-rw-r----- root shadow`) because it stores password hashes. `cat /etc/shadow` gave `Permission denied`, while `sudo head` worked.
- **www-data cannot log in** because its shell is `/usr/sbin/nologin`. `sudo su - www-data` replied `This account is currently not available.` It is a system user that only runs a service.
- **sudo vs su:**
  - Use `sudo` to run one admin command at a time.
  - `su -` switches to another account. As root it failed (`Authentication failure`) because Ubuntu locks the root password, so use `sudo su -` or `sudo -i` instead.

### Task 8: Create, modify, secure and delete users
- `getent passwd hamza` shows `/bin/bash`, changed from `/bin/sh` with `usermod -s`.
- `sudo chage -l hamza` shows a maximum of `90` days, so the password expires on Feb 19, 2027.
- `getent passwd sara` prints nothing, because `userdel -r` removed the account and its home directory.
- Locking with `passwd -l` shows status `L`, and `su - hamza` then failed. Unlocking with `passwd -u` shows `P` again.

### Task 9: User and group management
- `getent group developers` shows `developers:x:1003:hamza,ubuntu`.
- `groups student1` shows `student1 : student1`. Both match the checkpoint.
- **Why `-aG` matters:** `usermod -G developers student1` (without `-a`) removed student1 from the `users` group. `-a` adds a group without removing the existing ones.
- New group membership only applies after logging in again: `groups` didn't show `developers` until a new login.

### Task 10: Permissions, chmod, chown and chgrp
- `rwxr-xr-x` = 7 5 5 = **755**: owner 4+2+1, group 4+0+1, others 4+0+1. Reversed, 755 → `rwxr-xr-x`.
- **Why `./script.sh` failed:** it was `-rw-rw-r--` (664) with no execute bit, so `-bash: ./script.sh: Permission denied`. After `chmod u+x` it printed `Script is running`.
- **Why student1 can't write to /srv/project:** the directory is `drwxrwxr-x root developers` (775). Only the owner and the `developers` group can write. student1 was removed from developers in Task 9, so `touch` gave `Permission denied`, while ubuntu and hamza (both members) succeeded.

## Differences from the lab document

- **Ubuntu 24.04 vs 22.04:**
  - `adduser` prints `info:` lines and also adds new users to the `users` group.
  - `useradd -m` creates home folders as `750`, so `sudo ls -A /home/hamza` was needed to see the starter files.
- **Editors and pagers:** nano, vim and less were used in a real terminal. Their full-screen display can't be saved as text, so a `#` comment line in the logs says what was typed.
- **Step 6.5 (two terminals):** `tail -f` ran in the background of the same session while `logger` wrote the message.
- **Step 9.7 (log out and back in):** a fresh login was shown with `sudo su - ubuntu -c groups`.
- **Passwords:** passwords typed at prompts don't appear anywhere, because the terminal doesn't display them.
- **Command list:** `class2_commands.txt` lists all 299 commands, more than the 100 the document asks for.
