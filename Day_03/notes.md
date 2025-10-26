# Day 03: Linux Folder Structure and File Types

## What is Linux Filesystem Hierarchy?
- It's a tree-like structure where each directory has a specific role in system operation and management.
- Filesystem Hierarchy Standard **(FHS)** organizes all the files and directories under a single root folder `/`.

### Login Prompt.
* `root@ubuntu:/$` OR `root@ubuntu:/#`
- `root` is a UserName who logged in. Multiple users can use the same machine.
- `ubuntu` is a Hostname.
- `/` or `~` - Points to the current dir.

 <img width="259" height="98" alt="image" src="https://github.com/user-attachments/assets/707af81a-7d3a-4099-9eb2-cea66dcf6c10" />

### Key Directories
- **/bin:** Essential user binaries (ls, cp, mv), non-admin, accessible to all.
- **/sbin:** System binaries (administrative commands), admin-only for managing users/groups.
- **/lib, /lib64:** Essential libraries for kernel/system calls.
- **/boot:** Boot loader files (kernel, initrd), used on restarts.
- **/dev:** Device files.
- **/etc:** System configuration files.
- **/proc:** Virtual filesystem for process and kernel info.
- **/sys:** Virtual filesystem for system info.
- **/run:** Runtime variable data.

#### User Data & Home
- **/home:** User home directories (/home/linuxthefinalboss)—personal; /root for root (exception).
- **/root:** Home directory for root user.
 
#### Variable & Temporary Data
- **/var:** Variable data (logs, mail, spool)—logs (/var/log for Apache/HTTPD), libs (/var/lib), cache (/var/cache).
- **/tmp:** Temporary files (writable by all users)—auto-cleaned (like recently deleted photos).
  
#### Additional Software & User Programs
- **/usr:** User programs, libraries, documentation (/usr/bin apps like curl, /usr/lib libs, /usr/local local installs)—/bin shortcut to /usr/bin.
- **/usr/bin, /usr/sbin:** Non-essential user/system binaries.
- **/usr/local:** Locally installed software.
- **/opt:** Optional/additional software (e.g., Java versions) — common for org tools.

#### Mount Points
- **/mnt:** Temporary mount point for filesystems (manual, e.g., NFS).
- **/media:** Removable media (USB, CD-ROM)—auto.
- **/srv:** Data for services (web, ftp)—for sharing.
