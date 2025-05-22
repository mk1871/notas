Welcome back! Let's dive into building a custom router from scratch. It won't be the most complex router, but it will be practical for your current learning stage.

### Moving to a Single Point of Entry

Previously, each URI directly mapped to a controller file, like `/contact` mapping to `contact.php`. Now, we want a single entry point responsible for routing URIs to controllers.

Start by creating a `controllers` directory and moving your controller files (`about.php`, `contact.php`, `index.php`) into it.

### Inspecting the Current Request URI

Use PHP's superglobal `$_SERVER['REQUEST_URI']` to get the current URI, which includes the path and query string.

Example:

```php
dd($_SERVER['REQUEST_URI']);
```

Note that query strings (e.g., `?name=John`) are included, so you need to parse the URI to isolate the path.

Use PHP's `parse_url()` function to extract the path:

```php
$uri = parse_url($_SERVER['REQUEST_URI'], PHP_URL_PATH);
```

### Basic Routing Logic

Implement routing by checking the URI path and requiring the corresponding controller:

```php
if ($uri === '/') {
    require 'controllers/index.php';
} elseif ($uri === '/about') {
    require 'controllers/about.php';
} elseif ($uri === '/contact') {
    require 'controllers/contact.php';
} else {
    // Handle 404
}
```

### Avoiding Function Redeclaration Errors

Since `functions.php` is required in multiple controllers, use `require_once` to prevent redeclaration errors:

```php
require_once 'functions.php';
```

### Handling Unknown Routes (404)

If the requested URI doesn't match any route, respond with a 404 status code and display a friendly error page.

Set the status code and display a 404 view:

```php
http_response_code(404);
require 'views/404.php';
exit;
```

### Refactoring with a Routes Map

Instead of multiple `if` statements, use an associative array to map URIs to controller files:

```php
$routes = [
    '/' => 'controllers/index.php',
    '/about' => 'controllers/about.php',
    '/contact' => 'controllers/contact.php',
];

if (array_key_exists($uri, $routes)) {
    require $routes[$uri];
} else {
    http_response_code(404);
    require 'views/404.php';
    exit;
}
```

### Summary

- Use `$_SERVER['REQUEST_URI']` and `parse_url()` to get the current path.
- Map URIs to controllers using an associative array.
- Use `require_once` to avoid function redeclaration.
- Handle unknown routes gracefully with a 404 page and status code.
- Refactor routing logic into a dedicated `router.php` file and include it in your entry point.

This approach lays the foundation for a clean, maintainable routing system.

