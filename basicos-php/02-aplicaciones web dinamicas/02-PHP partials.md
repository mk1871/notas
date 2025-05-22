Let's get started cleaning things up.

When building a simple static website, duplication is often unavoidable, especially with HTML and CSS. However, since we're using a dynamic language like PHP, we can eliminate this duplication entirely.

### Organizing Files

To keep things tidy, create a new directory exclusively for your views. Move all your view files into this directory and update your PHP files to reference the new paths accordingly. This makes your project structure cleaner and easier to navigate.

### Using Partials to Remove Duplication

Within the views directory, create a subdirectory called `partials`. A _partial_ is a reusable piece of HTML.

For example, extract your navigation HTML into a file named `nav.php` inside the `partials` directory.

Then, in your main view files, include this partial using PHP's `require`:

```php
<?php require 'partials/nav.php'; ?>
```

This way, any changes to the navigation only need to be made in one place, and all views will reflect the update.

Repeat this process for other common sections like the header (`head.php`), footer (`footer.php`), and banner (`banner.php`).

### Benefits of Using Partials

- Eliminates duplication across multiple view files.
- Makes it easier to maintain and update shared UI components.
- Keeps your views focused and easier to understand.

### Making Partials Dynamic

Sometimes, partials contain hardcoded content, like the banner text showing "About" on every page. To fix this, pass variables from your controller (the PHP file) to your views and partials.

For example, define a `$heading` variable in your controller:

```php
$heading = 'About Us';
```

Then, in your banner partial, echo this variable:

```php
<h1><?= $heading ?></h1>
```

This approach makes your partials reusable and dynamic.

### Introducing Controllers

Think of your PHP files that prepare data and load views as _controllers_. A controller accepts a request (like visiting the About page) and provides a response (rendering the About view).

This separation of concerns—controllers handling logic and views handling presentation—is foundational in modern PHP applications and frameworks.

---

By organizing your views into directories, extracting partials, and passing dynamic data from controllers, your application becomes more maintainable and scalable.

Keep up the great work, and let's move on to the next episode!