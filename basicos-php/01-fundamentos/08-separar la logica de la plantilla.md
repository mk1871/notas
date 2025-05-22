
Before moving on to the technical check-in, let's take a few minutes to discuss code organization and why it's often beneficial to separate your PHP logic from the HTML.

Currently, we have a single HTML file with all PHP logic nested inside it, including data, filtering, functions, and loops. While combining PHP and HTML in one file is powerful, it can quickly become difficult to manage as the file grows.

It's important to prioritize readability and code organization from the start.

Many PHP developers separate the PHP code that gathers data from the HTML that displays it. For example, you can move all PHP logic out of the `<body>` tag and place it at the very top of the document. This clearly separates logic from presentation.

Regarding PHP code that loops over arrays and builds HTML, this part is often called the _template_ or _view_. In programming, we say the template or view should be _dumb_—meaning it should avoid complex logic, external service calls, or database interactions. Its job is simply to render data passed to it.

As your application grows, the PHP logic at the top of your file will also grow and become messy. The next step is to split this logic into its own file.

You might have one file for PHP logic (e.g., `index.php`) and another for the view or template (e.g., `index.view.php`).

In the view file, keep only the core HTML and any necessary PHP to echo or loop over data.

In the logic file, keep only the PHP that prepares data. You can omit the closing PHP tag if the file contains only PHP code.

To connect the two, use PHP's `require` or `include` to load the view file from the logic file:

```php
require 'index.view.php';
```

This way, the view has access to all data defined in the logic file.

If you see warnings about undefined variables in your editor (such as PHPStorm), it might be because the editor doesn't follow `require` or `include` statements when analyzing code. You can adjust your editor settings to fix this, but it's not an error and won't affect execution.

---

What we've accomplished here is foundational: separating PHP logic from the presentation layer improves maintainability and readability.

- Adjust PHP logic in `index.php`.
- Adjust presentation in `index.view.php`.
- The view remains _dumb_, simply rendering data passed to it.

This separation makes your code easier to reason about and maintain as your application grows.