# Docker Linux + Apache + PHP
This is an unofficial, open-source for Apache + PHP based projects that run on Docker-Compose. 

    Debian Stretch
    Apache 2.4.25
    PHP 5.6.33

## Database extensions enabled

    Firebird
    MS SQL
    MySQL
    ODBC
    Oracle
    PostgreSQL
    Redis
    SQLite3
    Sybase

## Modules enabled

    php5.6-bcmath     - Bcmath module for PHP
    php5.6-bz2        - bzip2 module for PHP
    php5.6-curl       - CURL module for PHP
    php5.6-dba        - DBA module for PHP
    php5.6-enchant    - Enchant module for PHP
    php5.6-gd         - GD module for PHP
    php5.6-gmp        - GMP module for PHP
    php5.6-imap       - IMAP module for PHP
    php5.6-interbase  - Interbase module for PHP
    php5.6-intl       - Internationalisation module for PHP
    php5.6-json       - JSON module for PHP
    php5.6-ldap       - LDAP module for PHP
    php5.6-mbstring   - MBSTRING module for PHP
    php5.6-mcrypt     - MCrypt module for php
    php5.6-mysql      - MySQL module for PHP
    php5.6-odbc       - ODBC module for PHP
    php5.6-opcache    - Zend OpCache module for PHP
    php5.6-pgsql      - PostgreSQL module for PHP
    php5.6-pspell     - pspell module for PHP
    php5.6-readline   - readline module for PHP
    php5.6-recode     - recode module for PHP
    php5.6-snmp       - SNMP module for PHP
    php5.6-soap       - SOAP module for PHP
    php5.6-sqlite3    - SQLite3 module for PHP
    php5.6-sybase     - Sybase module for PHP
    php5.6-tidy       - tidy module for PHP
    php5.6-xdebug     - Xdebug module for PHP
    php5.6-xml        - DOM, SimpleXML, WDDX, XML, and XSL module for PHP
    php5.6-xmlrpc     - XMLRPC-EPI module for PHP
    php5.6-xsl        - XSL module for PHP
    php5.6-zip        - Zip module for PHP 

## Usage
You are up and running in two simple steps:

    $ cd docker-ldap/5.6
    $ docker-compose up --build -d

Stopping execution in two simple steps:

    $ cd docker-ldap/5.6
    $ docker-compose down

You can just open your browser at:

    http://localhost

## Setting Volumes
Below is the volume example for the apache configuration:

    volumes:
        # volume for system files
        - /home/luis/www/html:/var/www/html
        # volume for SSL key files
        - /home/luis/www/ssl:/etc/apache2/ssl
        # volume for vhosts configuration environments
        - /home/luis/www/sites-enabled:/etc/apache2/sites-enabled