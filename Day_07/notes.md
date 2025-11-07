# Day 07: Users, Groups & Permissions

## Sample Environment Setup
For exercises, we'll use a test setup. Run these commands to prepare (as root or with sudo):

```bash
# Create test users and groups (if not existing)
sudo useradd -m -s /bin/bash testuser1
sudo useradd -m -s /bin/bash testuser2
sudo groupadd testgroup

# Create a test file and directory
touch testfile.txt
mkdir testdir
echo "Sample content" > testfile.txt

# Enable ACLs on filesystem if needed (e.g., for ext4)
sudo mount -o remount,acl /

# View initial state
ls -la testfile.txt testdir
id testuser1
getent group testgroup
```

This creates users `testuser1` and `testuser2`, group `testgroup`, and sample files for permission demos. ACL support requires filesystem mount option (acl).

## Notes

- **Why User & Group Management Matters?**
  - Controls access to resources, enforces least privilege, and prevents unauthorized actions in multi-user environments.
  - Critical for DevOps/SRE: Automate provisioning, secure shared systems, and audit compliance.

### Top 8 User & Permission Management Commands

| Command | Simple Description | Examples |
|---------|--------------------|----------|
| **USERADD**<br>`$ sudo useradd -m user` | Creates a new user account (non-interactive). | 1. Basic user with home: `sudo useradd -m testuser`<br>2. With shell and groups: `sudo useradd -m -s /bin/bash -G sudo testuser`<br>3. Custom UID: `sudo useradd -u 2000 -m testuser` |
| **ADDUSER**<br>`$ sudo adduser user` | Creates a new user interactively (prompts for details). | 1. Full interactive: `sudo adduser testuser` (sets pw, home, etc.)<br>2. Skip prompts: `sudo adduser --disabled-password testuser`<br>3. System user: `sudo adduser --system --group myservice` |
| **PASSWD**<br>`$ sudo passwd user` | Sets or changes a user's password. | 1. Set pw: `sudo passwd testuser`<br>2. Expire pw: `sudo passwd -e testuser`<br>3. Lock/unlock: `sudo passwd -l testuser` (lock) |
| **USERMOD**<br>`$ sudo usermod -aG group user` | Modifies an existing user's attributes. | 1. Add to group: `sudo usermod -aG docker testuser`<br>2. Change shell: `sudo usermod -s /bin/zsh testuser`<br>3. Lock account: `sudo usermod -L testuser` |
| **GROUPADD**<br>`$ sudo groupadd group` | Creates a new group. | 1. Basic group: `sudo groupadd testgroup`<br>2. With GID: `sudo groupadd -g 2000 testgroup`<br>3. System group: `sudo groupadd --system syslog` |
| **CHOWN**<br>`$ sudo chown user:group file` | Changes file ownership (user and/or group). | 1. Change user/group: `sudo chown testuser:testgroup file.txt`<br>2. Recursive: `sudo chown -R testuser: /home/testuser`<br>3. User only: `sudo chown testuser file.txt` |
| **CHMOD**<br>`$ chmod 755 file` | Changes file permissions (numeric or symbolic). | 1. Numeric: `chmod 644 file.txt` (rw-r--r--)<br>2. Symbolic: `chmod u+x,g-w file.txt`<br>3. Recursive: `chmod -R 755 /dir` |
| **SETFACL**<br>`$ setfacl -m u:user:rwx file` | Sets Access Control Lists for fine-grained permissions. | 1. Add user ACL: `setfacl -m u:testuser2:r file.txt`<br>2. Default for dir: `setfacl -m d:g:testgroup:rwx /dir`<br>3. Remove: `setfacl -x u:testuser2 file.txt` |

---

### User Concepts & Management
Users are identified by UID (User ID); groups by GID (Group ID). System users (UID < 1000) run services; regular users (UID >= 1000) are for humans. 

