Creating the WordPress database on an **Arch Linux LAMP stack** (using Apache, MariaDB, and PHP) is done through the MariaDB console. Here's a step-by-step guide:

-----

## 1\. Ensure MariaDB Service is Running

Before you can create a database, the MariaDB service must be running.

```bash
sudo systemctl start mariadb
```

If you haven't secured your MariaDB installation, it's highly recommended to run the security script:

```bash
sudo mysql_secure_installation
```

-----

## 2\. Access the MariaDB Console

Log into the MariaDB console as the root user. If you set a root password during the security installation, use the first command; otherwise, you might be able to use the second.

```bash
mysql -u root -p
# You will be prompted for the MariaDB root password.
```

*OR*

```bash
sudo mariadb
# This might work if your root user is authenticated via Unix socket.
```

-----

## 3\. Create the Database

Once you see the `MariaDB [(none)]>` prompt, run the following command to create your dedicated database. We'll name it `wordpress_db`.

```sql
CREATE DATABASE wordpress_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

**Note:** `utf8mb4_unicode_ci` is the recommended character set and collation for modern WordPress installations.

-----

## 4\. Create a Dedicated Database User

It's a security best practice to create a dedicated user for your WordPress site instead of using the MariaDB root user. Replace `'wpuser'` and `'your_strong_password'` with your desired username and a strong password.

```sql
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'your_strong_password';
```

-----

## 5\. Grant Privileges

Now, grant the newly created user full privileges (read, write, etc.) on the new database.

```sql
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wpuser'@'localhost';
```

-----

## 6\. Apply Changes and Exit

Flush the privileges to ensure MariaDB reloads the permission tables, and then exit the console.

```sql
FLUSH PRIVILEGES;
EXIT;
```

### Summary of Credentials

Keep these credentials handy, as you will need to enter them into the **`wp-config.php`** file for your WordPress installation:

| Setting | Value (Example) |
| :--- | :--- |
| **Database Name (`DB_NAME`)** | `wordpress_db` |
| **Database User (`DB_USER`)** | `wpuser` |
| **Database Password (`DB_PASSWORD`)** | `your_strong_password` |
| **Database Host (`DB_HOST`)** | `localhost` |