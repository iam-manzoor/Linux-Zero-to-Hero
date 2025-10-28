# Service Management in Linux with Systemd
- Service management in Linux is predominantly handled by `systemd` in modern distributions, providing powerful and flexible control over system and application services using unit files and the systemctl command.
- Managing services with systemd is mainly about using the `systemctl` command. Whether you need to start, stop, restart, or check on a service, systemctl does the job.
- Unit files are stored in locations such as `/etc/systemd/system/,` `/usr/lib/systemd/system/,` or `/run/systemd/system/` with administrator-created/customized files typically in `/etc/systemd/system/`.

- **Service Management with systemd:**
  ```bash
  # Service control
  systemctl status <service>     # Show service status
  systemctl start <service>      # Start a service
  systemctl stop <service>       # Stop a service
  systemctl restart <service>    # Restart a service
  systemctl reload <service>     # Reload config without restart
  
  # Boot management
  systemctl enable <service>     # Enable service at boot
  systemctl disable <service>    # Disable service at boot
  systemctl is-enabled <service> # Check if enabled
  
  # Information
  systemctl list-units --type=service        # List all services
  systemctl list-units --state=failed        # List failed services
  systemctl list-unit-files --type=service   # List all service files
  
  # Logs
  journalctl -u <service>        # View logs for service
  journalctl -u <service> -f     # Follow logs in real-time
  journalctl -b                  # Boot logs
  ```

## Structure and Sections of a systemd Unit File
- Systemd unit files are divided into sections denoted by square brackets [] with section names that are case-sensitive. The key sections are:

  - **[Unit]:** This section defines metadata about the unit, like a human-readable description, and it configures dependencies and ordering relationships with other units using directives like `After=,` `Requires=,` or `Wants=.`​
  - **[Service]:** Present in service units (.service files), this section defines how to start and manage the service. Directives here include:
    - ExecStart = for the command to launch the service
    - ExecStop = for stopping the service
    - Restart = specifying restart behavior on failure
    - User= and Group= to run the service under a specific system user/group.​
  - **[Install]:** This optional section defines how the unit should be enabled or disabled, specifying the target(s) the unit should be linked to when enabled (e.g., WantedBy=multi-user.target). This controls whether the unit starts automatically on boot.

### Sample Redis unit file
```bash
[Unit]
Description=Redis In-Memory Data Store

[Service]
User=redis
Group=redis
ExecStart=/usr/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/bin/redis-cli shutdown

[Install]
WantedBy=multi-user.target
```

### Init vs systemd
| Feature                   |  Init (SysVinit)                |  systemd                                               |
|--------------------------|---------------------------------|--------------------------------------------------------|
|Boot Startup              |  Sequential scripts             |  Parallel startup with dependency handling             |
|Service Configuration     |  Shell scripts (/etc/init.d/)   |  Unit files (.service,.socket, etc.)                   |
|Dependency Handling       |  Manual                         |  Automatic usingRequires=,After=                       |
|Service Management Tools  |  Minimal (servicecommand)       |  Advanced (systemctl,journalctl)                       |
|Logging                   |  Separate syslog                |  Integrated journaling system                          |
|Features                  |  Basic                          |  Advanced (timers, resource control, socket activation)|
|Resource Usage            |  Lightweight                    |  More resource-intensive                               |
|Adoption                  |  Older systems, minimal setups  |  Default in modern Linux distributions                 |


## Quick NGINX Service Management Demo (Hands-On)
### 0. Prerequisites
- You have sudo access.
- Port 80 is free (no Apache or another web server running).
- Package manager differs by distro:
  - Debian/Ubuntu: `apt`
  - RHEL/CentOS/Rocky/Alma/Amazon Linux: `yum` or `dnf`

### 1. Install NGINX
```bash
# Debian/Ubuntu
sudo apt update
sudo apt install -y nginx

# RHEL/CentOS (if EPEL needed on older versions)
sudo yum install -y nginx
# or
sudo dnf install -y nginx
```

### 2. Check the Service File & Status
```bash
systemctl status nginx
systemctl list-unit-files | grep -i nginx
```

### 3. Start the Service
```bash
sudo systemctl start nginx
systemctl status nginx --no-pager
```

