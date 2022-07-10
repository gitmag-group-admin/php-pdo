# PHP PDO

PHP PDO is a database access layer that provides a uniform interface for working with multiple databases.

PDO simplifies the common database operations including:

-   Creating database connections
-   Executing queries using prepared statements
-   Calling stored procedures
-   Performing transactions
-   And handling errors

PDO allows you to work with any database that has a PDO driver available. PDO relies on database-specific drivers, e.g., PDO_MYSQL for MySQL, PDO_PGSQL for PostgreSQL, PDO_OCI for Oracle database, etc., to function properly.

Therefore, to use PDO for a specific database, you need to have a corresponding database driver available.

The following diagram illustrates how PDO works:

![enter image description here](https://raw.githubusercontent.com/gitmag-group-admin/php-pdo/d9c7bea6ffc657a9ebb1de37ff33f8625dc70d6b/image/php-pdo.svg)

## Connecting to MySQL
### Prerequisites

Before connecting to a MySQL database server, you need to have:

-   A MySQL database server, a database, and an account that has access to the database.
-   PDO MySQL driver enabled in the php.ini file

### 1) Setting MySQL database parameters

Suppose you have a local MySQL database server that has the following information:

-   The host is `localhost`.
-   The `bookdb` database on the local database server.
-   The account with the user `root` and password `'S@cr@t1!'` that can access the `bookdb` database.

```php
$host = 'localhost';
$db = 'bookdb';
$user = 'root';
$password = 'S@cr@t1!';
```

### 2) Enable PDO_MySQL Driver

PDO_MYSQL is a driver that implements the PDO interface. PDO uses the PDO_MYSQL driver to connect to a MySQL database.

To check if the PDO_MYSQL driver is enabled, you open the `php.ini` file. The `php.ini` file is often located under the `php` directory
The following shows the extension line in the `php.ini` file:
```
;extension=php_pdo_mysql.dll
```

To enable the extension, you need to uncomment it by removing the semicolon (`;`) from the beginning of the line like this:

```
extension=php_pdo_mysql.dll
```
After that, you need to restart the web server for the change to take effect.

### MySQL data source name

PDO uses a data source name (DSN) that contains the following information:

-   The database server host
-   The database name
-   The user
-   The password
-   and other parameters such as character sets, etc.

PDO uses this information to make a connection to the database server. To connect to the MySQL database server, you use the following data source name format:

```php
$dsn = "mysql:host=localhost;dbname=bookdb;charset=UTF8";
```

> Note that the charset UTF-8 sets the character set of the database connection to UTF-8.

### Connecting to MySQL

The following `index.php` script illustrates how to connect to the `bookdb` database on the MySQL database server with the `root` account:

```php
$host = 'localhost';
$db = 'bookdb';
$user = 'root';
$password = 'S@cr@t1!';
$dsn = "mysql:host=$host;dbname=$db;charset=UTF8";

try {
	$pdo = new PDO($dsn, $user, $password);

	if ($pdo) {
		echo "Connected to the $db database successfully!";
	}
} catch (PDOException $e) {
	echo $e->getMessage();
}
```
How it works.

-   First, create a new PDO object with the data source name, user, and password. The PDO object is an instance of the `PDO` class.
-   Second, show the success message if the connection is established successfully or an error message if an error occurs.

If you have everything set up correctly, you will see the following message:
```
Connected to the bookdb database successfully!
```

### Error handling strategies

PDO supports three different error handling strategies:

-   `PDO::ERROR_SILENT` – PDO sets an error code for inspecting using the `PDO::errorCode()` and `PDO::errorInfo()` methods. The `PDO::ERROR_SILENT` is the default mode.
-   `PDO::ERRMODE_WARNING` – Besides setting the error code, PDO will issue an `E_WARNING` message.
-   `PDO::ERRMODE_EXCEPTION` – Besides setting the error code, PDO will raise a `PDOException`.

To set the error handling strategy, you can pass an associative array to the PDO constructor like this:

```php
$pdo = new PDO($dsn, $user, $password, [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION]);
```

Or you can use the `setAttribute()` method of the PDO instance:

```php
$pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
```

### Troubleshooting
There are some common issues when you connect to a MySQL database:

If the MySQL driver is not enabled in the `php.ini` file, you will get the error message:

```
could not find driver
```


If you provide an incorrect password, you get the following error message:

```
SQLSTATE[HY000] [1045] Access denied for user 'root'@'localhost' (using password: YES)
```

If you provide an invalid database name or the database does not exist, you get the following error message:

```
SQLSTATE[HY000] [1049] Unknown database 'bookdb'
```

If you provide an invalid database hostname, the following error message will display:

```
SQLSTATE[HY000] [2002] php_network_getaddresses: getaddrinfo failed: No such ho
```

### Connecting to the bookdb database from PHP

create a new `connect.php` file that connects to the `bookdb` database:

```php
$host     = 'localhost';
$db       = 'bookdb';
$user     = 'root';
$password = '';
$dsn = "mysql:host=$host;dbname=$db;charset=UTF8";

try {
	$options = [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION];
	$conn = new PDO($dsn, $user, $password, $options);

	if ($conn) {
		echo "Connected to the $db database successfully!";
	}
} catch (PDOException $e) {
	echo $e->getMessage();
}
```

To make it more reusable, you can define a function called `connect()` that returns a new database connection and return it from the `connect.php` file:

```php
$host     = 'localhost';
$db       = 'bookdb';
$user     = 'root';
$password = '';

function connect($host, $db, $user, $password) {
	$dsn = "mysql:host=$host;dbname=$db;charset=UTF8";

	try {
		$options = [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION];

		return new PDO($dsn, $user, $password, $options);
	} catch (PDOException $e) {
		die($e->getMessage());
	}
}

return connect($host, $db, $user, $password);
```

```php
$pdo = require 'connect.php';
var_dump($pdo);
```

### Using the class-based approach

To use a [class](https://www.phptutorial.net/php-oop/php-objects/) instead of a [function](https://www.phptutorial.net/php-tutorial/php-functions/) to create a new database connection, you can follow these steps:

First, create a new file called `Connection.php` and define the `Connection` class:

```php
$host     = 'localhost';
$db       = 'bookdb';
$user     = 'root';
$password = '';

class Connection {
	public static function make($host, $db, $user, $password) {
		$dsn = "mysql:host=$host;dbname=$db;charset=UTF8";

		try {
			$options = [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION];

			return new PDO($dsn, $username, $password, $options);
		} catch (PDOException $e) {
			die($e->getMessage());
		}
	}
}

return Connection::make($host, $db, $user, $password);
```

The `Connection` class has the `make()` method that returns a new instance of PDO.

Second, use the `Connection.php` file in other script files as follows:

```php
$pdo = require 'Connection.php';
var_dump($pdo);
```

In this tutorial, you have learned how to create the `bookdb` database in the MySQL server and developed a reusable script for connecting the database.

## CRUD using PDO

### The steps for inserting a row into a table

To insert a row into a table, you follow these steps:

-   First, connect to the database by creating a new `PDO` object.
-   Second, construct the `INSERT` statement. If you need to pass a value to the `INSERT` statement, you can use the placeholders in the format `:parameter`. Later, you can substitute the `parameter` by its value.
-   Third, create a prepared statement by calling the `prepare()` method of the PDO object. The `prepare()` method returns an instance of the `PDOStatement` class.
-   Finally, call the `execute()` method of the prepared statement and pass the values.

### Inserting a row into a table example

```php
$pdo = require_once 'connect.php';

$name = 'Macmillan';
$sql = 'INSERT INTO publishers(name) VALUES(:name)';

$statement = $pdo->prepare($sql);

$statement->execute([
	':name' => $name
]);

$publisher_id = $pdo->lastInsertId();

echo 'The publisher id ' . $publisher_id . ' was inserted';
```

### Updating data from PHP using PDO

To update data in a table from PHP using PDO, you follow these steps:

-   First, connect to the database by creating a new instance of the `PDO` class.
-   Next, construct an SQL `UPDATE` statement to update data in a table.
-   Then, call the `prepare()` method of the PDO object. The `prepare()` method returns a new instance of the `PDOStatement` class.
-   After that, bind the values to the `UPDATE` statement by calling the `bindParam()` method of the `PDOStatement` object.
-   Finally, execute the statement by calling the `execute()` method of the `PDOStatement`.

### PHP PDO update example

```php
$pdo = require_once 'connect.php';

$publisher = [
	'publisher_id' => 1,
	'name' => 'McGraw-Hill Education'
];

$sql = 'UPDATE publishers
        SET name = :name
        WHERE publisher_id = :publisher_id';

// prepare statement
$statement = $pdo->prepare($sql);

// bind params
$statement->bindParam(':publisher_id', $publisher['publisher_id'], PDO::PARAM_INT);
$statement->bindParam(':name', $publisher['name']);

// execute the UPDATE statment
if ($statement->execute()) {
	echo 'The publisher has been updated successfully!';
}
```

### PDO Querying Data

`query()` method of the `PDO` object and a prepared statement.

To select data from a table using PDO, you can use:

-   The `query()` method of a PDO object.
-   Or a prepared statement.

When a query doesn’t have any parameters, you can use the `query()` method. For example:

```sql
SELECT * FROM publishers;
```

However, if a query accepts one or more parameters, you should use a prepared statement for security reasons.

#### Using the query() method to select data from a table

To query data from a table using the `query()` method, you follow these steps:

1.  Create a database connection to the database server.
2.  Execute a `SELECT` statement by passing it to the `query()` method of a `PDO` object.

The `query()` method returns a `PDOStatement` object. If an error occurs, the `query()` method returns `false`.

```php
$pdo = require 'connect.php';

$sql = 'SELECT publisher_id, name 
		FROM publishers';

$statement = $pdo->query($sql);

// get all publishers
$publishers = $statement->fetchAll(PDO::FETCH_ASSOC);

if ($publishers) {
	// show the publishers
	foreach ($publishers as $publisher) {
		echo $publisher['name'] . '<br>';
	}
}
```
When you use the `PDO::FETCH_ASSOC` mode, the `PDOStatement` returns an associative of elements in which the key of each element is the column name of the result set.

#### Using a prepared statement to query data
The following example illustrates how to use a prepared statement to query data from a table:

```php
$publisher_id = 1;

// connect to the database and select the publisher
$pdo = require 'connect.php';

$sql = 'SELECT publisher_id, name 
		FROM publishers
        WHERE publisher_id = :publisher_id';

$statement = $pdo->prepare($sql);
$statement->bindParam(':publisher_id', $publisher_id, PDO::PARAM_INT);
$statement->execute();
$publisher = $statement->fetch(PDO::FETCH_ASSOC);

if ($publisher) {
	echo $publisher['publisher_id'] . '.' . $publisher['name'];
} else {
	echo "The publisher with id $publisher_id was not found.";
}

```

### PHP PDO Delete
To delete one or more rows from a table, you can use a prepared statement. Here are the steps:

-   First, make a connection to the database by creating a new instance of the PDO class.
-   Next, construct a `DELETE` statement.
-   Then, create a prepared statement by calling the `prepare()` method of the PDO instance.
-   After that, bind the parameters, if any, using the `bindParam()` method.
-   Finally, execute the `DELETE` statement by calling the `execute()` method of the prepared statement.

#### Delete one row from a table
The following example illustrates how to use a prepared statement to delete the publisher with id 1 from the `publishers` table:

```php
$publisher_id = 1;

// connect to the database and select the publisher
$pdo = require 'connect.php';

// construct the delete statement
$sql = 'DELETE FROM publishers
        WHERE publisher_id = :publisher_id';

// prepare the statement for execution
$statement = $pdo->prepare($sql);
$statement->bindParam(':publisher_id', $publisher_id, PDO::PARAM_INT);

// execute the statement
if ($statement->execute()) {
	echo 'publisher id ' . $publisher_id . ' was deleted successfully.';
}
```

#### Delete multiple rows from a table
Deleting multiple rows from the table is the same as the steps for deleting one row from a table.
To find the number of rows deleted, you use the `rowCount()` method of the `PDOStatement` object.
The following example shows how to delete publishers with an id greater than 3:

```php
$publisher_id = 3;

// connect to the database and select the publisher
$pdo = require 'connect.php';

$sql = 'DELETE FROM publishers
        WHERE publisher_id > :publisher_id';

$statement = $pdo->prepare($sql);
$statement->bindParam(':publisher_id', $publisher_id, PDO::PARAM_INT);

if ($statement->execute()) {
	echo $statement->rowCount() . ' row(s) was deleted successfully.';
}
```
