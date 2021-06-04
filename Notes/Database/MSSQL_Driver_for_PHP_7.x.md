---
tags: [Database, Linux, MySQL]
title: MSSQL_Driver_for_PHP_7.x
created: '2020-01-30T20:16:15.803Z'
modified: '2020-02-27T21:57:21.262Z'
---

# MSSQL Driver for PHP 7.x
Created Thursday 03 January 2019

Ref Page:
<https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu/>


## Install MS SQL Tools 

```
sudo -s -E
curl <https://packages.microsoft.com/keys/microsoft.asc> | apt-key add -
curl <https://packages.microsoft.com/config/ubuntu/16.04/prod.list> > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql17 mssql-tools
```
Verify PHP version with (php -v) Place that version in the php7.x-dev install in the next command. 

`sudo apt-get install unixodbc-dev php-pear libcurl3-openssl-dev php7.x-dev`


## Test DB Connection with 


`/opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P yourpassword -Q "SELECT @@VERSION"`


## Next Install the PHP drivers

```
sudo mount -o remount,exec /tmp/
sudo pecl install sqlsrv
sudo pecl install pdo_sqlsrv
sudo mount -o remount,noexec /tmp/
sudo -s -E
echo extension=pdo_sqlsrv.so >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/30-pdo_sqlsrv.ini
echo extension=sqlsrv.so >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/20-sqlsrv.ini
exit
```


# PHP Test Script

```
vi connect.php
	<?php
		$serverName = "localhost";
		$connectionOptions = array(
			"Database" => "SampleDB",
			"Uid" => "sa",
			"PWD" => "your_password"
		);
		//Establishes the connection
		$conn = sqlsrv_connect($serverName, $connectionOptions);
		if($conn)
			echo "Connected!"
	?>
```

`php connect.php`



