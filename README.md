# Apache PHP MySQL - Docker for Wordpress

Docker running Apache, PHP-Apache, MySQL and PHPMyAdmin.

**THIS ENVIRONMENT SHOULD ONLY BE USED FOR DEVELOPMENT!**

**DO NOT USE IT IN PRODUCTION!**

## Images to use

* [PHP-Apache](https://hub.docker.com/r/_/php/)
* [MySQL](https://hub.docker.com/_/mysql/)
* [Composer](https://hub.docker.com/r/composer/composer/)
* [PHPMyAdmin](https://hub.docker.com/r/phpmyadmin/phpmyadmin/)
* [Generate Certificate](https://hub.docker.com/r/jacoelho/generate-certificate/)

## Start using it

1. Download it :

    ```sh
    $ git clone https://github.com/aleix20bcn/docker-apache2-php-mysql-wordpress.git
    ```

2. Configure Apache :

    Create apache file **etc/apache/mydomain.com.conf** and configure the server section.

```sh
    
    <VirtualHost *:80>
        ServerAdmin web@localhost

        ServerName mydomain.com
        ServerAlias www.mydomain.com

        DocumentRoot /CODE/mydomain.com/live/current

        ErrorLog ${APACHE_LOG_DIR}/mydomain.com_error.log

        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/mydomain.com_access.log combined

        <Directory />
                Options FollowSymLinks
        </Directory>

        <Directory /CODE/mydomain.com/live/current/>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride All
                Order allow,deny
                allow from all
                # New directive needed in Apache 2.4.3: 
                Require all granted
        </Directory>

        RewriteEngine On
    </VirtualHost>
    
```

3. Configure .env file : 

```sh
    #Domain variables
    DOMAIN_NAME=<domain_name>
    
    #Database variables
    DATABASE=<database>
    DATABASE_USER=<user_database>
    DATABASE_PASS=<supersecretpassword_database>
```

4. Copy wordpres project to /web

5. Run :

    ```sh
    $ docker-compose up -d
    ```

6. Add dump database in container :

    ```sh
    $ cat backup.sql | docker exec -i mysql /usr/bin/mysql -u root --password=root MYDATABASE
    ```

7. Configure your "/etc/hosts" file in your computer :

    ```sh
    $ 127.0.0.1       mydomain.com
    ```

8. Open your favorite browser :

    * [http://mydomain.com](http://mydomain.com/)
    * [https://mydomain.com](https://mydomain.com/) ([HTTPS](#generating-ssl-certificates) not configured by default)
    * [phpMyAdmin](http://localhost:8080/) (user: root, pass: root)

## Directory tree

```sh
├── README.md
├── bin
│   └── linux
│       └── clean.sh
├── data
│   └── db
│       └── mysql
├── docker-compose.yml
├── etc
│   ├── apache
│   │   ├── mydomain.conf
│   ├── php
│   │   └── php.ini
│   └── ssl
│       ├── cacert.pem
│       ├── server.key
│       └── server.pem
└── web
```

## Configure correct connection to Database in wp-config.php

```php
<?php
return array (
  ...
  define('DB_NAME', '<database>');

  /** Tu nombre de usuario de MySQL */
  define('DB_USER', '<user_database>');

  /** Tu contraseña de MySQL */
  define('DB_PASSWORD', '<supersecretpassword_database>');

  /** Host de MySQL (es muy probable que no necesites cambiarlo) */
  define('DB_HOST', 'mysql');
  ...
);
?>
```

## Docker Container shell access

```sh
$ docker exec -i -t CONTAINER_NAME /bin/bash
```

## Backup SQL

```sh
$ docker exec CONTAINER /usr/bin/mysqldump -u root --password=root DATABASE > backup.sql
```

## Restore SQL

```sh
$ cat backup.sql | docker exec -i CONTAINER /usr/bin/mysql -u root --password=root DATABASE
```

## Delete all docker containers

```sh
$ docker rm -f $(docker ps -aq)
```

## Delete all docker images (IMPORTANT: on new deployment docker need to download newly images)

```sh
$ docker rmi $(docker images -q)
```

## Cleaning project

```sh
$ ./bin/linux/clean.sh $(pwd)
```
