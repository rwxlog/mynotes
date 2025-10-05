Setting up **phpMyAdmin** on Arch Linux involves installing the package, configuring the necessary PHP extensions, and correctly configuring the **Apache** web server to access the application.

This guide assumes you have a functional LAMP stack (Apache, MariaDB/MySQL, and PHP) already installed on your Arch Linux system.

## 1\. Install phpMyAdmin and Required PHP Modules

Use the `pacman` package manager to install the phpMyAdmin package along with essential PHP extensions required for database connectivity and functionality.

```bash
sudo pacman -S phpmyadmin php-apache php-mysqli php-gd php-intl php-json php-mbstring
```

  * **`phpmyadmin`**: The core application files.
  * **`php-apache`**: The PHP module for the Apache web server (`mod_php`).
  * **`php-mysqli`**: Essential for connecting PHP to MariaDB/MySQL.
  * The other modules are required for various features like image manipulation (`php-gd`), internationalization (`php-intl`), and multi-byte string support (`php-mbstring`).

-----

## 2\. Configure PHP Modules

You need to ensure the required extensions are enabled in your main PHP configuration file.

1.  Open the PHP configuration file (usually located at `/etc/php/php.ini`):
    ```bash
    sudo nano /etc/php/php.ini
    ```
2.  Search for the following lines and **uncomment** them by removing the semicolon (`;`) at the beginning:
    ```ini
    ;extension=mysqli
    ;extension=iconv
    ;extension=mbstring
    ;extension=gd
    ```
    They should look like this after editing:
    ```ini
    extension=mysqli
    extension=iconv
    extension=mbstring
    extension=gd
    ```
3.  Save the file and exit the editor.

-----

## 3\. Configure Apache for phpMyAdmin

Apache needs an **alias** configuration to know where the phpMyAdmin files are located on the filesystem (`/usr/share/webapps/phpMyAdmin`) and what URL alias to use (e.g., `/phpmyadmin`).

1.  Create a new Apache configuration file for phpMyAdmin:
    ```bash
    sudo nano /etc/httpd/conf/extra/phpmyadmin.conf
    ```
2.  Add the following content to the new file:
    ```apache
    Alias /phpmyadmin "/usr/share/webapps/phpMyAdmin"

    <Directory "/usr/share/webapps/phpMyAdmin">
        DirectoryIndex index.php
        AllowOverride All
        Options FollowSymlinks
        Require all granted
    </Directory>
    ```
3.  **Include** this new configuration file in Apache's main configuration. Open `/etc/httpd/conf/httpd.conf`:
    ```bash
    sudo nano /etc/httpd/conf/httpd.conf
    ```
4.  Scroll to the very end of the file and add this line:
    ```apache
    # phpMyAdmin configuration
    Include conf/extra/phpmyadmin.conf
    ```

-----

## 4\. Finalize Configuration and Restart Services

1.  **Restart Apache** to load the new configuration:
    ```bash
    sudo systemctl restart httpd
    ```
2.  If the PHP-FPM service is running (instead of `mod_php`), restart it as well, although the `php-apache` package suggests you are using `mod_php`.
    ```bash
    # sudo systemctl restart php-fpm  # (Optional, only if using PHP-FPM)
    ```

-----

## 5\. Access phpMyAdmin

Open your web browser and navigate to your server's address followed by the alias:

`http://localhost/phpmyadmin` (if on the same machine)
or
`http://your_server_ip/phpmyadmin`

You should see the phpMyAdmin login screen. Log in using your MariaDB/MySQL root user or another privileged database user.

