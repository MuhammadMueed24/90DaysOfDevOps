# Linux Maintenance Automation (All Scripts)

This file contains all scripts and cron configuration in one place.

---

## 1. Log Rotation Script (`log_rotate.sh`)

```bash
#!/bin/bash
LOG_DIR=$1
if [ -z "$LOG_DIR" ]; then
    echo "Error: please provide a log directory."
    exit 1
fi
if [ ! -d "$LOG_DIR" ]; then
    echo "Error: Directory does not exist"
    exit 1
fi
echo "Directory exists: $LOG_DIR"
echo "compressing .log files older then 7 days..."
COMPRESSED_COUNT=0
for file in $(find "$LOG_DIR" -type f -name "*.log" -mtime +7 ); do
    gzip "$file"
    ((COMPRESSED_COUNT++))
done
echo "Compressed files: $COMPRESSED_COUNT"
echo "deleting .gz files older than 30 days"
DELETED_COUNT=0
for file in $(find "$LOG_DIR" -type f -name "*.gz" -mtime +30); do
    rm "$file"
    ((DELETED_COUNT++))
done
echo "Deleted files: $DELETED_COUNT"
echo "-----------------------------"
echo "Rotation Summary:"
echo "Compressed: $COMPRESSED_COUNT"
echo "Deleted: $DELETED_COUNT"
```

---

## 2. Backup Script (`backup.sh`)

```bash
#!/bin/bash
SOURCE_DIR=$1
BACKUP_DIR=$2
if [ -z "$SOURCE_DIR" ] || [ -z "$BACKUP_DIR" ]; then
    echo "Usage: $0 <source_directory> <backup_directory>"
    exit 1
fi
if [ ! -d "$SOURCE_DIR" ]; then
    echo "Error: source directory does not exist"
    exit 1 
fi
if [ ! -d "$BACKUP_DIR" ]; then
    mkdir -p "$BACKUP_DIR"
fi
echo "Source: $SOURCE_DIR"
echo "Backup destination: $BACKUP_DIR"
TIMESTAMP=$(date +%F)
BACKUP_FILE="backup-$TIMESTAMP.tar.gz"
BACKUP_PATH="$BACKUP_DIR/$BACKUP_FILE"
echo "Backup file will be: $BACKUP_FILE"
echo "creating backup..."
tar -czf "$BACKUP_PATH" -C "$SOURCE_DIR"
if [ $? -ne 0 ]; then
    echo "Error: Backup failed."
    exit 1
fi
if [ ! -f "$BACKUP_PATH" ]; then
    echo "Error: Backup file not found."
    exit 1
fi
FILE_SIZE=$(du -h "$BACKUP_PATH" | cut -f1)
echo "Backup created successfully!"
echo "File: $BACKUP_FILE"
echo "Size: $FILE_SIZE"
echo "Cleaning up old backups (older than 14 days)..."
DELETED_COUNT=0
while IFS= read -r -d '' file; do
    rm "$file"
    ((DELETED_COUNT++))
done < <(find "$BACKUP_DIR" -type f -name "backup-*.tar.gz" -mtime +14 -print0)
echo "Old backups deleted: $DELETED_COUNT"
echo "-----------------------------"
echo "Backup Summary:"
echo "Created: $BACKUP_FILE"
echo "Size: $FILE_SIZE"
echo "Deleted old backups: $DELETED_COUNT"
```

---

## 3. Maintenance Script (`maintenance.sh`)

```bash
#!/bin/bash
LOG_FILE="/var/log/maintenance.log"
echo "===================================" >> "$LOG_FILE"
echo "Maintenance started: $(date)" >> "$LOG_FILE"
echo "===================================" >> "$LOG_FILE"
echo "[INFO] Running log rotation..." >> "$LOG_FILE"
/bin/bash /home/ubuntu/log_rotate.sh >> "$LOG_FILE" 2>&1
echo "[INFO] Running backup..." >> "$LOG_FILE"
/bin/bash /home/ubuntu/backup_rotate.sh >> "$LOG_FILE" 2>&1
echo "[INFO] Maintenance finished: $(date)" >> "$LOG_FILE"
echo "===================================" >> "$LOG_FILE"
echo "" >> "$LOG_FILE"
```

---

## 4. Crontab

```bash
# Run maintenance daily at 1:00 AM
0 1 * * * /bin/bash /home/ubuntu/maintenance.sh >> /var/log/maintenance_cron.log 2>&1
```