- **Key Files:**
  - **/etc/passwd:** Stores user account info (non-sensitive). World-readable for compatibility (e.g., for mailers). Each line is colon-separated fields. Format: `username:password:UID:GID:GECOS:home:shell`.
    - **Fields:** 
      - `username`: Unique login name.
      - `password`: 'x' (shadowed; actual hash in /etc/shadow).
      - `UID`: Numeric user ID (0=root, <1000=system).
      - `GID`: Primary group ID.
      - `GECOS`: Comment/info (e.g., full name).
      - `home`: Home directory path.
      - `shell`: Default shell (e.g., /bin/bash).
    - **Example:** `testuser1:x:1001:1001:Test User:/home/testuser1:/bin/bash`
    - **DevOps Note:** Parse with `awk -F: '{print $1, $3, $6}' /etc/passwd` to list users, UIDs, homes.
  - **/etc/shadow:** Secure storage for passwords and aging (sensitive). Root-only readable (mode 000640). Each line: colon-separated. Format: `username:encrypted_passwd:last_change:min:max:warn:inactive:expire:reserved`.
    - **Fields:**
      - `username`: Matches /etc/passwd.
      - `encrypted_passwd`: Hashed password ('!' or '*' = locked/disabled; starts with $ for algorithms like SHA-512).
      - `last_change`: Days since Jan 1, 1970, when pw changed.
      - `min`: Min days between changes (0=no min).
      - `max`: Max days before expiration.
      - `warn`: Days to warn before expiration.
      - `inactive`: Days inactive after expiration before lock.
      - `expire`: Days since epoch when account expires.
      - `reserved`: Unused (future).
    - **Example:** `testuser1:$6$salt$hash...:19500:0:99999:7:::`
    - **DevOps Note:** Audit expired pwds: `sudo awk -F: '$5==0 || $3 < (mktime("now")/86400 - 30)' /etc/shadow` (simplified check).
  - **/etc/sudoers:** Controls who can use sudo and what they can run. Edit only with visudo (checks syntax, prevents lockouts). Format: `who where=(as_whom) tags command`. Lines can include users, groups (%group), hosts, runas (user/group), tags (NOPASSWD), commands (ALL or paths).
    - **Key Elements:**
      - `who`: User (%group for groups).
      - `where`: Host (ALL=any).
      - `=(as_whom)`: Run as user/group (ALL=any).
      - `tags`: NOPASSWD (no pw prompt), PASSWD (require pw).
      - `command`: ALL (any cmd), /path/to/cmd, NOEXEC (no shell exec).
    - **Examples:**
      - `root ALL=(ALL:ALL) ALL`: Root can sudo anything as anyone (default).
      - `%sudo ALL=(ALL:ALL) ALL`: Sudo group members full access.
      - `devops1 ALL=(root) NOPASSWD: /usr/bin/docker *`: devops1 runs docker as root without pw, any args.
      - `testuser1 localhost=/bin/systemctl restart nginx`: Restricted: only restart nginx on local host.
    - **DevOps Note:** For teams, use groups: `%devops ALL=(ALL) NOPASSWD: /usr/bin/ansible-playbook`. Include files (/etc/sudoers.d/) for modular configs: `echo "user ALL=(ALL) ALL" | sudo tee /etc/sudoers.d/user`.

```bash
# View files (as root for shadow)
sudo cat /etc/passwd | head -5                   # Sample users
sudo cat /etc/shadow | grep testuser1             # Password hash/info
sudo visudo -c                                    # Validate sudoers
sudo visudo                                       # Safe edit sudoers
```

User data: `/etc/passwd` (info), `/etc/shadow` (hashed passwords, root-only).

```bash
# Create user interactively (recommended for beginners)
sudo adduser testuser3

# Create user non-interactively
sudo useradd -m -s /bin/bash -G sudo testuser4  # -m: home dir, -s: shell, -G: secondary groups

# Set/change password
sudo passwd testuser1

# Modify user
sudo usermod -aG docker testuser1                # -aG: append to group
sudo usermod -L testuser2                        # Lock account
sudo usermod -U testuser2                        # Unlock
sudo usermod -d /new/home -s /bin/zsh testuser1  # Change home/shell

# Delete user
sudo userdel -r testuser3                        # -r: remove home dir

# View user info
id testuser1                                     # UID, GID, groups
groups testuser1                                 # Groups only
getent passwd testuser1                          # Full entry
awk -F: '$1=="testuser1"' /etc/passwd            # Query with awk
```

---

### Group Concepts & Management
Groups simplify permissions for multiple users. Primary group (set at creation); secondary for extras. Data in `/etc/group`.

```bash
# Create/modify group
sudo groupadd newgroup
sudo groupmod -n renamedgroup newgroup           # Rename

# Manage members
sudo gpasswd -a testuser1 testgroup              # Add user
sudo gpasswd -d testuser2 testgroup              # Remove user
sudo gpasswd -M user1,user2 testgroup            # Set members list

# Delete group (must be empty)
sudo groupdel testgroup

# View groups
getent group testgroup                           # Full entry
grep '^testgroup' /etc/group                     # From file
```

---

### File Ownership & Permissions
Every file has an owner (user), group, and permissions that control access for the owner, group, and others (everyone else). Permissions are represented as `rwxrwxrwx` (or `-` for none), where the first three chars are for owner (u), next for group (g), last for others (o). Each position: r=read (view content), w=write (modify), x=execute (run as program or enter dir).

