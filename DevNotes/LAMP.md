Setting up the **LAMP** stack (**L**inux, **A**pache, **M**ariaDB/MySQL, **P**HP) on **Arch Linux** involves installing each component separately using `pacman` and then configuring them to work together.

-----

## 1\. Update the System

Always start by ensuring your system is fully updated.

```bash
sudo pacman -Syu
```

-----

## 2\. Install and Start Apache (Web Server)

Install the Apache web server package, which is named `apache` on Arch.

1.  **Install Apache:**

    ```bash
    sudo pacman -S apache
    ```

2.  **Enable and Start the Service:**

    ```bash
    sudo systemctl enable --now httpd
    ```

      * `enable`: Configures the service to start automatically on boot.
      * `--now`: Starts the service immediately.
      * The Apache service on Arch is named **`httpd`**.

3.  **Verify Apache:**

      * Open your web browser and navigate to `http://localhost`. You should see the default Apache "It works\!" page. The default web root directory is typically **`/srv/http`**.

-----

## 3\. Install and Configure MariaDB (Database)

MariaDB is the default MySQL implementation in the Arch repositories.

1.  **Install MariaDB:**

    ```bash
    sudo pacman -S mariadb
    ```

2.  **Initialize the Data Directory:**

      * You must initialize the database directories before starting the service for the first time.

    <!-- end list -->

    ```bash
    sudo mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
    ```

3.  **Enable and Start the Service:**

    ```bash
    sudo systemctl enable --now mariadb
    ```

4.  **Secure the Installation:**

      * Run the security script to set the root password and remove test users/databases. **Note:** The MariaDB root password is *not* your system root password.

    <!-- end list -->

    ```bash
    sudo mariadb-secure-installation
    ```

      * Press **Enter** for the current root password (as it's the first time), then follow the prompts to set a new root password and answer 'Y' to the security questions.

-----

## 4\. Install and Configure PHP

Install PHP and the necessary Apache module (`php-apache`).

1.  **Install PHP and Module:**

    ```bash
    sudo pacman -S php php-apache
    ```

2.  **Configure Apache to Use PHP:**

      * Edit the main Apache configuration file:

    <!-- end list -->

    ```bash
    sudo nano /etc/httpd/conf/httpd.conf
    ```

      * **A. Change the Multi-Processing Module (MPM):**
          * **Comment out** `mpm_event_module` (add `#` to the start of the line).
          * **Uncomment** `mpm_prefork_module` (remove `#` from the start of the line).
        <!-- end list -->
        ```apache
        #LoadModule mpm_event_module modules/mod_mpm_event.so
        LoadModule mpm_prefork_module modules/mod_mpm_prefork.so
        ```
      * **B. Load the PHP Module:**
          * Add the following lines to the end of the file:
        <!-- end list -->
        ```apache
        LoadModule php_module modules/libphp.so
        AddHandler php-script php
        Include conf/extra/php_module.conf
        ```
      * Save the file (`Ctrl+O`, then `Enter`) and exit (`Ctrl+X`).

3.  **Enable Database Extensions (Optional but Recommended):**

      * For PHP to interact with MariaDB, edit the main PHP configuration file:

    <!-- end list -->

    ```bash
    sudo nano /etc/php/php.ini
    ```

      * Search for the following lines and **uncomment them** (remove the `;` at the start of the line):

    <!-- end list -->

    ```ini
    ;extension=mysqli
    ;extension=pdo_mysql
    ```

4.  **Restart Apache:**

      * The web server must be restarted for all changes to take effect.

    <!-- end list -->

    ```bash
    sudo systemctl restart httpd
    ```

-----

## 5\. Test PHP

1.  **Create a Test File:**
      * Create a file named `info.php` in the web root directory (`/srv/http`).
    <!-- end list -->
    ```bash
    sudo nano /srv/http/info.php
    ```
2.  **Add PHP Code:**
      * Paste the following content:
    <!-- end list -->
    ```php
    <?php
    phpinfo();
    ?>
    ```
3.  **Verify Installation:**
      * Open your web browser and navigate to `http://localhost/info.php`. If you see the detailed PHP configuration page, the LAMP stack is correctly installed and configured.
