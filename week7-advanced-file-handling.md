# Week 7: Advanced File Handling and Data Formats

## Core Focus
Going beyond basic text file reading and writing to structured data formats: the `csv` module, the `json` module, and robust file-handling patterns using exception handling throughout.

## Prior Knowledge Assumed
- Basic text file I/O with `open()`, `with`, `read()`, `readline()`, `readlines()`, `write()` (GCSE Week 7)
- `strip()` and `split()` for parsing lines
- Basic `try`/`except FileNotFoundError`
- Exception handling with `try`/`except`/`else`/`finally` (Week 4 of this course)
- Dictionaries and lists of dictionaries (Week 5 of this course)

## Learning Objectives
- Use the `csv` module to read and write CSV files reliably
- Use `csv.DictReader` and `csv.DictWriter` for named-field CSV processing
- Use the `json` module to read and write JSON data
- Understand the difference between text files, CSV, and JSON and when to use each
- Apply robust exception handling to all file operations
- Write functions that cleanly separate file I/O from data processing

---

## Lesson Content

### 1. Why Go Beyond Basic Text Files?

At GCSE, CSV-style data was processed manually:
```python
# GCSE approach
with open("students.csv") as f:
    for line in f:
        parts = line.strip().split(",")
        name, score = parts[0], int(parts[1])
```

This breaks when:
- A field contains a comma (e.g. `"Smith, John"`)
- A field contains quotes or special characters
- The file has a header row that needs to be skipped or used
- You need to write data back correctly

The `csv` module handles all of these cases correctly.

---

### 2. The `csv` Module — Reading

```python
import csv

# Basic reader
with open("students.csv", "r", newline="") as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)   # row is a list of strings
```

**Reading with a header row:**
```python
import csv

with open("students.csv", "r", newline="") as f:
    reader = csv.reader(f)
    headers = next(reader)   # Read and skip the header row
    print(f"Columns: {headers}")
    
    for row in reader:
        name = row[0]
        score = int(row[1])
        print(f"{name}: {score}")
```

**Using `DictReader` (recommended for labelled data):**
```python
import csv

with open("students.csv", "r", newline="") as f:
    reader = csv.DictReader(f)   # First row automatically used as headers
    
    students = []
    for row in reader:
        students.append({
            "name":  row["name"],
            "score": int(row["score"]),
            "grade": row["grade"],
        })

print(students)
```

With `DictReader`, each row is a dictionary keyed by the column headers. This is far more readable and robust than positional indexing.

---

### 3. The `csv` Module — Writing

```python
import csv

students = [
    ["Alice", 85, "A"],
    ["Bob",   72, "B"],
    ["Charlie", 91, "A*"],
]

with open("output.csv", "w", newline="") as f:
    writer = csv.writer(f)
    writer.writerow(["name", "score", "grade"])   # Header
    writer.writerows(students)                     # All rows at once
```

**Using `DictWriter`:**
```python
import csv

students = [
    {"name": "Alice",   "score": 85, "grade": "A"},
    {"name": "Bob",     "score": 72, "grade": "B"},
    {"name": "Charlie", "score": 91, "grade": "A*"},
]

fieldnames = ["name", "score", "grade"]

with open("students.csv", "w", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=fieldnames)
    writer.writeheader()     # Write the header row
    writer.writerows(students)
```

> **Always use `newline=""` when opening CSV files on Windows** to prevent extra blank lines.

---

### 4. Complete CSV CRUD Example

```python
import csv

FILENAME = "records.csv"
FIELDS = ["id", "name", "score"]

def load_records():
    """Load all records from the CSV file."""
    try:
        with open(FILENAME, "r", newline="") as f:
            reader = csv.DictReader(f)
            return [row for row in reader]
    except FileNotFoundError:
        return []

def save_records(records):
    """Save all records to the CSV file."""
    with open(FILENAME, "w", newline="") as f:
        writer = csv.DictWriter(f, fieldnames=FIELDS)
        writer.writeheader()
        writer.writerows(records)

def add_record(records, record_id, name, score):
    """Add a new record to the list."""
    records.append({"id": str(record_id), "name": name, "score": str(score)})

def find_record(records, name):
    """Find a record by name (case-insensitive)."""
    for record in records:
        if record["name"].lower() == name.lower():
            return record
    return None

# Usage
data = load_records()
add_record(data, 1, "Alice", 85)
add_record(data, 2, "Bob", 72)
save_records(data)

loaded = load_records()
result = find_record(loaded, "alice")
print(result)
```

---

### 5. The `json` Module

JSON (JavaScript Object Notation) is a widely used format for structured data. Python's `json` module converts between Python objects and JSON strings or files.

**Python ↔ JSON type mapping:**

| Python | JSON |
|--------|------|
| dict   | object `{}` |
| list   | array `[]` |
| str    | string `""` |
| int/float | number |
| True/False | true/false |
| None   | null |

#### Writing JSON
```python
import json

data = {
    "students": [
        {"name": "Alice", "score": 85, "grade": "A"},
        {"name": "Bob",   "score": 72, "grade": "B"},
    ],
    "class": "10A",
    "year": 2025,
}

# Write to a file
with open("data.json", "w") as f:
    json.dump(data, f, indent=2)   # indent=2 for readable formatting

# Convert to a JSON string (not a file)
json_string = json.dumps(data, indent=2)
print(json_string)
```

#### Reading JSON
```python
import json

# Read from a file
with open("data.json", "r") as f:
    data = json.load(f)

print(data["class"])              # 10A
print(data["students"][0]["name"])  # Alice

# Parse a JSON string
json_string = '{"name": "Alice", "score": 85}'
record = json.loads(json_string)
print(record["name"])   # Alice
```

