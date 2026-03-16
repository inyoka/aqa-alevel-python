# Week 5: Tuples, Sets, and Dictionaries

## Core Focus
Advanced and confident use of Python's built-in data structures beyond lists: tuples (immutability and packing/unpacking), sets (operations and membership), and dictionaries (deep use, comprehensions, nested structures). Knowing which structure to choose for a given problem.

## Prior Knowledge Assumed
- Lists: creation, indexing, methods (GCSE Week 5)
- Basic dictionaries: used in GCSE for structured records (introduced in Week 6)
- Iteration with `for` loops
- Functions and return values

## Learning Objectives
- Use tuples correctly, exploiting their immutability and packing/unpacking
- Create and manipulate sets; apply set operations (union, intersection, difference)
- Use dictionaries with confidence: create, update, iterate, use `.get()`, `.items()`, `.keys()`, `.values()`
- Write dictionary comprehensions
- Use nested dictionaries and lists of dictionaries for structured data
- Choose the appropriate data structure for a given problem

---

## Lesson Content

### 1. Tuples

A **tuple** is an ordered, **immutable** sequence. Once created, its elements cannot be changed.

```python
# Creating tuples
point = (3, 7)
rgb = (255, 128, 0)
single = (42,)       # Note: trailing comma required for single-element tuple
empty = ()

# Accessing elements (same as lists)
print(point[0])   # 3
print(rgb[-1])    # 0

# Tuples are immutable
point[0] = 10   # TypeError: 'tuple' object does not support item assignment
```

#### When to use a tuple vs a list
| | Tuple | List |
|---|---|---|
| Mutable? | No | Yes |
| Use case | Fixed data, record-like structures, function return | Changing collections |
| As dict key? | Yes (if all elements are hashable) | No |
| Slightly faster? | Yes | No |

```python
# Tuple for a fixed record (name, age, grade)
student = ("Alice", 17, "A")
name, age, grade = student   # Unpacking

# List for a changing collection
scores = [78, 85, 92]
scores.append(88)
```

#### Packing and Unpacking
```python
# Packing: creating a tuple
coordinates = (10, 20)

# Unpacking: assigning to multiple variables
x, y = coordinates
print(x, y)   # 10 20

# Unpacking a function's return value
def min_max(numbers):
    """Return (minimum, maximum) as a tuple."""
    return min(numbers), max(numbers)

low, high = min_max([3, 1, 4, 1, 5, 9, 2, 6])
print(low, high)   # 1 9

# Extended unpacking
first, *rest = (1, 2, 3, 4, 5)
print(first)  # 1
print(rest)   # [2, 3, 4, 5]

*start, last = (1, 2, 3, 4, 5)
print(start)  # [1, 2, 3, 4]
print(last)   # 5
```

#### Tuples in Iteration
```python
pairs = [(1, "one"), (2, "two"), (3, "three")]

for number, word in pairs:   # Unpacking in the loop
    print(f"{number} is {word}")
```

---

### 2. Sets

A **set** is an unordered collection of **unique** elements. Sets are useful for:
- Removing duplicates
- Membership testing (faster than lists for large collections)
- Mathematical set operations

```python
# Creating sets
fruits = {"apple", "banana", "cherry"}
numbers = {1, 2, 3, 4, 5}
empty_set = set()      # NOT {} which creates an empty dict

# From a list (removes duplicates)
values = [1, 2, 2, 3, 3, 3, 4]
unique = set(values)
print(unique)   # {1, 2, 3, 4}
```

#### Membership Testing
```python
allowed_grades = {"A*", "A", "B", "C", "D", "E"}
grade = input("Enter grade: ").upper()

if grade in allowed_grades:     # O(1) lookup
    print("Valid grade")
else:
    print("Invalid grade")
```

#### Modifying Sets
```python
fruits = {"apple", "banana"}

fruits.add("cherry")
print(fruits)   # {'apple', 'banana', 'cherry'}

fruits.remove("banana")    # KeyError if not found
fruits.discard("grape")    # No error if not found

popped = fruits.pop()      # Remove and return an arbitrary element
```

#### Set Operations
```python
a = {1, 2, 3, 4, 5}
b = {3, 4, 5, 6, 7}

print(a | b)   # Union: {1, 2, 3, 4, 5, 6, 7}
print(a & b)   # Intersection: {3, 4, 5}
print(a - b)   # Difference (in a but not b): {1, 2}
print(a ^ b)   # Symmetric difference (in one but not both): {1, 2, 6, 7}

# Check subset/superset
print({3, 4} <= a)   # True (is subset)
print(a >= {3, 4})   # True (is superset)
```

**Practical example — finding common words in two documents:**
```python
def common_words(text1, text2):
    """Return the set of words that appear in both texts."""
    words1 = set(text1.lower().split())
    words2 = set(text2.lower().split())
    return words1 & words2

t1 = "the cat sat on the mat"
t2 = "the dog sat on the floor"
print(common_words(t1, t2))   # {'the', 'sat', 'on'}
```

---

### 3. Dictionaries — Advanced Use

GCSE introduced dictionaries as simple key-value stores. At A-Level, we use them more powerfully.

#### Iteration Patterns
```python
student = {"name": "Alice", "age": 17, "score": 92}

# Iterate over keys
for key in student:
    print(key)

# Iterate over values
for value in student.values():
    print(value)

# Iterate over key-value pairs
for key, value in student.items():
    print(f"{key}: {value}")
```

#### Safe Access with `.get()`
```python
record = {"name": "Bob", "score": 78}

# Risky — KeyError if "grade" not present
grade = record["grade"]

# Safe — returns None (or default) if key missing
grade = record.get("grade")              # None
grade = record.get("grade", "Unknown")   # "Unknown"
```

