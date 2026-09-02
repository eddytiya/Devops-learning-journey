Linux for DevOps - Day 4

## Grep, Sed, and AWK for Log Analysis

Today I continued my Linux for DevOps practice on an AWS Ubuntu server. The main focus was text processing using `grep`, `sed`, and `awk`.

These three commands are very useful in DevOps because logs, configuration files, command outputs, and system reports are mostly text-based.

## Practice File

I used a log file named:

```bash
app.log
```

The file contained application log entries with timestamps, log levels like `INFO` and `DEBUG`, process/thread names, and log messages.

## 1. Basic Setup

Create a practice folder:

```bash
mkdir day4
```

Move into the folder:

```bash
cd day4
```

Create or edit the log file:

```bash
vim app.log
```

or:

```bash
nano app.log
```

Display the file:

```bash
cat app.log
```

Display only the first few lines:

```bash
head app.log
```

Display the first 2 lines:

```bash
head -n 2 app.log
```

## 2. Grep Commands

Search for lowercase `info`:

```bash
grep info app.log
```

Expected result:

```text
No output if the file contains INFO in uppercase.
```

Search case-insensitively:

```bash
grep -i info app.log
```

Expected result:

```text
All lines containing INFO, info, Info, etc.
```

Count matching lines:

```bash
grep -i -c info app.log
```

Expected result:

```text
15
```

Search process list for a user:

```bash
ps aux | grep ubuntu
```

Expected result:

```text
Shows running processes that contain the word ubuntu.
```

## 3. Sed Commands

Print lines 1 to 10:

```bash
sed -n '1,10p' app.log
```

Expected result:

```text
Displays only lines 1 to 10 from app.log.
```

Replace `INFO` with `LOG` while printing lines 1 to 10:

```bash
sed '1,10 s/INFO/LOG/g;1,10p;11q' app.log
```

Expected result:

```text
Prints the first 10 lines and changes INFO to LOG in that displayed output.
```

Note:

```text
Without -n, sed can print normal output plus requested print output, so duplicate lines may appear.
```

Better controlled version:

```bash
sed -n '1,10{s/INFO/LOG/g;p}' app.log
```

Expected result:

```text
Only lines 1 to 10 are printed, with INFO replaced by LOG.
```

## 4. AWK Commands

Print every line:

```bash
awk '{print}' app.log
```

Expected result:

```text
Displays the complete file.
```

Print selected fields:

```bash
awk '{print $1, $2}' app.log
```

Expected result:

```text
Displays the first and second fields from each line.
```

Print more selected fields:

```bash
awk '{print $1, $2, $3, $5}' app.log
```

Expected result:

```text
Displays selected columns such as date, time, log level, and source field.
```

Filter only `INFO` lines:

```bash
awk '/INFO/ {print $1, $2, $3, $5}' app.log
```

Expected result:

```text
Shows selected fields only from log lines containing INFO.
```

Count `INFO` records:

```bash
awk '/INFO/ {count++} END {print count}' app.log
```

Expected result:

```text
15
```

Print lines 2 to 10:

```bash
awk 'NR>=2 && NR<=10 {print}' app.log
```

Expected result:

```text
Displays records from line number 2 to line number 10.
```

Filter records by time field:

```bash
awk '$2 >= "07:51:55,000]" && $2 <= "07:52:01,999]" {print}' app.log
```

Expected result:

```text
Displays log lines where the second field falls inside the selected time range.
```

## 5. Useful Comparison

| Command | Main Use | Example |
|---|---|---|
| `grep` | Search matching lines | `grep -i info app.log` |
| `sed` | Edit or replace text in stream | `sed -n '1,10{s/INFO/LOG/g;p}' app.log` |
| `awk` | Extract fields and create reports | `awk '/INFO/ {count++} END {print count}' app.log` |

## 6. Common Errors I Practiced Fixing

Wrong command spelling:

```bash
awl '/INFO/ {count++} END {print count}' app.log
```

Correct command:

```bash
awk '/INFO/ {count++} END {print count}' app.log
```

Missing space after `awk`:

```bash
awk'/INFO/ {count++} END {print count}' app.log
```

Correct command:

```bash
awk '/INFO/ {count++} END {print count}' app.log
```

Case-sensitive grep returning no output:

```bash
grep info app.log
```

Correct command:

```bash
grep -i info app.log
```

## 7. What I Learned

- `grep` is useful for searching text and counting matches.
- `sed` is useful for replacing text and printing selected line ranges.
- `awk` is useful for extracting columns, filtering records, and creating quick reports.
- Linux commands are case-sensitive.
- Small syntax mistakes like missing spaces can change the whole command.
- These tools are very useful for DevOps log analysis and troubleshooting.

## 8. Day 4 Summary

Today I practiced:

- Creating and reading log files
- Searching logs with `grep`
- Counting matching log entries
- Replacing text using `sed`
- Printing selected line ranges
- Extracting columns with `awk`
- Filtering logs by pattern
- Counting records using `awk`
- Checking running processes with `ps aux`

## Hashtags

`#Linux` `#DevOps` `#AWS` `#Ubuntu` `#Grep` `#Sed` `#AWK` `#LogAnalysis` `#LearningInPublic`
