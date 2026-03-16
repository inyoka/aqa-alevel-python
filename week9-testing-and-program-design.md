# Week 9: Testing, Debugging, and Program Design

## Core Focus
Designing programs well from the start using structured decomposition; testing systematically with normal, boundary, and erroneous inputs; debugging effectively using assertions and tracing; and understanding how good design and testing relate to exam success.

## Prior Knowledge Assumed
- Functions, parameters, return values, docstrings (GCSE Week 6 and Week 2 of this course)
- Exception handling (Week 4 of this course)
- Basic testing with `assert` (mentioned in GCSE Week 6)
- Writing programs with multiple functions
- Logical errors (introduced in Week 4)

## Learning Objectives
- Design a program using structured decomposition before writing code
- Apply systematic testing: normal, boundary, and erroneous test cases
- Use `assert` statements for precondition checking and unit tests
- Use Python's `unittest` module for basic structured testing
- Debug programs effectively using print-based tracing, assertions, and logical analysis
- Distinguish between testing and debugging
- Explain the difference between black-box and white-box testing
- Relate testing and design practices to AQA exam question requirements

---

## Lesson Content

### 1. Program Design Before Coding

At A-Level, the expectation is that students can **plan** a program before writing it, not just write code and hope it works.

**Design process:**
1. **Understand the problem** — what are the inputs? What are the outputs? What are the constraints?
2. **Identify sub-problems** — break the problem into smaller, manageable pieces
3. **Design the functions** — what does each function take in and return?
4. **Think about data structures** — what is the best way to store and process the data?
5. **Consider edge cases** — what inputs could cause problems?
6. **Write and test** — implement one function at a time, testing as you go

**Example: Student Grade Report System**

Problem: Read student names and scores from a CSV file, compute grades, and output a formatted report.

Design sketch (before writing any code):
```
main()
├── load_students(filename) → list of dicts
├── assign_grades(students) → modifies list in place
├── calculate_statistics(students) → dict of stats
└── display_report(students, statistics)
```

Each function has a clear, single responsibility. This makes each one easy to write, test, and debug independently.

---

### 2. Systematic Testing

Testing is not "run it once and see if it works." Good testing covers:

| Test type | Description | Example (for a function that validates age) |
|-----------|-------------|---------------------------------------------|
| Normal (valid) | Typical correct input | `is_valid_age(17)` → `True` |
| Boundary | Values at the edge of validity | `is_valid_age(0)` → `True`; `is_valid_age(120)` → `True` |
| Just outside boundary | One step outside the valid range | `is_valid_age(-1)` → `False`; `is_valid_age(121)` → `False` |
| Erroneous (invalid type) | Wrong data type | `is_valid_age("seventeen")` → should raise or return `False` |
| Extreme | Very large or unusual values | `is_valid_age(999)` → `False` |
| Empty/null | Missing or empty input | `is_valid_age(None)` → should not crash |

```python
def is_valid_age(age):
    """Return True if age is an integer between 0 and 120 inclusive."""
    if not isinstance(age, int):
        return False
    return 0 <= age <= 120

# Systematic test cases
assert is_valid_age(17)    == True,  "Normal case failed"
assert is_valid_age(0)     == True,  "Lower boundary failed"
assert is_valid_age(120)   == True,  "Upper boundary failed"
assert is_valid_age(-1)    == False, "Just below lower boundary failed"
assert is_valid_age(121)   == False, "Just above upper boundary failed"
assert is_valid_age("17")  == False, "String input failed"
assert is_valid_age(None)  == False, "None input failed"
assert is_valid_age(17.5)  == False, "Float input failed"

print("All tests passed.")
```

---

### 3. Using `assert`

`assert condition, "message"` raises an `AssertionError` if `condition` is `False`.

Use `assert` for:
- **Preconditions:** checking that function inputs are valid
- **Unit tests:** verifying that a function returns the expected value

```python
def calculate_grade(score):
    """Return a letter grade for a score 0-100."""
    assert isinstance(score, int), "Score must be an integer"
    assert 0 <= score <= 100, f"Score {score} is out of range [0, 100]"
    
    if score >= 90: return "A*"
    if score >= 80: return "A"
    if score >= 70: return "B"
    if score >= 60: return "C"
    if score >= 50: return "D"
    return "U"

# Unit tests
assert calculate_grade(95)  == "A*", "95 should be A*"
assert calculate_grade(90)  == "A*", "90 should be A* (boundary)"
assert calculate_grade(89)  == "A",  "89 should be A (boundary)"
assert calculate_grade(80)  == "A",  "80 should be A (boundary)"
assert calculate_grade(0)   == "U",  "0 should be U"
assert calculate_grade(100) == "A*", "100 should be A*"

print("Grade tests passed.")
```

---

### 4. Basic Unit Testing with `unittest`

Python's `unittest` module provides a formal framework for organising and running tests.

