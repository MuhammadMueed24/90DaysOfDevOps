# Day 12 – Revision & Consolidation

## 1. Mindset & Learning Plan Review

I revisited my Day 01 learning plan to check if my goals still make sense.

Observations:
- My main goal is to build strong Linux fundamentals for DevOps.
- The pace of learning one topic per day is working well.
- Hands-on practice helps more than just reading commands.

Possible improvements:
- Spend more time practicing commands instead of only documenting them.
- Keep a small cheat sheet of frequently used commands.

---

# 2. Processes & Services Review

Commands re-run today:

Check running processes
```bash
ps aux
```

Observation:
- Displays all running processes with user, CPU usage, memory usage, and command name.

Check service status
```bash
systemctl status ssh
```

Observation:
- Shows whether the service is active, inactive, or failed.
- Displays logs and service details.

Check service logs
```bash
journalctl -u ssh
```

Observation:
- Shows detailed logs for the selected service.
- Useful for debugging service failures.

---

# 3. File Skills Practice

### Append text to a file
```bash
echo "Linux practice" >> notes.txt
```

### Change file permissions
```bash
chmod 644 notes.txt
```

### View file ownership
```bash
ls -l notes.txt
```

### Copy a file
```bash
cp notes.txt backup-notes.txt
```

### Create a directory
```bash
mkdir practice-folder
```

Observation:
- File operations become faster once you memorize common commands.

---

# 4. Cheat Sheet Refresh (Top 5 Commands)

These are the commands I would use first during an incident:

1. `ls -l`  
   To inspect files, permissions, and ownership.

2. `ps aux`  
   To check running processes.

3. `systemctl status <service>`  
   To verify if a service is running correctly.

4. `journalctl -u <service>`  
   To check logs of a specific service.

5. `chmod`  
   To quickly fix file permission issues.

---

# 5. User & Group Sanity Practice

Create a user:

```bash
sudo useradd demo-user
```

Verify user:

```bash
id demo-user
```

Create a file:

```bash
touch test-file.txt
```

Change ownership:

```bash
sudo chown demo-user test-file.txt
```

Verify ownership:

```bash
ls -l test-file.txt
```

Observation:
- Ownership changes correctly when the user exists.
- Always verify with `ls -l`.

---

# Mini Self-Check

## 1. Which 3 commands save you the most time right now?

1. `ls -l` – quickly shows file permissions and ownership.  
2. `ps aux` – helps identify running processes and troubleshoot issues.  
3. `systemctl status` – instantly shows service health.

---

## 2. How do you check if a service is healthy?

Commands I run first:

```bash
systemctl status <service>
```

```bash
ps aux | grep <service>
```

```bash
journalctl -u <service>
```

These commands show service status, running processes, and logs.

---

## 3. How do you safely change ownership and permissions?

Steps:
1. Verify current ownership using `ls -l`.
2. Change ownership using `chown`.
3. Adjust permissions using `chmod`.

Example:

```bash
sudo chown user:group filename
chmod 644 filename
```

This ensures proper access without breaking permissions.

---

# Key Takeaways

- Practicing commands repeatedly improves retention.
- Linux permissions and ownership are critical for system security.
- Service management commands are essential for troubleshooting.
- Keeping a cheat sheet helps during real incidents.
