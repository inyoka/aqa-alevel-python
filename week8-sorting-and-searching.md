# Week 8: Sorting, Searching, and Algorithmic Thinking

## Core Focus
Implementing merge sort and quicksort in Python, applying recursive binary search, using Python's built-in `sorted()` with custom keys, and developing an awareness of algorithmic complexity (Big-O) as a tool for comparing approaches.

## Prior Knowledge Assumed
- Bubble sort and selection sort (GCSE Week 8)
- Linear search and binary search iteratively (GCSE Week 8)
- Recursion and recursive thinking (Week 3 of this course)
- `sorted()` with a key function (Week 2 of this course)
- Lists and list operations

## Learning Objectives
- Implement merge sort in Python and trace through its execution
- Implement quicksort in Python and understand the partition step
- Implement recursive binary search
- Use `sorted()` and `.sort()` with custom key functions confidently
- Understand what Big-O notation means and apply it informally to common algorithms
- Compare algorithms by their time complexity and choose appropriately
- Recognise when a built-in tool is preferable to a custom implementation

---

## Lesson Content

### 1. Reviewing GCSE Algorithms

**Bubble sort (GCSE):**
```python
def bubble_sort(lst):
    """Sort a list in place using bubble sort. O(n²)."""
    n = len(lst)
    for i in range(n):
        for j in range(n - i - 1):
            if lst[j] > lst[j + 1]:
                lst[j], lst[j + 1] = lst[j + 1], lst[j]
```

**Linear search (GCSE):**
```python
def linear_search(data, target):
    """Find target in data. Returns index or -1. O(n)."""
    for i, value in enumerate(data):
        if value == target:
            return i
    return -1
```

At GCSE, bubble sort and selection sort were sufficient. At A-Level, we need more efficient algorithms and an understanding of *why* efficiency matters.

---

### 2. Algorithmic Complexity — Big-O Informally

Big-O describes how the running time of an algorithm grows as the input size `n` grows.

| Big-O | Name | Example |
|-------|------|---------|
| O(1) | Constant | Look up a value by key in a dictionary |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Linear search, single loop over a list |
| O(n log n) | Log-linear | Merge sort, quicksort (average) |
| O(n²) | Quadratic | Bubble sort, selection sort, nested loops |

**Why this matters:**
- For n = 1,000: O(n²) → 1,000,000 operations; O(n log n) → ~10,000 operations
- For n = 1,000,000: O(n²) → 10¹² operations (impractical); O(n log n) → ~20,000,000 (fast)

At A-Level you are not expected to formally prove complexities, but you must:
1. Recognise which category an algorithm falls into
2. Explain why O(n log n) is better than O(n²) for large inputs
3. Justify your choice of algorithm for a given problem

---

### 3. Merge Sort

Merge sort uses a **divide and conquer** strategy:
1. Divide the list in half
2. Recursively sort each half
3. Merge the two sorted halves

```python
def merge_sort(lst):
    """
    Return a new sorted list using merge sort. O(n log n).
    Does not modify the original list.
    """
    if len(lst) <= 1:
        return lst   # Base case: a list of 0 or 1 elements is already sorted
    
    mid = len(lst) // 2
    left = merge_sort(lst[:mid])    # Recursively sort left half
    right = merge_sort(lst[mid:])   # Recursively sort right half
    
    return merge(left, right)

def merge(left, right):
    """Merge two sorted lists into one sorted list."""
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    # Append any remaining elements
    result.extend(left[i:])
    result.extend(right[j:])
    return result

# Test
data = [38, 27, 43, 3, 9, 82, 10]
sorted_data = merge_sort(data)
print(sorted_data)   # [3, 9, 10, 27, 38, 43, 82]
print(data)          # [38, 27, 43, 3, 9, 82, 10]  -- original unchanged
```

