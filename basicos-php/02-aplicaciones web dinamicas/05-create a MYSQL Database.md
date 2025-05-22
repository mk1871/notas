
Now that we've built a basic router, the next step is to learn how to interact with a MySQL database.

### Creating a Database

You can create a database directly from the terminal:

```bash
mysql -u root
CREATE DATABASE myapp;
```

Alternatively, use a GUI like TablePlus to create and manage your database visually. Connect to localhost on port 3306 with user `root` and no password (for local development).

### Creating a Table

Within your database, create a table for blog posts. Think of a table like an Excel spreadsheet or a web form.

Common columns might include:

- `id` (primary key, auto-incrementing)
- `title` (e.g., VARCHAR(255), not nullable)

Example column definition:

- Name: `title`
- Type: `VARCHAR(255)`
- Nullable: No
- Default: None

Save the table to persist the structure.

### Adding Data

Insert records into your table. The `id` will auto-increment:

|id|title|
|---|---|
|1|My first blog post|
|2|My second blog post|

### Relational Databases

In real applications, you might add more columns like post body, status (draft, published), and relationships to other tables, such as a `users` table to track authorship.

This linking of tables is what makes a database _relational_.

---

Your homework is to practice:

- Adding new columns
- Creating new tables
- Updating records

This will build your comfort with database structure before moving on to connecting PHP with MySQL.