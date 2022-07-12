# PHP PDO

 - [What is PDO](https://github.com/gitmag-group-admin/php-pdo/blob/main/README.md#what-is-pdo)
 - [Connecting to MySQL](https://github.com/gitmag-group-admin/php-pdo/blob/main/README.md#connecting-to-mysql)
 - [CRUD using PDO](https://github.com/gitmag-group-admin/php-pdo/blob/main/README.md#crud-using-pdo)
- [fetch](https://github.com/gitmag-group-admin/php-pdo/blob/main/README.md#fetch)
- [fetchAll](https://github.com/gitmag-group-admin/php-pdo/blob/main/README.md#fetchall)
- [fetchObject](https://github.com/gitmag-group-admin/php-pdo/blob/main/README.md#fetchobject)
- [fetchColumn](https://github.com/gitmag-group-admin/php-pdo/blob/main/README.md#fetchcolumn)
- [PDO FETCH_GROUP](https://github.com/gitmag-group-admin/php-pdo/blob/main/README.md#pdo-fetch_group)
- [PDO FETCH_KEY_PAIR](PDO%20FETCH_KEY_PAIR)
- [PDO FETCH_CLASS](https://github.com/gitmag-group-admin/php-pdo/blob/main/README.md#pdo-fetch_class)
- [Transaction](https://github.com/gitmag-group-admin/php-pdo/blob/main/README.md#php-pdo-transaction)

## What is PDO
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
$db = 'blog';
$user = 'root';
$password = 'P@ssW0rd';
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
$dsn = "mysql:host=localhost;dbname=blog;charset=UTF8";
```

> Note that the charset UTF-8 sets the character set of the database connection to UTF-8.

### Connecting to MySQL

The following `index.php` script illustrates how to connect to the `bookdb` database on the MySQL database server with the `root` account:

```php
$host = 'localhost';
$db = 'blog';
$user = 'root';
$password = 'P@ssW0rd';
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
$db       = 'blog';
$user     = 'root';
$password = 'P@ss@0rd';
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
$db       = 'blog';
$user     = 'root';
$password = 'P@ssW0rd';

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
$db       = 'blog';
$user     = 'root';
$password = 'P@ssW0rd';

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

$name = 'Economy';
$sql = 'INSERT INTO categories(name) VALUES(:name)';

$statement = $pdo->prepare($sql);

$statement->execute([
	':name' => $name
]);

$category_id = $pdo->lastInsertId();

echo 'The category id ' . $category_id. ' was inserted';
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
	'id' => 5,
	'name' => 'world'
];

$sql = 'UPDATE categories
        SET name = :name
        WHERE id = :category_id';

// prepare statement
$statement = $pdo->prepare($sql);

// bind params
$statement->bindParam(':category_id', $category['id'], PDO::PARAM_INT);
$statement->bindParam(':name', $category['name']);

// execute the UPDATE statment
if ($statement->execute()) {
	echo 'The category has been updated successfully!';
}
```

### PDO Querying Data

`query()` method of the `PDO` object and a prepared statement.

To select data from a table using PDO, you can use:

-   The `query()` method of a PDO object.
-   Or a prepared statement.

When a query doesn’t have any parameters, you can use the `query()` method. For example:

```sql
SELECT * FROM posts;
```

However, if a query accepts one or more parameters, you should use a prepared statement for security reasons.

#### Using the query() method to select data from a table

To query data from a table using the `query()` method, you follow these steps:

1.  Create a database connection to the database server.
2.  Execute a `SELECT` statement by passing it to the `query()` method of a `PDO` object.

The `query()` method returns a `PDOStatement` object. If an error occurs, the `query()` method returns `false`.

```php
$pdo = require 'connect.php';

$sql = 'SELECT * FROM posts';

$statement = $pdo->query($sql);

// get all posts
$posts = $statement->fetchAll(PDO::FETCH_ASSOC);

if ($posts) {
	// show the posts
	foreach ($posts as $post) {
		echo $post['title'] . '<br>';
	}
}
```
When you use the `PDO::FETCH_ASSOC` mode, the `PDOStatement` returns an associative of elements in which the key of each element is the column name of the result set.

#### Using a prepared statement to query data
The following example illustrates how to use a prepared statement to query data from a table:

```php
$post_id = 5;

// connect to the database and select the post
$pdo = require 'connect.php';

$sql = 'SELECT * FROM posts
        WHERE id = :post_id';

$statement = $pdo->prepare($sql);
$statement->bindParam(':post_id', $post_id, PDO::PARAM_INT);
$statement->execute();
$post = $statement->fetch(PDO::FETCH_ASSOC);

if ($post) {
	echo $post['id'] . '.' . $post['title'];
} else {
	echo "The publisher with id $post_id was not found.";
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
The following example illustrates how to use a prepared statement to delete the publisher with id 1 from the `categories` table:

```php
$category_id = 1;

// connect to the database and select the category
$pdo = require 'connect.php';

// construct the delete statement
$sql = 'DELETE FROM categories
        WHERE id = :category_id';

// prepare the statement for execution
$statement = $pdo->prepare($sql);
$statement->bindParam(':category_id', $category_id, PDO::PARAM_INT);

// execute the statement
if ($statement->execute()) {
	echo 'category id ' . $category_id . ' was deleted successfully.';
}
```

#### Delete multiple rows from a table
Deleting multiple rows from the table is the same as the steps for deleting one row from a table.
To find the number of rows deleted, you use the `rowCount()` method of the `PDOStatement` object.
The following example shows how to delete publishers with an id greater than 3:

```php
$category_id = 2;

// connect to the database and select the category
$pdo = require 'connect.php';

$sql = 'DELETE FROM categories
        WHERE id > :category_id';

$statement = $pdo->prepare($sql);
$statement->bindParam(':category_id', $category_id, PDO::PARAM_INT);

if ($statement->execute()) {
	echo $statement->rowCount() . ' row(s) was deleted successfully.';
}
```
## fetch

### Introduction to the PHP fetch() method

The `fetch()` is a method of the `PDOStatement` class. The `fetch()` method allows you to fetch a row from a result set associated with a `PDOStatement` object.

Internally, the `fetch()` method fetches a single row from a result set and moves the internal pointer to the next row in the result set. Therefore, the subsequent call to the `fetch()` method will return the next row from the result set.

To fetch all rows from a result set one by one, you typically use the `fetch()` method in a `while` loop.

The following shows the syntax of the `fetch()` method:

```php
public function fetch(
    int $mode = PDO::FETCH_DEFAULT, 
    int $cursorOrientation = PDO::FETCH_ORI_NEXT, 
    int $cursorOffset = 0
): mixed
```

The `fetch()` method accepts three optional parameters. The most important one is the first parameter $mode.

The `$mode` parameter determines how the `fetch()` returns the next row. The $mode parameter accepts one of the `PDO::FETCH_*` constants. The most commonly used modes are:

-   `PDO::FETCH_BOTH` – returns an array indexed by both column name and 0-indexed column number.
-   `PDO::FETCH_ASSOC` – returns an array indexed by column name
-   `PDO::FETCH_CLASS` – returns a new class instance by mapping the columns to the object’s properties.

The `fetch()` method returns a value depending on the `$mode` parameter. It returns `false` on failure.

### Using the PHP fetch() method with the query() method

If a query doesn’t accept a parameter, you can fetch a row from the result set as follows:

-   First, execute the query by calling the `query()` method of the `PDO` object.
-   Then, fetch each row from the result set using the `fetch()` method and a while loop:

The following example shows how to use the `fetch()` method to select each row from the books table:

```php
// connect to the database to get the PDO instance
$pdo = require 'connect.php';

// execute a query
$sql = 'SELECT book_id, title FROM books';
$statement = $pdo->query($sql);

// fetch the next row
while (($row = $statement->fetch(PDO::FETCH_ASSOC)) !== false) {
    echo $row['title'] . '<br>';
}
```

### Using the fetch() method with a prepared statement

When a query accepts one or more parameters, you can fetch the next row from the result set as follows:

-   First, execute the prepared statement.
-   Second, fetch the next row from the result set using the `fetch()` method.

The following example shows how to `fetch()` to fetch a book from the books table with publisher id 1:

```php
// connect to the database to get the PDO instance
$pdo = require 'connect.php';

$sql = 'SELECT book_id, title 
        FROM books 
        WHERE publisher_id =:publisher_id';

// prepare the query for execution
$statement = $pdo->prepare($sql);

// execute the query
$statement->execute([
    ':publisher_id' => 1
]);

// fetch the next row
while (($row = $statement->fetch(PDO::FETCH_ASSOC)) !== false) {
    echo $row['title'] . PHP_EOL;
}
```

## fetchAll

### Introduction to the PHP fetchAll() method
The `fetchAll()` is a method of the `PDOStatement` class. The `fetchAll()` method allows you to fetch all rows from a result set associated with a `PDOStatement` object into an array.

The following shows the syntax of the `fetchAll()` method:

```php
public function fetchAll(int $mode = PDO::FETCH_DEFAULT): array
```

The `$mode` parameter determines how the `fetchAll()` returns the next row. The `$mode` parameter accepts one of the `PDO::FETCH_*` constants. The most commonly used modes are:

-   `PDO::FETCH_BOTH` – returns an array indexed by both column name and 0-indexed column number. This is the default.
-   `PDO::FETCH_ASSOC` – returns an array indexed by column name
-   `[PDO::FETCH_CLASS](https://www.phptutorial.net/php-pdo/pdo-fetch_class/)` – returns a new class instance by mapping the columns to the object’s properties.

The `fetchAll()` method returns an array that contains all rows of a result set.

If the result set is empty, the `fetchAll()` method returns an empty array. If the `fetchAll()` fails to fetch data, it’ll return `false`.

It’s important to notice that if you have a large result set, the `fetchAll()` may consume a lot of server memory and possibly network resources. To avoid this, you should execute a query that retrieves only necessary data from the database server.

### Using the PHP fetchAll() method with the query() method

If a query doesn’t accept a parameter, you can fetch all rows from the result set as follows:

-   First, execute the query by calling the `query()` method of the `PDO` object.
-   Then, fetch all rows from the result set into an array using the `fetchAll()` method.

The following example illustrates how to use the `fetchAll()` method to select all rows from the `publishers` table:

```php
// connect to the database to get the PDO instance
$pdo = require 'connect.php';

$sql = 'SELECT publisher_id, name 
        FROM publishers';

// execute a query
$statement = $pdo->query($sql);

// fetch all rows
$publishers = $statement->fetchAll(PDO::FETCH_ASSOC);

// display the publisher name
foreach ($publishers as $publisher) {
    echo $publisher['name'] . '<br>';
}
```

### Using the fetchAll() method with a prepared statement

If a query accepts one or more parameters, you can:

-   First, execute a prepared statement.
-   Second, fetch all rows from the result set into an array using the `fetchAll()` method.

The following example shows how to use `fetchAll()` to fetch all publishers with the id greater than 2:

```php
// connect to the database to get the PDO instance
$pdo = require 'connect.php';

$sql = 'SELECT publisher_id, name 
        FROM publishers
        WHERE publisher_id > :publisher_id';

// execute a query
$statement = $pdo->prepare($sql);
$statement->execute([
    ':publisher_id' => 2
]);
// fetch all rows
$publishers = $statement->fetchAll(PDO::FETCH_ASSOC);

// display the publishers
foreach ($publishers as $publisher) {
    echo $publisher['publisher_id'] . '.' . $publisher['name'] . '<br>';
}
```

## fetchObject

### Introduction to the fetchObject() method

Suppose that you have a `pulishers` table:

```sql
CREATE TABLE IF NOT EXISTS publishers (
    publisher_id INT AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    PRIMARY KEY (publisher_id)
);
```

and a `Publisher` class:

```php

class Publisher {
    private $publisher_id;
    private $name;
}
```

To fetch each row from the `publishers` table and returns it as a `Publisher` object, you use the `fetchObject()` method:

```php
public function fetchObject(
    string|null $class = "stdClass", 
    array $constructorArgs = []
): object|false
```

The `fetchObject()` is a method of the `PDOStatement` class. It fetches the next row from the result set associated with a `PDOStatement` object and returns an object of a class. If the `fetchObject()` method fails, it’ll return `false` instead.

The `fetchObject()` method has two parameters:

-   `$class` specifies the class of the object to return. If you omit it, the `fetchObject()` method will return an instance of the `stdClass` class.
-   `constructorArgs` is an array that specifies the arguments passed to the constructor) of the `$class`.

The `fetchObject()` maps the columns of the row with the properties of the object with the following rules:

-   First, assign the column value to the property with the same name.
-   Second, call the `__set()` magic method if a column doesn’t have a property in the class with the same name. If the classs has no `__set()` magic method, create a public property with the value derived from the column.

### PHP fetchobject() method example

The following example shows how to use the `fetchObject()` method to fetch a row from the `publishers` table and returns a `Publisher` object:

```php
class Publisher
{
    private $publisher_id;
    private $name;
}

// connect to the database
$pdo = require 'connect.php';

// execute the query
$sql = 'SELECT publisher_id, name 
        FROM publishers 
        WHERE publisher_id=:publisher_id';

$statement = $pdo->prepare($sql);
$statement->execute([':publisher_id' => 1]);

// fetch the row into the Publisher object
$publisher = $statement->fetchObject('Publisher');

var_dump($publisher);
```

## fetchColumn

### Introduction to fetchColumn() method

Sometimes, you want to get the value of a single column from the next row of a result set. In this case, you can use the `fetchColumn()` method of the PDOStatement object.

```php
public PDOStatement::fetchColumn(int $column = 0): mixed
```

The `fetchColumn()` method accepts a column index that you want to retrieve.

The index of the first column is zero. The index of the second column is one, and so on. By default, the `fetchColumn()` method returns the value of the first row from the result set if you don’t explicitly pass a column index.

The `fetchColumn()` method returns the value of the column specified by the `$column` index. If the result set has no more rows, the method returns `false`.

Because of this, you should not use the `fetchColumn()` to retrieve values from the boolean columns. The reason is that you won’t be able to know whether `false` comes from the selected column or the result of no more rows.

### A fetchColumn() method example

The following example uses the `fetchColumn()` method to get the name of the publisher with id 1:

```php
$pdo = require 'connect.php';

$sql = 'SELECT name 
        FROM publishers 
        WHERE publisher_id = :publisher_id';

$statement = $pdo->prepare($sql);
$statement->execute(
    ['publisher_id' => 1]
);

$publisher_name = $pdo->fetchColumn();
echo $publisher_name;
```
## PDO FETCH_GROUP

### Introduction to the PDO::FETCH_GROUP mode

The `PDO::FETCH_GROUP` allows you to group rows from the result set into a nested array, where the indexes will be the unique values from the column and the values will be arrays of the remaining columns.

For example, if you have a query like this:

```sql
SELECT role, username, email FROM users;
```

The `PDO::FETCH_GROUP` mode will return the following output:

```php
[
    'admin' => [
        0 => [
            'username' => 'admin',
            'email' => 'admin@phptutorial.net'
        ],
        1 => [
            'username' => 'bob',
            'email' => 'bob@phptutorial.net'
        ]
    ]
    'contributor' => [
        0 => [
            'username' => 'alex',
            'email' => 'alex@phptutorial.net'
        ],
        1 => [
            'username' => 'alice',
            'email' => 'alice@phptutorial.net'
        ]
    ]
]
```

The `PDO::FETCH_GROUP` is helpful in case you want to group rows by unique values of the first column in the result set. For example, you can use the `PDO::FETCH_GROUP` to select data for generating groupings of options within a select element.

### The PDO::FETCH_GROUP example

The following example selects the books and publishers from the `books` and `publishers` table. The `PDO::FETCH_GROUP` groups the books by the publisher names:

```php
$pdo = require 'connect.php';

$sql = 'SELECT name, book_id, title
        FROM publishers p
        INNER JOIN books b ON b.publisher_id = p.publisher_id';

$statement = $pdo->query($sql);

$publishers = $statement->fetchAll(PDO::FETCH_GROUP | PDO::FETCH_ASSOC);
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Books</title>
</head>
<body>
    <label for="book">Select a book:</label>
    <select name="book" id="book">
        <?php foreach ($publishers as $publisher => $books) : ?>
        <optgroup label="<?php echo $publisher ?>">
            <?php foreach ($books as $book) : ?>
            <option value="<?php echo $book['book_id'] ?>"><?php echo $book['title'] ?></option>
            <?php endforeach ?>
        </optgroup>
        <?php endforeach ?>
    </select>
</body>
</html>
```

## PDO FETCH_KEY_PAIR

### Introduction to the PDO FETCH_KEY_PAIR mode

Both `fetch()` and fetchAll() methods accept a very useful fetch mode called `PDO::FETCH_KEY_PAIR`.

The `PDO::FETCH_KEY_PAIR` mode allows you to retrieve a two-column result in an array where the first column is the key and the second column is the value.

In practice, you’ll use the `PDO::FETCH_KEY_PAIR` to fetch data for constructing a `<select>` element with data that comes from the database.

For example, you can create a `<select>` element with the values are publisher id and texts are publisher names:
```php
$pdo = require 'connect.php';

$sql = 'SELECT publisher_id, name 
        FROM publishers';

$statement = $pdo->query($sql);
$publishers = $statement->fetchAll(PDO::FETCH_KEY_PAIR);
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Publishers</title>
</head>
<body>
    <label for="publisher">Select a pulisher</label>
    <select name="publisher" id="publisher">
        <?php foreach ($publishers as $publisher_id => $name): ?>
        <option value="<?php echo $publisher_id ?>"><?php echo $name ?></option>
        <?php endforeach ?>
    </select>
</body>
</html>
```

## PDO FETCH_CLASS

### Introduction to the PDO::FETCH_CLASS mode

Each row in the `books` table can map to an instance of the `Book` class.

To select data from the `books` table and map the columns to the properties of a `Book` object, you can use the `PDO::FETCH_CLASS` mode. For example:

```php
class Book
{
}

$pdo = require 'connect.php';

$sql = 'SELECT book_id, title, isbn, published_date
        FROM books
        WHERE book_id = :book_id';

$statement = $pdo->prepare($sql);
$statement->execute([':book_id' => 1]);
$statement->setFetchMode(PDO::FETCH_CLASS, 'Book');
$book = $statement->fetch();

var_dump($book);
```

### Returning an array of objects

The following example shows how to use the `PDO::FETCH_CLASS` mode to select data from the `books` table and return an array of `Book` objects:

```php
class Book
{
}

$pdo = require 'connect.php';

$sql = 'SELECT book_id, title, isbn, published_date
        FROM books';

$books = $statement->query()->fetchAll(PDO::FETCH_CLASS, 'Book');

var_dump($books);
```

### Assigning object properties

The following example illustrates how to select one row from the `books` table and return a new instance of the `Book` class:

```php
class Book
{
    private $book_id;
    private $title;
    private $isbn;
    private $published_date;

    public function __set($name, $value)
    {
        // empty
    }
}

$pdo = require 'connect.php';

$sql = 'SELECT 
            book_id, 
            title, 
            isbn, 
            published_date, 
            publisher_id
        FROM books
        WHERE book_id = :book_id';

$statement = $pdo->prepare($sql);
$statement->execute([':book_id' => 1]);
$statement->setFetchMode(PDO::FETCH_CLASS, 'Book');

$book = $statement->fetch();

var_dump($book);
```

### Assigning object properties and calling constructor

By default, PDO assigns column values to the object properties before calling the constructor.

To instruct PDO to call the constructor before assigning column values to object properties, you combine the `PDO::FETCH_CLASS` with `PDO::FETCH_PROPS_LATE` flag. For example:

```php
class Book
{
    public function __construct()
    {
        if (isset($this->isbn)) {
            echo 'ISBN:' . $this->isbn;
        }
        echo 'ISBN has not assigned yet.';
    }
}

$pdo = require 'connect.php';

$sql = 'SELECT book_id, title, isbn, published_date, 
        FROM books
        WHERE book_id = :book_id';

$statement = $pdo->prepare($sql);
$statement->execute([':book_id' => 1]);
$statement->setFetchMode(PDO::FETCH_CLASS | PDO::FETCH_PROPS_LATE, 'Book');
$book = $statement->fetch();

var_dump($book);
```

## PHP PDO Transaction

### Introduction to PHP PDO transaction

To start a transaction in PDO, you use the `PDO::beginTransaction()` method:

```php
$pdo->beginTransaction();
```

The `beginTransaction()` method turns off the autocommit mode. It means that the changes made to the database via the PDO object won’t take effect until you call the `PDO::commit()` method.

To commit a transaction, you call the `PDO::commit()` method:

```php
$pdo->commit();
```

If you want to roll back the transaction, you can call the `PDO::rollback()` method:

```php
$pdo->rollback();
```

The `PDO::rollback()` method rolls back all changes made to the database. Also, it returns the connection to the autocommit mode.

The `PDO::beginTransaction()` method throws an exception if the database doesn’t support transactions.

### PDO transaction example

Suppose that you need to insert data into three tables: `books`, `authors`, and `book_authors`.

To do that, you need to:

-   Get the author id if the author exists; otherwise, insert the author into the `authors` table.
-   Insert the book into the `books` table.
-   Insert the link between book and author into the `book_authors` table.

To organize the code, we’ll place all functions in the `functions.php` file and include it in the mains script.

The following `get_author_id()` function finds author by first name and last name and returns the author id if the author exists in the `authors` table:

```php
function get_author_id(\PDO $pdo, string $first_name, string $last_name)
{
	$sql = 'SELECT author_id 
            FROM authors 
            WHERE first_name = :first_name 
                AND last_name = :last_name';

	$statement = $pdo->prepare($sql);

	$statement->bindParam(':first_name', $first_name, PDO::PARAM_STR);
	$statement->bindParam(':last_name', $last_name, PDO::PARAM_STR);

	if ($statement->execute()) {
		$row = $statement->fetch(PDO::FETCH_ASSOC);
		return $row !== false ? $row['author_id'] : false;
	}

	return false;
}
```

The following `insert_author()` function inserts a new author into the `authors` table and returns the author id:

```php

function insert_author(\PDO $pdo, string $first_name, string $last_name): int {
	$sql = 'INSERT INTO authors(first_name, last_name) 
            VALUES(:first_name, :last_name)';

	$statement = $pdo->prepare($sql);

	$statement->bindParam(':first_name', $first_name, PDO::PARAM_STR);
	$statement->bindParam(':last_name', $last_name, PDO::PARAM_STR);

	$statement->execute();

	return  $pdo->lastInsertId();
}
```

The following `insert_book()` function inserts a new book into the `books` table:

```php

function insert_book(\PDO $pdo, string $title, string $isbn, string $published_date, int $publisher_id): int {
	$sql = 'INSERT INTO books(title, isbn, published_date, publisher_id) 
            VALUES(:title, :isbn, :published_date, :publisher_id)';

	$statement = $pdo->prepare($sql);

	$statement->bindParam(':title', $title, PDO::PARAM_STR);
	$statement->bindParam(':isbn', $isbn, PDO::PARAM_STR);
	$statement->bindParam(':published_date', $published_date, PDO::PARAM_STR);
	$statement->bindParam(':publisher_id', $publisher_id, PDO::PARAM_INT);

	$statement->execute();

	return  $pdo->lastInsertId();
}
```

The following function inserts a new row into the `book_authors` table:

```php

function insert_book_author(\PDO $pdo, int $book_id, int $author_id) {
	$sql = 'INSERT INTO book_authors(book_id, author_id) 
            VALUES(:book_id, :author_id)';

	$statement = $pdo->prepare($sql);

	$statement->bindParam(':book_id', $book_id, PDO::PARAM_INT);
	$statement->bindParam(':author_id', $author_id, PDO::PARAM_INT);

	$statement->execute();
}
```

The following script uses those functions above to perform a transaction:

```php
require 'functions.php';
$pdo = require 'connect.php';

$book = [
	'title' => 'Eternal',
	'isbn' => '9780525539766',
	'published_date' => '2021-03-23',
	'publisher_id' => 2,
];

$author = [
	'first_name' => 'Lisa',
	'last_name' => 'Scottoline',
];

try {
	$pdo->beginTransaction();

	// find the author by first name and last name
	$author_id = get_author_id(
		$pdo,
		$author['first_name'],
		$author['last_name']
	);

	// if author not found, insert a new author
	if (!$author_id) {
		$author_id = insert_author(
			$pdo,
			$author['first_name'],
			$author['last_name']
		);
	}

	$book_id = insert_book(
		$pdo,
		$book['title'],
		$book['isbn'],
		$book['published_date'],
		$book['publisher_id']
	);

	// insert the link between book and author
	insert_book_author($pdo, $book_id, $author_id);

	// commit the transaction
	$pdo->commit();
} catch (\PDOException $e) {
	// rollback the transaction
	$pdo->rollBack();

	// show the error message
	die($e->getMessage());
}
```
