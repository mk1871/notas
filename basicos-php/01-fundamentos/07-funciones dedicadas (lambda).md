
Welcome back! In this video, we'll push a little beyond your comfort zone by exploring lambda functions, refactoring, and making functions more generic and flexible.

### Revisiting the Books List and Filtering

We have our books list and the `filterByAuthor` function from the last episode. While it works, it's not very flexible. What if you want to filter by release year or other criteria? Creating separate functions for each filter isn't practical.

Instead, we want a more generic and flexible approach.

### Extracting Logic into Variables

Previously, we looped directly over the function call:

```php
foreach (filterByAuthor($books, 'Andy Weir') as $book) {
    // ...
}
```

We can extract this into a variable for clarity:

```php
$filteredBooks = filterByAuthor($books, 'Andy Weir');
foreach ($filteredBooks as $book) {
    // ...
}
```

This technique is called _extracting a variable_—taking logic and assigning it to a variable for reuse.

### Named vs. Anonymous Functions

So far, we've used named functions, which have explicit names. But PHP also supports **anonymous functions** (also called _lambda functions_), which have no name and can be assigned to variables or passed as arguments.

Example of an anonymous function assigned to a variable:

```php
$filterByAuthor = function($books) {
    // filtering logic
};
```

You can then call it via the variable:

```php
$filteredBooks = $filterByAuthor($books);
```

### Refactoring for Generic Filtering

To make our filter function more generic:

- Rename the function to `filter`.
- Accept a generic array `$items` instead of `$books`.
- Accept a `$key` and `$value` to filter by any property.

Example function signature:

```php
function filter(array $items, string $key, $value) {
    $filteredItems = [];
    foreach ($items as $item) {
        if ($item[$key] === $value) {
            $filteredItems[] = $item;
        }
    }
    return $filteredItems;
}
```

This allows filtering by any key-value pair, e.g., author or release year.

### Enhancing Flexibility with Callbacks

What if you want more complex filtering, like books released after 2000? Equality checks won't suffice.

We can pass a **callback function** to handle the filtering logic:

```php
function filter(array $items, callable $callback) {
    $filteredItems = [];
    foreach ($items as $item) {
        if ($callback($item)) {
            $filteredItems[] = $item;
        }
    }
    return $filteredItems;
}
```

Usage example:

```php
$filteredBooks = filter($books, function($book) {
    return $book['releaseYear'] >= 2000;
});
```

This approach gives you full control over the filtering condition.

### Using PHP's Built-in `array_filter`

PHP provides a built-in function `array_filter` that does exactly this:

```php
$filteredBooks = array_filter($books, function($book) {
    return $book['author'] === 'Andy Weir';
});
```

This is more concise and leverages PHP's standard library.

---

Feel free to watch this video again, ask questions, and work through the homework to solidify your understanding before moving on.