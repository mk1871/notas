
Welcome back! Let's pick up where we left off.

Previously, we created a connection string and passed it to a new PDO instance. We prepared and executed a query, fetched all results as an associative array, and looped over the posts to display their titles.

### Refactoring into a Database Class

To make our code more manageable, let's refactor this into a class.

Start by defining a class named `Database`:

```php
class Database {
    public function query($sql) {
        // Query logic here
    }
}
```

Create an instance of the class:

```php
$db = new Database();
```

Call the query method:

```php
$posts = $db->query('SELECT * FROM posts');
```

### Making the Query Dynamic

Modify the `query` method to accept a SQL string as a parameter, allowing different queries:

```php
public function query($sql) {
    // Prepare and execute $sql
}
```

### Using the Constructor to Initialize PDO

Add a constructor method to initialize the PDO connection once when the class instance is created:

```php
public function __construct() {
    $this->connection = new PDO($dsn, $username, $password);
}
```

Store the PDO instance in a class property for reuse.

### Fetching Single or Multiple Records

Currently, `fetchAll()` returns an array of records. To fetch a single record, use `fetch()`.

You can return the statement itself and decide later whether to fetch one or all records.

### Summary

- Encapsulate database logic in a `Database` class.
- Initialize PDO in the constructor.
- Make the query method accept dynamic SQL.
- Return the statement to allow flexible fetching.
- Use `fetch()` for single records and `fetchAll()` for multiple.

This sets a solid foundation for database interaction in PHP. Stay tuned for the next episode!