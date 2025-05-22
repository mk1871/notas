
Let's dive right in.

We have our database and a `posts` table. Now, let's learn how to query this table from PHP using SQL.

### Basic SQL Queries

To select all records from the `posts` table:

```sql
SELECT * FROM posts;
```

You can also select specific columns:

```sql
SELECT id FROM posts;
SELECT title FROM posts;
SELECT id, title FROM posts;
```

Add a `WHERE` clause to filter results:

```sql
SELECT * FROM posts WHERE id = 1;
```

### Connecting to MySQL with PDO

In PHP, use PDO (PHP Data Objects) to connect to MySQL:

```php
$dsn = 'mysql:host=localhost;port=3306;dbname=myapp;charset=utf8mb4';
$username = 'root';
$password = ''; // or your password

$pdo = new PDO($dsn, $username, $password);
```

PDO uses a **Data Source Name (DSN)** string to specify connection details like host, port, database name, and charset.

### Preparing and Executing Queries

Prepare a query:

```php
$stmt = $pdo->prepare('SELECT * FROM posts');
```

Execute the query:

```php
$stmt->execute();
```

Fetch all results as an associative array:

```php
$posts = $stmt->fetchAll(PDO::FETCH_ASSOC);
```

This returns an array of records, each represented as an associative array with column names as keys.

### Displaying Results

Loop over the results and display them:

```php
foreach ($posts as $post) {
    echo "<li>{$post['title']}</li>";
}
```

### Summary

- Use SQL to query your database.
- Connect to MySQL using PDO with a DSN string.
- Prepare and execute queries safely.
- Fetch results as associative arrays.
- Loop over and display data in your application.

There's more to learn, but you're making great progress!