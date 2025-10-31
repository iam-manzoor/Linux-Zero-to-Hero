# Day 06 Advanced Linux Commands

## Sample Log file for practice
```
Oct 31 08:15:01 server1 sshd[2345]: Accepted password for user2 from 192.168.1.100 port 60234 ssh2
Oct 31 08:15:05 server1 systemd[1]: Started Daily Cleanup Service.
Oct 31 08:15:10 server1 kernel: [123457.123456] eth0: link down
Oct 31 08:15:11 server1 sshd[2348]: Failed password for root from 10.10.10.15 port 45321 ssh2
Oct 31 08:15:15 server1 httpd[4322]: Warning: MaxClients reached, further connections will be queued
Oct 31 08:15:20 server1 CRON[5678]: (root) CMD (run-parts /etc/cron.daily/)
Oct 31 08:15:25 server1 kernel: [123457.654321] eth0: link up
Oct 31 08:15:30 server1 sshd[2350]: Accepted password for admin from 192.168.1.101 port 60250 ssh2
Oct 31 08:15:35 server1 systemd[1]: Stopping User Manager for UID 1000
Oct 31 08:15:40 server1 sshd[2348]: Failed password for root from 10.10.10.15 port 45322 ssh2
Oct 31 08:15:45 server1 sudo:    user2 : TTY=pts/1 ; PWD=/home/user2 ; USER=root ; COMMAND=/bin/systemctl restart httpd
Oct 31 08:15:50 server1 sshd[2350]: Accepted publickey for admin from 192.168.1.101 port 60255 ssh2
Oct 31 08:15:55 server1 kernel: [123458.111213] CPU0: Core temperature above threshold, cpu clock throttled
Oct 31 08:16:00 server1 httpd[4322]: Error: File not found /var/www/html/favicon.ico
Oct 31 08:16:05 server1 CRON[5678]: (root) CMD (run-parts /etc/cron.hourly/)
```
