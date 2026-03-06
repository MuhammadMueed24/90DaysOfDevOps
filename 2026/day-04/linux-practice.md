# Day 04 – Linux Practice: Processes and Services

## Process Checks

### Command
```bash
pgrep nginx

1653
1655
1656
```

## Service Checks
### Command
```bash
systemctl list-units --type=service --state=active | grep nginx

nginx.service    loaded active running A high performance web server and a reverse proxy server
```

## log cheks
### Command
```bash

journalctl -u ssh | tail -n 5

Mar 06 16:12:00 ip-172-31-74-56 sshd[906]: Accepted publickey for ubuntu from 192.140.151.213 port 33336 ssh2: RSA SHA256:ZjP3CUFsDezadRqw8sFlZ1DvEKbQzem2YXBtgfH+Kp4
Mar 06 16:12:00 ip-172-31-74-56 sshd[906]: pam_unix(sshd:session): session opened for user ubuntu(uid=1000) by ubuntu(uid=0)
Mar 06 16:35:33 ip-172-31-74-56 sshd[7119]: Server listening on 0.0.0.0 port 22.
Mar 06 16:35:33 ip-172-31-74-56 systemd[1]: Started ssh.service - OpenBSD Secure Shell server.
Mar 06 16:35:33 ip-172-31-74-56 sshd[7119]: Server listening on :: port 22.
```
