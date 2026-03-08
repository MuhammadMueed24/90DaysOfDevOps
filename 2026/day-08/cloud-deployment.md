# Day 08 – Cloud Server Deployment

## SSH Connection to EC2
<img width="1460" height="388" alt="Screenshot from 2026-03-09 03-59-43" src="https://github.com/user-attachments/assets/9ab75240-2c56-419d-a71d-85fb4f758156" />

## Nginx Web Server Running
<img width="1002" height="401" alt="Screenshot from 2026-03-09 02-47-03" src="https://github.com/user-attachments/assets/0b3eb7b5-0483-4546-bf6c-6a87a0df6588" />

---

## Commands Used

- ssh -i aws-key.pem ubuntu@server-ip
- sudo apt update
- sudo apt install nginx
- sudo systemctl start nginx
- sudo systemctl status nginx
- scp -i aws-key.pem ubuntu@server-ip:~/nginx-logs.txt .

---

## Challenges Faced

- Permission denied when downloading logs
- Fixed by using correct username and permissions

---

## What I Learned

- Launching EC2 instances
- SSH connection to cloud servers
- Installing Nginx
- Viewing and exporting logs
- Security group configuration