```python
import unittest

def add(a, b):
    return a + b

def divide(a, b):
    if b == 0:
        raise ZeroDivisionError("Cannot divide by zero")
    return a / b

class TestMathFunctions(unittest.TestCase):
    
    def test_add_positive(self):
        self.assertEqual(add(2, 3), 5)
    
    def test_add_negative(self):
        self.assertEqual(add(-1, -2), -3)
    
    def test_add_zero(self):
        self.assertEqual(add(0, 5), 5)
    
    def test_divide_normal(self):
        self.assertAlmostEqual(divide(10, 4), 2.5)
    
    def test_divide_by_zero(self):
        with self.assertRaises(ZeroDivisionError):
            divide(10, 0)

if __name__ == "__main__":
    unittest.main()
```

**Key `unittest` assertions:**

| Method | Tests that |
|--------|-----------|
| `assertEqual(a, b)` | `a == b` |
| `assertNotEqual(a, b)` | `a != b` |
| `assertTrue(x)` | `x` is truthy |
| `assertFalse(x)` | `x` is falsy |
| `assertAlmostEqual(a, b)` | `a ≈ b` (for floats) |
| `assertRaises(exc)` | A specific exception is raised |
| `assertIsNone(x)` | `x is None` |
| `assertIn(a, b)` | `a in b` |

---

### 5. Debugging Strategies

**Debugging** is the process of finding and fixing errors. It is distinct from testing (which finds *that* there is an error).

#### Strategy 1: Print-based tracing
```python
def calculate_average(numbers):
    print(f"DEBUG: Input = {numbers}")           # Add temporarily
    total = sum(numbers)
    print(f"DEBUG: Total = {total}")             # Add temporarily
    count = len(numbers)
    print(f"DEBUG: Count = {count}")             # Add temporarily
    return total / count

# Remove debug prints once the bug is found
```

#### Strategy 2: Rubber duck debugging
Explain your code line-by-line out loud (or in writing) as if explaining it to someone who knows nothing about programming. Often, you spot the error in the act of explaining it.

#### Strategy 3: Simplify the input
Reduce the input to the smallest case that still shows the bug.

```python
# Bug appears on a list of 100 items
# Start by testing with just 2 or 3 items
calculate_average([5, 15])    # Expected: 10.0
calculate_average([])         # Expected: what should happen?
```

#### Strategy 4: Isolate the function
Test each function independently with known inputs rather than running the whole program.

#### Strategy 5: Read the error message carefully
```
Traceback (most recent call last):
  File "main.py", line 12, in <module>
    result = divide(10, 0)
  File "main.py", line 7, in divide
    return a / b
ZeroDivisionError: division by zero
```
The traceback tells you: the type of error, the file and line number, and the call chain that led there. Always read it top to bottom.

---

### 6. Black-Box vs White-Box Testing

| | Black-box testing | White-box testing |
|---|---|---|
| Based on | The specification (inputs/outputs) | The internal code structure |
| Tester knows implementation? | No | Yes |
| What it tests | Correct outputs for given inputs | Code paths, branches, edge cases within the code |
| AQA relevance | Test cases based on requirements | Test cases derived from the code's structure |

**Black-box example:** Given the specification "function returns True if a score is a pass (≥ 50), False otherwise", write test cases without looking at the code:
- Normal: 75 → True, 40 → False
- Boundary: 50 → True, 49 → False
- Edge: 0 → False, 100 → True

**White-box example:** Given the code below, identify test cases that exercise every branch:
```python
def classify(n):
    if n < 0:
        return "negative"
    elif n == 0:
        return "zero"
    else:
        return "positive"
# Tests needed: n < 0, n = 0, n > 0
```

---

### 7. Program Design: A Full Worked Example

**Problem:** A program reads a list of temperatures from a file (one per line), calculates statistics, and writes a report.

**Step 1: Decompose**
```
main()
├── load_temperatures(filename) → list of floats
├── calculate_stats(temps) → dict
├── classify_temperatures(temps) → dict
└── write_report(stats, classifications, output_filename)
```

**Step 2: Define each function's contract**
- `load_temperatures`: reads file, returns list of floats, handles missing/malformed file
- `calculate_stats`: takes non-empty list, returns {min, max, mean, range}
- `classify_temperatures`: returns {below_zero, zero_to_ten, above_ten} counts
- `write_report`: writes formatted report to file

**Step 3: Implement and test each function independently**

