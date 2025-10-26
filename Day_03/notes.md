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
- **/sbin:** System binaries (administrative commands), admin-only for managing users/groups.`(reboot, shutdown, mount, umount, mkfs, fdisk, etc...)`
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

## Linux File Types:
- There are `regular files`, `directories`, and `special files`. The special files are further divided into subtypes.
- **Regular Files:** Most common files storing data such as text, images, scripts, or binaries. They are indicated by dash `-` when you run `ls -l` command.
- **Directory Files:** Special files container reference to other files and directories. Indicated by `d` in the permission field.
- **Special Files:** There are subtypes.
  - **Symbolic Link (Symlinks):** Points to another file or directory; it acts as a shortcut. Marked with `l`
  - **Hard Links:** Multiple filenames refer to the same data on the disk. Deleting one doesnt remove the data from the disk until all links are deleted.
  - **Block Device Files:** It represent devices that read/write data in blocks, such as a hard disk.
  - **Character Device Files:** Represent a device that handles data character by character, such as a keyboard.
  - **Named Pipes (FIFO):** For inter-process communication. Marked with `p`
  - **Socket:** For network communication.
 
## Navigating the Filesystem.
- `cd <dir>`: Change directory
- `ls`: List directory content
- `pwd`: Present working directory
- `tree`: Visualize directory structure.

## Commands to Identify File Types
- `ls -l`: Shows file type in first character:
    - `-` Regular file
    - `d` Directory
    - `l` Symbolic link
    - `c` Character device
    - `b` Block device
    - `p` Named pipe (FIFO)
    - `s` Socket
  - `file <filename>`: Describes file content type.
  - `stat <filename>`: Detailed file metadata, including inode.

## Navigating: Paths & Hidden Files
- **Absolute Paths:** Full from / (e.g., /home/linuxthefinalboss/docs.txt).
- **Relative Paths:** From current dir (e.g., ~/docs.txt or ../docs.txt).
- **Hidden:** Dot-prefixed (.bashrc)—`ls -a` reveals; `find . -name ".*"` hunts.

## Symbolic vs Hard Links
- Symbolic links (symlinks) and hard links are two methods that Linux uses to reference the same file data in different ways. Both make files appear in multiple locations, but they differ fundamentally in how they point to data and how the system handles them.

|Feature                   |  Hard Link                                                                                     |  Symbolic (Soft) Link      |                                                       
|--------------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
Definition                |  A direct reference to the same inode as the original file — both share the same data blocks.  |  A shortcut or pointer to the original file’s pathname.                           
Command                   |  ln source_file link_name                                                                      |  ln -s source_file link_name                                                      
Dependency                |  Independent of the original file — the data remains even if the original file is deleted.     |  Dependent on the original file — breaks if the original file is deleted or moved.
Cross-Filesystem Support  |  Cannot span multiple filesystems or partitions.                                               |  Can cross filesystem or partition boundaries.                                    
Links to Directories      |  Not allowed (mostly restricted for safety).                                                   |  Allowed — can point to directories or files.                                     
Inode Number              |  Shares the same inode number as the original file.                                            |  Has a different inode, storing only the path reference.                          
Disk Space Usage          |  Occupies negligible extra space since data blocks are shared.                                 |  Uses a small space for storing the target path.                                    
Performance               |  Slightly faster as it accesses data directly.                                                 |  Slightly slower due to path resolution.                                         

## Question
- 1) Create a symlink and then create a hardlink using the source as the symlink. When the source file is deleted symlink will be broken. What is the result of a hardlink?
  - Ans: Hardlink will be broken too.

