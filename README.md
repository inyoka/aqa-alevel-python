# AQA A-Level Python – 10 Week Scheme of Work

A structured week-by-week Python course for students who have already completed the [GCSE Python guide](https://github.com/nuast-dev/gcse-python). Designed for AQA A-Level Computer Science (7517) but broadly applicable to strong A-Level Python teaching. 🚀

## Who Is This For?

Students who:
- Have completed (or equivalent to) the [GCSE Python guide](https://github.com/nuast-dev/gcse-python)
- Need to build the additional Python knowledge required at A-Level
- Are starting, or are midway through, AQA A-Level Computer Science

This course **does not** reteach GCSE Python foundations, nor does it include OOP content (covered in the separate [A-Level OOP guide](https://github.com/nuast-dev/alevel-oop)).

---

## 📚 Weekly Topics

### [Week 1: Bridging and Transition](week1-bridging-and-transition.md)
Retrieval of GCSE knowledge and raising A-Level expectations
- Rapid retrieval of GCSE fundamentals
- Code quality, naming, and documentation
- Thinking about edge cases and robustness
- Introduction to A-Level problem-solving mindset

### [Week 2: Advanced Functions](week2-advanced-functions.md)
Going further with functions and procedural design
- Default parameters, `*args`, and `**kwargs`
- Lambda expressions and anonymous functions
- `map()`, `filter()`, and `sorted()` with key functions
- Higher-order functions and functions as first-class objects
- Closures and scope revisited

### [Week 3: Recursion](week3-recursion.md)
Recursive thinking and implementation
- What recursion is and when to use it
- Base cases and recursive cases
- Tracing recursive calls
- Classic recursive problems: factorial, Fibonacci, power
- Recursive list and string processing
- Comparing recursion with iteration

### [Week 4: Exception Handling and Robust Programs](week4-exception-handling.md)
Writing programs that handle errors gracefully
- Types of Python errors and what causes them
- `try`, `except`, `else`, and `finally`
- Handling specific error types in context
- Raising exceptions intentionally
- Writing robust input validation with exceptions
- Logical errors that don't crash but produce wrong output

### [Week 5: Tuples, Sets, and Dictionaries](week5-tuples-sets-dictionaries.md)
Advanced use of Python's core data structures
- Tuples: immutability, packing and unpacking, use cases
- Sets: creation, operations, membership testing, deduplication
- Dictionaries: advanced use, iteration, nested dicts, dict comprehensions
- Choosing the right data structure for the problem

### [Week 6: 2D Data and Nested Structures](week6-2d-data-and-nested-structures.md)
Working with grid-like and complex data
- 2D lists as grids and matrices
- Accessing, updating, and traversing 2D lists
- Lists of dictionaries for record-style data
- Dictionaries of lists for grouped data
- Practical applications: tables, game grids, student records

### [Week 7: Advanced File Handling and Data Formats](week7-advanced-file-handling.md)
Beyond basic text files
- The `csv` module: `reader`, `writer`, `DictReader`, `DictWriter`
- The `json` module: reading and writing structured data
- Robust file handling: `with`, exception handling, validation
- Choosing between plain text, CSV, and JSON

### [Week 8: Sorting, Searching, and Algorithmic Thinking](week8-sorting-and-searching.md)
Implementing and analysing algorithms at A-Level depth
- Merge sort and quicksort in Python
- Recursive binary search
- Python's built-in `sort()` and `sorted()` with custom keys
- Introduction to algorithmic complexity and Big-O awareness
- Comparing algorithm efficiency

### [Week 9: Testing, Debugging, and Program Design](week9-testing-and-program-design.md)
Writing well-designed, testable programs
- Systematic testing: normal, boundary, and erroneous inputs
- Assertions and defensive programming
- Debugging techniques and strategies
- Modular design and procedural decomposition
- Tracing through complex code

### [Week 10: Python and Databases — SQL Integration](week10-python-and-sql.md)
Connecting Python programs to relational databases
- Introduction to `sqlite3`
- Creating tables and inserting data from Python
- Executing SELECT queries and processing results
- Parameterised queries and avoiding injection issues
- Linking Python programs to SQL concepts from the [A-Level SQL guide](https://github.com/nuast-dev/alevel-sql)

---

## 🎯 Learning Outcomes

By the end of this course, students will be able to:
- ✓ Write well-structured, robust Python programs beyond GCSE level
- ✓ Use advanced function techniques including recursion and higher-order functions
- ✓ Handle errors gracefully using exception handling
- ✓ Work confidently with tuples, sets, dictionaries, and 2D data structures
- ✓ Read and write CSV and JSON files using the standard library
- ✓ Implement and compare sorting and searching algorithms
- ✓ Test and debug programs systematically
- ✓ Connect Python programs to SQLite databases
- ✓ Apply sound program design principles

## 📝 Weekly Format

Each week uses a consistent structure:
- **Week number and title**
- **Core focus**
- **Prior knowledge assumed**
- **Learning objectives**
- **Lesson content** with annotated Python examples
- **Example activities**
- **Misconceptions / likely errors**
- **Assessment opportunities**
- **Homework / independent study**

## 📖 Companion Guides

| Guide | Content | Link |
|-------|---------|-------|
| GCSE Python | 8-week Python foundations | [gcse-python](https://github.com/nuast-dev/gcse-python) |
| A-Level OOP | Full OOP: classes, encapsulation, inheritance, polymorphism | [alevel-oop](https://github.com/nuast-dev/alevel-oop) |
| A-Level SQL | Relational databases and SQL | [alevel-sql](https://github.com/nuast-dev/alevel-sql) |

---

## 📋 Curriculum Boundaries

### What is assumed from the GCSE guide

Students are expected to already know:
- Variables, data types, and casting (`int`, `float`, `str`, `bool`)
- Input/output with `print()` and `input()`
- Arithmetic and comparison operators
- `if`/`elif`/`else` conditionals
- `for` and `while` loops, `break`, `continue`
- String indexing, slicing, and common string methods
- List creation, indexing, and common list methods
- Basic 2D lists (introduced but not deeply explored)
- Defining and calling functions with parameters and return values
- Basic scope (local vs global)
- Docstrings and basic code documentation
- Reading and writing text files with `open()`, `with`, `strip()`, `split()`
- Basic `try`/`except FileNotFoundError`
- Linear search, binary search, bubble sort, selection sort
- Basic validation algorithms

### What is intentionally excluded (covered by the OOP guide)

The following OOP content is fully covered in the [A-Level OOP guide](https://github.com/nuast-dev/alevel-oop) and is not repeated here:
- Classes, objects, and the `__init__` constructor
- Instance and class attributes; `self`
- Encapsulation: public, protected, and private attributes; `@property`
- Inheritance: base and derived classes; `super()`; method overriding
- Polymorphism: method overriding, duck typing, operator overloading
- Abstract classes and the `abc` module
- Object associations: association, aggregation, composition
- OOP implementations of linked lists, stacks, and queues
- UML class diagram notation

### New A-Level Python content covered by this scheme

This course introduces and develops:
- Advanced function techniques: `*args`, `**kwargs`, lambda, `map`, `filter`, higher-order functions, closures
- Recursive thinking and recursive algorithm implementation
- Comprehensive exception handling: `try`/`except`/`else`/`finally`, raising exceptions, input validation
- Deeper data structures: tuples (packing/unpacking), sets (operations), dictionaries (advanced patterns, comprehensions)
- 2D data and nested structures beyond GCSE introductions
- The `csv` module and `json` module for structured file I/O
- Merge sort and quicksort as Python implementations
- Algorithmic complexity awareness (Big-O as a concept)
- `sorted()` with custom `key` functions
- Systematic testing strategies, assertions, and debugging methodology
- Program design: decomposition, abstraction, modular structure
- The `sqlite3` module for Python–database integration
- Parameterised SQL queries from Python
