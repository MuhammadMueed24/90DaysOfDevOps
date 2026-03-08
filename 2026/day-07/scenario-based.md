# Linux Scenario-Based Troubleshooting Guide

## Scenario 1: Service Not Starting

A web application service called `myapp` failed to start after a server reboot.

Step 1:  
systemctl status myapp  
Why: Verify if the service is running, failed, or inactive.

Step 2:  
journalctl -u myapp -n 50  
Why: View the last 50 log lines to identify errors or issues.

Step 3:  
systemctl is-enabled myapp  
Why: Confirm whether the service starts automatically on server boot.

Step 4:  
systemctl start myapp  
Why: Attempt to start the service after investigating the issue.

---

## Scenario 2: High CPU Usage

The application server is reported to be slow. You want to identify which process is using high CPU.

Step 1:  
top  
Why: Monitor system processes and resource usage in real-time.

Step 2:  
htop  
Why: Provides an interactive, color-coded view of CPU and memory usage by process.

Step 3:  
ps aux --sort=-%cpu | head -10  
Why: Quickly identify the top 10 processes using the most CPU.

Step 4:  
kill <PID>  
Why: Terminate the process consuming excessive CPU (replace <PID> with the actual Process ID).

---

## Scenario 3: Finding Service Logs

A developer asks where the logs for the `docker` service are located. The service is managed by systemd.

Step 1:  
systemctl status docker  
Why: Confirm the current state of the Docker service.

Step 2:  
journalctl -u docker -n 50  
Why: Examine recent log entries to troubleshoot errors.

Step 3:  
journalctl -u docker -f  
Why: Monitor Docker logs as events happen, similar to `tail -f`.

---

## Scenario 4: File Permissions Issue

A script at `/home/user/backup.sh` is not executing: `./backup.sh` returns "Permission denied".

Step 1:  
ls -l /home/user/backup.sh  
Why: Determine if the script has execute (`x`) permission.

Step 2:  
chmod +x /home/user/backup.sh  
Why: Grant execute permission so the script can run.

Step 3:  
ls -l /home/user/backup.sh  
Why: Ensure the script now shows `x` in the permissions.

Step 4:  
./backup.sh  
Why: Execute the script after fixing permissions.
