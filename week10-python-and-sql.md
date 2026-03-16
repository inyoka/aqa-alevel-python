# Week 10: Python and Databases — SQL Integration

## Core Focus
Connecting Python programs to a SQLite relational database using the `sqlite3` module: creating tables, inserting and querying data, using parameterised queries, and understanding how Python-database integration fits alongside the SQL knowledge developed in the [A-Level SQL guide](https://github.com/nuast-dev/alevel-sql).

## Prior Knowledge Assumed
- File handling and CSV/JSON (Week 7 of this course)
- Exception handling (Week 4 of this course)
- Dictionaries and lists of dictionaries (Week 5 of this course)
- SQL basics: SELECT, INSERT, UPDATE, DELETE, WHERE, ORDER BY (from the A-Level SQL guide)
- Functions and decomposition

## Learning Objectives
- Connect a Python program to a SQLite database using `sqlite3`
- Create tables, insert data, and retrieve results from Python
- Use parameterised queries to pass data safely
- Fetch query results as lists of tuples or as `Row` objects
- Understand the connection/cursor model
- Handle database errors with exception handling
- Understand when a database is more appropriate than a CSV or JSON file
- Relate Python `sqlite3` usage to the SQL taught in the A-Level SQL guide

---

## Lesson Content

### 1. Why Use a Database from Python?

| CSV/JSON file | SQLite database |
|---|---|
| Simple; no setup | Requires `sqlite3` (built in, no install needed) |
| Good for small, flat data | Good for relational, structured data |
| No querying language | Full SQL: filter, sort, join, aggregate |
| Concurrent access is unsafe | Better support for concurrent reads |
| Good for data exchange | Good for persistent application data |

A database is the right choice when:
- Data is relational (linked tables)
- You need to query, filter, or aggregate data efficiently
- The data set is large
- Multiple records need updating atomically

---

### 2. The `sqlite3` Module — Basics

SQLite stores the entire database in a single file. No server is needed.

```python
import sqlite3

# Connect to a database (creates the file if it doesn't exist)
connection = sqlite3.connect("school.db")

# Create a cursor (used to execute SQL statements)
cursor = connection.cursor()

# Execute a SQL statement
cursor.execute("""
    CREATE TABLE IF NOT EXISTS students (
        id      INTEGER PRIMARY KEY AUTOINCREMENT,
        name    TEXT NOT NULL,
        score   INTEGER NOT NULL,
        grade   TEXT
    )
""")

# Always commit changes to persist them
connection.commit()

# Always close the connection when done
connection.close()
```

---

### 3. Inserting Data

```python
import sqlite3

connection = sqlite3.connect("school.db")
cursor = connection.cursor()

# Insert a single row
cursor.execute(
    "INSERT INTO students (name, score, grade) VALUES (?, ?, ?)",
    ("Alice", 85, "A")    # Parameters are passed as a tuple
)

# Insert multiple rows at once
students = [
    ("Bob",     72, "B"),
    ("Charlie", 91, "A*"),
    ("Diana",   68, "C"),
]
cursor.executemany(
    "INSERT INTO students (name, score, grade) VALUES (?, ?, ?)",
    students
)

connection.commit()
connection.close()
```

> **Always use parameterised queries (`?` placeholders).** Never format SQL strings with user input directly — this prevents SQL injection.

```python
# NEVER do this:
name = input("Name: ")
cursor.execute(f"SELECT * FROM students WHERE name = '{name}'")   # UNSAFE!

# Always do this:
cursor.execute("SELECT * FROM students WHERE name = ?", (name,))  # SAFE
```

---

### 4. Querying Data

```python
import sqlite3

connection = sqlite3.connect("school.db")
cursor = connection.cursor()

# Fetch all rows
cursor.execute("SELECT * FROM students ORDER BY score DESC")
rows = cursor.fetchall()   # Returns a list of tuples

for row in rows:
    print(row)   # e.g. (1, 'Charlie', 91, 'A*')

# Access by position
for row in rows:
    student_id = row[0]
    name       = row[1]
    score      = row[2]
    grade      = row[3]
    print(f"{name}: {score} ({grade})")

# Fetch one row
cursor.execute("SELECT * FROM students WHERE name = ?", ("Alice",))
alice = cursor.fetchone()   # Returns one tuple, or None
if alice:
    print(f"Found: {alice}")

# Fetch a limited number of rows
cursor.execute("SELECT * FROM students ORDER BY score DESC LIMIT 3")
top_three = cursor.fetchmany(3)

connection.close()
```

---

### 5. Using `Row` Factory for Named Access

By default, results are tuples. Setting `row_factory` gives named access like a dictionary.

```python
import sqlite3

connection = sqlite3.connect("school.db")
connection.row_factory = sqlite3.Row   # Enable named column access

cursor = connection.cursor()
cursor.execute("SELECT * FROM students ORDER BY name")

for row in cursor.fetchall():
    print(f"{row['name']}: {row['score']} ({row['grade']})")

connection.close()
```

---

### 6. Updating and Deleting Data

```python
import sqlite3

connection = sqlite3.connect("school.db")
cursor = connection.cursor()

# Update a record
cursor.execute(
    "UPDATE students SET score = ?, grade = ? WHERE name = ?",
    (95, "A*", "Alice")
)
print(f"Rows updated: {cursor.rowcount}")

# Delete a record
cursor.execute(
    "DELETE FROM students WHERE name = ?",
    ("Bob",)
)
print(f"Rows deleted: {cursor.rowcount}")

connection.commit()
connection.close()
```

---

### 7. Using `with` for Connection Management

Using `with` ensures the connection is properly closed even if an error occurs.

```python
import sqlite3

with sqlite3.connect("school.db") as connection:
    cursor = connection.cursor()
    cursor.execute("SELECT COUNT(*) FROM students")
    count = cursor.fetchone()[0]
    print(f"Total students: {count}")
# Connection automatically closed here
```

---

### 8. Robust Database Functions

Combining everything into a reusable module:

```python
import sqlite3

DB_FILE = "school.db"

def get_connection():
    """Return a database connection with row factory enabled."""
    connection = sqlite3.connect(DB_FILE)
    connection.row_factory = sqlite3.Row
    return connection

def initialise_database():
    """Create tables if they do not already exist."""
    with get_connection() as conn:
        conn.execute("""
            CREATE TABLE IF NOT EXISTS students (
                id    INTEGER PRIMARY KEY AUTOINCREMENT,
                name  TEXT NOT NULL,
                score INTEGER NOT NULL,
                grade TEXT
            )
        """)

def add_student(name, score, grade):
    """Insert a new student record."""
    try:
        with get_connection() as conn:
            conn.execute(
                "INSERT INTO students (name, score, grade) VALUES (?, ?, ?)",
                (name, score, grade)
            )
    except sqlite3.Error as e:
        print(f"Database error: {e}")

def get_all_students():
    """Return all students ordered by score descending."""
    try:
        with get_connection() as conn:
            cursor = conn.execute("SELECT * FROM students ORDER BY score DESC")
            return [dict(row) for row in cursor.fetchall()]
    except sqlite3.Error as e:
        print(f"Database error: {e}")
        return []

def find_student(name):
    """Find a student by name (case-insensitive). Returns dict or None."""
    try:
        with get_connection() as conn:
            cursor = conn.execute(
                "SELECT * FROM students WHERE LOWER(name) = LOWER(?)",
                (name,)
            )
            row = cursor.fetchone()
            return dict(row) if row else None
    except sqlite3.Error as e:
        print(f"Database error: {e}")
        return None

def update_score(name, new_score, new_grade):
    """Update a student's score and grade."""
    try:
        with get_connection() as conn:
            conn.execute(
                "UPDATE students SET score = ?, grade = ? WHERE LOWER(name) = LOWER(?)",
                (new_score, new_grade, name)
            )
            return conn.execute("SELECT changes()").fetchone()[0] > 0
    except sqlite3.Error as e:
        print(f"Database error: {e}")
        return False

def delete_student(name):
    """Delete a student by name."""
    try:
        with get_connection() as conn:
            conn.execute(
                "DELETE FROM students WHERE LOWER(name) = LOWER(?)",
                (name,)
            )
    except sqlite3.Error as e:
        print(f"Database error: {e}")

# Demo
initialise_database()
add_student("Alice",   85, "A")
add_student("Bob",     72, "B")
add_student("Charlie", 91, "A*")

for student in get_all_students():
    print(student)

print(find_student("alice"))
update_score("Bob", 78, "B")
print(get_all_students())
```

---

### 9. Aggregation and Filtering in SQL from Python

These SQL techniques align directly with the [A-Level SQL guide](https://github.com/nuast-dev/alevel-sql):

```python
import sqlite3

def get_average_score():
    """Return the average score across all students."""
    with get_connection() as conn:
        cursor = conn.execute("SELECT AVG(score) FROM students")
        return cursor.fetchone()[0]

def get_students_above(threshold):
    """Return students with scores above the threshold."""
    with get_connection() as conn:
        cursor = conn.execute(
            "SELECT * FROM students WHERE score > ? ORDER BY score DESC",
            (threshold,)
        )
        return [dict(row) for row in cursor.fetchall()]

def count_by_grade():
    """Return a dictionary mapping each grade to its count."""
    with get_connection() as conn:
        cursor = conn.execute(
            "SELECT grade, COUNT(*) AS count FROM students GROUP BY grade ORDER BY grade"
        )
        return {row["grade"]: row["count"] for row in cursor.fetchall()}

print(f"Average score: {get_average_score():.1f}")
print(f"Students above 80: {get_students_above(80)}")
print(f"Count by grade: {count_by_grade()}")
```

---

### 10. Multiple Tables and Joins

```python
import sqlite3

with get_connection() as conn:
    # Create a second table
    conn.execute("""
        CREATE TABLE IF NOT EXISTS subjects (
            id   INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL UNIQUE
        )
    """)
    conn.execute("""
        CREATE TABLE IF NOT EXISTS results (
            student_id INTEGER REFERENCES students(id),
            subject_id INTEGER REFERENCES subjects(id),
            score      INTEGER,
            PRIMARY KEY (student_id, subject_id)
        )
    """)

# Query with a JOIN
with get_connection() as conn:
    cursor = conn.execute("""
        SELECT s.name AS student, sub.name AS subject, r.score
        FROM results r
        JOIN students s   ON r.student_id = s.id
        JOIN subjects sub ON r.subject_id = sub.id
        ORDER BY s.name, sub.name
    """)
    for row in cursor.fetchall():
        print(f"{row['student']}: {row['subject']} = {row['score']}")
```

This links directly to JOIN queries covered in the [A-Level SQL guide](https://github.com/nuast-dev/alevel-sql).

---

### 11. Linking to the A-Level SQL Guide

| SQL concept (from alevel-sql guide) | Python `sqlite3` equivalent |
|---|---|
| `SELECT * FROM table` | `cursor.execute("SELECT * FROM table")` |
| `INSERT INTO ... VALUES` | `cursor.execute("INSERT INTO ...", params)` |
| `UPDATE ... SET ... WHERE` | `cursor.execute("UPDATE ...", params)` |
| `DELETE FROM ... WHERE` | `cursor.execute("DELETE FROM ...", params)` |
| `WHERE`, `ORDER BY`, `LIMIT` | Written directly in the SQL string |
| `JOIN` | Written directly in the SQL string |
| `GROUP BY`, `COUNT()`, `AVG()` | Written in SQL; results fetched as tuples |

Python handles the **connection**, **parameterisation**, **error handling**, and **result processing**. SQL handles the **querying**.

---

## Example Activities

### Activity 1: Student Database CRUD
Build a command-line student management system backed by SQLite:
1. Add a student (name, score, grade)
2. View all students (sorted by score descending)
3. Search for a student by name
4. Update a student's score and grade
5. Delete a student

### Activity 2: Library System
Design and implement a two-table database: `books` (id, title, author, year) and `loans` (id, book_id, borrower_name, loan_date). Write functions to:
- Add a book
- Loan a book to a borrower
- Return a book (delete the loan)
- Find all books currently on loan
- Find all books by a given author

### Activity 3: Analytics
Given a database of student exam results (student_name, subject, score), write Python functions to:
1. Return the average score per subject
2. Find the highest-scoring student in each subject
3. Find students who scored above average in more than two subjects
4. Export the results to a JSON file

### Activity 4: Migrate from CSV to SQLite
Write a program that:
1. Reads an existing CSV file of student records
2. Creates a SQLite database with appropriate tables
3. Imports all records from the CSV into the database
4. Verifies the import by checking the count matches

---

## Misconceptions / Likely Errors

| Misconception | Reality |
|---|---|
| `sqlite3.connect()` creates a server | SQLite is serverless — the database is a single file |
| Forgetting `commit()` is fine | Without `commit()`, changes are not saved and will be lost when the connection closes |
| F-string formatting is fine for SQL parameters | Never use f-strings for SQL — always use `?` placeholders to prevent SQL injection |
| `fetchall()` returns a list of dicts | By default it returns a **list of tuples**; use `row_factory = sqlite3.Row` for named access |
| `with sqlite3.connect()` calls `commit()` automatically | Using `with` commits on **normal exit** but rolls back on exception — this is actually useful behaviour |

**SQL injection demonstration (never do this):**
```python
# DANGEROUS - user could enter: Alice'; DROP TABLE students; --
name = input("Search: ")
cursor.execute(f"SELECT * FROM students WHERE name = '{name}'")  # Never!

# SAFE - parameterised query
cursor.execute("SELECT * FROM students WHERE name = ?", (name,))
```

---

## Assessment Opportunities

- **Code reading:** Given a `sqlite3` code snippet, state what SQL is executed and what format the results are returned in
- **Exam-style question:** "Write Python code to insert a new record into a `products` table with columns `id`, `name`, and `price`."
- **Security question:** "Explain why parameterised queries should be used when taking user input to query a database."
- **Design task:** "Design a database schema for a cinema booking system. State the tables, columns, and relationships. Write the Python functions you would need."

---

## Homework / Independent Study

1. **Complete CRUD app:** Build a complete to-do list application backed by SQLite. Tasks have: title, priority (1–5), completed (yes/no), created date. Support: add, view (sorted by priority), mark complete, delete, and view only incomplete tasks.

2. **Aggregation practice:** Add five sample students to your school database with a variety of grades. Write Python functions to compute: class average, grade distribution (count per grade), highest and lowest scores.

3. **Linking to SQL guide:** If you have access to the A-Level SQL guide, identify which SQL queries from that guide you can now execute from Python using `sqlite3`. Write at least three examples.

4. **Research:** Look up SQLite's documentation on transactions. Explain in your own words what a transaction is and when you would use `conn.rollback()`.
