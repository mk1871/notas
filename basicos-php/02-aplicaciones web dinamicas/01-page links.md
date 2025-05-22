
Welcome back to Section 2 of PHP for Beginners! In this section, we'll explore building websites and applications using PHP and MySQL.

### Starting with an HTML Boilerplate

To make things easier and have something to work with, we'll start with an existing HTML skeleton. I grabbed a free component from TailwindUI.com—a clean, practical HTML template with CSS utility classes.

If you're unfamiliar with Tailwind CSS, don't worry; you won't be tested on it. It's just a quick way to have something visually appealing in the browser.

### Setting Up Tailwind CSS

Add the required classes to your `<html>` and `<body>` tags:

```html
<html class="h-full bg-white">
<body class="h-full">
```

Then import Tailwind CSS via CDN:

```html
<script src="https://cdn.tailwindcss.com"></script>
```

Refresh your browser to see the styled template.

### Template Structure

The template includes:

- Navigation area
- Header area
- Main content section

These sections resemble typical website layouts.

### Updating Navigation Links

Change navigation links to point to your pages:

- Dashboard → Home (`/`)
- Team → About Us (`/about`)
- Projects → Contact (`/contact`)

Update the links accordingly in your HTML.

### Creating Additional Pages

Create new PHP files for pages like `about.php` and `contact.php`, each loading their respective views (e.g., `about.view.php`, `contact.view.php`).

Duplicate your `index.view.php` for these pages and update content accordingly.

### Understanding PHP’s Built-in Server Behavior

When you request a URL that doesn't match a file, PHP's built-in server defaults to `index.php`. That's why `/about.php` works but `/about` does not yet.

We'll learn how to handle cleaner URLs later.

### Managing Navigation State

Notice the active navigation item has a different background color and an `aria-current` attribute for accessibility.

Currently, these styles are hardcoded, which means you must update every page manually—a tedious and error-prone process.

### Summary

- Use existing HTML templates to jumpstart your project.
- Import Tailwind CSS for quick styling.
- Create separate PHP files and views for each page.
- Update navigation links to point to the correct pages.
- Understand how PHP’s built-in server handles requests.
- Recognize the need for better navigation state management and URL routing, which we'll cover next.

This sets the stage for building more dynamic and maintainable PHP applications.