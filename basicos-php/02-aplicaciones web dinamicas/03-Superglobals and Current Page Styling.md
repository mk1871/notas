
Let's dive right in.

Previously, we noticed that the navigation always highlights the About page because the active styles are hardcoded. To fix this, we need to apply those styles conditionally based on the current page.

### Echoing Variables and Dumping Data

When using `echo`, PHP expects a string. If you try to echo an array, you'll get a warning like _Array to String Conversion_. To inspect arrays or objects, use `var_dump()` wrapped in `<pre>` tags for readable formatting:

```php
echo '<pre>';
var_dump($variable);
echo '</pre>';
```

You can also use `die()` to stop execution immediately after dumping, which is useful for debugging.

### Creating a `dd` (Dump and Die) Function

To avoid repeating the dump and die pattern, create a helper function:

```php
function dd($value) {
    echo '<pre>';
    var_dump($value);
    echo '</pre>';
    die();
}
```

Call `dd($variable)` to dump and stop execution.

### Using PHP Superglobals to Detect Current Page

PHP provides superglobals like `$_SERVER` accessible anywhere. The key `$_SERVER['REQUEST_URI']` contains the current URL path.

You can echo it to see the current page:

```php
echo $_SERVER['REQUEST_URI'];
```

### Applying Conditional Navigation Styles

In your navigation partial, use PHP to conditionally apply active styles based on the current URL:

```php
<?php if ($_SERVER['REQUEST_URI'] === '/'): ?>
    class="bg-gray-900 text-white"
<?php else: ?>
    class="text-gray-300 hover:bg-gray-700 hover:text-white"
<?php endif; ?>
```

This can be refactored using the ternary operator for brevity:

```php
<?= ($_SERVER['REQUEST_URI'] === '/') 
    ? 'class="bg-gray-900 text-white"' 
    : 'class="text-gray-300 hover:bg-gray-700 hover:text-white"' ?>
```

### Creating a Helper Function for URL Checks

To avoid repeating the URL check logic, define a function:

```php
function isCurrentPage($url) {
    return $_SERVER['REQUEST_URI'] === $url;
}
```

Use it in your navigation:

```php
<?= isCurrentPage('/') 
    ? 'class="bg-gray-900 text-white"' 
    : 'class="text-gray-300 hover:bg-gray-700 hover:text-white"' ?>
```

### Organizing Functions

Extract such helper functions into a separate `functions.php` file and include it in your pages with `require 'functions.php';`. This avoids duplicating functions across files.

---

With these techniques, your navigation styling becomes dynamic and maintainable. Keep practicing, and let's move on to the next episode!