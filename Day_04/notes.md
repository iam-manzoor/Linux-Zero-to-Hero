# Service Management in Linux with Systemd
- Service management in Linux is predominantly handled by `systemd` in modern distributions, providing powerful and flexible control over system and application services using unit files and the systemctl command.
- Managing services with systemd is mainly about using the `systemctl` command. Whether you need to start, stop, restart, or check on a service, systemctl does the job.

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

  - **[Unit]:** This section defines metadata about the unit, like a human-readable description, and it configures dependencies and ordering relationships with other units using directives like After=, Requires=, or Wants=.​
  - **[Service]:** Present in service units (.service files), this section defines how to start and manage the service. Directives here include:
    - ExecStart = for the command to launch the service
    - ExecStop = for stopping the service
    - Restart = specifying restart behavior on failure
    - User= and Group= to run the service under a specific system user/group.​
  - **[Install]:** This optional section defines how the unit should be enabled or disabled, specifying the target(s) the unit should be linked to when enabled (e.g., WantedBy=multi-user.target). This controls whether the unit starts automatically on boot.