**Tracing merge sort on [38, 27, 43, 3]:**
```
merge_sort([38, 27, 43, 3])
├── merge_sort([38, 27])
│   ├── merge_sort([38])  → [38]
│   ├── merge_sort([27])  → [27]
│   └── merge([38], [27]) → [27, 38]
├── merge_sort([43, 3])
│   ├── merge_sort([43])  → [43]
│   ├── merge_sort([3])   → [3]
│   └── merge([43], [3])  → [3, 43]
└── merge([27, 38], [3, 43]) → [3, 27, 38, 43]
```

---

### 4. Quicksort

Quicksort also uses divide and conquer but works differently:
1. Choose a **pivot** element
2. Partition the list: elements less than pivot on the left, greater on the right
3. Recursively sort each partition

```python
def quicksort(lst):
    """
    Return a new sorted list using quicksort.
    Average O(n log n), worst case O(n²).
    """
    if len(lst) <= 1:
        return lst   # Base case
    
    pivot = lst[len(lst) // 2]   # Choose middle element as pivot
    
    left   = [x for x in lst if x < pivot]
    middle = [x for x in lst if x == pivot]
    right  = [x for x in lst if x > pivot]
    
    return quicksort(left) + middle + quicksort(right)

data = [3, 6, 8, 10, 1, 2, 1]
print(quicksort(data))   # [1, 1, 2, 3, 6, 8, 10]
```

**Why quicksort can be O(n²) in the worst case:**
If the pivot is always the smallest or largest element (e.g. sorting an already-sorted list with pivot as first element), each partition only removes one element. Choosing the middle element as pivot reduces this risk.

---

### 5. Merge Sort vs Quicksort vs Bubble Sort

| Algorithm | Best | Average | Worst | Space | Stable? |
|-----------|------|---------|-------|-------|---------|
| Bubble sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection sort | O(n²) | O(n²) | O(n²) | O(1) | No |
| Merge sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quicksort | O(n log n) | O(n log n) | O(n²) | O(log n) | No |

**Stable sort:** equal elements maintain their original relative order.

---

### 6. Recursive Binary Search

Binary search requires the list to be **sorted**. It halves the search space on each step.

```python
def binary_search(data, target, low=0, high=None):
    """
    Search for target in sorted list data[low..high].
    Returns index of target or -1 if not found. O(log n).
    """
    if high is None:
        high = len(data) - 1
    
    if low > high:
        return -1   # Base case: not found
    
    mid = (low + high) // 2
    
    if data[mid] == target:
        return mid              # Base case: found
    elif data[mid] < target:
        return binary_search(data, target, mid + 1, high)
    else:
        return binary_search(data, target, low, mid - 1)

numbers = [2, 5, 8, 12, 16, 23, 38, 56, 72, 91]
print(binary_search(numbers, 23))   # 5
print(binary_search(numbers, 10))   # -1
```

**Why O(log n)?** Each comparison halves the remaining search space. For n = 1,000, at most 10 comparisons are needed. For n = 1,000,000, at most 20 comparisons.

---

### 7. Python's Built-In `sorted()` and `.sort()`

For practical programs, Python's built-in sort (Timsort, O(n log n)) should almost always be used instead of a custom implementation.

```python
# Sort a list of numbers
numbers = [38, 27, 43, 3, 9]
print(sorted(numbers))           # Returns new list
numbers.sort()                   # Sorts in place

# Sort strings
words = ["banana", "apple", "cherry"]
print(sorted(words))             # Alphabetical

# Reverse
print(sorted(numbers, reverse=True))

# Custom key
students = [
    {"name": "Charlie", "score": 91},
    {"name": "Alice",   "score": 85},
    {"name": "Bob",     "score": 72},
]

# By score, descending
by_score = sorted(students, key=lambda s: s["score"], reverse=True)

# By name, alphabetical
by_name = sorted(students, key=lambda s: s["name"])

# Multi-key sort: year group first, then score descending within year
mixed = [
    {"name": "Alice",   "year": 12, "score": 85},
    {"name": "Charlie", "year": 13, "score": 91},
    {"name": "Bob",     "year": 12, "score": 72},
    {"name": "Diana",   "year": 13, "score": 78},
]

sorted_mixed = sorted(mixed, key=lambda s: (s["year"], -s["score"]))
for s in sorted_mixed:
    print(f"Year {s['year']}: {s['name']} ({s['score']})")
```