```python
import csv

def load_temperatures(filename):
    """Load temperatures from file. Returns list of floats."""
    try:
        with open(filename, "r") as f:
            temps = []
            for line in f:
                stripped = line.strip()
                if stripped:
                    try:
                        temps.append(float(stripped))
                    except ValueError:
                        print(f"Warning: skipping invalid line: {stripped!r}")
            return temps
    except FileNotFoundError:
        print(f"File not found: {filename}")
        return []

def calculate_stats(temps):
    """Return statistical summary of temperatures."""
    if not temps:
        raise ValueError("Cannot calculate stats for an empty list")
    return {
        "min":   min(temps),
        "max":   max(temps),
        "mean":  sum(temps) / len(temps),
        "range": max(temps) - min(temps),
        "count": len(temps),
    }

def classify_temperatures(temps):
    """Count how many temperatures fall into each category."""
    return {
        "below_zero":  sum(1 for t in temps if t < 0),
        "zero_to_ten": sum(1 for t in temps if 0 <= t <= 10),
        "above_ten":   sum(1 for t in temps if t > 10),
    }

def write_report(stats, classifications, output_filename):
    """Write a formatted temperature report to a file."""
    with open(output_filename, "w") as f:
        f.write("=== Temperature Report ===\n")
        f.write(f"Count:  {stats['count']}\n")
        f.write(f"Min:    {stats['min']:.1f}°C\n")
        f.write(f"Max:    {stats['max']:.1f}°C\n")
        f.write(f"Mean:   {stats['mean']:.2f}°C\n")
        f.write(f"Range:  {stats['range']:.1f}°C\n\n")
        f.write("=== Classification ===\n")
        for category, count in classifications.items():
            f.write(f"{category}: {count}\n")

def main():
    temps = load_temperatures("temps.txt")
    if not temps:
        print("No temperature data. Exiting.")
        return
    stats = calculate_stats(temps)
    classifications = classify_temperatures(temps)
    write_report(stats, classifications, "report.txt")
    print("Report written to report.txt")

# Unit tests
def test_calculate_stats():
    stats = calculate_stats([10.0, 20.0, 30.0])
    assert stats["min"] == 10.0
    assert stats["max"] == 30.0
    assert abs(stats["mean"] - 20.0) < 0.001
    assert stats["range"] == 20.0
    print("calculate_stats tests passed")

def test_classify_temperatures():
    c = classify_temperatures([-5.0, 0.0, 5.0, 15.0])
    assert c["below_zero"] == 1
    assert c["zero_to_ten"] == 2
    assert c["above_ten"] == 1
    print("classify_temperatures tests passed")

test_calculate_stats()
test_classify_temperatures()
main()
```

---

## Example Activities

### Activity 1: Test Case Table
Write a complete test case table for a function `calculate_discount(price, percentage)`:
- Columns: test type, input (price, percentage), expected output, actual output, pass/fail
- Include at least: 3 normal cases, 2 boundary cases, 2 erroneous cases

### Activity 2: Debug This
Students are given a program with three logical errors (no crashes). They must:
1. Write test cases that reveal each error
2. Fix each error
3. Confirm that their fixes work

### Activity 3: Design Before You Code
Given a problem description (e.g. "a quiz program that reads questions from a file, tests the user, and saves scores"), students must:
1. Identify the sub-problems
2. Design the function signatures (name, parameters, return value, docstring)
3. Only then write the code
4. Test each function independently

### Activity 4: unittest Suite
Write a `unittest` test class for a `StringProcessor` module that includes functions: `word_count()`, `most_common_word()`, `reverse_words()`, `is_palindrome()`. Write at least three test cases per function.

---

## Misconceptions / Likely Errors

| Misconception | Reality |
|---|---|
| "Testing means running the program once" | Testing requires systematic input coverage: normal, boundary, erroneous |
| `assert` is the same as `if` with a print | `assert` raises `AssertionError` and halts execution; intended for developer checks, not user-facing validation |
| Unit tests test the whole program | A unit test tests a **single function** in isolation |
| Debugging and testing are the same | Testing **finds** errors; debugging **locates and fixes** them |
| A program that passes all tests is correct | Tests can only show the presence of errors, not their absence |

---

## Assessment Opportunities

- **Design task:** "State the functions you would use in a program to manage a library catalogue. For each function, state the parameters and return value."
- **Test table:** "Complete a test table for the following function, including at least one boundary test and one erroneous test."
- **Code review:** Students annotate a classmate's program identifying: missing edge case handling, unclear function names, missing docstrings, untested functions.
- **Exam-style question:** "A function is tested with input 50 and returns the correct result. Describe two further test cases that should be used and explain why."

---

## Homework / Independent Study

1. **Test suite:** Choose any program you have written in this course. Write a complete test table and then convert it into Python `assert` statements or a `unittest` class.

2. **Design exercise:** Design (not implement) a program that manages a school library: borrowing books, returning books, and tracking who has what. Write the function signatures and docstrings only.

3. **Debugging challenge:** A classmate's binary search implementation has a subtle off-by-one error. Write test cases that reveal the error, then fix it.

4. **Exam preparation:** Review the AQA mark scheme for a recent programming question. Identify which marks are for: correct logic, correct handling of edge cases, correct function decomposition, and appropriate variable naming.
