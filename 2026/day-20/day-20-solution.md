# Day 20 Solution - Log Analyzer

## Objective
Build a Bash script that analyzes server log files and generates a daily summary report.

---

## Script Name
log_analyzer.sh

---

## Features Implemented

### Task 1: Input Validation
- Accepts log file path as command-line argument
- Shows error if no file provided
- Shows error if file does not exist

### Task 2: Error Count
Counts lines containing:

- ERROR
- Failed

### Task 3: Critical Events
Displays lines containing CRITICAL with line numbers.

### Task 4: Top 5 Error Messages
Extracts ERROR messages and shows top 5 most frequent.

### Task 5: Summary Report
Creates report file:

log_report_<date>.txt

Includes:

- Date
- Log file name
- Total lines
- Error count
- Top 5 errors
- Critical events

---

## Commands Used

- grep
- awk
- wc
- sort
- uniq
- head
- date

---

## Sample Run

```bash
./log_analyzer.sh system.log
