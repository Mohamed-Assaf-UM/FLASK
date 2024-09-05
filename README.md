## SQL and SQLite Overview

**SQL** (Structured Query Language) is a standard language used to manage and manipulate relational databases. It allows users to perform operations like querying, updating, and managing databases.

**SQLite** is a lightweight, serverless database engine that's widely used for embedded systems. It's self-contained and doesn't require complex configurations, making it ideal for small-scale applications and for learning database management.

---
In the context of databases, a **cursor** is an object that allows you to interact with the data in a database. Think of it like a pointer or a control handle to perform SQL commands and retrieve results.

Here’s an easy way to understand it:

- **Creating the Cursor**: When you connect to a database, you need a way to execute SQL queries and fetch results. The cursor is that way. It acts as a messenger between your Python code and the database.
  
- **Executing Queries**: You use the cursor to execute SQL commands like creating tables, inserting data, or retrieving records from the database.
  
- **Fetching Results**: After executing a query (like `SELECT *`), the cursor holds the results temporarily, and you can fetch them (usually with `fetchall()` or `fetchone()`).

Example:
```python
cursor = connection.cursor()
```
Here, `cursor` is created using the `connection` to the database. It allows us to execute SQL queries, like:

```python
cursor.execute('SELECT * FROM employees')
```

In simple terms, **a cursor is a tool that helps you send SQL commands to the database and get the results back.**
### Connecting to SQLite Database

We begin by using the `sqlite3` library to connect to an SQLite database. If the specified database doesn't exist, SQLite automatically creates it.

```python
import sqlite3

# Connect to an SQLite database
connection = sqlite3.connect('example.db')
```

This connects to (or creates) a database file named `example.db`. The `connection` object allows us to interact with the database.

---

### Creating a Table

Once connected, we can create tables to store data. Here’s how to create an `employees` table:

```python
cursor = connection.cursor()

# Create a table called employees
cursor.execute('''
    CREATE TABLE IF NOT EXISTS employees (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        age INTEGER,
        department TEXT
    )
''')
```

- **id**: A unique identifier for each employee.
- **name**: The name of the employee.
- **age**: Age of the employee.
- **department**: The department where the employee works.

---

### Inserting Data into the Table

We can insert data into the table using SQL's `INSERT INTO` command. Here's how to add employees to the table:

```python
# Insert employee data into the table
cursor.execute('''
    INSERT INTO employees (name, age, department)
    VALUES ('Krish', 32, 'Data Scientist')
''')

cursor.execute('''
    INSERT INTO employees (name, age, department)
    VALUES ('Bob', 25, 'Engineering')
''')

cursor.execute('''
    INSERT INTO employees (name, age, department)
    VALUES ('Charlie', 35, 'Finance')
''')

# Commit the changes to the database
connection.commit()
```

This code inserts three employees: Krish, Bob, and Charlie.

---

### Querying Data from the Table

Once data is inserted, we can retrieve it using SQL’s `SELECT` statement:

```python
# Query data from the employees table
cursor.execute('SELECT * FROM employees')
rows = cursor.fetchall()

# Print the queried data
for row in rows:
    print(row)
```

Output:
```
(1, 'Krish', 32, 'Data Scientist')
(2, 'Bob', 25, 'Engineering')
(3, 'Charlie', 35, 'Finance')
```

---

### Updating Data in the Table

We can update existing data using the `UPDATE` statement. For example, updating Krish's age:

```python
# Update Krish's age to 34
cursor.execute('''
    UPDATE employees
    SET age = 34
    WHERE name = 'Krish'
''')

# Commit the change
connection.commit()
```

After the update, querying the table shows that Krish's age has been updated to 34.

---

### Deleting Data from the Table

We can remove specific data using the `DELETE` statement. For instance, removing Bob’s record:

```python
# Delete Bob from the employees table
cursor.execute('''
    DELETE FROM employees
    WHERE name = 'Bob'
''')

# Commit the change
connection.commit()
```

