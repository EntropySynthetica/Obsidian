---
tags: [Database, Linux, MySQL]
title: MSSQL_Driver_for_PHP_7.x
created: '2020-01-30T20:16:15.803Z'
modified: '2022-08-25T21:57:21.262Z'
---

# MSSQL Driver for PHP 7.x
Created Thursday 03 January 2019


To install PHP 7.4 or 8.0, replace 8.1 with 7.4 or 8.0 in the following commands.


## Install MS SQL Tools 

Ref: https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup-tools?view=sql-server-ver16#ubuntu

Import Public Repo GPG Key
```bash
curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

Register the Repo

```bash
curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list | sudo tee /etc/apt/sources.list.d/msprod.list
```

Update Apt and Install
```bash
sudo apt-get update 
sudo apt-get install mssql-tools unixodbc-dev
```



## Test DB Connection with 

```bash
/opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P yourpassword -Q "SELECT @@VERSION"
```


## Next Install the PHP drivers

Ref: https://docs.microsoft.com/en-us/sql/connect/php/installation-tutorial-linux-mac?view=sql-server-ver16#installing-on-ubuntu

Install PHP Pre-Req

```bash
apt-get install php8.1-dev php8.1-xml
```

Install PHP Drivers for MSSQL
```bash
sudo mount -o remount,exec /tmp/
sudo pecl install sqlsrv
sudo pecl install pdo_sqlsrv
sudo mount -o remount,noexec /tmp/
sudo -s -E
printf "; priority=20\nextension=sqlsrv.so\n" > /etc/php/8.1/mods-available/sqlsrv.ini printf "; priority=30\nextension=pdo_sqlsrv.so\n" > /etc/php/8.1/mods-available/pdo_sqlsrv.ini
exit
sudo phpenmod -v 8.1 sqlsrv pdo_sqlsrv
```


# PHP Test Script
### Basic Test Script

```
vi connect.php
```


```php
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

```bash
php connect.php
```


### SQLSRV Test Script
```PHP
<?php
$serverName = "yourServername";
$connectionOptions = array(
    "database" => "yourDatabase",
    "uid" => "yourUsername",
    "pwd" => "yourPassword"
);

function exception_handler($exception) {
    echo "<h1>Failure</h1>";
    echo "Uncaught exception: " , $exception->getMessage();
    echo "<h1>PHP Info for troubleshooting</h1>";
    phpinfo();
}

set_exception_handler('exception_handler');

// Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if ($conn === false) {
    die(formatErrors(sqlsrv_errors()));
}

// Select Query
$tsql = "SELECT @@Version AS SQL_VERSION";

// Executes the query
$stmt = sqlsrv_query($conn, $tsql);

// Error handling
if ($stmt === false) {
    die(formatErrors(sqlsrv_errors()));
}
?>

<h1> Success Results : </h1>

<?php
while ($row = sqlsrv_fetch_array($stmt, SQLSRV_FETCH_ASSOC)) {
    echo $row['SQL_VERSION'] . PHP_EOL;
}

sqlsrv_free_stmt($stmt);
sqlsrv_close($conn);

function formatErrors($errors)
{
    // Display errors
    echo "<h1>SQL Error:</h1>";
    echo "Error information: <br/>";
    foreach ($errors as $error) {
        echo "SQLSTATE: ". $error['SQLSTATE'] . "<br/>";
        echo "Code: ". $error['code'] . "<br/>";
        echo "Message: ". $error['message'] . "<br/>";
    }
}
?>
```

### PDO_SQLSRV Test Script

```PHP
<?php
try {
    $serverName = "yourServername";
    $databaseName = "yourDatabase";
    $uid = "yourUsername";
    $pwd = "yourPassword";
    
    $conn = new PDO("sqlsrv:server = $serverName; Database = $databaseName;", $uid, $pwd);

    // Select Query
    $tsql = "SELECT @@Version AS SQL_VERSION";

    // Executes the query
    $stmt = $conn->query($tsql);
} catch (PDOException $exception1) {
    echo "<h1>Caught PDO exception:</h1>";
    echo $exception1->getMessage() . PHP_EOL;
    echo "<h1>PHP Info for troubleshooting</h1>";
    phpinfo();
}

?>

<h1> Success Results : </h1>

<?php
try {
    while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
        echo $row['SQL_VERSION'] . PHP_EOL;
    }
} catch (PDOException $exception2) {
    // Display errors
    echo "<h1>Caught PDO exception:</h1>";
    echo $exception2->getMessage() . PHP_EOL;
}

unset($stmt);
unset($conn);
?>
```