### 4. Enable at Boot
```bash
sudo systemctl enable nginx
systemctl is-enabled nginx
```

### 5. Verify Listening Port
```bash
ss -tlnp | grep :80   # or: sudo lsof -i :80
```

### 6. Test Default Page
```bash
curl -I http://localhost
# Expect: HTTP/1.1 200 OK
```
(Optional in browser: http://<server_ip>)

### 7. Modify a Simple Page
```bash
sudo bash -c 'echo "<h1>NGINX Demo $(hostname)</h1>" > /var/www/html/index.nginx-debian.html'
curl http://localhost
```

### 8. Test Config Before Reload
```bash
sudo nginx -t
```
Always do this before reload/restart after edits.

### 9. Reload (Zero-Downtime Config Apply)
```bash
sudo systemctl reload nginx
```

### 10. View Logs
```bash
# Systemd journal
journalctl -u nginx -n 20
journalctl -u nginx -f

# Access and error logs
sudo tail -f /var/log/nginx/access.log /var/log/nginx/error.log
```

### 11. Restart vs Reload
```bash
sudo systemctl restart nginx   # Full stop/start (connections dropped)
sudo systemctl reload nginx    # Graceful (keeps connections)
```

### 12. Temporarily Stop & Disable
```bash
sudo systemctl stop nginx
sudo systemctl disable nginx
systemctl is-enabled nginx
```

### 13. Re-enable & Start Again
```bash
sudo systemctl enable --now nginx
```

### 14. Show Startup Time Impact (Optional)
```bash
systemd-analyze blame | grep -i nginx || true
```

### 15. Create a Drop-In Override (Customization)
Add environment variable or change restart policy without editing the main unit:
```bash
sudo systemctl edit nginx
```
Add:
```
[Service]
Environment=APP_ENV=demo
```
Then:
```bash
sudo systemctl daemon-reload
sudo systemctl restart nginx
systemctl show nginx | grep -i APP_ENV
```
Remove override:
```bash
sudo rm -r /etc/systemd/system/nginx.service.d
sudo systemctl daemon-reload
```

### 16. Uninstall (Optional Cleanup)
```bash
# Debian/Ubuntu
sudo apt remove -y nginx
sudo apt purge -y nginx
sudo rm -rf /var/log/nginx /etc/nginx

# RHEL/CentOS
sudo yum remove -y nginx
```

## Sample Interview Questions
1. Explain the complete Linux boot process, step by step.
2. What is the difference between BIOS and UEFI?
3. What is the role of the bootloader, and how can you troubleshoot bootloader issues?
4. Compare systemd, SysVinit, and Upstart. Why is systemd preferred in modern distributions?
5. How do you check if a service is enabled to start at boot? How do you enable/disable it?
6. What is the difference between `systemctl stop` and `systemctl disable`?
7. How can you view logs for a specific service?
8. What would you do if a critical service fails to start during boot?
9. How can you secure the bootloader?
10. What is the purpose of the `dmesg` command?

## Interview Question Answers
1. **Boot Process:** BIOS/UEFI → Bootloader (GRUB) → Kernel loading → Init system (systemd) → User space services
2. **BIOS vs UEFI:** BIOS is legacy firmware with 16-bit mode; UEFI is modern with 32/64-bit, faster boot, secure boot, and GUI
3. **Bootloader:** GRUB loads kernel and initramfs; troubleshoot with rescue mode, check `/boot/grub/grub.cfg`, use `grub-install`
4. **Init Systems:** systemd is parallel, faster, dependency-based; SysVinit is sequential, script-based; Upstart was transitional
5. **Service Boot Management:** `systemctl is-enabled service`, `systemctl enable/disable service`
6. **Stop vs Disable:** `stop` halts running service; `disable` prevents auto-start at boot
7. **Service Logs:** `journalctl -u service`, `systemctl status service` shows recent logs
8. **Failed Service:** Check `systemctl status`, `journalctl -u service`, use rescue mode, check dependencies
9. **Secure Bootloader:** Set GRUB password, enable secure boot, restrict physical access
10. **dmesg Purpose:** Shows kernel ring buffer messages, hardware detection, driver loading, boot errors
