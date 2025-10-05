I can certainly give you the content of the restore guide as a plain response\!

Here is the complete **WordPress Local Restore Guide**:

# 🛠️ WordPress Local Restore Guide (For Arch Linux / LAMP Setup)

This guide walks you through restoring your local WordPress site (files + database) from a backup created by the `backup-wordpress.sh` script.

## ⚠️ Important Prerequisites

1.  **Stop Apache:** Ensure Apache/httpd is stopped so no PHP processes are running, preventing file-in-use errors:

    ```bash
    sudo systemctl stop httpd
    ```

2.  **Ensure MySQL is running:**

    ```bash
    sudo systemctl start mysqld
    ```

## 🎯 Step 1: Locate Your Backup

Find the full path to the backup folder you wish to restore. (Example: `~/wordpress-backups/backup-2025-10-04_12-20-15`)

```bash
BACKUP_PATH="/home/yourusername/wordpress-backups/backup-2025-10-04_12-20-15"
# Change the path above to your actual backup directory!
```

## ⚙️ Step 2: Restore the Database

We will drop the existing database and import the `db.sql` file from the backup.

### A. Define Variables

```bash
DB_NAME="wordpress"
DB_USER="root"
DB_PASS="your_mysql_password" # Use the same password as in your backup script
SQL_FILE="$BACKUP_PATH/db.sql"
```

### B. Drop and Recreate Database

Access the MySQL shell to ensure a clean slate for the import.

```bash
# Log into MySQL/MariaDB
mysql -u "$DB_USER" -p"$DB_PASS"

# In the MySQL shell, run the following three lines:
DROP DATABASE IF EXISTS $DB_NAME;
CREATE DATABASE $DB_NAME;
exit
```

### C. Import Data

Load the backed-up data into the fresh database.

```bash
echo "Importing database data..."
mysql -u "$DB_USER" -p"$DB_PASS" "$DB_NAME" < "$SQL_FILE"
echo "✅ Database restored successfully."
```

## 📂 Step 3: Restore the Files

This step assumes your WordPress root directory is `/srv/http/wordpress`. We delete the existing files and extract the compressed backup.

### A. Clean Current WordPress Directory

```bash
WP_DIR="/srv/http/wordpress"

# WARNING: This deletes the contents of your current WordPress directory!
echo "Deleting current WordPress files in $WP_DIR..."
sudo rm -rf "$WP_DIR"/*
sudo rm -rf "$WP_DIR"/.* 2>/dev/null
```

### B. Extract Backup Files

```bash
# The compressed file created by the backup script
TAR_FILE="$BACKUP_PATH/wordpress-files.tar.gz"

echo "Extracting backup files to $WP_DIR..."

# The --strip-components=1 removes the top-level 'wordpress/' folder name during extraction.
sudo tar -xzf "$TAR_FILE" -C "$WP_DIR" --strip-components=1

# Correct permissions for the http user (necessary for Arch LAMP stacks)
sudo chown -R http:http "$WP_DIR"
echo "✅ Files restored and permissions set."
```

## 🚀 Step 4: Restart Apache

Bring your local site back online and test the restoration.

```bash
sudo systemctl start httpd
```

Your WordPress site should now be fully restored to the state of the chosen backup\!