# Migration of Existing WordPress Site to Docker + Traefik
This guide outlines the steps to migrate an existing WordPress site to Docker + Traefik. Please follow the instructions below:

## Step 1: Export Database Dump
Export the database dump from your existing site and save it as a .sql file.

## Step 2: Update .env File
Update the .env file with the actual information. Open the file and modify the following variables:
```sh
ALIAS=my-containers-alias-name
HOST=example.com
PHPMYADMIN_DOMAIN=pma.example.com
MYSQL_ROOT_PASSWORD=N0t%aStR0Ng!PasSwoRd
WORDPRESS_DB_NAME=database_name_here
WORDPRESS_DB_USER=username_here
WORDPRESS_DB_PASSWORD=password_here
```

## Step 3: Copy Existing WordPress Content
Copy the existing WordPress content and paste it inside the www folder.

## Step 4: Fix Permissions
Execute the fix_perm.sh script to fix the permissions for the content inside the www folder.

## Step 5: Start Docker Container
Start the Docker container using docker-compose.

## Step 6: Import Database Dump
Access phpMyAdmin by navigating to pma.example.com in your web browser. Import the previously exported database dump from Step 0.

## Step 7: Update Database Credentials
Update the database credentials in the wp-config.php file located in the www folder. Modify the following lines based on the information in your .env file:
```php
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'database_name_here' );
/** MySQL database username */
define( 'DB_USER', 'username_here' );
/** MySQL database password */
define( 'DB_PASSWORD', 'password_here' );
/** MySQL hostname */
define( 'DB_HOST', 'localhost' );
```

## Step 8: (Optional) Add WP_HOME and WP_SITEURL
To facilitate easier switching between SSL and non-SSL during testing, you can add the following lines to the `wp-config.php` file:
```php
define('WP_HOME','https://example.com/');
define('WP_SITEURL','https://example.com/');
```
If you encounter any issues, change https to http in the above lines.

## Step 9: Configure Site for Reverse Proxies with SSL
To ensure proper functionality behind reverse proxies with SSL enabled, add the following code to the `wp-config.php` file:
```php
/**
 * Handle SSL reverse proxy
 */
if ($_SERVER['HTTP_X_FORWARDED_PROTO'] == 'https')
    $_SERVER['HTTPS']='on';

if (isset($_SERVER['HTTP_X_FORWARDED_HOST'])) {
    $_SERVER['HTTP_HOST'] = $_SERVER['HTTP_X_FORWARDED_HOST'];
}
```

## Step 10: (Optional) Add Redis Credentials
If you are using Redis, add the following lines to the wp-config.php file, assuming that `redis` is the container name defined in your docker-compose file:
```php
define('WP_REDIS_HOST', 'redis');
define('WP_REDIS_PORT', '6379');
```
You will need to install and activate Redis Object Cache plugin to make it work.


That's it! You have successfully migrated your existing WordPress site to Docker + Traefik.