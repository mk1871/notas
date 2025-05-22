
Before moving on to Section 2, let's do a quick technical check-in—a chapter review to ensure everything covered so far is committed to memory. Once you ace the quiz, you can proceed.

### Variables

To define a variable, always start with a dollar sign:

```php
$businessName = "Leracasts";
```

Strings are surrounded by quotes, and statements end with a semicolon.

You can also create variables for numbers:

```php
$cost = 15; // $15 per month
```

### Arrays

When grouping related attributes, use arrays:

```php
$business = [
    'name' => 'Leracasts',
    'cost' => 15
];
```

Access values by key:

```php
echo $business['name'];
```

### Conditionals

Use conditionals to make decisions. For example, if the monthly cost is too high, don't proceed:

```php
if ($business['cost'] > 99) {
    echo 'Not interested';
}
```

### Loops

To iterate over collections like categories, use `foreach`:

```php
foreach ($business['categories'] as $category) {
    echo $category . '<br>';
}
```

### Functions

Functions encapsulate reusable logic. For example, a `register` function might accept a user and handle sign-up tasks like creating a database record, signing in, sending a welcome email, and redirecting to a dashboard.

```php
function register($user) {
    // Create user record
    // Sign in user
    // Send welcome email
    // Redirect to dashboard
}
```

### Separating Logic and Presentation

It's important to separate PHP logic from the HTML template or view.

For example, in your main PHP file, define variables and functions, then include the view:

```php
$business = [...];
function register($user) { ... }

require 'index.view.php';
```

In `index.view.php`, you can access `$business` and render the HTML:

```php
<h1><?= $business['name'] ?></h1>
<ul>
<?php foreach ($business['categories'] as $category): ?>
    <li><?= $category ?></li>
<?php endforeach; ?>
</ul>
```

---

Before moving on, take the quiz linked in the description on the Leracasts website to test your understanding. You don't need to be a subscriber—just signed in. See you in the next video!