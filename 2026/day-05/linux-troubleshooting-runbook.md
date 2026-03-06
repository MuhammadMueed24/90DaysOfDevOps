# Day 05 – Linux Troubleshooting Runbook

## Target service 
Serivce: SSH
---

# 1.Environment Basics

### Command
``` bash
uname -a

```
### purpose
Shows system kernal and architecture information

### observation
Confirmed the system is running a linux kernal


### Command
``` bash
cat /etc/os-release

```
### purpose
display OS information

### observation
displays os name, version.

# 2.Filesystemm Sanity

### Command
``` bash
mkdir /tmp/runbook-demo

```
### purpose
Creates a temporary directory to test filesystem functionality.

### observation
Directory created successfully

### Command
``` bash
cp /etc/hosts /tmp/runbook-demo/hosts-copy
ls -l /tmp/runbook-demo

```
### purpose
Tests file copy operations and verifies filesystem permissions.

### observation
File copied successfully which confirms filesystem operations are working normally.

# 3.Snapshot - CPU & Memory

### Command
``` bash
top
```
### purpose
Displays real-time CPU and memory usage for running processes.

### observation
System CPU usage was within normal limits and no abnormal spikes were observed.

### Command
``` bash
free -h

```
### purpose
Displays memory usage in a human readable format.

### observation
Memory usage appeared normal with available free memory and no signs of memory exhaustion.

# 4. Snapshot – Disk & IO

### Command
``` bash
df -h

```
### purpose
Displays disk space usage for all mounted filesystems.

### observation
Disk utilization was within acceptable limits and no partition was critically full.

### Command
``` bash
du -sh /var/log

```
### purpose
Checks total disk usage of log files.

### observation
Log directory size is manageable and not consuming excessive disk space.

# 5. Snapshot – Network

### Command
``` bash
ss -tulpn

```
### purpose
Shows listening ports and associated services.

### observation
Confirmed that SSH service is listening on port 22.


### Command
``` bash
ping google.com

```
### purpose
Tests external network connectivity.

### observation
Server successfully reached external network which confirms network connectivity is working.

# 6. Logs Reviewed

### Command
``` bash
journalctl -u ssh -n 50

```
### purpose
Displays the latest 50 log entries for the SSH service.

### observation
Recent logs show successful login activity and normal SSH server operations without critical errors.

