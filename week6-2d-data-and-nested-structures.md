# Week 6: 2D Data and Nested Structures

## Core Focus
Working confidently with 2D lists (grids, matrices, tables), lists of dictionaries, and dictionaries of lists. Understanding how to access, update, and traverse nested structures for realistic data problems.

## Prior Knowledge Assumed
- Basic 2D lists (introduced in GCSE Week 5)
- Dictionaries (GCSE and Week 5 of this course)
- Nested `for` loops (GCSE Week 3)
- List comprehensions
- Functions and decomposition

## Learning Objectives
- Create and traverse 2D lists (row/column access)
- Update, search, and process data within 2D structures
- Use 2D lists to represent grids, game boards, and tables
- Use lists of dictionaries to represent collections of records
- Use dictionaries of lists to group data
- Design appropriate nested structures for a given problem
- Apply functional decomposition to programs that use complex nested data

---

## Lesson Content

### 1. 2D Lists — The Foundation

A 2D list is a list of lists. It is the natural way to represent a **table** or **grid** in Python.

```python
# Creating a 2D list (3 rows, 4 columns)
grid = [
    [1,  2,  3,  4],
    [5,  6,  7,  8],
    [9, 10, 11, 12],
]

# Access: grid[row][column] — both 0-indexed
print(grid[0][0])   # 1  (top-left)
print(grid[1][2])   # 7  (row 1, column 2)
print(grid[2][3])   # 12 (bottom-right)

# Update a value
grid[1][1] = 99
```

---

### 2. Creating 2D Lists Programmatically

```python
# 3-row, 4-column grid initialised to 0
rows, cols = 3, 4
grid = [[0] * cols for _ in range(rows)]
print(grid)
# [[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]
```

> **Important:** Do NOT use `[[0] * cols] * rows`. This creates rows that are references to the same list — modifying one row modifies all of them!

```python
# Bug: all rows are the same object
bad_grid = [[0] * 4] * 3
bad_grid[0][0] = 99
print(bad_grid)  # [[99, 0, 0, 0], [99, 0, 0, 0], [99, 0, 0, 0]]  -- NOT what you want

# Correct: each row is an independent list
good_grid = [[0] * 4 for _ in range(3)]
good_grid[0][0] = 99
print(good_grid)  # [[99, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]
```

---

### 3. Traversing 2D Lists

```python
grid = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9],
]

# Print every element
for row in grid:
    for value in row:
        print(value, end=" ")
    print()   # New line after each row

# Access with indices (useful when you need row/col)
for row_index in range(len(grid)):
    for col_index in range(len(grid[row_index])):
        print(f"[{row_index}][{col_index}] = {grid[row_index][col_index]}")
```

---

### 4. Practical 2D List: A Grade Table

```python
# Rows: students; Columns: subjects (Maths, English, Science)
grades = [
    [85, 78, 91],   # Alice
    [72, 85, 69],   # Bob
    [90, 88, 95],   # Charlie
]
names = ["Alice", "Bob", "Charlie"]
subjects = ["Maths", "English", "Science"]

def row_average(row):
    """Return the average of a row."""
    return sum(row) / len(row)

def column_average(grid, col):
    """Return the average of a column in the grid."""
    return sum(grid[row][col] for row in range(len(grid))) / len(grid)

# Student averages
for i, name in enumerate(names):
    avg = row_average(grades[i])
    print(f"{name}: {avg:.1f}")

# Subject averages
for j, subject in enumerate(subjects):
    avg = column_average(grades, j)
    print(f"{subject} average: {avg:.1f}")
```

---

### 5. 2D List as a Game Board

```python
def create_board(rows, cols, empty=" "):
    """Create an empty game board."""
    return [[empty] * cols for _ in range(rows)]

def display_board(board):
    """Print the board with row/column separators."""
    print("  " + " | ".join(str(c) for c in range(len(board[0]))))
    print("  " + "-+-".join(["-"] * len(board[0])))
    for r, row in enumerate(board):
        print(f"{r} " + " | ".join(row))

def place_marker(board, row, col, marker):
    """Place a marker if the cell is empty. Returns True on success."""
    if board[row][col] == " ":
        board[row][col] = marker
        return True
    return False

def check_full(board):
    """Return True if no empty cells remain."""
    for row in board:
        for cell in row:
            if cell == " ":
                return False
    return True

# Demonstration
board = create_board(3, 3)
display_board(board)
place_marker(board, 1, 1, "X")
place_marker(board, 0, 2, "O")
display_board(board)
```

---

### 6. Lists of Dictionaries

A **list of dictionaries** is the natural structure for a collection of records where each record has named fields.

```python
students = [
    {"name": "Alice",   "age": 17, "score": 85, "grade": "A"},
    {"name": "Bob",     "age": 16, "score": 72, "grade": "B"},
    {"name": "Charlie", "age": 17, "score": 91, "grade": "A*"},
    {"name": "Diana",   "age": 16, "score": 68, "grade": "C"},
]

# Access a specific record
print(students[0]["name"])   # Alice

# Add a new student
students.append({"name": "Eve", "age": 17, "score": 79, "grade": "B"})

# Search by name
def find_student(students, name):
    """Return the first student with the given name, or None."""
    for student in students:
        if student["name"].lower() == name.lower():
            return student
    return None

result = find_student(students, "Bob")
print(result)

# Filter
high_achievers = [s for s in students if s["score"] >= 80]
```

