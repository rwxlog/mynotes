Yes, I'd be happy to show you how to automate your WordPress backups using a **Bash script** and **Cron**\! 🤖

This setup runs silently in the background and saves your backups to the destination you already established (`~/wordpress-backups`).

-----

## 1\. Create the Backup Script

First, we'll create a script file (e.g., `wp-backup.sh`) in your home directory or a dedicated scripts folder.

### **The Script (`~/wp-backup.sh`):**

```bash
#!/bin/bash

# Configuration
BACKUP_DIR="/home/YOUR_USER/wordpress-backups" # <-- Update 'YOUR_USER'
WORDPRESS_DIR="/srv/http/wordpress"
DATABASE_NAME="wordpress"                      # <-- Update if needed
DB_USER="root"
DATE=$(date +%F)

# 1. Create the backup directory if it doesn't exist
mkdir -p $BACKUP_DIR

# 2. Export the Database
# NOTE: This will prompt you for the DB root password when run manually.
# For cron, we'll use a .my.cnf file (see Step 3)
mysqldump -u $DB_USER -p $DATABASE_NAME > $BACKUP_DIR/db-$DATE.sql

# 3. Copy WordPress Files
cp -r $WORDPRESS_DIR $BACKUP_DIR/site-$DATE

# 4. Clean up old backups (optional but recommended)
# This keeps the last 7 daily backups
find $BACKUP_DIR -type d -name "site-*" -mtime +7 -exec rm -rf {} \;
find $BACKUP_DIR -type f -name "db-*.sql" -mtime +7 -delete

echo "WordPress backup completed for $DATE."
```

### **Make it Executable:**

Save the content above and make the script runnable:

```bash
chmod +x ~/wp-backup.sh
```

-----

## 2\. Set Up Passwordless Database Access

For the script to run automatically via Cron, it **cannot** prompt for the MySQL password. The safest way to handle this is using a **MySQL configuration file** (`.my.cnf`).

1.  **Create/Edit the file:**

    ```bash
    nano ~/.my.cnf
    ```

2.  **Add the credentials:**

    ```ini
    [mysqldump]
    user=root
    password=YOUR_MYSQL_ROOT_PASSWORD # <-- Put your actual password here!

    [mysql]
    user=root
    password=YOUR_MYSQL_ROOT_PASSWORD # <-- Put your actual password here!
    ```

3.  **Secure the file:** This file contains a plain-text password, so restrict access immediately:

    ```bash
    chmod 600 ~/.my.cnf
    ```

-----

## 3\. Schedule the Backup with Cron

Cron is a job scheduler that runs commands at specified times. We'll use it to run the script daily.

1.  **Open the crontab editor:**

    ```bash
    crontab -e
    ```

2.  **Add the job:** Paste this line at the bottom. It schedules the script to run **every day at 3:00 AM**.

    ```cron
    # m h dom mon dow command
    0 3 * * * /home/YOUR_USER/wp-backup.sh # <-- Update 'YOUR_USER'
    ```

      * `0`: Minute (0)
      * `3`: Hour (3 AM)
      * `*`: Day of the month (Every day)
      * `*`: Month (Every month)
      * `*`: Day of the week (Every day)

The backups will now run automatically\! You can check the `~/wordpress-backups/` directory the next morning.