
Welcome back, and congratulations on making it this far! I know it's not easy, especially the first time around.

In this video, we'll introduce **functions**, which you can think of as the _verbs_ of the programming world. Functions are responsible for _doing things_.

### Example Setup: Books Array

Let's revisit our books array. I've added a release year to each book and included a third book, _The Martian_ by Andy Weir. This setup will help us explore filtering.

Imagine this is part of a book repository with hundreds or thousands of books. Users will want to filter books by release year, author, or other criteria.

For example, you might want to display only books written by Andy Weir.

### Filtering Books by Author

Here's how you might approach this:

Inside your loop over books, add a conditional to check if the book's author is Andy Weir. Only render the list item if this condition is true.

```php
<?php if ($book['author'] === 'Andy Weir'): ?>
    <li><?= $book['name'] ?> (<?= $book['releaseYear'] ?>)</li>
<?php endif; ?>
```

### Common Mistake: Assignment vs. Comparison

If you accidentally use a single equal sign (`=`) instead of triple equals (`===`), you're assigning a value rather than checking for equality.

```php
// Incorrect: assigns 'Andy Weir' to $book['author']
if ($book['author'] = 'Andy Weir') { ... }
```

This causes every book's author to be set to Andy Weir, which is not what you want.

Use triple equals (`===`) to check for equality:

```php
if ($book['author'] === 'Andy Weir') { ... }
```

### Defining a Function to Filter Books

Instead of hardcoding the filtering logic inside the loop, you can create a function to filter books automatically.

Functions are like verbs: you call them, they do something, and often return a result.

Here's how to define a function:

```php
function filterByAuthor($books) {
    // function logic here
}
```

You call it like this:

```php
$filteredBooks = filterByAuthor($books);
```

### Function Behavior

Initially, if your function doesn't return anything, calling it and echoing its result will output nothing.

Add a `return` statement to send back a value:

```php
function filterByAuthor($books) {
    return "gibberish";
}
```

Now calling the function will output "gibberish".

### Building the Filtering Logic

Inside the function:

1. Create an empty array `$filteredBooks`.
2. Loop over `$books`.
3. For each book, check if the author matches "Andy Weir".
4. If yes, append the book to `$filteredBooks`.
5. Return `$filteredBooks`.