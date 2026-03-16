# Week 1: Bridging and Transition

## Core Focus
Rapid retrieval of GCSE Python knowledge, raising code quality expectations, and introducing the A-Level mindset of writing robust, well-designed programs.

## Prior Knowledge Assumed
Everything from the [GCSE Python guide](https://github.com/nuast-dev/gcse-python):
- Variables, data types, casting
- `if`/`elif`/`else`, `for`/`while` loops
- Functions with parameters and return values
- Lists, basic strings, file reading and writing
- Linear search, bubble sort
- Basic `try`/`except`

## Learning Objectives
- Quickly retrieve and consolidate GCSE Python knowledge
- Identify gaps or shaky areas to address early
- Understand what A-Level Python expects beyond GCSE
- Write cleaner, better-documented, more robust code from the start
- Recognise the difference between code that works and code that is well designed

---

## Lesson Content

### 1. What Changes at A-Level

At GCSE, the goal is *get it working*. At A-Level, the goals are:
- Get it working **correctly** for all inputs, including unexpected ones
- Write code that another programmer can read and understand
- Design programs in a structured, decomposed way
- Think about what could go wrong and handle it

**GCSE-style code:**
```python
x = int(input("Enter a number: "))
print(x * 2)
```

**A-Level expectations:**
```python
def get_positive_integer(prompt):
    """Ask the user for a positive integer, repeating until valid."""
    while True:
        try:
            value = int(input(prompt))
            if value > 0:
                return value
            print("Please enter a positive integer.")
        except ValueError:
            print("That is not a valid integer. Try again.")

def double(n):
    """Return double the value of n."""
    return n * 2

def main():
    number = get_positive_integer("Enter a positive integer: ")
    print(f"Double: {double(number)}")

main()
```

Key differences:
- Input is validated and protected against non-integer input
- Logic is separated into focused functions
- Functions are documented
- A `main()` function controls program flow

---

### 2. Code Quality Expectations

#### Naming Conventions
```python
# Poor naming
x = 85
def f(a, b):
    return a + b

# Clear naming
score = 85
def calculate_total(price, tax):
    return price + tax
```

#### Docstrings at A-Level
```python
def is_prime(n):
    """
    Check whether n is a prime number.
    
    Parameters:
        n (int): A positive integer greater than 1.
    
    Returns:
        bool: True if n is prime, False otherwise.
    """
    if n < 2:
        return False
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False
    return True
```

#### Avoiding Magic Numbers
```python
# Avoid
if score >= 70:
    ...

# Prefer
PASS_THRESHOLD = 70
if score >= PASS_THRESHOLD:
    ...
```

---

### 3. Retrieval: Functions, Loops, and Lists

This section recaps and stress-tests GCSE content at speed.

```python
# Retrieval task: can you write this from scratch?

def calculate_average(numbers):
    """Return the average of a list of numbers."""
    if len(numbers) == 0:
        return 0
    return sum(numbers) / len(numbers)

def find_max(numbers):
    """Return the largest value in a list without using max()."""
    if len(numbers) == 0:
        return None
    largest = numbers[0]
    for n in numbers:
        if n > largest:
            largest = n
    return largest

def count_above(numbers, threshold):
    """Return the count of values above threshold."""
    count = 0
    for n in numbers:
        if n > threshold:
            count += 1
    return count
```

---

### 4. Thinking About Edge Cases

GCSE programs often assume valid input. A-Level programs must consider:

| Scenario | Example |
|---|---|
| Empty input | An empty list, empty string, or no file |
| Boundary values | The minimum or maximum allowed value |
| Wrong type | A string where an integer is expected |
| Negative numbers | When only positive values make sense |
| Division by zero | When a denominator could be zero |

```python
def safe_average(numbers):
    """Return the average, or None if the list is empty."""
    if len(numbers) == 0:
        return None        # Handle empty list explicitly
    return sum(numbers) / len(numbers)

result = safe_average([])
if result is None:
    print("No data to average.")
else:
    print(f"Average: {result:.2f}")
```

---

### 5. From GCSE Style to A-Level Style: A Worked Example

**Problem:** Read a list of student names and scores from user input, then display the highest scorer.

**GCSE approach (monolithic):**
```python
names = []
scores = []
n = int(input("How many students? "))
for i in range(n):
    names.append(input("Name: "))
    scores.append(int(input("Score: ")))

best = 0
for i in range(len(scores)):
    if scores[i] > scores[best]:
        best = i
print(f"Top student: {names[best]} with {scores[best]}")
```

**A-Level approach (decomposed, robust):**
```python
def get_student_count():
    """Get a valid number of students from the user."""
    while True:
        try:
            n = int(input("How many students? "))
            if n > 0:
                return n
            print("Must be at least 1.")
        except ValueError:
            print("Please enter a whole number.")

def collect_students(n):
    """Collect n student name/score pairs."""
    students = []
    for _ in range(n):
        name = input("Student name: ").strip()
        while True:
            try:
                score = int(input(f"Score for {name}: "))
                break
            except ValueError:
                print("Score must be a whole number.")
        students.append({"name": name, "score": score})
    return students

def find_top_student(students):
    """Return the student with the highest score."""
    return max(students, key=lambda s: s["score"])

def main():
    n = get_student_count()
    students = collect_students(n)
    top = find_top_student(students)
    print(f"\nTop student: {top['name']} with {top['score']}")

main()
```

---

## Example Activities

### Activity 1: Retrieval Quiz (10 minutes)
Students write from memory:
1. A function that takes a list and returns the sum of even numbers
2. A function that checks if a string is a palindrome
3. A while loop that keeps asking for input until the user enters "quit"

### Activity 2: Code Critique
Give students a working but poorly written GCSE-style program. Ask them to:
1. Identify three things that could go wrong
2. Identify three improvements to code quality
3. Rewrite one function to A-Level standard

### Activity 3: Refactoring Challenge
Students take a working GCSE program (provided) and refactor it to:
- Use clear function decomposition
- Add docstrings
- Handle at least two edge cases
- Use a `main()` function

---

## Misconceptions / Likely Errors

| Misconception | Reality |
|---|---|
| "It works, so it's good" | Working is necessary but not sufficient at A-Level |
| `return` and `print` do the same job | `print` displays output; `return` passes a value back to the caller |
| Global variables are fine to use freely | Prefer passing values as parameters; global state is hard to test and debug |
| Adding more comments makes code better | Clear naming often makes comments unnecessary; prefer self-documenting code |
| Docstrings are optional extras | At A-Level, they are expected on every significant function |

---

## Assessment Opportunities

- **Quick quiz:** Five short retrieval questions on GCSE Python topics — functions, loops, lists, string methods
- **Code critique:** Annotate a provided program with improvements
- **Mini task:** Rewrite a monolithic GCSE program using proper decomposition
- **Exit ticket:** Write a function from a specification (one clear input, one clear output, one edge case to handle)

---

## Homework / Independent Study

1. **GCSE retrieval set:** Complete ten short Python tasks covering functions, loops, strings, and lists — all from memory without looking anything up. Mark your own work and note which areas need reviewing.

2. **Code improvement:** Take any program you wrote at GCSE and refactor it to A-Level standard: decompose into functions, add docstrings, handle at least two edge cases.

3. **Explore:** Look at one or two functions in the Python standard library documentation (`len`, `sorted`, `range`). Note what the documentation tells you about parameters and return values.
