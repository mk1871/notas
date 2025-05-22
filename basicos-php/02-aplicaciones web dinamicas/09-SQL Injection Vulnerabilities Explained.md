
A lot of programming involves iterating on code until it’s not only functional but also clean and flexible. Let’s look at how we can make our database class more flexible.

### Making the Connection Configuration Dynamic

Currently, the DSN (Data Source Name) string in our PDO connection is hardcoded with values like host, port, and username. These values often change between environments (local development vs. production), so hardcoding them isn’t ideal.

Instead, we can extract these into a configuration array:

```php
$config = [
    'host' => 'localhost',
    'port' => 3306,
    'database' => 'myapp',
    'charset' => 'utf8mb4',
];
```

We can then build the DSN dynamically using PHP’s `http_build_query` function, which creates a query string from the array:

```php
$dsn = 'mysql:' . str_replace('&', ';', http_build_query($config));
```

This produces a DSN like:

```undefined
mysql:host=localhost;port=3306;database=myapp;charset=utf8mb4
```

### Passing Configuration to the Database Class

Instead of hardcoding config inside the class, pass the configuration array when instantiating the class:

```php
$db = new Database($config);
```

Inside the class constructor, use this config to build the DSN and establish the PDO connection.

### Handling Username and Password

You can pass username and password separately to the PDO constructor, allowing them to be dynamic as well. You can set default values for convenience:

```php
public function __construct(array $config, $username = 'root', $password = '') {
    // Build DSN and initialize PDO
}
```

### Using Constants for PDO Options

PDO uses constants like `PDO::ATTR_DEFAULT_FETCH_MODE` to configure behavior. These constants are static and shared across all instances.

For example, to set the default fetch mode to associative arrays:

```php
$options = [
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
];
```

Pass these options when creating the PDO instance.

### Summary

- Extract connection details into a config array to support different environments.
- Use `http_build_query` with a custom separator to build DSN strings dynamically.
- Pass config, username, and password to the database class constructor.
- Use PDO constants for options like fetch mode.
- Return config arrays from files using `return` and load them with `require`.

This approach makes your database connection flexible and maintainable across environments.