#### Building Dictionaries Dynamically
```python
# Count word frequencies
text = "the cat sat on the mat the cat"
word_count = {}

for word in text.split():
    word_count[word] = word_count.get(word, 0) + 1

print(word_count)
# {'the': 3, 'cat': 2, 'sat': 1, 'on': 1, 'mat': 1}
```

#### Dictionary Comprehensions
```python
# Create a dictionary mapping numbers to their squares
squares = {n: n ** 2 for n in range(1, 6)}
print(squares)
# {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}

# Filter a dictionary
high_scores = {name: score for name, score in scores.items() if score >= 80}

# Invert a dictionary (swap keys and values)
original = {"a": 1, "b": 2, "c": 3}
inverted = {v: k for k, v in original.items()}
print(inverted)   # {1: 'a', 2: 'b', 3: 'c'}
```

---

### 4. Nested Dictionaries and Lists of Dictionaries

#### List of Dictionaries (Records)
```python
students = [
    {"name": "Alice", "score": 85, "grade": "A"},
    {"name": "Bob",   "score": 72, "grade": "B"},
    {"name": "Charlie", "score": 91, "grade": "A*"},
]

# Find the top scorer
top = max(students, key=lambda s: s["score"])
print(f"Top: {top['name']} ({top['score']})")

# Filter students with grade A or above
high_achievers = [s for s in students if s["grade"] in {"A", "A*"}]
```

#### Nested Dictionaries
```python
school = {
    "10A": {
        "Alice": 85,
        "Bob":   72,
    },
    "10B": {
        "Charlie": 91,
        "Diana":   68,
    }
}

# Access nested value
print(school["10A"]["Alice"])   # 85

# Add a new student
school["10A"]["Eve"] = 79

# Iterate over a nested structure
for class_name, class_students in school.items():
    print(f"\nClass {class_name}:")
    for student_name, score in class_students.items():
        print(f"  {student_name}: {score}")
```

---

### 5. Choosing the Right Data Structure

| Need | Best choice |
|---|---|
| An ordered, changeable collection | List |
| A fixed, ordered record (coordinate, RGB, etc.) | Tuple |
| Unique items; fast membership check; set operations | Set |
| Key-value lookup; structured named record | Dictionary |
| Collection of records, each with named fields | List of dictionaries |

```python
# Scenario: store a set of valid postcodes
# List — works but slow for lookup
valid_postcodes = ["LS1", "LS2", "LS3", "M1"]
if "LS2" in valid_postcodes:   # O(n)
    ...

# Set — better for lookup
valid_postcodes = {"LS1", "LS2", "LS3", "M1"}
if "LS2" in valid_postcodes:   # O(1)
    ...
```

---

## Example Activities

### Activity 1: Set Operations Puzzle
Given two lists of student names — one who passed the maths exam and one who passed the English exam:
1. Find students who passed both
2. Find students who passed maths but not English
3. Find students who passed at least one exam
4. Find students who passed exactly one exam (not both)

### Activity 2: Word Frequency Counter
Write a function that takes a string of text and returns a dictionary mapping each unique word (lowercase, stripped of punctuation) to its frequency. Then:
1. Find the three most common words
2. Find all words that appear exactly once
3. Return a sorted list of `(word, count)` tuples, sorted by frequency descending

### Activity 3: Dictionary Comprehension Drill
Write the following as dictionary comprehensions:
1. Map each letter in the alphabet to its position (a→1, b→2, ...)
2. Given a list of strings, map each string to its length, but only include strings with more than 3 characters
3. Given a dictionary mapping names to scores, create a new dictionary mapping scores to names (assume all scores are unique)

### Activity 4: Nested Structure Design
A cinema has multiple screens, each showing a film. Each screen has a list of available seat numbers. Design a nested dictionary structure to represent this, then write functions to:
- Check if a specific seat is available
- Book a seat (removing it from available)
- Count total available seats across the whole cinema

---

## Misconceptions / Likely Errors

| Misconception | Reality |
|---|---|
| `{}` creates an empty set | `{}` creates an empty **dictionary**. Use `set()` for an empty set |
| Tuples can be changed if they contain a list | The tuple itself is immutable, but if it *contains* a list, that list can be modified |
| Dictionary keys must be strings | Keys can be any **hashable** type: int, float, tuple (not list!) |
| Sets preserve insertion order | Sets are **unordered** — do not rely on any particular order |
| Dictionaries are unordered | Since Python 3.7+, dictionaries **maintain insertion order** |
| `list.remove()` and `set.discard()` work the same | `list.remove()` raises `ValueError` if the element is absent; `set.discard()` does not raise an error |

---

## Assessment Opportunities

- **Predict the output:** Show a sequence of set operations and ask students to state the result
- **Exam-style question:** "Write a function that takes a list of integers and returns a dictionary mapping each unique integer to the number of times it appears."
- **Choose and justify:** "A program stores a list of valid UK currency denominations. The program frequently checks whether a given coin is valid. State which data structure you would use and explain why."
- **Short writing:** "Explain the difference between a tuple and a list. Give one situation where a tuple is more appropriate."

---

## Homework / Independent Study

1. **Practice task:** Write a program that reads a list of exam results (name, subject, score) and uses a nested dictionary to store the data. Provide functions to: add a result, look up a student's score in a subject, and find the top scorer for each subject.

2. **Set challenge:** Given two sentences, write a program that reports: the words unique to sentence 1, the words unique to sentence 2, and the words common to both.

3. **Comprehension practice:** Rewrite the word-frequency counter from Activity 2 using a single dictionary comprehension where possible.

4. **Exam preparation:** In the AQA specification, dictionaries are referenced as part of the *data structures* content. Write notes explaining when you would choose a dictionary over a list of lists for storing structured data.
