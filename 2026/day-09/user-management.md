# Day 09 Challenge

## Users & Groups Created
**Users Created**
- tokyo
- berlin
- professor
- nairobi

**Groups Created**
- developers
- admins
- project-team

---

## Group Assignments

| User | Groups |
|-----|------|
| tokyo | tokyo, developers, project-team |
| berlin | berlin, developers, admins |
| professor | professor, admins |
| nairobi | nairobi, project-team |

---

## Directories Created

| Directory | Group Owner | Permissions | Purpose |
|-----------|-------------|-------------|---------|
| /opt/dev-project | developers | 775 (rwxrwxr-x) | Shared directory for developers |
| /opt/team-workspace | project-team | 775 (rwxrwxr-x) | Workspace for project-team |

---

## Commands Used

```bash
# Create users with home directories
sudo useradd -m tokyo
sudo useradd -m berlin
sudo useradd -m professor
sudo useradd -m nairobi

# Set passwords
sudo passwd tokyo
sudo passwd berlin
sudo passwd professor
sudo passwd nairobi

# Create groups
sudo groupadd developers
sudo groupadd admins
sudo groupadd project-team

# Add users to groups
sudo usermod -aG developers tokyo
sudo usermod -aG developers berlin
sudo usermod -aG admins berlin
sudo usermod -aG admins professor
sudo usermod -aG project-team tokyo
sudo usermod -aG project-team nairobi

# Verify groups
groups tokyo
groups berlin
groups professor
groups nairobi

# Create shared directories
sudo mkdir /opt/dev-project
sudo mkdir /opt/team-workspace

# Set group ownership
sudo chgrp developers /opt/dev-project
sudo chgrp project-team /opt/team-workspace

# Set permissions
sudo chmod 775 /opt/dev-project
sudo chmod 775 /opt/team-workspace

# Test file creation
sudo -u tokyo touch /opt/dev-project/test_tokyo.txt
sudo -u berlin touch /opt/dev-project/test_berlin.txt
sudo -u nairobi touch /opt/team-workspace/test_nairobi.txt

# Verify permissions
ls -ld /opt/dev-project
ls -ld /opt/team-workspace
ls -l /opt/dev-project
ls -l /opt/team-workspace

# What I Learned
1. How to create Linux users with home directories and assign passwords.
2. How to create groups and add users to single or multiple groups.
3. How to manage shared directories using group ownership and permissions (775) to allow collaboration between users.
