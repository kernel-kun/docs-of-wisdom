# 🐍 Python for DSA — Complete Quick Reference

> **Purpose:** Single source of revision for Python-specific tools, data structures, and patterns used in DSA interviews.  
> **Language:** Python 3.10+

---

## Table of Contents

1. [Built-in Functions](#1-built-in-functions)
2. [The `collections` Module](#2-the-collections-module)
3. [The `functools` Module](#3-the-functools-module)
4. [The `itertools` Module](#4-the-itertools-module)
5. [The `heapq` Module — Heaps / Priority Queues](#5-the-heapq-module--heaps--priority-queues)
6. [The `bisect` Module — Binary Search](#6-the-bisect-module--binary-search)
7. [Queues, Stacks, and Deques](#7-queues-stacks-and-deques)
8. [Sorting Tricks](#8-sorting-tricks)
9. [String Manipulation](#9-string-manipulation)
10. [Math Utilities](#10-math-utilities)
11. [Bit Manipulation Built-ins](#11-bit-manipulation-built-ins)
12. [Common Python Gotchas in Interviews](#12-common-python-gotchas-in-interviews)
13. [Complexity Cheat Sheet for Python Operations](#13-complexity-cheat-sheet)
14. [Learning Resources](#14-learning-resources)


## Detailed Table of Contents

- [1. Built-in Functions](#1-built-in-functions)
	- [Type Conversion \& Inspection](#type-conversion--inspection)
	- [Character \& ASCII](#character--ascii)
	- [Binary, Hex, Octal](#binary-hex-octal)
	- [Aggregation](#aggregation)
	- [Iteration Helpers](#iteration-helpers)
	- [Identity \& Memory](#identity--memory)
	- [Infinity \& Special Values](#infinity--special-values)
- [2. The `collections` Module](#2-the-collections-module)
	- [`Counter` — Frequency Counting](#counter--frequency-counting)
	- [`defaultdict` — Dict with Default Values](#defaultdict--dict-with-default-values)
	- [`deque` — Double-Ended Queue](#deque--double-ended-queue)
	- [`OrderedDict` — Dict that Remembers Insertion Order](#ordereddict--dict-that-remembers-insertion-order)
	- [`namedtuple` — Lightweight Immutable Objects](#namedtuple--lightweight-immutable-objects)
- [3. The `functools` Module](#3-the-functools-module)
	- [`lru_cache` — Memoization Decorator](#lru_cache--memoization-decorator)
	- [`cache` — Simpler Alias (Python 3.9+)](#cache--simpler-alias-python-39)
	- [`reduce` — Fold a Sequence](#reduce--fold-a-sequence)
	- [`partial` — Pre-fill Function Arguments](#partial--pre-fill-function-arguments)
	- [`cmp_to_key` — Custom Comparators for Sorting](#cmp_to_key--custom-comparators-for-sorting)
- [4. The `itertools` Module](#4-the-itertools-module)
	- [`permutations` \& `combinations`](#permutations--combinations)
	- [`product` — Cartesian Product](#product--cartesian-product)
	- [`accumulate` — Running Prefix Sums (and more)](#accumulate--running-prefix-sums-and-more)
	- [`chain` — Flatten Multiple Iterables](#chain--flatten-multiple-iterables)
	- [`groupby` — Group Consecutive Elements](#groupby--group-consecutive-elements)
	- [`zip_longest` — Zip with Padding](#zip_longest--zip-with-padding)
- [5. The `heapq` Module — Heaps / Priority Queues](#5-the-heapq-module--heaps--priority-queues)
	- [Basic Operations](#basic-operations)
	- [Max Heap (Negate Values)](#max-heap-negate-values)
	- [N Largest / N Smallest](#n-largest--n-smallest)
	- [Merge Sorted Iterables](#merge-sorted-iterables)
	- [Heap with Custom Objects (Tuples)](#heap-with-custom-objects-tuples)
- [6. The `bisect` Module — Binary Search](#6-the-bisect-module--binary-search)
- [7. Queues, Stacks, and Deques](#7-queues-stacks-and-deques)
	- [Stack (Use a `list`)](#stack-use-a-list)
	- [Queue (Use `collections.deque`)](#queue-use-collectionsdeque)
	- [Priority Queue (Use `heapq`)](#priority-queue-use-heapq)
	- [Thread-Safe Queue (rarely needed in DSA)](#thread-safe-queue-rarely-needed-in-dsa)
- [8. Sorting Tricks](#8-sorting-tricks)
	- [Basic Sorting](#basic-sorting)
	- [Multi-Key Sorting](#multi-key-sorting)
	- [Custom Comparator](#custom-comparator)
	- [Sorting Stability](#sorting-stability)
- [9. String Manipulation](#9-string-manipulation)
	- [Common Operations](#common-operations)
	- [String Building (Avoid `+=` in Loops!)](#string-building-avoid--in-loops)
	- [Useful String Checks](#useful-string-checks)
- [10. Math Utilities](#10-math-utilities)
	- [`divmod` — Quotient and Remainder Together](#divmod--quotient-and-remainder-together)
- [11. Bit Manipulation Built-ins](#11-bit-manipulation-built-ins)
- [12. Common Python Gotchas in Interviews](#12-common-python-gotchas-in-interviews)
	- [Gotcha 1: Mutable Default Arguments](#gotcha-1-mutable-default-arguments)
	- [Gotcha 2: List Multiplication Creates Shared References](#gotcha-2-list-multiplication-creates-shared-references)
	- [Gotcha 3: Integer Division with Negatives](#gotcha-3-integer-division-with-negatives)
	- [Gotcha 4: Operator Precedence with Bitwise Operators](#gotcha-4-operator-precedence-with-bitwise-operators)
	- [Gotcha 5: Shallow vs Deep Copy](#gotcha-5-shallow-vs-deep-copy)
	- [Gotcha 6: `is` vs `==`](#gotcha-6-is-vs-)
	- [Gotcha 7: Modifying a List While Iterating](#gotcha-7-modifying-a-list-while-iterating)
	- [Gotcha 8: Global Variables in Nested Functions](#gotcha-8-global-variables-in-nested-functions)
- [13. Complexity Cheat Sheet for Python Operations](#13-complexity-cheat-sheet-for-python-operations)
	- [List](#list)
	- [Dict](#dict)
	- [Set](#set)
	- [Deque](#deque)
	- [Heap (via `heapq`)](#heap-via-heapq)
- [14. Learning Resources](#14-learning-resources)
	- [Data Structures](#data-structures)
		- [Heaps](#heaps)


---

## 1. Built-in Functions

### Type Conversion & Inspection

```python
int("42")           # str → int: 42
int("1101", 2)      # binary str → int: 13
int("FF", 16)       # hex str → int: 255
float("3.14")       # str → float: 3.14
str(42)             # int → str: "42"
bool(0)             # → False (0, None, "", [], {}, set() are falsy)
type(x)             # returns the type of x
isinstance(x, int)  # True if x is an int (or subclass)
```

### Character & ASCII

```python
ord('a')            # char → ASCII code: 97
ord('A')            # → 65
ord('0')            # → 48
chr(97)             # ASCII code → char: 'a'
chr(65)             # → 'A'

# Common patterns:
ord(ch) - ord('a')  # letter → 0-25 index (for bitmasks, frequency arrays)
chr(ord('a') + 3)   # → 'd' (offset arithmetic)
ch.isalpha()        # True if letter
ch.isdigit()        # True if digit
ch.isalnum()        # True if letter or digit
ch.lower()          # → lowercase
ch.upper()          # → uppercase
```

### Binary, Hex, Octal

```python
bin(13)             # → '0b1101' (binary string)
hex(255)            # → '0xff' (hex string)
oct(8)              # → '0o10' (octal string)

bin(13).count('1')  # count set bits: 3
(13).bit_count()    # count set bits: 3 (Python 3.10+)
(13).bit_length()   # bits needed: 4
```

### Aggregation

```python
min(3, 1, 4)        # → 1
max(3, 1, 4)        # → 4
sum([1, 2, 3])      # → 6
sum([1, 2, 3], 10)  # → 16 (start from 10)

# With key functions:
min(words, key=len)              # shortest word
max(nums, key=lambda x: abs(x)) # largest absolute value
min(pairs, key=lambda p: p[1])   # pair with smallest second element
```

### Iteration Helpers

```python
# enumerate — index + value
for i, val in enumerate(arr):
    print(i, val)

for i, val in enumerate(arr, start=1):  # start index from 1
    print(i, val)

# zip — iterate multiple sequences in parallel
for a, b in zip(list1, list2):
    print(a, b)

# zip with different lengths (stops at shortest):
list(zip([1,2,3], ['a','b']))  # → [(1,'a'), (2,'b')]

# zip_longest (from itertools) — pads with fillvalue:
from itertools import zip_longest
list(zip_longest([1,2,3], ['a','b'], fillvalue=None))
# → [(1,'a'), (2,'b'), (3,None)]

# reversed — iterate backwards (O(1) memory)
for val in reversed(arr):
    print(val)

# sorted — returns new sorted list
sorted([3, 1, 4])              # → [1, 3, 4]
sorted([3, 1, 4], reverse=True) # → [4, 3, 1]
sorted(words, key=len)          # sort by length

# map — apply function to each element
list(map(int, ["1", "2", "3"]))  # → [1, 2, 3]
list(map(str, [1, 2, 3]))       # → ["1", "2", "3"]

# filter — keep elements where function returns True
list(filter(lambda x: x > 0, [-1, 2, -3, 4]))  # → [2, 4]

# any / all
any([False, False, True])  # → True (at least one truthy)
all([True, True, True])    # → True (all truthy)
all([True, False, True])   # → False
```

### Identity & Memory

```python
id(x)               # memory address of object (useful for debugging reference issues)
x is y              # True if same object (not just equal value)
x is None           # preferred way to check for None (not x == None)
```

### Infinity & Special Values

```python
float('inf')        # positive infinity
float('-inf')       # negative infinity
float('nan')        # not a number

# In DSA, commonly used for:
min_val = float('inf')   # initialize for finding minimum
max_val = float('-inf')  # initialize for finding maximum
```

---

## 2. The `collections` Module

### `Counter` — Frequency Counting

```python
from collections import Counter

# Count occurrences
nums = [1, 2, 2, 3, 3, 3]
freq = Counter(nums)
# Counter({3: 3, 2: 2, 1: 1})

# Access counts
freq[3]          # → 3
freq[99]         # → 0 (missing keys return 0, no KeyError!)

# Most common elements
freq.most_common(2)  # → [(3, 3), (2, 2)] — top 2 most frequent

# Convert to regular dict
dict(freq)       # → {1: 1, 2: 2, 3: 3}

# Arithmetic on Counters
a = Counter("aab")   # Counter({'a': 2, 'b': 1})
b = Counter("abc")   # Counter({'a': 1, 'b': 1, 'c': 1})

a + b    # Counter({'a': 3, 'b': 2, 'c': 1}) — add counts
a - b    # Counter({'a': 1}) — subtract (drops zero/negative)
a & b    # Counter({'a': 1, 'b': 1}) — min of each (intersection)
a | b    # Counter({'a': 2, 'b': 1, 'c': 1}) — max of each (union)

# Count from string
Counter("hello")  # Counter({'l': 2, 'h': 1, 'e': 1, 'o': 1})

# Check if one Counter is a subset of another (anagram check!)
Counter("ab") <= Counter("aab")  # True — "ab" chars are subset of "aab"
```

**DSA use cases:**
- Frequency counting (obvious)
- Anagram detection: `Counter(s1) == Counter(s2)`
- Check if one string's chars are subset of another: `Counter(s1) <= Counter(s2)`
- Top K frequent elements: `Counter(nums).most_common(k)`

### `defaultdict` — Dict with Default Values

```python
from collections import defaultdict

# defaultdict(factory_function) — auto-creates missing keys

# --- defaultdict(int) — default value: 0 ---
freq = defaultdict(int)
freq['a'] += 1      # no KeyError! starts from 0
freq['a'] += 1
freq['b'] += 1
# defaultdict(int, {'a': 2, 'b': 1})

# --- defaultdict(list) — default value: [] ---
graph = defaultdict(list)
graph[1].append(2)   # no need to check if key exists
graph[1].append(3)
graph[2].append(1)
# defaultdict(list, {1: [2, 3], 2: [1]})

# --- defaultdict(set) — default value: set() ---
groups = defaultdict(set)
groups['vowels'].add('a')
groups['vowels'].add('e')

# --- defaultdict(lambda: float('inf')) — custom default ---
dist = defaultdict(lambda: float('inf'))
dist['start'] = 0
print(dist['unknown'])  # → inf (not KeyError)
```

**Why `defaultdict(int)` for bitmasks?**
- `int()` returns `0`
- `0` is the identity for OR: `0 | x = x`
- So `mask[val] |= (1 << i)` works even for new keys
- Without it, you'd need `mask[val] = mask.get(val, 0) | (1 << i)`

**DSA use cases:**
- Adjacency lists: `defaultdict(list)`
- Frequency maps: `defaultdict(int)`
- Grouping: `defaultdict(list)` or `defaultdict(set)`
- Bitmask tracking: `defaultdict(int)`
- Graph with weighted edges: `defaultdict(dict)`

### `deque` — Double-Ended Queue

```python
from collections import deque

dq = deque([1, 2, 3])

# O(1) operations on both ends:
dq.append(4)        # add to right: [1, 2, 3, 4]
dq.appendleft(0)    # add to left:  [0, 1, 2, 3, 4]
dq.pop()            # remove from right: 4, deque is [0, 1, 2, 3]
dq.popleft()        # remove from left:  0, deque is [1, 2, 3]

# Peek (without removing):
dq[0]               # leftmost element
dq[-1]              # rightmost element

# Rotate:
dq.rotate(1)        # rotate right by 1: [3, 1, 2]
dq.rotate(-1)       # rotate left by 1:  [1, 2, 3]

# Fixed-size deque (sliding window!):
window = deque(maxlen=3)
for x in [1, 2, 3, 4, 5]:
    window.append(x)
    print(list(window))
# [1]
# [1, 2]
# [1, 2, 3]
# [2, 3, 4]  ← oldest element auto-removed!
# [3, 4, 5]

# Convert to list:
list(dq)
```

**DSA use cases:**
- BFS queue: `deque` with `popleft()` + `append()`
- Sliding window maximum/minimum (monotonic deque)
- Implementing a stack + queue hybrid
- 0-1 BFS: `appendleft()` for 0-weight edges, `append()` for 1-weight

### `OrderedDict` — Dict that Remembers Insertion Order

```python
from collections import OrderedDict

od = OrderedDict()
od['a'] = 1
od['b'] = 2
od['c'] = 3

# Move to end:
od.move_to_end('a')        # move 'a' to the end
od.move_to_end('c', last=False)  # move 'c' to the beginning

# Pop from specific end:
od.popitem(last=True)      # pop last item (LIFO)
od.popitem(last=False)     # pop first item (FIFO)
```

**DSA use cases:**
- **LRU Cache implementation** (LeetCode 146) — `move_to_end` on access, `popitem(last=False)` to evict
- Note: In Python 3.7+, regular `dict` preserves insertion order, but `OrderedDict` has `move_to_end` and `popitem` with `last` parameter

### `namedtuple` — Lightweight Immutable Objects

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
p = Point(3, 4)
print(p.x, p.y)    # 3 4
print(p[0], p[1])  # 3 4 (also indexable)

# Useful for graph nodes, coordinates, etc.
Edge = namedtuple('Edge', ['src', 'dst', 'weight'])
```

---

## 3. The `functools` Module

### `lru_cache` — Memoization Decorator

```python
from functools import lru_cache

@lru_cache(maxsize=None)  # None = unlimited cache size
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)

fib(100)  # instant! Without cache, this would take forever

# Check cache stats:
fib.cache_info()  # CacheInfo(hits=98, misses=101, maxsize=None, currsize=101)

# Clear cache:
fib.cache_clear()
```

**Important notes:**
- Arguments must be **hashable** (no lists, dicts, sets — use tuples instead)
- `maxsize=None` → unlimited cache (use for DP)
- `maxsize=128` (default) → LRU eviction (use for bounded caching)
- `maxsize` should be a power of 2 for best performance

```python
# ❌ Won't work — list is not hashable:
@lru_cache(maxsize=None)
def solve(arr):  # arr is a list → TypeError!
    pass

# ✅ Use tuple instead:
@lru_cache(maxsize=None)
def solve(arr):  # arr is a tuple → works!
    pass

solve(tuple([1, 2, 3]))  # convert list to tuple before calling
```

**DSA use cases:**
- Top-down DP (memoized recursion)
- DFS with memoization on grid problems
- Game theory (minimax with memoization)

### `cache` — Simpler Alias (Python 3.9+)

```python
from functools import cache

@cache  # equivalent to @lru_cache(maxsize=None)
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)
```

### `reduce` — Fold a Sequence

```python
from functools import reduce

# reduce(function, iterable, initial)
# Applies function cumulatively: f(f(f(initial, a), b), c)

reduce(lambda a, b: a + b, [1, 2, 3, 4])     # → 10 (sum)
reduce(lambda a, b: a * b, [1, 2, 3, 4])     # → 24 (product)
reduce(lambda a, b: a ^ b, [1, 2, 3])        # → 0 (XOR all)
reduce(lambda a, b: a if a > b else b, nums) # → max (but just use max())
```

**DSA use cases:**
- XOR all elements: `reduce(lambda a, b: a ^ b, nums)`
- Product of array: `reduce(lambda a, b: a * b, nums)`
- Generally prefer explicit loops for readability in interviews

### `partial` — Pre-fill Function Arguments

```python
from functools import partial

def power(base, exponent):
    return base ** exponent

square = partial(power, exponent=2)
cube = partial(power, exponent=3)

square(5)  # → 25
cube(3)    # → 27

# Useful with sorted/map:
from functools import partial

def distance(point, origin=(0, 0)):
    return (point[0] - origin[0])**2 + (point[1] - origin[1])**2

# Sort points by distance from (1, 1):
dist_from_center = partial(distance, origin=(1, 1))
sorted(points, key=dist_from_center)
```

**DSA use cases:**
- Creating specialized comparison functions for sorting
- Partially applying graph traversal functions
- Simplifying callback-heavy code

### `cmp_to_key` — Custom Comparators for Sorting

```python
from functools import cmp_to_key

# Python 3 removed the cmp parameter from sorted().
# Use cmp_to_key to convert old-style comparators.

def compare(a, b):
    # Return negative if a < b, zero if a == b, positive if a > b
    if a + b > b + a:
        return -1  # a should come first
    elif a + b < b + a:
        return 1   # b should come first
    return 0

# LeetCode 179: Largest Number
nums = ["3", "30", "34", "5", "9"]
sorted(nums, key=cmp_to_key(compare))
# → ["9", "5", "34", "3", "30"] → "9534330"
```

**DSA use cases:**
- Custom sorting where comparison isn't a simple key extraction
- LeetCode 179 (Largest Number)
- Any problem requiring non-trivial ordering

---

## 4. The `itertools` Module

### `permutations` & `combinations`

```python
from itertools import permutations, combinations, combinations_with_replacement

# All permutations (order matters):
list(permutations([1, 2, 3]))
# [(1,2,3), (1,3,2), (2,1,3), (2,3,1), (3,1,2), (3,2,1)]

list(permutations([1, 2, 3], 2))  # length-2 permutations
# [(1,2), (1,3), (2,1), (2,3), (3,1), (3,2)]

# All combinations (order doesn't matter):
list(combinations([1, 2, 3], 2))
# [(1,2), (1,3), (2,3)]

# Combinations with replacement:
list(combinations_with_replacement([1, 2, 3], 2))
# [(1,1), (1,2), (1,3), (2,2), (2,3), (3,3)]
```

### `product` — Cartesian Product

```python
from itertools import product

# Replaces nested loops:
list(product([1, 2], ['a', 'b']))
# [(1,'a'), (1,'b'), (2,'a'), (2,'b')]

# Repeat parameter (like nested loops of same iterable):
list(product([0, 1], repeat=3))
# [(0,0,0), (0,0,1), (0,1,0), (0,1,1), (1,0,0), (1,0,1), (1,1,0), (1,1,1)]

# Grid traversal directions:
directions = list(product([-1, 0, 1], repeat=2))
# [(-1,-1), (-1,0), (-1,1), (0,-1), (0,0), (0,1), (1,-1), (1,0), (1,1)]
```

### `accumulate` — Running Prefix Sums (and more)

```python
from itertools import accumulate
import operator

# Prefix sums:
list(accumulate([1, 2, 3, 4]))
# [1, 3, 6, 10]

# Running maximum:
list(accumulate([3, 1, 4, 1, 5], max))
# [3, 3, 4, 4, 5]

# Running product:
list(accumulate([1, 2, 3, 4], operator.mul))
# [1, 2, 6, 24]

# With initial value (Python 3.8+):
list(accumulate([1, 2, 3], initial=0))
# [0, 1, 3, 6]
```

### `chain` — Flatten Multiple Iterables

```python
from itertools import chain

# Concatenate iterables:
list(chain([1, 2], [3, 4], [5]))
# [1, 2, 3, 4, 5]

# Flatten list of lists:
list(chain.from_iterable([[1, 2], [3, 4], [5]]))
# [1, 2, 3, 4, 5]
```

### `groupby` — Group Consecutive Elements

```python
from itertools import groupby

# Groups consecutive equal elements:
data = [1, 1, 2, 2, 2, 3, 1, 1]
for key, group in groupby(data):
    print(key, list(group))
# 1 [1, 1]
# 2 [2, 2, 2]
# 3 [3]
# 1 [1, 1]  ← note: only groups CONSECUTIVE elements!

# With key function:
words = ["hi", "hey", "bye", "be", "hello"]
for key, group in groupby(sorted(words, key=lambda w: w[0]), key=lambda w: w[0]):
    print(key, list(group))
# b ['be', 'bye']
# h ['hello', 'hey', 'hi']
```

### `zip_longest` — Zip with Padding

```python
from itertools import zip_longest

list(zip_longest([1, 2, 3], ['a', 'b'], fillvalue='-'))
# [(1, 'a'), (2, 'b'), (3, '-')]
```

---

## 5. The `heapq` Module — Heaps / Priority Queues

Python's `heapq` implements a **min-heap** using a regular list.

### Basic Operations

```python
import heapq

# Create a heap from a list (in-place, O(N)):
nums = [5, 3, 8, 1, 2]
heapq.heapify(nums)
# nums is now a valid min-heap: [1, 2, 8, 5, 3]

# Push an element (O(log N)):
heapq.heappush(nums, 0)

# Pop the smallest element (O(log N)):
smallest = heapq.heappop(nums)  # → 0

# Peek at smallest without removing (O(1)):
smallest = nums[0]

# Push and pop in one operation (more efficient):
heapq.heappushpop(nums, 4)  # push 4, then pop smallest
heapq.heapreplace(nums, 4)  # pop smallest, then push 4
```

### Max Heap (Negate Values)

```python
import heapq

# Python only has min-heap. For max-heap, negate values:
max_heap = []
heapq.heappush(max_heap, -5)
heapq.heappush(max_heap, -3)
heapq.heappush(max_heap, -8)

largest = -heapq.heappop(max_heap)  # → 8

# ⚠️ AVOID heapq._heapify_max — it's a private/undocumented function
# that doesn't work with heappush/heappop. Always use negation instead.
```

### N Largest / N Smallest

```python
import heapq

nums = [5, 3, 8, 1, 2, 9, 4]

heapq.nsmallest(3, nums)  # → [1, 2, 3]
heapq.nlargest(3, nums)   # → [9, 8, 5]

# With key function:
people = [("Alice", 30), ("Bob", 25), ("Charlie", 35)]
heapq.nsmallest(2, people, key=lambda p: p[1])
# → [("Bob", 25), ("Alice", 30)]
```

### Merge Sorted Iterables

```python
import heapq

# Merge multiple sorted lists into one sorted iterator:
merged = heapq.merge([1, 3, 5], [2, 4, 6], [0, 7])
list(merged)  # → [0, 1, 2, 3, 4, 5, 6, 7]
```

### Heap with Custom Objects (Tuples)

```python
import heapq

# Tuples are compared element by element:
heap = []
heapq.heappush(heap, (5, "task_a"))
heapq.heappush(heap, (1, "task_b"))
heapq.heappush(heap, (3, "task_c"))

heapq.heappop(heap)  # → (1, "task_b") — smallest priority first

# ⚠️ If first elements are equal, Python compares second elements.
# If second elements aren't comparable (e.g., custom objects), use a tiebreaker:
counter = 0
heap = []
heapq.heappush(heap, (priority, counter, task_object))
counter += 1
# The counter ensures unique ordering even with equal priorities.
```

**DSA use cases:**
- Dijkstra's algorithm (min-heap of `(distance, node)`)
- K-th largest/smallest element
- Merge K sorted lists (LeetCode 23)
- Median from data stream (two heaps)
- Task scheduling problems
- Top K frequent elements

---

## 6. The `bisect` Module — Binary Search

```python
import bisect

# bisect works on SORTED lists

arr = [1, 3, 5, 7, 9]

# Find insertion point (maintains sorted order):
bisect.bisect_left(arr, 5)   # → 2 (leftmost position for 5)
bisect.bisect_right(arr, 5)  # → 3 (rightmost position for 5)
bisect.bisect(arr, 5)        # → 3 (same as bisect_right)

# bisect_left vs bisect_right with duplicates:
arr2 = [1, 3, 5, 5, 5, 7, 9]
bisect.bisect_left(arr2, 5)   # → 2 (first 5 is at index 2)
bisect.bisect_right(arr2, 5)  # → 5 (after last 5)

# Insert while maintaining sorted order:
bisect.insort_left(arr, 4)   # arr is now [1, 3, 4, 5, 7, 9]
bisect.insort_right(arr, 6)  # arr is now [1, 3, 4, 5, 6, 7, 9]

# Binary search for exact match:
def binary_search(arr, target):
    i = bisect.bisect_left(arr, target)
    if i < len(arr) and arr[i] == target:
        return i
    return -1

# Find number of elements less than x:
count_less = bisect.bisect_left(arr, x)

# Find number of elements less than or equal to x:
count_leq = bisect.bisect_right(arr, x)

# Find number of elements in range [lo, hi]:
count_in_range = bisect.bisect_right(arr, hi) - bisect.bisect_left(arr, lo)
```

**DSA use cases:**
- Binary search on sorted arrays
- Finding insertion points
- Counting elements in ranges
- Longest Increasing Subsequence (O(N log N) with patience sorting)
- Implementing SortedList-like behavior

---

## 7. Queues, Stacks, and Deques

### Stack (Use a `list`)

```python
stack = []
stack.append(1)     # push: O(1)
stack.append(2)
stack.append(3)
stack.pop()         # pop: O(1) → 3
stack[-1]           # peek: O(1) → 2
len(stack)          # size: O(1)
not stack           # is empty? (Pythonic)
```

### Queue (Use `collections.deque`)

```python
from collections import deque

queue = deque()
queue.append(1)     # enqueue (right): O(1)
queue.append(2)
queue.append(3)
queue.popleft()     # dequeue (left): O(1) → 1
queue[0]            # peek front: O(1) → 2
len(queue)          # size: O(1)

# ⚠️ DON'T use list as a queue — list.pop(0) is O(N)!
```

### Priority Queue (Use `heapq`)

See [Section 5](#5-the-heapq-module--heaps--priority-queues).

### Thread-Safe Queue (rarely needed in DSA)

```python
from queue import Queue, LifoQueue, PriorityQueue

q = Queue()          # FIFO
q.put(1)
q.get()              # → 1

s = LifoQueue()      # LIFO (stack)
pq = PriorityQueue() # Priority queue
```

---

## 8. Sorting Tricks

### Basic Sorting

```python
# In-place sort (modifies original list):
arr.sort()
arr.sort(reverse=True)
arr.sort(key=lambda x: x[1])

# Returns new sorted list (original unchanged):
sorted(arr)
sorted(arr, reverse=True)
sorted(arr, key=lambda x: x[1])
```

### Multi-Key Sorting

```python
# Sort by multiple criteria using tuples:
students = [("Alice", 90), ("Bob", 85), ("Charlie", 90)]

# Sort by grade descending, then name ascending:
students.sort(key=lambda s: (-s[1], s[0]))
# [("Alice", 90), ("Charlie", 90), ("Bob", 85)]

# Equivalent using operator.itemgetter:
from operator import itemgetter
students.sort(key=itemgetter(1, 0))  # sort by grade, then name (both ascending)
```

### Custom Comparator

```python
from functools import cmp_to_key

# When you need comparison logic that can't be expressed as a key:
def compare(a, b):
    # Return negative if a should come first
    # Return positive if b should come first
    # Return 0 if equal
    return (a > b) - (a < b)  # standard ascending

sorted(arr, key=cmp_to_key(compare))
```

### Sorting Stability

Python's sort is **stable** — equal elements maintain their relative order. This means you can sort by multiple keys by sorting multiple times (least significant key first):

```python
# Sort by grade, then by name (for equal grades):
students.sort(key=lambda s: s[0])  # first sort by name
students.sort(key=lambda s: s[1])  # then sort by grade (stable!)
```

---

## 9. String Manipulation

### Common Operations

```python
s = "hello world"

s.split()           # → ["hello", "world"] (split by whitespace)
s.split(",")        # split by comma
" ".join(["a","b"]) # → "a b" (join with separator)

s.strip()           # remove leading/trailing whitespace
s.lstrip()          # remove leading whitespace
s.rstrip()          # remove trailing whitespace

s.startswith("he")  # → True
s.endswith("ld")    # → True

s.find("lo")        # → 3 (index of first occurrence, -1 if not found)
s.index("lo")       # → 3 (same but raises ValueError if not found)
s.count("l")        # → 3 (count occurrences)

s.replace("world", "python")  # → "hello python"

s[::-1]             # → "dlrow olleh" (reverse string)
```

### String Building (Avoid `+=` in Loops!)

```python
# ❌ BAD — O(N²) because strings are immutable:
result = ""
for ch in chars:
    result += ch  # creates a new string each time!

# ✅ GOOD — O(N) using list + join:
parts = []
for ch in chars:
    parts.append(ch)
result = "".join(parts)

# ✅ ALSO GOOD — list comprehension:
result = "".join(ch for ch in chars if ch.isalpha())
```

### Useful String Checks

```python
s.isalpha()     # all alphabetic?
s.isdigit()     # all digits?
s.isalnum()     # all alphanumeric?
s.islower()     # all lowercase?
s.isupper()     # all uppercase?
s.isspace()     # all whitespace?
```

---

## 10. Math Utilities

```python
import math

# Rounding:
math.ceil(3.2)      # → 4 (round up)
math.floor(3.8)     # → 3 (round down)
round(3.5)          # → 4 (banker's rounding in Python 3!)
round(2.5)          # → 2 (rounds to nearest even!)

# Absolute value:
abs(-5)             # → 5

# Power & Roots:
math.sqrt(16)       # → 4.0
math.pow(2, 10)     # → 1024.0
2 ** 10             # → 1024 (integer power — prefer this)
pow(2, 10, 1000)    # → 24 (modular exponentiation: 2^10 % 1000)

# GCD & LCM:
math.gcd(12, 8)     # → 4
math.lcm(12, 8)     # → 24 (Python 3.9+)
math.gcd(12, 8, 6)  # → 2 (multiple args, Python 3.9+)

# Factorial:
math.factorial(5)   # → 120

# Logarithms:
math.log(8, 2)      # → 3.0 (log base 2)
math.log2(8)        # → 3.0 (more precise for base 2)
math.log10(1000)    # → 3.0

# Constants:
math.pi             # → 3.141592653589793
math.e              # → 2.718281828459045
math.inf            # → inf (same as float('inf'))

# Integer division (careful with negatives!):
7 // 2              # → 3
-7 // 2             # → -4 (floors toward negative infinity!)
int(-7 / 2)         # → -3 (truncates toward zero — C-style)

# Modulo (careful with negatives!):
7 % 3               # → 1
-7 % 3              # → 2 (Python: result has sign of divisor)
# In C/Java: -7 % 3 = -1 (result has sign of dividend)

# Combinatorics (Python 3.8+):
math.comb(5, 2)     # → 10 (5 choose 2)
math.perm(5, 2)     # → 20 (5 permute 2)
```

### `divmod` — Quotient and Remainder Together

```python
divmod(17, 5)       # → (3, 2) — quotient=3, remainder=2
# Useful for coordinate conversions:
row, col = divmod(index, num_cols)
```

---

## 11. Bit Manipulation Built-ins

```python
# See docs/bit-manipulation-guide.md for the full deep dive!

bin(13)             # → '0b1101'
int('1101', 2)      # → 13
(13).bit_count()    # → 3 (Python 3.10+)
(13).bit_length()   # → 4
bin(13).count('1')  # → 3 (all Python 3 versions)

# Operators: &  |  ^  ~  <<  >>
# See the bit manipulation guide for patterns and use cases.
```

---

## 12. Common Python Gotchas in Interviews

### Gotcha 1: Mutable Default Arguments

```python
# ❌ BUG — default list is shared across all calls!
def add_item(item, lst=[]):
    lst.append(item)
    return lst

add_item(1)  # → [1]
add_item(2)  # → [1, 2] — NOT [2]!

# ✅ FIX:
def add_item(item, lst=None):
    if lst is None:
        lst = []
    lst.append(item)
    return lst
```

### Gotcha 2: List Multiplication Creates Shared References

```python
# ❌ BUG — all rows share the same inner list!
grid = [[0] * 3] * 3
grid[0][0] = 1
# grid is now [[1,0,0], [1,0,0], [1,0,0]] — all rows changed!

# ✅ FIX:
grid = [[0] * 3 for _ in range(3)]
grid[0][0] = 1
# grid is now [[1,0,0], [0,0,0], [0,0,0]] — only first row changed
```

### Gotcha 3: Integer Division with Negatives

```python
# Python floors toward negative infinity:
-7 // 2   # → -4 (not -3!)

# If you want C-style truncation toward zero:
int(-7 / 2)  # → -3
```

### Gotcha 4: Operator Precedence with Bitwise Operators

```python
# ❌ BUG — == has higher precedence than &
if n & (n - 1) == 0:  # parsed as: n & ((n-1) == 0) → n & True/False
    pass

# ✅ FIX — use parentheses:
if (n & (n - 1)) == 0:
    pass
```

### Gotcha 5: Shallow vs Deep Copy

```python
import copy

a = [[1, 2], [3, 4]]

b = a[:]           # shallow copy — inner lists are shared!
b = list(a)        # also shallow copy
b = a.copy()       # also shallow copy

b[0][0] = 99       # modifies a[0][0] too!

c = copy.deepcopy(a)  # deep copy — fully independent
c[0][0] = 99          # a is unchanged
```

### Gotcha 6: `is` vs `==`

```python
a = [1, 2, 3]
b = [1, 2, 3]

a == b   # → True (same value)
a is b   # → False (different objects)

# ⚠️ Small integers (-5 to 256) are cached in CPython:
a = 256
b = 256
a is b   # → True (cached!)

a = 257
b = 257
a is b   # → False (not cached)

# Always use == for value comparison, is only for None:
if x is None:
    pass
```

### Gotcha 7: Modifying a List While Iterating

```python
# ❌ BUG — skips elements:
nums = [1, 2, 3, 4, 5]
for i, n in enumerate(nums):
    if n % 2 == 0:
        nums.pop(i)

# ✅ FIX — iterate over a copy, or build a new list:
nums = [n for n in nums if n % 2 != 0]

# ✅ Or iterate backwards:
for i in range(len(nums) - 1, -1, -1):
    if nums[i] % 2 == 0:
        nums.pop(i)
```

### Gotcha 8: Global Variables in Nested Functions

```python
# ❌ BUG — can't reassign outer variable:
def outer():
    count = 0
    def inner():
        count += 1  # UnboundLocalError!
    inner()

# ✅ FIX 1 — use nonlocal:
def outer():
    count = 0
    def inner():
        nonlocal count
        count += 1
    inner()

# ✅ FIX 2 — use mutable container:
def outer():
    count = [0]
    def inner():
        count[0] += 1  # mutating, not reassigning
    inner()
```

---

## 13. Complexity Cheat Sheet for Python Operations

### List

| Operation | Average | Worst |
|-----------|---------|-------|
| `append(x)` | O(1) | O(1) amortized |
| `pop()` (last) | O(1) | O(1) |
| `pop(0)` (first) | **O(N)** | O(N) |
| `insert(i, x)` | **O(N)** | O(N) |
| `x in list` | **O(N)** | O(N) |
| `list[i]` | O(1) | O(1) |
| `list[i:j]` | O(j-i) | O(j-i) |
| `sort()` | O(N log N) | O(N log N) |
| `len(list)` | O(1) | O(1) |
| `list.reverse()` | O(N) | O(N) |

### Dict

| Operation | Average | Worst |
|-----------|---------|-------|
| `d[key]` | O(1) | O(N) |
| `d[key] = val` | O(1) | O(N) |
| `key in d` | O(1) | O(N) |
| `del d[key]` | O(1) | O(N) |
| `len(d)` | O(1) | O(1) |
| `d.keys()` | O(1) | O(1) |
| Iteration | O(N) | O(N) |

### Set

| Operation | Average | Worst |
|-----------|---------|-------|
| `add(x)` | O(1) | O(N) |
| `x in set` | O(1) | O(N) |
| `remove(x)` | O(1) | O(N) |
| `union (\|)` | O(len(s)+len(t)) | — |
| `intersection (&)` | O(min(len(s),len(t))) | — |
| `difference (-)` | O(len(s)) | — |

### Deque

| Operation | Time |
|-----------|------|
| `append(x)` | O(1) |
| `appendleft(x)` | O(1) |
| `pop()` | O(1) |
| `popleft()` | O(1) |
| `deque[i]` | **O(N)** |
| `len(deque)` | O(1) |

### Heap (via `heapq`)

| Operation | Time |
|-----------|------|
| `heapify(list)` | O(N) |
| `heappush(heap, x)` | O(log N) |
| `heappop(heap)` | O(log N) |
| `heap[0]` (peek) | O(1) |
| `nsmallest(k, list)` | O(N + k log N) |

---

## 14. Learning Resources

### Data Structures

#### Heaps
- [Understanding what `Heap` is?](https://www.youtube.com/watch?v=hW8PrQrvMNc&list=PL_z_8CaSLPWdtY9W22VjnPxG30CXNZpI9&index=2)
- [How to use `Heap` in Python](https://www.youtube.com/watch?v=wGSQ486Y4sc)

---

*Keep this document updated as you discover new Python tricks during your DSA journey! 🚀*