---

### 7. Dictionaries of Lists (Grouped Data)

A **dictionary of lists** groups related items under a common key.

```python
# Group students by year group
year_groups = {
    "Year 12": ["Alice", "Bob", "Charlie"],
    "Year 13": ["Diana", "Eve", "Frank"],
}

# Add to a group
year_groups["Year 12"].append("Grace")

# Count students per group
for group, members in year_groups.items():
    print(f"{group}: {len(members)} students")
```

**Building a grouped structure dynamically:**
```python
# Group a list of (name, grade) pairs by grade
results = [
    ("Alice", "A"), ("Bob", "B"), ("Charlie", "A*"),
    ("Diana", "B"), ("Eve", "A"), ("Frank", "C"),
]

by_grade = {}
for name, grade in results:
    if grade not in by_grade:
        by_grade[grade] = []
    by_grade[grade].append(name)

print(by_grade)
# {'A': ['Alice', 'Eve'], 'B': ['Bob', 'Diana'], 'A*': ['Charlie'], 'C': ['Frank']}

# Or using .setdefault()
by_grade = {}
for name, grade in results:
    by_grade.setdefault(grade, []).append(name)
```

---

### 8. Comparing 2D List and List-of-Dicts

| | 2D list | List of dicts |
|---|---|---|
| Access | `data[row][col]` — by position | `data[i]["field"]` — by name |
| Readability | Less clear what columns represent | Clear field names |
| Flexibility | Fixed number of columns per row | Rows can have different keys |
| Use case | Grid/matrix, homogeneous table | Collection of records |

```python
# 2D list approach — score table
scores_2d = [
    ["Alice", 85, 78, 91],
    ["Bob",   72, 85, 69],
]
# scores_2d[0][1] means... what exactly? Requires knowing column order.

# List-of-dicts approach — self-documenting
scores_records = [
    {"name": "Alice", "maths": 85, "english": 78, "science": 91},
    {"name": "Bob",   "maths": 72, "english": 85, "science": 69},
]
# scores_records[0]["maths"] is immediately clear
```

---

## Example Activities

### Activity 1: Grade Table Processor
Given a 2D list of student scores (4 students × 3 subjects), write functions to:
1. Calculate each student's average score
2. Calculate each subject's average score
3. Find the highest score in the entire grid (and state which student/subject)
4. Display the results in a formatted table

### Activity 2: Seating Plan
Create a 5×6 grid representing a classroom seating plan. Write functions to:
1. Assign a student name to a seat (row, col)
2. Find the seat of a given student (linear search)
3. List all empty seats
4. Display the seating plan

### Activity 3: School Database
Using a list of dictionaries, build a simple school database:
1. Load student records from a list (name, year, score)
2. Find all students in a given year group
3. Find the top student in each year group
4. Display all students sorted by score descending
5. Update a student's score

### Activity 4: Timetable
A school timetable maps each day of the week to a list of subjects. Build this as a dictionary of lists:
1. Create a timetable for one student
2. Find all days that include "Maths"
3. Count how many times each subject appears in the week
4. Add a new lesson to Thursday

---

## Misconceptions / Likely Errors

| Misconception | Reality |
|---|---|
| `[[0] * n] * m` creates independent rows | All rows are the same object; use `[[0]*n for _ in range(m)]` |
| `grid[col][row]` is the standard order | Python conventions use `grid[row][col]` |
| `enumerate()` can't be used with 2D lists | `enumerate()` works on the outer list; combine with inner `enumerate()` for full row/col indexing |
| Dictionaries cannot be stored in lists | Lists can contain any Python object, including dictionaries |
| `list_of_dicts[0].name` accesses a field | Dictionaries use square brackets: `list_of_dicts[0]["name"]` |

**Common 2D indexing error:**
```python
grid = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# Wrong: grid[col][row]
print(grid[2][0])   # Returns 7 (row 2, col 0), not column 2, row 0

# Correct mental model: grid[row][col]
print(grid[0][2])   # 3 (row 0, col 2)
```

---

## Assessment Opportunities

- **Code reading:** Given a 2D list manipulation, trace through and state the final state of the list
- **Design task:** "Design a data structure to store the results of a round-robin tournament. Justify your choice."
- **Exam-style question:** "Write a function that takes a 2D list of integers and returns the index of the row with the highest total."
- **Error correction:** Provide code that uses `[[0] * n] * m` and has a resulting bug; students identify and correct it.

---

## Homework / Independent Study

1. **Grid algorithms:** Write a function `transpose(matrix)` that swaps rows and columns of a 2D list. For example, `[[1,2,3],[4,5,6]]` becomes `[[1,4],[2,5],[3,6]]`.

2. **Record system:** Write a phonebook program using a list of dictionaries. Support: add contact, search by name, update phone number, delete contact, display all contacts alphabetically.

3. **Data analysis:** Given a CSV file where each row is a student name followed by scores in five subjects, load it into a list of dictionaries, then compute: per-student average, per-subject class average, and a rank order of students by average score.

4. **Challenge:** Implement Conway's Game of Life for a 10×10 grid. Use a 2D list for the board. Rules: any live cell with 2 or 3 live neighbours survives; any dead cell with exactly 3 live neighbours becomes alive; all others die.
