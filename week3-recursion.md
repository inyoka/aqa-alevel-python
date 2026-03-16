# Week 3: Recursion

## Core Focus
Understanding recursive thinking, writing recursive functions correctly, tracing recursive calls, and comparing recursion with iteration.

## Prior Knowledge Assumed
- Functions with parameters and return values (GCSE Week 6)
- Loops: `for` and `while` (GCSE Week 3)
- The call stack (introduced conceptually through function calls)
- Basic algorithms: factorial, Fibonacci (may have encountered iteratively)

## Learning Objectives
- Explain what recursion is and identify the two essential components
- Identify and write correct base cases
- Trace a recursive call and describe the call stack
- Implement classic recursive algorithms: factorial, power, Fibonacci
- Write recursive functions to process lists and strings
- Recognise when recursion is natural and when iteration is preferable
- Understand why infinite recursion occurs and how to avoid it

---

## Lesson Content

### 1. What is Recursion?

A function is **recursive** if it calls itself as part of its own definition.

Every correct recursive function has:
1. **A base case** — a condition that stops the recursion without another recursive call
2. **A recursive case** — a call to the same function with a simpler (smaller) version of the problem

Without a base case, recursion runs forever (or until Python hits its recursion limit).

---

### 2. Factorial — The Classic Example

Mathematically: `n! = n × (n-1)!`, with `0! = 1`.

```python
def factorial(n):
    """
    Return n! (n factorial) recursively.
    
    Base case: factorial(0) = 1
    Recursive case: factorial(n) = n * factorial(n - 1)
    """
    if n == 0:          # base case
        return 1
    return n * factorial(n - 1)   # recursive case

print(factorial(5))   # 120
print(factorial(0))   # 1
print(factorial(1))   # 1
```

**Tracing `factorial(4)`:**
```
factorial(4)
  → 4 * factorial(3)
         → 3 * factorial(2)
                → 2 * factorial(1)
                       → 1 * factorial(0)
                              → 1            # base case reached
                       → 1 * 1 = 1
                → 2 * 1 = 2
         → 3 * 2 = 6
  → 4 * 6 = 24
```

---

### 3. Power Function

```python
def power(base, exp):
    """
    Return base raised to the power exp (non-negative integer).
    
    Base case: anything to the power 0 is 1.
    Recursive case: base^exp = base * base^(exp-1)
    """
    if exp == 0:
        return 1
    return base * power(base, exp - 1)

print(power(2, 10))   # 1024
print(power(3, 4))    # 81
print(power(5, 0))    # 1
```

---

### 4. Fibonacci

Mathematically: `fib(0) = 0`, `fib(1) = 1`, `fib(n) = fib(n-1) + fib(n-2)`.

```python
def fibonacci(n):
    """
    Return the nth Fibonacci number (0-indexed).
    
    Base cases: fib(0) = 0, fib(1) = 1
    Recursive case: fib(n) = fib(n-1) + fib(n-2)
    """
    if n == 0:
        return 0
    if n == 1:
        return 1
    return fibonacci(n - 1) + fibonacci(n - 2)

for i in range(10):
    print(fibonacci(i), end=" ")
# 0 1 1 2 3 5 8 13 21 34
```

**Important note on efficiency:** Naïve recursive Fibonacci recalculates the same subproblems many times. For large `n`, this is very slow. A-Level students should recognise this and know that iterative solutions or memoisation are preferable for Fibonacci in practice.

```python
# Iterative version for comparison (much more efficient)
def fibonacci_iterative(n):
    """Return the nth Fibonacci number iteratively."""
    if n == 0:
        return 0
    a, b = 0, 1
    for _ in range(n - 1):
        a, b = b, a + b
    return b
```

---

### 5. Recursive List Processing

Recursion naturally expresses "do something to the first element, then do the same to the rest."

```python
def recursive_sum(numbers):
    """Return the sum of a list of numbers recursively."""
    if len(numbers) == 0:    # base case: empty list sums to 0
        return 0
    return numbers[0] + recursive_sum(numbers[1:])   # first + sum of rest

print(recursive_sum([1, 2, 3, 4, 5]))  # 15
print(recursive_sum([]))               # 0
```

```python
def recursive_max(numbers):
    """Return the maximum value in a non-empty list recursively."""
    if len(numbers) == 1:
        return numbers[0]
    rest_max = recursive_max(numbers[1:])
    return numbers[0] if numbers[0] > rest_max else rest_max

print(recursive_max([3, 7, 1, 9, 2]))  # 9
```

---

### 6. Recursive String Processing

```python
def reverse_string(s):
    """Return the reverse of string s recursively."""
    if len(s) == 0:
        return ""
    return reverse_string(s[1:]) + s[0]

print(reverse_string("hello"))  # olleh
print(reverse_string(""))       # ""
```

```python
def is_palindrome(s):
    """Check if string s is a palindrome recursively."""
    if len(s) <= 1:
        return True
    if s[0] != s[-1]:
        return False
    return is_palindrome(s[1:-1])

print(is_palindrome("racecar"))  # True
print(is_palindrome("hello"))    # False
print(is_palindrome("a"))        # True
```