Now, if we query the table again, only Krish and Charlie's records will remain.

---

### Working with Sales Data

Let's create and work with another table, this time for **sales** data. We will follow the same process: connecting to a new database, creating a table, inserting data, and querying it.

#### Creating the Sales Table

```python
# Connect to a sales database
connection = sqlite3.connect('sales_data.db')
cursor = connection.cursor()

# Create a table for sales data
cursor.execute('''
    CREATE TABLE IF NOT EXISTS sales (
        id INTEGER PRIMARY KEY,
        date TEXT NOT NULL,
        product TEXT NOT NULL,
        sales INTEGER,
        region TEXT
    )
''')
```

#### Inserting Data into the Sales Table

We can insert multiple rows into the sales table at once using `executemany()`:

```python
# Insert sales data into the table
sales_data = [
    ('2023-01-01', 'Product1', 100, 'North'),
    ('2023-01-02', 'Product2', 200, 'South'),
    ('2023-01-03', 'Product1', 150, 'East'),
    ('2023-01-04', 'Product3', 250, 'West'),
    ('2023-01-05', 'Product2', 300, 'North')
]

cursor.executemany('''
    INSERT INTO sales (date, product, sales, region)
    VALUES (?, ?, ?, ?)
''', sales_data)

# Commit the changes
connection.commit()
```

The `cursor.executemany()` function allows you to **insert many rows of data into a database at once** instead of doing it one by one.

### What Does This Code Do?

```python
cursor.executemany('''
    INSERT INTO sales (date, product, sales, region)
    VALUES (?, ?, ?, ?)
''', sales_data)
```

1. **`INSERT INTO`**: This adds new rows to the `sales` table.
2. **`?` Marks**: These are placeholders where actual values (like dates, products, etc.) will go.
3. **`sales_data`**: This is a list of multiple pieces of data to insert. Each tuple inside this list represents one row.

### Example Breakdown

The data we want to insert looks like this:
```python
sales_data = [
    ('2023-01-01', 'Product1', 100, 'North'),
    ('2023-01-02', 'Product2', 200, 'South'),
    ...
]
```

- First, the code inserts the row: `'2023-01-01', 'Product1', 100, 'North'`.
- Then, it inserts the next row: `'2023-01-02', 'Product2', 200, 'South'`.
- And it continues doing this for every row in the `sales_data` list.

### Summary

Instead of inserting one row at a time, **`executemany()` inserts all the rows in one go** by filling in the placeholders (`?`) with the data from each tuple in `sales_data`. This makes the process faster and easier!
#### Querying Sales Data

To view the sales data, we use:

```python
# Query data from the sales table
cursor.execute('SELECT * FROM sales')
rows = cursor.fetchall()

# Print the queried data
for row in rows:
    print(row)
```
````
(1, '2023-01-01', 'Product1', 100, 'North')
(2, '2023-01-02', 'Product2', 200, 'South')
(3, '2023-01-03', 'Product1', 150, 'East')
(4, '2023-01-04', 'Product3', 250, 'West')
(5, '2023-01-05', 'Product2', 300, 'North')
````
This will output the sales data, showing the product sales across different regions.

---

### Closing the Connection

Always remember to close the database connection when done:

```python
connection.close()
```

---

### Common Error: "Cannot Operate on a Closed Database"

If you try to query data after closing the connection, you'll encounter the following error:

```
ProgrammingError: Cannot operate on a closed database.
```

This happens because the `connection.close()` command terminates the connection, so make sure all database operations are completed before closing.

---
Once you close the connection to the database using connection.close(), you can no longer interact with the database, including querying data.

Here’s why:

Connection is the Link: The connection is like a bridge between your Python code and the database. When it's open, you can send commands (like queries) through it.

Closing the Connection: When you use connection.close(), you're essentially cutting off that bridge. This means Python no longer has a way to talk to the database.

No Way to Communicate: After the connection is closed, trying to execute a query results in an error because the database can't understand the request anymore. It's like trying to make a phone call after hanging up—you have no connection!