Permissions can be set using two modes:
- **Numeric (Octal) Mode:** A 3-digit octal number (000-777) where each digit represents perms for owner/group/other. Calculate by adding: r=4, w=2, x=1 (e.g., rwx=7, rw-=6, r-x=5). Leading 4/2/1 for special perms (SUID/SGID/Sticky). Quick for scripts/automation.
- **Symbolic Mode:** Specify target (u=owner, g=group, o=others, a=all), operator (+=add, -=remove, ==set exactly), and perms (r/w/x). E.g., `a+rx` adds read/exec for all. Flexible for incremental changes; recursive with `-R`.

Use `ls -l` to view: `-rwxr-xr-x 1 owner group 1024 Oct 8 10:00 file.txt` (first `-` = regular file, then perms, owner, group, size, date, name). A '+' at end (e.g., `rw-r--r--+`) indicates ACLs are present.

#### Numeric Mode Combinations
All possible combinations for a single triad (owner/group/other) are based on binary bits (3 bits per category: rwx). Below is a table of all 8 (000-777) for one category (e.g., owner); apply the same logic to group/other for the full 3-digit mode.

| Octal | Symbolic Equivalent | Meaning | Use Case |
|-------|---------------------|---------|----------|
| 0 (---) | --- | No permissions | Block access entirely (rare for owner) |
| 1 (--x) | --x | Execute only | Scripts/binaries (no read needed) |
| 2 (-w-) | -w- | Write only | Temp files (dangerous without read) |
| 3 (-wx) | -wx | Write + execute | Rarely used standalone |
| 4 (r--) | r-- | Read only | View-only files (e.g., configs) |
| 5 (r-x) | r-x | Read + execute | Shared scripts/binaries |
| 6 (rw-) | rw- | Read + write | Editable files (no exec) |
| 7 (rwx) | rwx | Full access | Owner/executables |

**Full Mode Examples:** Combine for 3 digits (owner-group-other). E.g., 644 = owner rw- (6) + group r-- (4) + other r-- (4) = `rw-r--r--` (common for docs).

| Full Octal | Perm String | Owner | Group | Other | Common Use |
|------------|-------------|-------|-------|-------|------------|
| 000 | --------- | --- | --- | --- | No access (locked files) |
| 400 | r-------- | r-- | --- | --- | Owner read-only (private) |
| 600 | rw------- | rw- | --- | --- | Owner read/write (secrets) |
| 644 | rw-r--r-- | rw- | r-- | r-- | Standard files (readable by all) |
| 700 | rwx------ | rwx | --- | --- | Owner full (personal dirs) |
| 755 | rwxr-xr-x | rwx | r-x | r-x | Executables/dirs (shared run) |
| 775 | rwxrwxr-x | rwx | rwx | r-x | Group collaboration (team dirs) |
| 777 | rwxrwxrwx | rwx | rwx | rwx | World-writable (avoid; e.g., /tmp with sticky) |

**Note:** For special perms, prefix: 4755 (SUID + 755), 2755 (SGID + 755), 1755 (Sticky + 755).

#### Symbolic Mode Examples
| Command | Effect | Result Perms (from 644 base) | Notes |
|---------|--------|------------------------------|-------|
| `chmod u+x file` | Add execute to owner | rwxr--r-- | Makes executable |
| `chmod g-w file` | Remove write from group | rw-r--r-- | Restrict group edits |
| `chmod o= file` | Set others to none | rw-r----- | Block public access |
| `chmod a+rx file` | Add read/exec to all | rwxr-xr-x | Broad sharing |
| `chmod -R g+w dir` | Recursively add group write | Applies to dir & contents | For team folders |

| Command | Purpose | Example / Notes |
|---------|---------|-----------------|
| `chown user:group file` | Change owner and/or group | `sudo chown testuser1:testgroup testfile.txt` (full change); use `:group` for group only |
| `chgrp group file` | Change group only | `sudo chgrp testgroup testdir` (shorter than chown) |
| `chmod numeric file` | Set permissions numerically | `chmod 755 testfile.txt` (rwxr-xr-x: owner full, others read/exec); common: 644 (rw-r--r-- for files), 755 (for dirs/scripts) |
| `chmod symbolic file` | Modify permissions symbolically | `chmod u+x,g-w,o-r testfile.txt` (+ add, - remove, = exact set; u=owner, g=group, o=others, a=all); recursive: `-R` |
| `ls -l file` | View ownership & permissions | Shows `drwxr-xr-x` (d=dir); pipe to `grep` for audits |
| `stat file` | Detailed metadata | Includes exact perms (e.g., 0755), times, inode; useful for forensics |