---

### 8. When to Use What

| Situation | Recommendation |
|-----------|---------------|
| Sorting in production code | Use `sorted()` or `.sort()` (Timsort) |
| Exam question asking to implement a sort | Use merge sort (guaranteed O(n log n)) |
| Searching unsorted data | Linear search |
| Searching sorted data | Binary search (iterative or recursive) |
| Very small datasets (< 20 items) | Any algorithm; complexity doesn't matter |
| Need stable sort | Merge sort or Python's built-in (both stable) |

---

## Example Activities

### Activity 1: Trace Tables for Merge Sort
Trace `merge_sort([5, 2, 8, 1])`:
1. Draw the full recursion tree showing how the list is split
2. Show each `merge()` step
3. Count the total number of comparisons made

### Activity 2: Implement and Compare
Implement both merge sort and bubble sort. Time both algorithms on:
1. A randomly shuffled list of 100 integers
2. A list of 1,000 integers
3. A list of 10,000 integers

Use Python's `time` module. Record results in a table. What pattern do you observe?

### Activity 3: Binary Search Extension
Modify the binary search function to:
1. Return the index of the *first* occurrence of a target (in a list with duplicates)
2. Return all indices where the target appears
3. Handle the case where the input list is not sorted (raise a `ValueError`)

### Activity 4: Sort by Multiple Criteria
Given a list of exam results (student name, subject, score), write a program that:
1. Sorts by subject name, then by score descending within each subject
2. Ranks students overall by their average score across all subjects
3. Prints a formatted results table

---

## Misconceptions / Likely Errors

| Misconception | Reality |
|---|---|
| "Sorted means binary search works on any list" | Binary search requires the list to be sorted **in the correct order** before searching |
| Quicksort is always faster than merge sort | Quicksort has O(n²) worst case; merge sort is always O(n log n) |
| `sorted()` is O(n²) | Python uses Timsort which is O(n log n) — always prefer it for production code |
| Merge sort modifies the original list | This implementation of merge sort returns a new list; the original is unchanged |
| Big-O is about actual speed | Big-O describes how time *scales* with input size; a slow O(n log n) can be slower than a fast O(n²) for tiny inputs |

**Common merge sort error — missing base case:**
```python
def merge_sort(lst):
    # Missing: if len(lst) <= 1: return lst
    mid = len(lst) // 2
    left = merge_sort(lst[:mid])   # RecursionError on empty/single list!
    ...
```

---

## Assessment Opportunities

- **Trace question:** "Show the steps of merge sort applied to `[8, 3, 6, 1, 4]`."
- **Comparison question:** "State one advantage of merge sort over quicksort and one advantage of quicksort over merge sort."
- **Code completion:** Provide the `merge()` function and ask students to write `merge_sort()`.
- **Complexity question:** "A list has 1,000 elements. Estimate the maximum number of comparisons needed by (a) linear search, (b) binary search, (c) bubble sort."

---

## Homework / Independent Study

1. **Implement and test:** Write both merge sort and quicksort from memory. Test each with: an empty list, a single element, a list already sorted, a list sorted in reverse, and a list with duplicate values.

2. **Quicksort worst case:** Demonstrate that picking the first element as pivot on an already-sorted list of 10 elements results in O(n²) behaviour. Show the partition sizes at each step.

3. **Sort key challenge:** Given a list of file names (e.g. `"report_2025_03.csv"`), sort them by year, then by month, extracted from the filename. Use a lambda as the key.

4. **Research:** Look up Python's `bisect` module. Explain what `bisect.bisect_left()` does and provide one practical example of using it on a sorted list.
