
# 1. Hello, Laravel

Hi, my name is Jeffrey! I have been working with Laravel for over a decade, and would love to show you everything you need to know about building web apps with Laravel. The first step is to get your tooling in order. Let's go!
# 2. Your First Route and View

Whenever I learn a new framework, I transform into a beginner who wants to build a laughably basic three-page layout. It's a great way to learn how a request flows through your codebase! In this episode, you'll learn about basic routing and views.

# 3. Create a Layout File Using Laravel Components

We should create a layout file to reduce the amount of HTML duplication from the previous episode. This will provide the perfect opportunity to discuss Laravel components.

# 4. Make a Pretty Layout Using TailwindCSS

Now that we understand the basics of how to link from page to page, let's take one episode to make a simple, but attractive HTML and CSS layout. Luckily, TailwindCSS and its components will make this work a cinch.

# 5. Style the Currently Active Navigation Link

In this lesson, we'll learn how to apply special styling to the navigation link that matches the current page. Laravel provides a `Request` object that makes things like this a cinch.

# 6. View Data and Route Wildcards

We can pass an array of data as the second argument of the `view()` function. Each key in this array will then be extracted into a variable within your view. Let's build a basic two-page Job listings section to illustrate this.

# 7. Autoloading, Namespaces, and Models

We'll touch on a number of important topics in this episode, including PSR-4 autoloading, namespaces, and the MVC architecture.

# 8. Introduction to Migrations

It's time to move on to the subject of databases. In this lesson, we'll discuss Laravel migrations. You can think of migrations as PHP blueprints that determine your table structure.

# 9. Meet Eloquent

This is a special episode. You're now ready to learn about Eloquent, Laravel's ORM. An ORM allows you to map an object within a database into a PHP object. Let's have a look!

# 10. Model Factories

Model factories allow us to scaffold example data for the purposes of both testing and preparing a local environment. As with everything, Laravel makes this all a cinch. Let's have a look.

# 11. Two Key Eloquent Relationship Types

For day 11, we'll focus exclusively on two Eloquent relationship types: `belongsTo()` and `hasMany()`. As you'll quickly find, these two will take you a very, very long way.

# 12. Pivot Tables and BelongsToMany Relationships

The next key Eloquent relationship type to understand is `belongsToMany()`. You will reach for this type whenever you're working with pivot tables. We'll use the example of jobs and tags to illustrate the basic concept.

# 13. Eager Loading and the N+1 Problem

Lazy loading is useful, but, if you're not careful, it can lead to some nasty performance issues. In this episode, we'll discuss the N+1 problem and how to avoid it by leveraging eager loading.

# 14. All You Need to Know About Pagination

Today, we move on to the topic of pagination. Up until this point, we've been fetching every record from the database. But that's hardly appropriate in most situations. Let's fix that!

# 15. Understanding Database Seeders

What happens when we refresh our migrations, and then lose all of our records within the database? Do we have to manually run all of those factories again? Absolutely not. Instead, we can read for database seeders.

# 16. Forms and CSRF Explained (with Examples)

We finally get to discuss forms. However, in order to do so, we also must review a confusing topic: Cross-Site Request Forgery (CSRF).

# 17. Always Validate. Never Trust the User.

So far, we've taken the happy path. But that just won't do in real life. No we must always assume that the user is malicious. For this reason, both client and server-side validation is a must.

# 18. Editing, Updating, and Deleting a Resource

In this episode, you'll learn how to prepare the appropriate markup and routing to handle the process of editing, updating, and deleting a record.

# 19. Routes Reloaded - 6 Essential Tips

Let's return to our routes file. In this episode, I have six essential routing tips that you should be aware of. You'll learn about implicit model binding, route resources, listing your routes, and more.

# 20. Starter Kits, Breeze, and Middleware

We begin this new chapter with a review of Laravel Breeze, which provides initial project scaffolding for your new application. We also discuss middleware for the first time.

# 21. Make a Login and Registration System From Scratch: Part 1

Now that you know how to use Laravel Breeze to jump-start a new app, let's take two episodes to review how you might build the beginnings of an authentication system from scratch.

# 22. Make a Login and Registration System From Scratch: Part 2

Okay, let's finish up. In this episode, we'll write the necessary controller logic to handle registering a user, as well as authenticating them.

# 23. 6 Steps to Authorization Mastery

This is a special episode. We can now move on to the important subject of authorization. Laravel has a variety of APIs to assist in this area, so I've broken down everything you need to know into six steps.