---

### 7. Recursive Binary Search

Binary search is a natural candidate for recursion.

```python
def binary_search(data, target, low, high):
    """
    Search for target in data[low..high] using binary search.
    
    Returns the index of target if found, or -1 if not found.
    """
    if low > high:               # base case: search space exhausted
        return -1
    
    mid = (low + high) // 2
    
    if data[mid] == target:      # base case: found
        return mid
    elif data[mid] < target:
        return binary_search(data, target, mid + 1, high)
    else:
        return binary_search(data, target, low, mid - 1)

numbers = [2, 5, 8, 12, 16, 23, 38, 56, 72, 91]
print(binary_search(numbers, 23, 0, len(numbers) - 1))   # 5
print(binary_search(numbers, 50, 0, len(numbers) - 1))   # -1
```

---

### 8. Recursion vs Iteration

| Aspect | Recursion | Iteration |
|---|---|---|
| Elegance | Often more elegant and closer to the mathematical definition | Can be verbose for naturally recursive problems |
| Performance | Overhead from function calls; risk of deep call stacks | Usually faster in Python for simple tasks |
| Memory | Each call uses stack space | Constant memory for simple loops |
| Readability | Natural for tree/nested structures | Natural for sequence processing |
| Python limit | Default recursion limit is 1000 | No built-in limit |

**Rule of thumb for A-Level:** If the problem is naturally recursive (trees, nested structures, divide-and-conquer), use recursion. For simple sequences, iteration is often better.

```python
import sys
print(sys.getrecursionlimit())  # 1000 by default

# Increase the limit (use with care)
sys.setrecursionlimit(5000)
```

---

### 9. Common Recursive Mistakes

**Forgetting the base case:**
```python
def bad_factorial(n):
    return n * bad_factorial(n - 1)  # No base case → RecursionError!
```

**Base case that is never reached:**
```python
def broken(n):
    if n == 0:
        return 0
    return broken(n + 1)   # n is growing, never reaches 0!
```

**Not simplifying toward the base case:**
```python
def broken2(n):
    if n == 0:
        return 1
    return n * broken2(n)  # n unchanged → infinite recursion
```

---

## Example Activities

### Activity 1: Trace Tables
For `factorial(3)`:
1. Draw a complete trace table showing each call, the values of parameters, and the return value.
2. How many function calls are made in total?

### Activity 2: Sum of Digits (Recursive)
Write a recursive function `digit_sum(n)` that returns the sum of the digits of a positive integer `n`. For example, `digit_sum(1234)` → `10`.

Hint: `n % 10` gives the last digit. `n // 10` removes the last digit.

### Activity 3: Count Down
Write a recursive function `count_down(n)` that prints the numbers from `n` down to `1`, then prints `"Go!"`. Do not use any loop.

### Activity 4: Flatten a Nested List
A list may contain integers or other lists. Write a recursive function `flatten(lst)` that returns a flat list of all integers.

```python
flatten([1, [2, 3], [4, [5, 6]], 7])
# Expected: [1, 2, 3, 4, 5, 6, 7]
```

### Activity 5: Efficiency Comparison
Write both a recursive and an iterative version of the power function. Time both versions for `power(2, 30)` using Python's `time` module. Which is faster? Why?

---

## Misconceptions / Likely Errors

| Misconception | Reality |
|---|---|
| "Recursion always needs just one base case" | Some recursive functions need multiple base cases (e.g. Fibonacci has two) |
| "Recursive solutions are always simpler to write" | Recursion can be more complex when the problem is not naturally recursive |
| "Recursion is always slow" | Simple tail recursion or problems with small input are fine; Python doesn't optimise tail calls |
| "The function returns when it calls itself" | The function continues after the recursive call returns — it then uses the returned value |
| Missing `return` on recursive case | Without `return`, the result of the recursive call is discarded and `None` propagates up |

**Classic mistake — missing `return`:**
```python
def bad_factorial(n):
    if n == 0:
        return 1
    n * bad_factorial(n - 1)   # Missing return! Returns None
```

---

## Assessment Opportunities

- **Exam-style question:** "Trace through `power(2, 3)` and show all function calls."
- **Write from specification:** "Write a recursive function that returns the number of vowels in a string."
- **Identify the error:** Give students broken recursive code (missing base case, wrong simplification). Ask them to identify and fix the error.
- **Compare and contrast:** "State one advantage and one disadvantage of a recursive implementation of Fibonacci compared to an iterative one."

---

## Homework / Independent Study

1. **Practice:** Write a recursive function `gcd(a, b)` that returns the greatest common divisor using the Euclidean algorithm: if `b == 0`, return `a`; otherwise return `gcd(b, a % b)`.

2. **Trace:** Write out a full trace for `fibonacci(5)`. Count the total number of function calls. Can you see why this is inefficient?

3. **Research:** Look up the term *memoisation*. Explain in your own words how it could be applied to improve the recursive Fibonacci function. (Optional: implement it using a dictionary.)

4. **Challenge:** Write a recursive function `permutations(s)` that returns a list of all permutations of a string `s`. For example, `permutations("abc")` should return all six orderings of the three characters.
