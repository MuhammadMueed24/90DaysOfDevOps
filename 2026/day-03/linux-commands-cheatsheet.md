# Linux Commands Cheat Sheet
**Day 03 – Linux Commands Practice**

This cheat sheet contains commonly used Linux commands that are helpful for **process management, file system operations, and network troubleshooting**. These commands are essential for DevOps engineers when diagnosing issues in production environments.

---

# Process Management Commands

| Command | Description |
|-------|-------------|
| `ps` | Displays currently running processes. |
| `ps aux` | Shows detailed information about all running processes. |
| `top` | Displays real-time system processes and resource usage. |
| `htop` | Interactive process viewer with better visualization than `top`. |
| `kill <PID>` | Terminates a process using its Process ID. |
| `kill -9 <PID>` | Forcefully kills a process. |
| `pkill <process_name>` | Kills processes by name instead of PID. |
| `pgrep <process_name>` | Finds the PID of a running process. |
| `nice` | Starts a process with a specified priority. |
| `renice` | Changes the priority of an already running process. |

---

# File System Commands

| Command | Description |
|-------|-------------|
| `ls` | Lists files and directories. |
| `ls -la` | Lists files with detailed information including hidden files. |
| `cd <directory>` | Changes the current directory. |
| `pwd` | Shows the current working directory. |
| `mkdir <dir>` | Creates a new directory. |
| `rm <file>` | Deletes a file. |
| `rm -r <dir>` | Deletes a directory and its contents recursively. |
| `cp <src> <dest>` | Copies files or directories. |
| `mv <src> <dest>` | Moves or renames files or directories. |
| `cat <file>` | Displays the content of a file. |
| `less <file>` | Views file content page by page. |
| `tail -f <file>` | Displays the last lines of a file and follows updates (useful for logs). |
| `chmod` | Changes file permissions. |
| `chown` | Changes file ownership. |
| `df -h` | Shows disk space usage in human-readable format. |
| `du -sh <dir>` | Shows total size of a directory. |

---

# Networking Troubleshooting Commands

| Command | Description |
|-------|-------------|
| `ping <host>` | Tests connectivity to another host. |
| `ip addr` | Displays IP addresses of network interfaces. |
| `curl <url>` | Sends HTTP requests and retrieves responses from servers. |
| `dig <domain>` | Queries DNS information for a domain. |
| `netstat -tulnp` | Shows listening ports and active connections. |
| `ss -tulnp` | Displays socket statistics (modern replacement for netstat). |
| `traceroute <host>` | Shows the path packets take to reach a destination. |

---

# Summary

This cheat sheet covers essential Linux commands used for:

- **Managing processes**
- **Navigating and managing the file system**
- **Troubleshooting network connectivity**