#### Handling JSON Errors
```python
import json

def load_json_file(filename):
    """Load a JSON file safely."""
    try:
        with open(filename, "r") as f:
            return json.load(f)
    except FileNotFoundError:
        print(f"File not found: {filename}")
        return None
    except json.JSONDecodeError as e:
        print(f"Invalid JSON in {filename}: {e}")
        return None

def save_json_file(filename, data):
    """Save data as a JSON file."""
    try:
        with open(filename, "w") as f:
            json.dump(data, f, indent=2)
        return True
    except (IOError, TypeError) as e:
        print(f"Could not save {filename}: {e}")
        return False
```

---

### 6. Choosing Between Text, CSV, and JSON

| Format | Best for | Pros | Cons |
|--------|---------|------|------|
| Plain text | Simple lines, log files, single values | Easy to read/write | No structure for multiple fields |
| CSV | Tabular data (rows and columns) | Compact, widely supported | Poor for nested/complex data |
| JSON | Structured, nested, hierarchical data | Flexible, human-readable, nested | Slightly larger files |

```python
# Use plain text for: a list of names, a log file, a simple config value
# Use CSV for: a student grade table, a product catalogue
# Use JSON for: a complex configuration, data with nested structures,
#               communicating between programs
```

---

### 7. Robust File Handling Patterns

#### A Reusable File-Reading Function
```python
import csv
import json

def read_csv(filename):
    """Read a CSV file with headers into a list of dicts. Returns [] on error."""
    try:
        with open(filename, "r", newline="") as f:
            return list(csv.DictReader(f))
    except FileNotFoundError:
        print(f"Warning: {filename} not found.")
        return []
    except csv.Error as e:
        print(f"CSV error in {filename}: {e}")
        return []

def write_csv(filename, records, fieldnames):
    """Write a list of dicts to a CSV file. Returns True on success."""
    try:
        with open(filename, "w", newline="") as f:
            writer = csv.DictWriter(f, fieldnames=fieldnames)
            writer.writeheader()
            writer.writerows(records)
        return True
    except IOError as e:
        print(f"Could not write {filename}: {e}")
        return False

def read_json(filename, default=None):
    """Read a JSON file. Returns default value on error."""
    try:
        with open(filename, "r") as f:
            return json.load(f)
    except (FileNotFoundError, json.JSONDecodeError) as e:
        print(f"Could not read {filename}: {e}")
        return default

def write_json(filename, data):
    """Write data to a JSON file. Returns True on success."""
    try:
        with open(filename, "w") as f:
            json.dump(data, f, indent=2)
        return True
    except (IOError, TypeError) as e:
        print(f"Could not write {filename}: {e}")
        return False
```

---

## Example Activities

### Activity 1: CSV Student Database
Create a student database stored in a CSV file. Write functions to:
1. Load all students from the file
2. Add a new student
3. Find a student by name and display their details
4. Update a student's score
5. Delete a student
6. Save changes back to the file

### Activity 2: JSON Configuration
Write a program that stores its settings (e.g. school name, pass threshold, max score) in a JSON file. On startup, load the settings from JSON. Allow the user to update settings and save them back. If the file doesn't exist, use default values.

### Activity 3: CSV to JSON Converter
Write a function `csv_to_json(csv_filename, json_filename)` that reads a CSV file with headers and writes the data as a JSON array. Test it with a student grade CSV file.

### Activity 4: Log File Analyser
Write a program that reads a plain-text log file where each line has the format:
`2025-01-15 14:32:07 ERROR Failed to connect`

Parse each line and:
1. Count entries by level (ERROR, WARNING, INFO)
2. Find all ERROR messages
3. Write a summary to a new file
4. Handle malformed lines gracefully with exception handling

---

## Misconceptions / Likely Errors

| Misconception | Reality |
|---|---|
| The `csv` module is only for reading | `csv.writer` and `csv.DictWriter` handle writing too |
| `json.load()` and `json.loads()` are the same | `json.load()` reads from a **file object**; `json.loads()` parses a **string** |
| `json.dump()` and `json.dumps()` are the same | `json.dump()` writes to a **file**; `json.dumps()` returns a **string** |
| Omitting `newline=""` on Windows is fine | On Windows, text mode adds `\r\n` which creates blank lines in CSV output |
| CSV handles all special characters automatically | The `csv` module handles quoting and commas; it does not handle encoding — use `encoding="utf-8"` if needed |
| JSON can store any Python object | JSON supports dict, list, str, int, float, bool, None — **not** sets, tuples, or custom objects directly |

---

## Assessment Opportunities

- **Code reading:** Given a `csv.DictReader` loop, trace through and predict what the list of dictionaries will contain
- **Spot the difference:** Given two versions of a CSV-writing function (one using manual `join`, one using `csv.writer`), explain why the `csv` module version is more robust
- **Exam-style question:** "Write a function that reads a JSON file containing a list of products (name, price) and returns the name of the most expensive product."
- **Short answer:** "State one advantage of using JSON over plain text for storing structured data in a Python program."

---

## Homework / Independent Study

1. **Practice task:** Write a contact book program that loads/saves data in JSON format. Support adding, searching, updating, and deleting contacts.

2. **Format comparison:** Store the same data set (ten student records) as a plain text file, a CSV file, and a JSON file. Compare the file sizes and the code needed to read each one. Write a brief comparison.

3. **Extend the log analyser:** Add the ability to filter log entries by date range. The date is the first token on each line.

4. **Research:** Look up Python's `os.path` module. Find three functions that are useful for working with file paths and filenames. Write a short example of each.