#### Access Control Lists (ACLs)
ACLs extend basic permissions by allowing specific users/groups beyond owner/group/other. Useful for DevOps: granular access in shared repos or configs without changing ownership. Requires filesystem support (e.g., ext4 with `acl` mount option; check `tune2fs -l /dev/sda1 | grep acl`).

- **Basic vs. ACL:** Basic = 3 slots (u/g/o); ACL = unlimited entries. ACLs shown as `+` in `ls -l`. Mask limits effective group/other ACL perms.
- **Types:** Access ACLs (file access); Default ACLs (inherit to new files in dir).
- **Commands:** `getfacl` views; `setfacl` modifies. Install if needed: `apt install acl`.

| Command | Purpose | Example / Notes |
|---------|---------|-----------------|
| `getfacl file` | View ACL entries | `getfacl testfile.txt` (shows owner, group, mask, other, +entries like `user::rw-`, `user:testuser2:r--`) |
| `setfacl -m entry file` | Modify/add ACL | `setfacl -m u:testuser2:rwx testfile.txt` (add rwx for testuser2); `m:g:team:r--` for group; `d:u:testuser1:rwx testdir` for default |
| `setfacl -x entry file` | Remove ACL entry | `setfacl -x u:testuser2 testfile.txt` (remove testuser2 entry) |
| `setfacl -b file` | Remove all ACLs | `setfacl -b testfile.txt` (back to basic perms) |
| `setfacl -R -m entry dir` | Recursive modify | Applies to dir & contents; use `-d` for default only |
| `setfacl --set-file=ref file` | Copy ACLs from ref | `setfacl --set-file=template.txt testfile.txt` (clone ACLs) |

**DevOps Note:** For CI/CD shares: `setfacl -R -m g:devops:rwx /shared/repo` (team read/write). Audit: `getfacl /shared/* | grep user:`. Conflicts: Effective perms = min of basic + ACL + mask.

---

### Special Permissions
Special permissions override standard ones for security/collaboration. Set like regular (symbolic: `u+s` for SUID) or numeric (add to octal: 4xxx for SUID). View in `ls -l`: uppercase S/T if no x bit (e.g., `rws` = SUID with x).

| Permission | Purpose | Example / Notes |
|------------|---------|-----------------|
| **SUID (Set User ID)** | Executes as file owner (not caller), even if no perms. Risky: potential escalation. Numeric: 4xxx (e.g., 4755). Symbolic: `u+s`. | `chmod u+s testfile.txt` (ls: `-rwsr-xr-x`); Real: `/usr/bin/passwd` (runs as root to edit shadow) |
| **SGID (Set Group ID)** | On files: runs as group owner. On dirs: new files inherit dir's group (for team shares). Numeric: 2xxx (e.g., 2755). Symbolic: `g+s`. | `chmod g+s testdir` (ls: `drwxr-sr-x`); Useful for collaborative dirs like `/var/mail` |
| **Sticky Bit** | On dirs: only owner/root can delete files (others can't rm even if w perm). Numeric: 1xxx (e.g., 1755). Symbolic: `+t`. | `chmod +t testdir` (ls: `drwxr-xr-t`); Real: `/tmp` (prevents cross-user deletion) |

```bash
# Set special perms
chmod u+s testfile.txt                           # SUID: run as owner
chmod g+s testdir                                # SGID: inherit group
chmod +t testdir                                 # Sticky: restrict delete in dir

# Numeric: 4755 for SUID+755, 2755 for SGID+755, 1755 for Sticky+755
chmod 4755 testfile.txt

# View (s/t appear in ls -l)
ls -la testfile.txt testdir

# Examples:
# SUID: /usr/bin/passwd (users change own password as root)
# SGID: /usr/bin/wall (broadcast as group)
# Sticky: /tmp (users can't delete others' files)
```

---

### Account Security & Switching
- Lock: `usermod -L user` or `passwd -l user` (blocks login).
- Policies: Use `chage -M 90 user` (max age), `/etc/login.defs` for defaults.
- Switching: `su - user` (full session), `sudo -u user cmd` (single cmd). Controlled by /etc/sudoers.

```bash
# Lock/unlock
sudo usermod -L testuser1
sudo usermod -U testuser1

# Enforce policy
sudo chage -l testuser1                          # View policy
sudo chage -M 30 -m 7 testuser1                  # Max 30 days, min 7

# Switch
sudo -u testuser1 whoami                         # Run as user
su - testuser1                                   # Switch session
```

---
