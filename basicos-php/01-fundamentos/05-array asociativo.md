Let's continue focusing on arrays. You now know how to define an array and loop over it, but what about accessing individual items within that array?

For example, if you want to grab the second item from the `$books` array, you might try:

```php
echo $books[2];
```

However, this will give you the third item, not the second. That's because arrays in PHP (and most programming languages) are **zero-based**. This means the first item is at index `0`, the second at `1`, and so on.

So to get the second item, you should use:

```php
echo $books[1];
```

And the first item is:

```php
echo $books[0];
```

## Storing More Complex Data with Arrays

Often, you need more than just a string for each item. For example, you might want to store the book's author, a description, or category.

Instead of just strings, you can store **arrays within arrays**. This way, each book becomes an array containing multiple pieces of information.

Example:

```php
$books = [
    [
        "name" => "Do Androids Dream of Electric Sheep?",
        "author" => "Philip K. Dick",
        "purchaseUrl" => "https://example.com/androids-dream"
    ],
    [
        "name" => "Project Hail Mary",
        "author" => "Andy Weir",
        "purchaseUrl" => "https://example.com/hail-mary"
    ]
];
```

This is called an **associative array**, where each value is associated with a key.

> An associative array is a collection of key-value pairs, allowing you to label each piece of data with a descriptive key.

## Accessing Data in Associative Arrays

When looping over an array of associative arrays, each item is itself an array. To access individual properties, use the keys:

```php
<?php foreach ($books as $book): ?>
    <li>
        <?= $book['name'] ?> by <?= $book['author'] ?>
        <a href="<?= $book['purchaseUrl'] ?>">Buy here</a>
    </li>
<?php endforeach; ?>
```

If you try to echo the entire `$book` array directly, PHP will throw an error because it cannot convert the array to a string.

## Naming Conventions

Notice the keys like `purchaseUrl` use camelCase, where each new word starts with a capital letter. This is a common convention but not mandatory.

## Summary

- Arrays are zero-based; the first item is at index 0.
- Arrays can contain other arrays, allowing you to store complex data structures.
- Associative arrays use keys to label values, making data easier to understand and access.
- Use `foreach` loops to iterate over arrays and access nested data via keys.
- When embedding variables inside HTML, use PHP's alternative syntax and short echo tags for cleaner code.

Keep practicing these concepts, and you'll be able to handle more complex datasets in PHP with ease.