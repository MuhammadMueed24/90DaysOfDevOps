# Day 11 Challenge

## Files & Directories Created

Files:
- devops-file.txt
- team-notes.txt
- project-config.yaml
- heist-project/vault/gold.txt
- heist-project/plans/strategy.conf
- bank-heist/access-codes.txt
- bank-heist/blueprints.pdf
- bank-heist/escape-plan.txt

Directories:
- app-logs/
- heist-project/
- heist-project/vault/
- heist-project/plans/
- bank-heist/

Users Created:
- tokyo
- berlin
- nairobi
- professor

Groups Created:
- heist-team
- planners
- vault-team
- tech-team

---

## Ownership Changes

devops-file.txt  
Before: maddy:maddy  
After: tokyo:maddy  

devops-file.txt  
Before: tokyo:maddy  
After: berlin:maddy  

team-notes.txt  
Before: maddy:maddy  
After: maddy:heist-team  

project-config.yaml  
Before: maddy:maddy  
After: professor:heist-team  

app-logs/  
Before: maddy:maddy  
After: berlin:heist-team  

heist-project/ (recursive)  
Before: maddy:maddy  
After: professor:planners  

bank-heist/access-codes.txt  
Before: maddy:maddy  
After: tokyo:vault-team  

bank-heist/blueprints.pdf  
Before: maddy:maddy  
After: berlin:tech-team  

bank-heist/escape-plan.txt  
Before: maddy:maddy  
After: nairobi:vault-team  

---

## Commands Used

Check ownership
```bash
ls -l
```

Create files
```bash
touch devops-file.txt
touch team-notes.txt
touch project-config.yaml
```

Create directories
```bash
mkdir app-logs
mkdir -p heist-project/vault
mkdir -p heist-project/plans
mkdir bank-heist
```

Create files inside directories
```bash
touch heist-project/vault/gold.txt
touch heist-project/plans/strategy.conf

touch bank-heist/access-codes.txt
touch bank-heist/blueprints.pdf
touch bank-heist/escape-plan.txt
```

Create users
```bash
sudo useradd tokyo
sudo useradd berlin
sudo useradd nairobi
sudo useradd professor
```

Create groups
```bash
sudo groupadd heist-team
sudo groupadd planners
sudo groupadd vault-team
sudo groupadd tech-team
```

Change owner
```bash
sudo chown tokyo devops-file.txt
sudo chown berlin devops-file.txt
```

Change group
```bash
sudo chgrp heist-team team-notes.txt
```

Change owner and group together
```bash
sudo chown professor:heist-team project-config.yaml
sudo chown berlin:heist-team app-logs
```

Recursive ownership change
```bash
sudo chown -R professor:planners heist-project/
```

Ownership for practice challenge
```bash
sudo chown tokyo:vault-team bank-heist/access-codes.txt
sudo chown berlin:tech-team bank-heist/blueprints.pdf
sudo chown nairobi:vault-team bank-heist/escape-plan.txt
```

Verify changes
```bash
ls -l
ls -l bank-heist/
ls -lR heist-project/
```

---

## What I Learned

1. Every file and directory in Linux has an **owner (user)** and a **group**.
2. The **chown command** is used to change file ownership and can also change group ownership.
3. The **chgrp command** changes only the group of a file or directory.
4. The **-R flag** allows recursive ownership changes for directories and their contents.
5. Always verify ownership changes using the **ls -l** command.
