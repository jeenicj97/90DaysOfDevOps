# Day 19 – Shell Scripting Project: Log Rotation, Backup & Crontab

---

## Challenge Tasks

### Task 1: Log Rotation Script
Create `log_rotate.sh` that:
1. Takes a log directory as an argument (e.g., `/var/log/myapp`)
2. Compresses `.log` files older than 7 days using `gzip`
3. Deletes `.gz` files older than 30 days
4. Prints how many files were compressed and deleted
5. Exits with an error if the directory doesn't exist

```
!/bin/bash

set -euo pipefail

if [ $# -eq 0 ]; then
        echo "Usage: ./log_rotate.sh <log directory path>"
        exit 1
fi

log_dir=$1

if [ ! -d "$log_dir" ]; then
        echo "Error! Directory not found."
        exit 1
fi

compressed_count=$(find "$log_dir" -type f -name "*.log" -mtime +7 | wc -l)
find "$log_dir" -type f -name "*.log" -mtime +7 -exec gzip {} \;

deleted_count=$(find "$log_dir" -type f -name "*.gz" -mtime +30 | wc -l)
find "$log_dir" -type f -name "*.gz" -mtime +30 -delete

echo "==============="
echo "Log Rotation Complete"
echo "Compressed: $compressed_count"
echo "Deleted: $deleted_count"
echo "================"
```
![Image Alt]()

---

### Task 2: Server Backup Script
Create `backup.sh` that:
1. Takes a source directory and backup destination as arguments
2. Creates a timestamped `.tar.gz` archive (e.g., `backup-2026-02-08.tar.gz`)
3. Verifies the archive was created successfully
4. Prints archive name and size
5. Deletes backups older than 14 days from the destination
6. Handles errors — exit if source doesn't exist

```
#!/bin/bash

set -euo pipefail

if [ $# -ne 2 ]; then
        echo "Usage: ./sh log_rotate.sh <src_dir> <backup_dir>"
        exit 1
fi

SOURCE_DIR=$1
BACKUP_DIR=$2

if [ ! -d "$SOURCE_DIR" ]; then
        echo "Error! Source directory doesn't exist"
        exit 1
fi

mkdir -p "$BACKUP_DIR"

TIMESTAMP=$(date +%Y-%m-%d_%H-%M-%S)
ARCHIVE="$BACKUP_DIR/backup-${TIMESTAMP}.tar.gz"

tar -czf "$ARCHIVE" "$SOURCE_DIR"

if [ -f "$ARCHIVE" ]; then
        SIZE=$(du -sh "$ARCHIVE" | awk '{print $1}')
        echo "=====backup successfull======="
        echo "Archive: $ARCHIVE"
        echo "Size: $SIZE"
        echo "============================="
else
        echo "Error! Backup failed - archive not found"
        exit 1
fi


OLD_COUNT=$(find "$BACKUP_DIR" -type f -name "*.tar.gz" -mtime +14 | wc -l)

if [ "$OLD_COUNT" -gt 0 ]; then
        find "$BACKUP_DIR" -type f -name "*.tar.gz" -mtime +14 -delete
        echo "Cleaned up $OLD_COUNT old backups older than 14"
else
        echo "No Old backups to clean up"
fi
```

![Image Alt]()

---

### Task 3: Crontab
1. Read: `crontab -l` — what's currently scheduled?
2. Understand cron syntax:
   ```
   * * * * *  command
   │ │ │ │ │
   │ │ │ │ └── Day of week (0-7)
   │ │ │ └──── Month (1-12)
   │ │ └────── Day of month (1-31)
   │ └──────── Hour (0-23)
   └────────── Minute (0-59)
   ```
3. Write cron entries (in your markdown, don't apply if unsure) for:
   - Run `log_rotate.sh` every day at 2 AM
   - Run `backup.sh` every Sunday at 3 AM
   - Run a health check script every 5 minutes

---

### Task 4: Combine — Scheduled Maintenance Script
Create `maintenance.sh` that:
1. Calls your log rotation function
2. Calls your backup function
3. Logs all output to `/var/log/maintenance.log` with timestamps
4. Write the cron entry to run it daily at 1 AM

---

## What you learned (3 key points)


---
