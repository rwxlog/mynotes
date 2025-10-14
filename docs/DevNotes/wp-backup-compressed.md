# 🧩 WordPress Local Backup Guide (For Arch Linux / LAMP Setup)

This guide helps you automatically back up your local WordPress site (files + database) to your home directory, using **compression** for efficiency.

-----

## 🪣 Step 1: Create the Backup Folder

```bash
mkdir -p ~/wordpress-backups
```

## ⚙️ Step 2: Create the Backup Script

Create and open the script file. Note that this script uses `tar` compression by default.

```bash
nano ~/backup-wordpress.sh
```

Paste this content inside:

```bash
#!/bin/bash

# === WordPress Local Backup Script (with compression) ===

# Configuration
WP_DIR="/srv/http/wordpress"
BACKUP_DIR="$HOME/wordpress-backups"
DB_NAME="wordpress"           # Change if your database name is different
DB_USER="root"
DB_PASS="your_mysql_password" # Set your local MySQL password (if required)

# === Timestamp and Destination ===
DATE=$(date +%F_%H-%M-%S)
DEST_DIR="$BACKUP_DIR/backup-$DATE"
COMPRESSED_FILENAME="wordpress-files.tar.gz"

# === Create backup folder ===
echo "Creating backup directory: $DEST_DIR"
mkdir -p "$DEST_DIR"

# === Backup MySQL database ===
echo "1/2 Backing up database ($DB_NAME)..."
# Note: For passwordless local MySQL, you can omit -p"$DB_PASS"
mysqldump -u "$DB_USER" -p"$DB_PASS" "$DB_NAME" > "$DEST_DIR/db.sql"

# === Backup and compress WordPress files ===
echo "2/2 Compressing and backing up WordPress files..."
# The -C flag changes the directory before creating the archive, ensuring the archive only contains the 'wordpress' folder.
tar -czf "$DEST_DIR/$COMPRESSED_FILENAME" -C "$(dirname "$WP_DIR")" "$(basename "$WP_DIR")"

# === Optional: Delete backups older than 7 days ===
echo "Cleaning up backups older than 7 days..."
find "$BACKUP_DIR" -type d -mtime +7 -exec rm -rf {} \;

echo "✅ Backup completed successfully to: $DEST_DIR"
```

## 🔐 Step 3: Make It Executable

```bash
chmod +x ~/backup-wordpress.sh
```

## 🚀 Step 4: Run It Manually

Run the script anytime:

```bash
~/backup-wordpress.sh
```

This creates a new folder like:

```
~/wordpress-backups/backup-2025-10-04_12-20-15/
```

Containing:

  * **`wordpress-files.tar.gz`** → Your full WordPress directory (compressed)
  * **`db.sql`** → Your MySQL database dump

## ⏰ Step 5 (Optional): Automate with Cron

To back up every day at 2 AM, edit your crontab:

```bash
crontab -e
```

Add this line (ensure you replace `yourusername` with your actual username):

```
0 2 * * * /home/yourusername/backup-wordpress.sh
```

✅ Done\! You now have a reliable, compressed local backup system.