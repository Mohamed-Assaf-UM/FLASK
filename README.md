## SQL and SQLite Overview

**SQL** (Structured Query Language) is a standard language used to manage and manipulate relational databases. It allows users to perform operations like querying, updating, and managing databases.

**SQLite** is a lightweight, serverless database engine that's widely used for embedded systems. It's self-contained and doesn't require complex configurations, making it ideal for small-scale applications and for learning database management.

---

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


