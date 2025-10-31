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

## Notes

- **Why Learn Advanced Linux Commands?**
  - These commands are essential for text processing, automation, and efficient system administration in high-volume environments like logs and configs.
  - Mastery of these tools is expected in DevOps, SRE, and system engineering interviews, where you'll debug issues or automate workflows on the fly.

## Top 6 Log Parsing Commands

| Command | Simple Description | Examples |
|---------|--------------------|----------|
| **GREP**<br>`$ grep <pattern> file.log` | Finds lines in a file that contain a specific word or pattern. | 1. Find file names that match: `grep -l "linuxthefinalboss" *.log`<br>2. Case insensitive word match: `grep -i "linuxthefinalboss" test.log`<br>3. Show line numbers: `grep -n "linuxthefinalboss" test.log`<br>4. Invert matches: `grep -v "linuxthefinalboss" test.log`<br>5. Take patterns from a file: `grep -f pattern.txt test.log`<br>6. Search recursively in a dir: `grep -R "linuxthefinalboss" /home` |
| **CUT**<br>`$ cut -d' ' -f3 file.log` | Extracts specific parts (like columns or characters) from each line of a file. | 1. Cut first 3 bytes: `cut -b1-3 file.log`<br>2. Select 2nd column delimited by a space: `cut -d' ' -f2 test.log`<br>3. Specify character position: `cut -c1-8 test.log` |
| **SED**<br>`$ sed /<regex>/<replace>/g` | Edits text files by replacing, deleting, or inserting parts based on patterns. | 1. Substitute a string: `sed s/linuxthefinalboss/go/g test.log`<br>2. Replace the 2nd occurrence: `sed s/linuxthefinalboss/go/2 test.log`<br>3. Replace case insensitive: `sed /linuxthefinalboss/go/I test.log`<br>4. Replace string on line range of 2-4: `sed '2,4s/linuxthefinalboss/go/' test.log`<br>5. Delete a line: `sed '4d' test.log` |
| **AWK**<br>`$ awk '{print $4}' test.log` | Processes text files by scanning for patterns and handling columns (fields). | 1. Print matched lines: `awk /linuxthefinalboss/ {print} test.log`<br>2. Split a line into fields: `awk '{print $1 $3}' test.log`<br>3. Print lines 2 to 7: `awk 'NR>=2 && NR<=7 {print NR, $0}' test.log`<br>4. Print lines with more than 10 characters: `awk 'length($0)>10' test.log`<br>5. Find a string (field=4) = "linux" print line: `awk '$4=="linux" {print $0}' test.log` |
| **SORT**<br>`$ sort test.log` | Arranges the lines in a file in order (like alphabetical or numerical). | 1. Output to a file: `sort -o output.txt input.txt`<br>2. Sort in reverse order: `sort -r test.log`<br>3. Sort numerically: `sort -n test.log`<br>4. Sort based on the 3rd column: `sort -k3n test.log`<br>5. Check if a file is ordered: `sort -c test.log`<br>6. Sort and remove duplicates: `sort -u test.log` |
| **UNIQ**<br>`$ uniq test.log` | Removes or counts duplicate lines (works best on sorted files). | 1. Tell how many times a line repeats: `uniq -c test.log`<br>2. Print repeated lines: `uniq -d test.log`<br>3. Print unique lines: `uniq -u test.log`<br>4. Skip the first two fields: `uniq -f 2 test.log`<br>5. Compare case-insensitive: `uniq -i test.log`
