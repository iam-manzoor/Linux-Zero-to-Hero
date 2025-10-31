# Day 06 Advanced Linux Commands

## Sample Log file for practice
```
2025-10-07 10:10:10 INFO  systemd 127.0.0.1 Started user session for user1
2025-10-07 10:12:15 WARN  kernel 0.0.0.0 CPU temperature high
2025-10-07 10:13:25 ERROR cron 127.0.0.1 Cron job backup failed
2025-10-07 10:15:40 INFO  sshd 192.168.0.1 Accepted publickey for user1
2025-10-07 10:17:50 ERROR kernel 0.0.0.0 Disk read error on /dev/sda1
2025-10-07 10:18:30 INFO  systemd 127.0.0.1 Network interface eth0 up
2025-10-07 10:19:50 INFO  cron 127.0.0.1 Running scheduled task cleanup
2025-10-07 10:20:55 WARN  systemd 127.0.0.1 Service nginx failed restart
2025-10-07 10:22:05 INFO  kernel 0.0.0.0 USB device connected
2025-10-07 10:23:15 ERROR sshd 192.168.0.10 Failed password attempt for user2
2025-10-07 10:24:30 INFO  cron 127.0.0.1 Daily job report sent
2025-10-07 10:25:45 WARN  systemd 127.0.0.1 Low memory detected
2025-10-07 10:27:10 INFO  kernel 0.0.0.0 Loaded new kernel module
2025-10-07 10:28:25 ERROR systemd 127.0.0.1 Failed to start Docker daemon
2025-10-07 10:29:40 INFO  systemd 127.0.0.1 System shutdown initiated
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

---

### grep (Global Regular Expression Print)
**grep** searches for patterns (strings or regex) in files, outputting matching lines. Use it for log filtering, error hunting, or auditing. Supports basic/extended regex (-E) and fixed strings (-F).

Here is a detailed list of important grep commands with options, examples, and explanations:

#### Case-insensitive search
```
grep -i "error" logfile.txt
```
Finds matches of "error" ignoring case variants like "Error", "ERROR" etc.

#### Invert match (exclude lines matching pattern)
```
grep -v "debug" logfile.txt
```
Prints lines that do NOT contain "debug". Useful for excluding debug info.

#### Show line numbers
```
grep -n "disk" logfile.txt
```
Displays line numbers where "disk" is found, aiding file navigation.

#### Count matching lines
```
grep -c "warning" logfile.txt
```
Outputs how many lines contain "warning", useful for quick stats.

#### Recursive search in directories
```
grep -r "timeout" /var/log/
```
Searches "timeout" in all files under /var/log and subfolders.

#### Show file names with matches
```
grep -l "fail" *.log
```
Lists log files that contain "fail", instead of matching lines.

#### Match whole words only
```
grep -w "user" logfile.txt
```
Matches "user" but not "username" or "superuser" for precise matches.

#### Show only matched parts
```
grep -o "http[s]*://[^ ]*" logfile.txt
```
Extracts and prints only URLs matching regex within lines.

#### Print context lines after match
```
grep -A 3 "error" logfile.txt
```
Prints matched line plus 3 lines after it for context.

#### Print context lines before match
```
grep -B 2 "fail" logfile.txt
```
Prints matched line plus 2 lines before it.

#### Print context lines around match
```
grep -C 2 "timeout" logfile.txt
```
Prints 2 lines before and after each match.

#### Multiple patterns
```
grep -e "error" -e "fail" logfile.txt
```
Matches lines containing "error" or "fail".

#### Patterns from file
```
grep -f patterns.txt logfile.txt
```
Searches for all patterns listed in patterns.txt in logfile.txt.

#### Use extended regex (ERE)
```
grep -E "(error|fail|warn)" logfile.txt
```
Allows using advanced regex features like grouping and alternations.

#### Highlight matches
```
grep --color=auto "timeout" logfile.txt
```
Highlights matching strings in colored output for better visibility.

#### Quiet mode (no output, only return code)
```
grep -q "panic" logfile.txt
```
Use in scripts to check if "panic" exists without printing lines.

#### Line-buffered output for real-time use
```
grep --line-buffered "connect" logfile.txt
```
Flushes output line-by-line immediately (useful in pipelines).

---

### awk
**awk** processes text by records (lines) and fields (space-separated by default). Use patterns/actions for filtering, math, and formatting. FS (field separator) and OFS (output separator) are customizable.

#### Print entire line matching a pattern:
```
awk '/error/ {print $0}' logfile.txt
```
Prints all lines containing "error". $0 represents the whole line.

#### Print specific fields/columns:
```
awk '{print $1, $4}' file.txt
```
Prints the 1st and 4th columns separated by default whitespace.

#### Set field separator:
```
awk -F',' '{print $1, $3}' data.csv
```
Uses comma as field delimiter. Prints 1st and 3rd columns from CSV.

#### Assign and use variable:
```
awk -v var="Warning:" '$0 ~ var {print var, $0}' logfile.txt
```
Searches lines containing "Warning:" and prints with the variable prefix.

#### Print lines based on condition:
```
awk '$3 > 50 {print $1, "passed"}' scores.txt
```
If 3rd column (score) is greater than 50, prints name and "passed".

#### Use BEGIN and END blocks:
```
awk 'BEGIN {print "Start"} {print $0} END {print "End"}' file.txt
```
Prints "Start" once, then lines of file, then "End" after last line.

#### Calculate sum of a column:
```
awk '{sum += $2} END {print "Total:", sum}' file.txt
```
Adds values in 2nd column and prints total after processing all lines.

---

### sed (Stream Editor)
**sed** edits streams/files non-interactively: substitute, delete, insert, or transform. Use -i for in-place edits (with backup via -i.bak). Chain commands with -e.

#### Replace first occurrence of a string in each line:
```
sed 's/old/new/' file.txt
```
Replaces first "old" with "new" in each line.

#### Replace all occurrences in each line (global):
```
sed 's/old/new/g' file.txt
```
Replaces all "old" with "new" in each line.

#### Replace string only on a specific line:
```
sed '3 s/old/new/' file.txt
```
Replaces "old" with "new" on line 3 only.

#### Delete lines matching a pattern:
```
sed '/pattern/d' file.txt
```
Deletes lines containing "pattern".

#### Print specific lines (line range):
```
sed -n '5,10p' file.txt
```
Prints only lines 5 to 10 (-n suppresses default output).

#### In-place replace (backup to .bak)
```
sed -i.bak 's/user1/admin/g' sample.log
```
Take a backup of the file and store the changes permanently.

#### Insert a line before matched line:
```
sed '/pattern/i New line added before' file.txt
```
Inserts preceded text above lines matching "pattern".

#### Append a line after matched line:
```
sed '/pattern/a New line added after' file.txt
```
Adds text after lines matching "pattern".
