# 🧮 Bit Manipulation & Bitmasking — Complete Beginner to Expert Guide (Python)

## Table of Contents

- [1. What Are Bits?](#1-what-are-bits)
- [2. Number Systems](#2-number-systems)
- [3. Python Built-in Functions for Bits](#3-python-built-in-functions-for-bits)
  - [`bin(x)` — Convert integer to binary string](#binx--convert-integer-to-binary-string)
  - [`int(string, base)` — Convert binary string back to integer](#intstring-base--convert-binary-string-back-to-integer)
  - [`bit_count()` — Count the number of 1-bits (Python 3.10+)](#bit_count--count-the-number-of-1-bits-python-310)
  - [`bit_length()` — Number of bits needed to represent the number](#bit_length--number-of-bits-needed-to-represent-the-number)
  - [`bin(x).count('1')` — Alternative to bit\_count (works on all Python 3 versions)](#binxcount1--alternative-to-bit_count-works-on-all-python-3-versions)
- [4. Bitwise Operators — The Core Toolkit](#4-bitwise-operators--the-core-toolkit)
  - [4.1 AND (`&`) — Both bits must be 1](#41-and---both-bits-must-be-1)
  - [4.2 OR (`|`) — At least one bit must be 1](#42-or---at-least-one-bit-must-be-1)
  - [4.3 XOR (`^`) — Exactly one bit must be 1](#43-xor---exactly-one-bit-must-be-1)
  - [4.4 NOT (`~`) — Flip all bits](#44-not---flip-all-bits)
  - [4.5 Left Shift (`<<`) — Shift bits left, fill with 0s](#45-left-shift---shift-bits-left-fill-with-0s)
  - [4.6 Right Shift (`>>`) — Shift bits right, discard rightmost bits](#46-right-shift---shift-bits-right-discard-rightmost-bits)
  - [Quick Reference Table](#quick-reference-table)
- [5. Common Bit Manipulation Patterns](#5-common-bit-manipulation-patterns)
  - [Pattern 1: Check if the i-th bit is set](#pattern-1-check-if-the-i-th-bit-is-set)
  - [Pattern 2: Set the i-th bit (turn it ON)](#pattern-2-set-the-i-th-bit-turn-it-on)
  - [Pattern 3: Clear the i-th bit (turn it OFF)](#pattern-3-clear-the-i-th-bit-turn-it-off)
  - [Pattern 4: Toggle the i-th bit (flip it)](#pattern-4-toggle-the-i-th-bit-flip-it)
  - [Pattern 5: Check if a number is a power of 2](#pattern-5-check-if-a-number-is-a-power-of-2)
  - [Pattern 6: Count set bits (Kernighan's Algorithm)](#pattern-6-count-set-bits-kernighans-algorithm)
    - [🔬 Kernighan's Algorithm — Deep Dive](#-kernighans-algorithm--deep-dive)
      - [The Core Insight: What Does `n - 1` Do to Binary?](#the-core-insight-what-does-n---1-do-to-binary)
      - [Why `n & (n - 1)` Removes the Lowest Set Bit](#why-n--n---1-removes-the-lowest-set-bit)
      - [The Naive Approach vs Kernighan's](#the-naive-approach-vs-kernighans)
      - [Complete Visual Walkthrough](#complete-visual-walkthrough)
      - [Performance Comparison](#performance-comparison)
      - [Applications of `n & (n - 1)` Beyond Counting](#applications-of-n--n---1-beyond-counting)
      - [Complement: Isolating the Lowest Set Bit with `n & (-n)`](#complement-isolating-the-lowest-set-bit-with-n---n)
  - [Pattern 7: Get the lowest set bit](#pattern-7-get-the-lowest-set-bit)
  - [Pattern 8: Check if more than one bit is set](#pattern-8-check-if-more-than-one-bit-is-set)
- [6. Bitmasking — The Power Technique](#6-bitmasking--the-power-technique)
  - [What Is a Bitmask?](#what-is-a-bitmask)
  - [Why Use Bitmasks Instead of Sets/Arrays?](#why-use-bitmasks-instead-of-setsarrays)
  - [Real-World Analogy: File Permissions](#real-world-analogy-file-permissions)
  - [Bitmask as a Subset Representation](#bitmask-as-a-subset-representation)
  - [Enumerating All Subsets of a Bitmask](#enumerating-all-subsets-of-a-bitmask)
- [7. Solving "Two Out of Three" with Bitmasks](#7-solving-two-out-of-three-with-bitmasks)
  - [Problem](#problem)
  - [Bitmask Approach](#bitmask-approach)
  - [Step-by-Step Dry Run](#step-by-step-dry-run)
  - [Why `m & (m - 1)` Works for "At Least 2 Bits"](#why-m--m---1-works-for-at-least-2-bits)
  - [Generalizing to K Arrays](#generalizing-to-k-arrays)
- [8. Practice Problems — Curated LeetCode List](#8-practice-problems--curated-leetcode-list)
  - [🟢 Easy — Build Your Foundation](#-easy--build-your-foundation)
  - [🟡 Medium — Apply Bitmasking Patterns](#-medium--apply-bitmasking-patterns)
  - [🔴 Hard — Master Bitmask DP](#-hard--master-bitmask-dp)
  - [📋 Suggested Study Order](#-suggested-study-order)
- [Appendix: Cheat Sheet](#appendix-cheat-sheet)
- [9. When to Use Bitmasking — Pattern Recognition Guide](#9-when-to-use-bitmasking--pattern-recognition-guide)
  - [Pattern A: "Track Presence Across Multiple Groups"](#pattern-a-track-presence-across-multiple-groups)
  - [Pattern B: "Enumerate All Subsets of a Small Set"](#pattern-b-enumerate-all-subsets-of-a-small-set)
  - [Pattern C: "DP Where State = Which Items Are Used/Visited"](#pattern-c-dp-where-state--which-items-are-usedvisited)
  - [Pattern D: "Find the Unique / Missing / Duplicate Element"](#pattern-d-find-the-unique--missing--duplicate-element)
  - [Pattern E: "Check If Two Sets Share Any Elements (Quickly)"](#pattern-e-check-if-two-sets-share-any-elements-quickly)
  - [Pattern F: "Toggle / Flip States"](#pattern-f-toggle--flip-states)
  - [Pattern G: "Represent Constraints / Requirements as Bits"](#pattern-g-represent-constraints--requirements-as-bits)
  - [🧠 The Decision Flowchart](#-the-decision-flowchart)
  - [🚩 Red Flags — When NOT to Use Bitmasks](#-red-flags--when-not-to-use-bitmasks)
  - [💡 Quick "Smell Test" Questions](#-quick-smell-test-questions)
- [Python-Specific Interview Notes](#python-specific-interview-notes)


---

## 1. What Are Bits?

A **bit** (binary digit) is the smallest unit of data in computing. It can be either `0` or `1`.

Every integer in your program is stored as a sequence of bits in memory:

```
Decimal 5  →  Binary 101
Decimal 13 →  Binary 1101
Decimal 0  →  Binary 0
Decimal 1  →  Binary 1
```

**How to read binary (right to left):**

```
Binary:  1   1   0   1
         ↓   ↓   ↓   ↓
Power:   2³  2²  2¹  2⁰
Value:   8   4   0   1   →  8 + 4 + 0 + 1 = 13
```

Each position represents a power of 2. If the bit is `1`, that power is "on"; if `0`, it's "off".

---

## 2. Number Systems

| System | Base | Digits | Python Prefix | Example |
|--------|------|--------|---------------|---------|
| Binary | 2 | 0, 1 | `0b` | `0b1101` = 13 |
| Octal | 8 | 0-7 | `0o` | `0o15` = 13 |
| Decimal | 10 | 0-9 | (none) | `13` |
| Hexadecimal | 16 | 0-9, A-F | `0x` | `0xD` = 13 |

```python
# You can write integers in any base in Python:
a = 13       # decimal
b = 0b1101   # binary
c = 0xD      # hex

print(a == b == c)  # True — they're all the same number!
```

---

## 3. Python Built-in Functions for Bits

### `bin(x)` — Convert integer to binary string

```python
>>> bin(13)
'0b1101'

>>> bin(5)
'0b101'

>>> bin(0)
'0b0'

>>> bin(-5)
'-0b101'
```

Returns a **string** prefixed with `'0b'`. The number itself doesn't change — this is just a way to *view* it.

### `int(string, base)` — Convert binary string back to integer

```python
>>> int('1101', 2)
13

>>> int('0b1101', 2)
13

>>> int('FF', 16)
255
```

### `bit_count()` — Count the number of 1-bits (Python 3.10+)

```python
>>> (13).bit_count()
3  # binary 1101 has three 1s

>>> (7).bit_count()
3  # binary 111 has three 1s

>>> (8).bit_count()
1  # binary 1000 has one 1
```

### `bit_length()` — Number of bits needed to represent the number

```python
>>> (13).bit_length()
4  # 1101 needs 4 bits

>>> (1).bit_length()
1

>>> (0).bit_length()
0
```

### `bin(x).count('1')` — Alternative to bit_count (works on all Python 3 versions)

```python
>>> bin(13).count('1')
3
```

---

## 4. Bitwise Operators — The Core Toolkit

These operators work on the **individual bits** of integers.

### 4.1 AND (`&`) — Both bits must be 1

```
  1101  (13)
& 1010  (10)
------
  1000  (8)
```

```python
>>> 13 & 10
8
```

**Use case:** Check if a specific bit is set, clear bits.

**Mental model:** AND is like a **filter** — only lets through positions where BOTH inputs are 1.

### 4.2 OR (`|`) — At least one bit must be 1

```
  1101  (13)
| 1010  (10)
------
  1111  (15)
```

```python
>>> 13 | 10
15
```

**Use case:** Set (turn on) specific bits, combine flags.

**Mental model:** OR is like **paint** — once a bit is 1, it stays 1.

**Key property: OR is idempotent** — `x | x = x`. Doing it twice doesn't change anything. This is why bitmasks handle duplicates for free!

### 4.3 XOR (`^`) — Exactly one bit must be 1

```
  1101  (13)
^ 1010  (10)
------
  0111  (7)
```

```python
>>> 13 ^ 10
7
```

**Use case:** Toggle bits, find unique elements, swap without temp variable.

**Key properties:**
- `x ^ x = 0` (anything XOR itself is 0)
- `x ^ 0 = x` (anything XOR 0 is itself)
- XOR is commutative and associative

### 4.4 NOT (`~`) — Flip all bits

```python
>>> ~13
-14

>>> ~0
-1
```

In Python, `~x` equals `-(x + 1)` due to two's complement representation.

**Use case:** Invert a mask, compute complements.

### 4.5 Left Shift (`<<`) — Shift bits left, fill with 0s

```
  0001  (1)
<< 3
------
  1000  (8)
```

```python
>>> 1 << 3
8  # equivalent to 1 * 2³

>>> 5 << 1
10  # equivalent to 5 * 2¹
```

**Key insight:** `x << n` is equivalent to `x * 2ⁿ`.

**Use case:** Create bitmasks (`1 << i` creates a mask with only bit `i` set).

### 4.6 Right Shift (`>>`) — Shift bits right, discard rightmost bits

```
  1101  (13)
>> 1
------
  0110  (6)
```

```python
>>> 13 >> 1
6  # equivalent to 13 // 2

>>> 13 >> 2
3  # equivalent to 13 // 4
```

**Key insight:** `x >> n` is equivalent to `x // 2ⁿ` (integer division).

---

### Quick Reference Table

| Operator | Symbol | Example | Result | Use Case |
|----------|--------|---------|--------|----------|
| AND | `&` | `13 & 10` | `8` | Check/clear bits |
| OR | `\|` | `13 \| 10` | `15` | Set bits, combine flags |
| XOR | `^` | `13 ^ 10` | `7` | Toggle bits, find unique |
| NOT | `~` | `~13` | `-14` | Invert mask |
| Left Shift | `<<` | `1 << 3` | `8` | Create masks, multiply by 2ⁿ |
| Right Shift | `>>` | `13 >> 1` | `6` | Extract bits, divide by 2ⁿ |

---

## 5. Common Bit Manipulation Patterns

### Pattern 1: Check if the i-th bit is set

```python
def is_bit_set(num, i):
    return (num & (1 << i)) != 0

# Example:
# num = 13 = 1101
# Is bit 2 set?  1101 & 0100 = 0100 ≠ 0 → True ✅
# Is bit 1 set?  1101 & 0010 = 0000 = 0 → False ✅
```

### Pattern 2: Set the i-th bit (turn it ON)

```python
def set_bit(num, i):
    return num | (1 << i)

# Example:
# num = 9 = 1001
# Set bit 1:  1001 | 0010 = 1011 = 11
```

### Pattern 3: Clear the i-th bit (turn it OFF)

```python
def clear_bit(num, i):
    return num & ~(1 << i)

# Example:
# num = 13 = 1101
# Clear bit 2:  1101 & ~(0100) = 1101 & 1011 = 1001 = 9
```

### Pattern 4: Toggle the i-th bit (flip it)

```python
def toggle_bit(num, i):
    return num ^ (1 << i)

# Example:
# num = 13 = 1101
# Toggle bit 1:  1101 ^ 0010 = 1111 = 15
# Toggle bit 1 again:  1111 ^ 0010 = 1101 = 13 (back to original!)
```

### Pattern 5: Check if a number is a power of 2

```python
def is_power_of_two(n):
    return n > 0 and (n & (n - 1)) == 0

# Why? Powers of 2 have exactly one bit set:
# 8 = 1000, 8-1 = 0111 → 1000 & 0111 = 0000 ✅
# 6 = 0110, 6-1 = 0101 → 0110 & 0101 = 0100 ≠ 0 ❌
```

### Pattern 6: Count set bits (Kernighan's Algorithm)

```python
def count_bits(n):
    count = 0
    while n:
        n &= (n - 1)  # removes the lowest set bit
        count += 1
    return count

# Example: n = 13 = 1101
# Iteration 1: 1101 & 1100 = 1100, count = 1
# Iteration 2: 1100 & 1011 = 1000, count = 2
# Iteration 3: 1000 & 0111 = 0000, count = 3
# Loop ends. Answer: 3 ✅
```

#### 🔬 Kernighan's Algorithm — Deep Dive

Named after **Brian Kernighan** (co-author of *"The C Programming Language"* with Dennis Ritchie), this algorithm exploits a single insight that makes bit counting dramatically faster for sparse numbers.

##### The Core Insight: What Does `n - 1` Do to Binary?

When you subtract 1 from a binary number, something very specific happens:

```
Rule: n - 1 flips the lowest set bit to 0, and all zeros below it to 1.

n     = ...1 0 0 0    (some prefix, then a 1, then zeros)
n - 1 = ...0 1 1 1    (prefix unchanged, the 1 becomes 0, zeros become 1)
```

**Why?** Subtraction borrows from the lowest `1`. That `1` becomes `0`, and all the `0`s below it (which couldn't lend) become `1` from the borrow chain. Everything above the lowest `1` is untouched.

**Concrete examples:**

```
n = 12 = 1100        n = 40 = 101000        n = 7 = 0111
n-1=11 = 1011        n-1=39 = 100111        n-1=6 = 0110
          ^^^                  ^^^^^^                  ^
         changed               changed                changed
```

##### Why `n & (n - 1)` Removes the Lowest Set Bit

When you AND `n` with `n - 1`:
- Bits **above** the lowest set bit: unchanged in both → AND preserves them
- The lowest set bit itself: `1` in `n`, `0` in `n-1` → AND gives `0` (removed!)
- Bits **below** the lowest set bit: `0` in `n`, `1` in `n-1` → AND gives `0`

```
n     = 1 0 1 1 0 0    (44)
n - 1 = 1 0 1 0 1 1    (43)
n&(n-1)= 1 0 1 0 0 0    (40)  ← lowest set bit (position 2) is gone!
```

##### The Naive Approach vs Kernighan's

**Naive — check every bit position:**

```python
def count_bits_naive(n):
    count = 0
    while n:
        count += n & 1   # check if last bit is 1
        n >>= 1          # shift right (remove last bit)
    return count
```

**Time:** O(log N) — always checks every bit, even if most are 0.

For `n = 128 = 10000000`, naive does **8 iterations** to find just **1** set bit.

**Kernighan's — skip directly to set bits:**

```python
def count_bits_kernighan(n):
    count = 0
    while n:
        n &= (n - 1)  # jump to next set bit
        count += 1
    return count
```

**Time:** O(K) where K = number of set bits. For `n = 128`, just **1 iteration**!

##### Complete Visual Walkthrough

```
n = 52 = 110100  (3 set bits)

Step 1:
  n     = 110100
  n - 1 = 110011
  n & (n-1) = 110000  ← removed bit at position 2
  count = 1

Step 2:
  n     = 110000
  n - 1 = 101111
  n & (n-1) = 100000  ← removed bit at position 4
  count = 2

Step 3:
  n     = 100000
  n - 1 = 011111
  n & (n-1) = 000000  ← removed bit at position 5
  count = 3

n = 0, loop ends. Answer: 3 ✅
(Only 3 iterations for 3 set bits, not 6 iterations for 6 bit positions!)
```

##### Performance Comparison

| Number | Binary | Total bits | Set bits (K) | Naive iterations | Kernighan iterations |
|--------|--------|-----------|-------------|-----------------|---------------------|
| 128 | 10000000 | 8 | 1 | 8 | **1** |
| 255 | 11111111 | 8 | 8 | 8 | 8 |
| 1024 | 10000000000 | 11 | 1 | 11 | **1** |
| 13 | 1101 | 4 | 3 | 4 | **3** |
| 4096 | 1000000000000 | 13 | 1 | 13 | **1** |

Kernighan's is **never worse** than naive, and **dramatically better** for sparse numbers (few set bits).

##### Applications of `n & (n - 1)` Beyond Counting

| Pattern | Code | What It Does |
|---------|------|-------------|
| Count set bits | `while n: n &= n-1; count += 1` | Kernighan's algorithm |
| Is power of 2? | `n > 0 and (n & (n-1)) == 0` | Powers of 2 have exactly 1 bit |
| More than 1 bit set? | `(n & (n-1)) != 0` | Used in "Two Out of Three" bitmask solution |
| Remove lowest set bit | `n &= (n-1)` | Useful in subset enumeration |
| Round down to nearest power of 2 | Keep removing lowest bit until only one remains | `while n & (n-1): n &= n-1` |

##### Complement: Isolating the Lowest Set Bit with `n & (-n)`

While Kernighan's *removes* the lowest set bit, `n & (-n)` *isolates* it:

```python
lowest_bit = n & (-n)
```

**How it works (two's complement):**

```
n    = 0110 1100  (108)
-n   = 1001 0100  (two's complement: flip all bits, add 1)

n & (-n) = 0000 0100  (4) ← only the lowest set bit survives!
```

**Why?** In two's complement, `-n = ~n + 1`. Flipping all bits and adding 1 causes a carry chain that stops at the lowest set bit — making it the only position where both `n` and `-n` have a `1`.

**Used in:**
- **Fenwick Trees (Binary Indexed Trees)** — `i += i & (-i)` to traverse the tree
- **LeetCode 260 (Single Number III)** — split two unique numbers by their differing bit
- **Finding the rightmost differing bit** between two numbers: `(a ^ b) & -(a ^ b)`

### Pattern 7: Get the lowest set bit

```python
def lowest_set_bit(n):
    return n & (-n)

# Example: n = 12 = 1100
# -12 in two's complement = ...0100
# 1100 & 0100 = 0100 = 4 (bit position 2)
```

### Pattern 8: Check if more than one bit is set

```python
def more_than_one_bit(n):
    return n & (n - 1) != 0

# This is the "at least 2 arrays" check in our bitmask approach!
# 011 & 010 = 010 ≠ 0 → True (2 bits set)
# 001 & 000 = 000 = 0 → False (only 1 bit set)
```

---

## 6. Bitmasking — The Power Technique

### What Is a Bitmask?

A **bitmask** is an integer used to represent a **set of boolean flags**. Each bit position represents a different flag/element/property.

```
Bitmask: 1 0 1 1 = 11
         ↓ ↓ ↓ ↓
Bit 3:   ✅ (element 3 is present)
Bit 2:   ❌ (element 2 is absent)
Bit 1:   ✅ (element 1 is present)
Bit 0:   ✅ (element 0 is present)
```

### Why Use Bitmasks Instead of Sets/Arrays?

| Feature | Set/Array | Bitmask |
|---------|-----------|---------|
| Storage | O(N) | O(1) — single integer |
| Union | O(N) | O(1) — just `\|` |
| Intersection | O(N) | O(1) — just `&` |
| Membership check | O(1) avg for set | O(1) — just `&` |
| Iteration over subsets | Straightforward | Requires bit tricks |
| Max elements | Unlimited | 32 or 64 (int size) |

### Real-World Analogy: File Permissions

```
Unix permissions: rwx = 111 = 7
                  r-x = 101 = 5
                  r-- = 100 = 4

# Check if write permission is set:
permissions = 0b101  # r-x
has_write = permissions & 0b010  # 0 → No write permission
```

### Bitmask as a Subset Representation

If you have a set `{A, B, C, D}`, every subset can be represented by a 4-bit mask:

```
0000 = {}           (empty set)
0001 = {A}
0010 = {B}
0011 = {A, B}
0100 = {C}
0101 = {A, C}
0110 = {B, C}
0111 = {A, B, C}
1000 = {D}
...
1111 = {A, B, C, D} (full set)
```

Total subsets = 2⁴ = 16. This is why bitmask DP iterates from `0` to `2ⁿ - 1`.

### Enumerating All Subsets of a Bitmask

```python
def enumerate_subsets(mask):
    """Enumerate all subsets of a given bitmask."""
    subsets = []
    sub = mask
    while sub:
        subsets.append(sub)
        sub = (sub - 1) & mask  # magic line!
    subsets.append(0)  # empty subset
    return subsets

# Example: mask = 0b1010 (elements {1, 3})
# Subsets: 1010, 1000, 0010, 0000
# i.e., {1,3}, {3}, {1}, {}
```

---

## 7. Solving "Two Out of Three" with Bitmasks

Now let's apply everything to the problem that started this journey.

### Problem

Given 3 arrays, return values present in **at least 2** of them.

### Bitmask Approach

For each value, maintain a bitmask indicating which arrays contain it:

```
Bit 0 (value 1) → nums1
Bit 1 (value 2) → nums2
Bit 2 (value 4) → nums3
```

```python
from collections import defaultdict
from typing import List

class Solution:
    def twoOutOfThree(self, nums1: List[int], nums2: List[int], nums3: List[int]) -> List[int]:
        mask = defaultdict(int)
        
        # For each array, OR the corresponding bit for each value
        for i, arr in enumerate([nums1, nums2, nums3]):
            for val in arr:
                mask[val] |= (1 << i)
        
        # Return values where at least 2 bits are set
        return [val for val, m in mask.items() if m & (m - 1)]
```

### Step-by-Step Dry Run

```
Input: nums1 = [1,1,3,2], nums2 = [2,3], nums3 = [3]

Processing nums1 (i=0, bit = 1 << 0 = 1 = 0b001):
  mask[1] = 000 | 001 = 001
  mask[1] = 001 | 001 = 001  (duplicate — OR is idempotent, no change!)
  mask[3] = 000 | 001 = 001
  mask[2] = 000 | 001 = 001

Processing nums2 (i=1, bit = 1 << 1 = 2 = 0b010):
  mask[2] = 001 | 010 = 011  ← 2 is now in arrays 0 and 1
  mask[3] = 001 | 010 = 011  ← 3 is now in arrays 0 and 1

Processing nums3 (i=2, bit = 1 << 2 = 4 = 0b100):
  mask[3] = 011 | 100 = 111  ← 3 is now in all three arrays

Final masks:
  mask[1] = 001 → 001 & 000 = 0 → only 1 bit → skip
  mask[3] = 111 → 111 & 110 = 110 ≠ 0 → multiple bits → ✅ include
  mask[2] = 011 → 011 & 010 = 010 ≠ 0 → multiple bits → ✅ include

Result: [3, 2] ✅
```

### Why `m & (m - 1)` Works for "At Least 2 Bits"

```
m = 001 (1 bit):   001 & 000 = 000 = 0 → False (only 1 array)
m = 010 (1 bit):   010 & 001 = 000 = 0 → False (only 1 array)
m = 011 (2 bits):  011 & 010 = 010 ≠ 0 → True (2 arrays) ✅
m = 100 (1 bit):   100 & 011 = 000 = 0 → False (only 1 array)
m = 101 (2 bits):  101 & 100 = 100 ≠ 0 → True (2 arrays) ✅
m = 110 (2 bits):  110 & 101 = 100 ≠ 0 → True (2 arrays) ✅
m = 111 (3 bits):  111 & 110 = 110 ≠ 0 → True (3 arrays) ✅
```

`n & (n-1)` removes the lowest set bit. If the result is non-zero, there was more than one bit set.

### Generalizing to K Arrays

The beauty of this approach: **nothing changes** for K arrays!

```python
class Solution:
    def valuesInKOrMoreArrays(self, arrays: List[List[int]], k: int) -> List[int]:
        mask = defaultdict(int)
        
        for i, arr in enumerate(arrays):
            for val in arr:
                mask[val] |= (1 << i)
        
        return [val for val, m in mask.items() if bin(m).count('1') >= k]
```

Works for any K ≤ number of arrays (up to 64 arrays with a 64-bit integer, or unlimited in Python since Python integers have arbitrary precision).

---

## 8. Practice Problems — Curated LeetCode List

### 🟢 Easy — Build Your Foundation

| # | Problem | Key Concept | Why Practice This |
|---|---------|-------------|-------------------|
| 1 | [191. Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/) | Kernighan's algorithm, `n & (n-1)` | The most fundamental bit counting technique |
| 2 | [231. Power of Two](https://leetcode.com/problems/power-of-two/) | `n & (n-1) == 0` | Understand single-bit numbers |
| 3 | [136. Single Number](https://leetcode.com/problems/single-number/) | XOR all elements | The classic "XOR cancels duplicates" trick |
| 4 | [190. Reverse Bits](https://leetcode.com/problems/reverse-bits/) | Shift and build | Practice bit-by-bit construction |
| 5 | [338. Counting Bits](https://leetcode.com/problems/counting-bits/) | DP + bit manipulation | Combine DP with `i & (i-1)` |
| 6 | [461. Hamming Distance](https://leetcode.com/problems/hamming-distance/) | XOR + count bits | XOR highlights differences |
| 7 | [2032. Two Out of Three](https://leetcode.com/problems/two-out-of-three/) | Bitmask per value | The problem that started this guide! |

### 🟡 Medium — Apply Bitmasking Patterns

| # | Problem | Key Concept | Why Practice This |
|---|---------|-------------|-------------------|
| 8 | [137. Single Number II](https://leetcode.com/problems/single-number-ii/) | Bit counting per position | Generalize XOR trick to "appears 3 times" |
| 9 | [260. Single Number III](https://leetcode.com/problems/single-number-iii/) | XOR + lowest set bit to split | Two unique numbers — split using bit trick |
| 10 | [78. Subsets](https://leetcode.com/problems/subsets/) | Bitmask enumeration | Enumerate all 2ⁿ subsets using masks 0 to 2ⁿ-1 |
| 11 | [784. Letter Case Permutation](https://leetcode.com/problems/letter-case-permutation/) | Bitmask for choices | Each letter = 1 bit of choice (upper/lower) |
| 12 | [318. Maximum Product of Word Lengths](https://leetcode.com/problems/maximum-product-of-word-lengths/) | Bitmask per word (26 bits for a-z) | Check if two words share letters with `&` |
| 13 | [1239. Maximum Length of a Concatenated String with Unique Characters](https://leetcode.com/problems/maximum-length-of-a-concatenated-string-with-unique-characters/) | Bitmask + backtracking | Combine bitmask subset checks with DFS |
| 14 | [201. Bitwise AND of Numbers Range](https://leetcode.com/problems/bitwise-and-of-numbers-range/) | Common prefix of bits | Understand how AND behaves over ranges |
| 15 | [477. Total Hamming Distance](https://leetcode.com/problems/total-hamming-distance/) | Bit-by-bit counting | Count contributions per bit position |

### 🔴 Hard — Master Bitmask DP

| # | Problem | Key Concept | Why Practice This |
|---|---------|-------------|-------------------|
| 16 | [847. Shortest Path Visiting All Nodes](https://leetcode.com/problems/shortest-path-visiting-all-nodes/) | BFS + bitmask state | Bitmask represents visited nodes in graph |
| 17 | [1125. Smallest Sufficient Team](https://leetcode.com/problems/smallest-sufficient-team/) | Bitmask DP (set cover) | Classic bitmask DP — skills as bits |
| 18 | [943. Find the Shortest Superstring](https://leetcode.com/problems/find-the-shortest-superstring/) | Bitmask DP (TSP variant) | Traveling salesman with bitmask states |
| 19 | [1494. Parallel Courses II](https://leetcode.com/problems/parallel-courses-ii/) | Bitmask DP + subset enumeration | Enumerate subsets of available courses |
| 20 | [691. Stickers to Spell Word](https://leetcode.com/problems/stickers-to-spell-word/) | Bitmask DP | Target letters as bitmask state |

### 📋 Suggested Study Order

```
Week 1: Easy problems (1-7)
  → Goal: Master basic operators, counting bits, XOR trick
  
Week 2: Medium problems (8-11)
  → Goal: Understand bitmask as subset, XOR generalizations
  
Week 3: Medium problems (12-15)
  → Goal: Apply bitmasks to string/array problems
  
Week 4: Hard problems (16-17)
  → Goal: Bitmask DP fundamentals
  
Week 5: Hard problems (18-20)
  → Goal: Advanced bitmask DP, subset enumeration
```

---

## Appendix: Cheat Sheet

```python
# ============================================
# BIT MANIPULATION CHEAT SHEET (Python)
# ============================================

# --- Basic Operations ---
x & y          # AND: both bits 1
x | y          # OR: at least one bit 1
x ^ y          # XOR: exactly one bit 1
~x             # NOT: flip all bits (equals -(x+1))
x << n         # Left shift: multiply by 2^n
x >> n         # Right shift: divide by 2^n (floor)

# --- Common Tricks ---
x & 1          # Check if odd (last bit)
x & (x - 1)   # Remove lowest set bit
x & (-x)       # Isolate lowest set bit
x | (x - 1)    # Set all bits below lowest set bit

# --- Checks ---
x & (x - 1) == 0 and x > 0   # Is power of 2?
x & (x - 1) != 0              # More than 1 bit set?

# --- Bit at position i ---
(x >> i) & 1       # Get bit i
x | (1 << i)       # Set bit i
x & ~(1 << i)      # Clear bit i
x ^ (1 << i)       # Toggle bit i

# --- Counting ---
bin(x).count('1')   # Count set bits (simple)
x.bit_count()       # Count set bits (Python 3.10+)
x.bit_length()      # Number of bits needed

# --- Masks ---
(1 << n) - 1       # Mask of n ones: 0...01111 (n=4 → 15)
~((1 << n) - 1)    # Mask of n zeros at bottom

# --- Subset Enumeration ---
# Enumerate all subsets of mask:
sub = mask
while sub:
    # process sub
    sub = (sub - 1) & mask
# don't forget to process sub = 0 (empty set)
```

---

## 9. When to Use Bitmasking — Pattern Recognition Guide

This is the most important section for interviews. Knowing *how* bitmasks work is useless if you can't recognize *when* to use them. Below are **7 concrete patterns** with trigger phrases, mental models, and real problem examples.

---

### Pattern A: "Track Presence Across Multiple Groups"

**🔍 Trigger phrases in the problem:**
- "appears in at least K of the arrays/lists/groups"
- "common to two or more collections"
- "which elements are shared between..."

**💡 Mental model:** Each group gets a bit position. For each value, OR in the group's bit. At the end, count bits.

**🧩 Why bitmask beats alternatives:**
- A hashmap of sets works but uses more memory and code
- OR is idempotent — duplicates within a group are handled for free
- Generalizes from 3 groups to K groups with zero code changes

**📝 Example problems:**
- [2032. Two Out of Three](https://leetcode.com/problems/two-out-of-three/) — the problem we solved
- [2duplicates across K sorted arrays] — common interview variant

**🚨 When NOT to use this pattern:**
- When groups are very large (thousands) — bitmask integers become huge
- When you need to know *how many times* a value appears (bitmask only tracks presence, not count)

```python
# Template:
mask = defaultdict(int)
for i, group in enumerate(groups):
    for val in group:
        mask[val] |= (1 << i)
result = [val for val, m in mask.items() if bin(m).count('1') >= k]
```

---

### Pattern B: "Enumerate All Subsets of a Small Set"

**🔍 Trigger phrases in the problem:**
- "all possible subsets / combinations"
- "try every possible selection"
- "N ≤ 20" (or sometimes N ≤ 15, N ≤ 23)
- "minimize/maximize over all subsets"

**💡 Mental model:** If you have N elements, there are 2ᴺ subsets. Each subset is a number from `0` to `2ᴺ - 1`. Bit `i` being set means "element i is included."

**🧩 Why bitmask beats alternatives:**
- Iterating `for mask in range(1 << n)` is cleaner than recursive backtracking
- Subset operations (union, intersection, complement) are single operations
- Easy to memoize — the mask IS the state (an integer, perfect as a dict key or array index)

**📝 Example problems:**
- [78. Subsets](https://leetcode.com/problems/subsets/) — enumerate all subsets
- [90. Subsets II](https://leetcode.com/problems/subsets-ii/) — with duplicates
- [784. Letter Case Permutation](https://leetcode.com/problems/letter-case-permutation/) — each letter = 1 bit of choice

**🚨 The N ≤ 20 rule:**
- 2²⁰ = ~1 million — manageable
- 2²⁵ = ~33 million — borderline
- 2³⁰ = ~1 billion — too slow
- If N > 20, bitmask enumeration is almost certainly not the intended approach

```python
# Template: enumerate all subsets of {0, 1, ..., n-1}
n = len(elements)
for mask in range(1 << n):
    subset = []
    for i in range(n):
        if mask & (1 << i):
            subset.append(elements[i])
    # process subset
```

---

### Pattern C: "DP Where State = Which Items Are Used/Visited"

**🔍 Trigger phrases in the problem:**
- "visit all nodes" / "use all items"
- "minimum cost to cover all..."
- "assign N people to N tasks"
- "traveling salesman" / "Hamiltonian path"
- N ≤ 20 with exponential-looking state space

**💡 Mental model:** The DP state includes a bitmask representing "which items have been used so far." Transitions flip one bit (use one more item).

**🧩 Why bitmask beats alternatives:**
- Without bitmask: state space is N! (factorial) — impossible
- With bitmask: state space is N × 2ᴺ — feasible for N ≤ 20
- The mask compresses "which subset is used" into a single integer

**📝 Example problems:**
- [847. Shortest Path Visiting All Nodes](https://leetcode.com/problems/shortest-path-visiting-all-nodes/) — BFS with `(node, visited_mask)` state
- [943. Find the Shortest Superstring](https://leetcode.com/problems/find-the-shortest-superstring/) — TSP variant
- [1125. Smallest Sufficient Team](https://leetcode.com/problems/smallest-sufficient-team/) — set cover with bitmask DP
- [1494. Parallel Courses II](https://leetcode.com/problems/parallel-courses-ii/) — scheduling with bitmask

**🚨 Red flag that you need bitmask DP:**
- The brute force is O(N!) but N is small (≤ 15-20)
- You need to track "which items are done" but the ORDER within the done set doesn't matter
- The problem feels like assignment/matching/covering

```python
# Template: Bitmask DP
# dp[mask] = best answer using the subset represented by mask
dp = [float('inf')] * (1 << n)
dp[0] = 0  # base case: empty set

for mask in range(1 << n):
    for i in range(n):
        if not (mask & (1 << i)):  # if item i is not yet used
            new_mask = mask | (1 << i)
            dp[new_mask] = min(dp[new_mask], dp[mask] + cost(mask, i))

answer = dp[(1 << n) - 1]  # all items used
```

---

### Pattern D: "Find the Unique / Missing / Duplicate Element"

**🔍 Trigger phrases in the problem:**
- "every element appears twice except one"
- "find the single/unique number"
- "find the missing number"
- "appears K times except one appears once"

**💡 Mental model:** XOR cancels pairs. If every number appears twice, XOR-ing all of them gives 0. The one that appears once survives.

**🧩 Why XOR is magical here:**
- `a ^ a = 0` — pairs cancel
- `a ^ 0 = a` — identity
- Commutative and associative — order doesn't matter
- O(1) space, O(N) time — can't beat it

**📝 Example problems:**
- [136. Single Number](https://leetcode.com/problems/single-number/) — one unique, rest appear twice
- [137. Single Number II](https://leetcode.com/problems/single-number-ii/) — rest appear 3 times (need bit counting per position)
- [260. Single Number III](https://leetcode.com/problems/single-number-iii/) — two unique numbers (XOR + split by lowest set bit)
- [268. Missing Number](https://leetcode.com/problems/missing-number/) — XOR with indices

**🚨 Limitations:**
- Only works cleanly when duplicates appear an even number of times (for basic XOR)
- For "appears 3 times" variants, you need per-bit counting (modular arithmetic on each bit position)

```python
# Template: Find single number
result = 0
for num in nums:
    result ^= num
# result is the unique number
```

---

### Pattern E: "Check If Two Sets Share Any Elements (Quickly)"

**🔍 Trigger phrases in the problem:**
- "no common characters/elements"
- "disjoint sets"
- "maximum product of sizes where sets don't overlap"
- Alphabet-based problems (26 letters = 26 bits)

**💡 Mental model:** Represent each set as a bitmask. Two sets are disjoint if `mask_a & mask_b == 0`.

**🧩 Why bitmask beats alternatives:**
- Checking disjointness of two sets: O(min(|A|, |B|)) with sets, O(1) with bitmasks
- When comparing all pairs of N sets: O(N² × avg_size) with sets, O(N²) with bitmasks
- For 26 lowercase letters, the mask fits in a single 32-bit integer

**📝 Example problems:**
- [318. Maximum Product of Word Lengths](https://leetcode.com/problems/maximum-product-of-word-lengths/) — 26-bit mask per word, check `&` for overlap
- [1239. Maximum Length of Concatenated String with Unique Characters](https://leetcode.com/problems/maximum-length-of-a-concatenated-string-with-unique-characters/) — combine strings only if masks don't overlap

```python
# Template: Represent a word as a bitmask of its characters
def word_to_mask(word):
    mask = 0
    for ch in word:
        mask |= (1 << (ord(ch) - ord('a')))
    return mask

# Check if two words share any letters:
if word_mask_a & word_mask_b == 0:
    # disjoint — no common letters
```

---

### Pattern F: "Toggle / Flip States"

**🔍 Trigger phrases in the problem:**
- "flip", "toggle", "switch"
- "light switches" / "on-off states"
- "applying an operation twice undoes it"

**💡 Mental model:** XOR is the toggle operator. `x ^ 1 = flip`, `x ^ 0 = keep`. Applying XOR twice returns to original state.

**🧩 Why XOR is natural here:**
- Toggling bit i: `state ^= (1 << i)`
- Toggling is self-inverse: toggle twice = no change
- Can represent N binary states in a single integer

**📝 Example problems:**
- [1284. Minimum Number of Flips to Convert Binary Matrix to Zero Matrix](https://leetcode.com/problems/minimum-number-of-flips-to-convert-binary-matrix-to-zero-matrix/) — BFS over bitmask states
- [1611. Minimum One Bit Operations to Make Integers Zero](https://leetcode.com/problems/minimum-one-bit-operations-to-make-integers-zero/) — Gray code + bit manipulation

```python
# Template: Toggle bit i in a state
state ^= (1 << i)  # flip the i-th element's on/off status
```

---

### Pattern G: "Represent Constraints / Requirements as Bits"

**🔍 Trigger phrases in the problem:**
- "required skills" / "required items"
- "cover all requirements"
- "minimum team/set that satisfies all conditions"
- Each requirement is binary (met or not met)

**💡 Mental model:** Each requirement is a bit. A person/item "covers" some bits. Goal: find minimum items whose OR covers all bits.

**🧩 Why bitmask is perfect:**
- Union of covered requirements = OR of masks
- "All requirements met" = `covered_mask == (1 << num_requirements) - 1`
- Natural fit for DP: `dp[covered_mask] = min items needed`

**📝 Example problems:**
- [1125. Smallest Sufficient Team](https://leetcode.com/problems/smallest-sufficient-team/) — skills as bits, people cover subsets
- [691. Stickers to Spell Word](https://leetcode.com/problems/stickers-to-spell-word/) — target letters as bits

```python
# Template: Set cover with bitmask DP
target = (1 << num_requirements) - 1  # all bits set
dp = {0: []}  # mask → minimum items to achieve this mask

for item_mask in item_masks:
    for covered, items in list(dp.items()):
        new_covered = covered | item_mask
        if new_covered not in dp or len(dp[new_covered]) > len(items) + 1:
            dp[new_covered] = items + [item]

answer = dp[target]
```

---

### 🧠 The Decision Flowchart

Use this mental flowchart when reading a problem:

```
START: Read the problem
  │
  ├─ Does it involve SUBSETS of a small set (N ≤ 20)?
  │   └─ YES → Pattern B (enumerate) or Pattern C (bitmask DP)
  │
  ├─ Does it ask about PRESENCE across multiple groups?
  │   └─ YES → Pattern A (OR bitmask per value)
  │
  ├─ Does it ask for a UNIQUE/MISSING element with duplicates?
  │   └─ YES → Pattern D (XOR trick)
  │
  ├─ Does it check if sets are DISJOINT / share elements?
  │   └─ YES → Pattern E (AND to check overlap)
  │
  ├─ Does it involve TOGGLING / FLIPPING states?
  │   └─ YES → Pattern F (XOR toggle)
  │
  ├─ Does it involve COVERING REQUIREMENTS with items?
  │   └─ YES → Pattern G (set cover bitmask DP)
  │
  └─ None of the above?
      └─ Bitmask is probably not the right tool.
          Consider: hashmap, sorting, two pointers, sliding window, etc.
```

### 🚩 Red Flags — When NOT to Use Bitmasks

| Red Flag | Why | Alternative |
|----------|-----|-------------|
| N > 25 | 2²⁵ = 33M states — too slow for bitmask enumeration | Greedy, DP with different state, divide & conquer |
| Need to count occurrences (not just presence) | Bitmask is binary — can't store counts | HashMap / Counter |
| Elements are not from a small, bounded domain | Can't map elements to bit positions efficiently | HashSet |
| Order matters within the subset | Bitmask only represents *which* elements, not *what order* | Permutation-based DP, factorial state |
| Problem involves continuous ranges | Bits are discrete | Segment tree, interval-based approaches |

### 💡 Quick "Smell Test" Questions

Before committing to a bitmask approach, ask yourself:

1. **"Can I number the elements 0 to N-1?"** — If yes, each element maps to a bit position. If elements are arbitrary (like strings or large numbers), you'd need a mapping step first.

2. **"Is N small enough?"** — Rule of thumb:
   - N ≤ 15: Bitmask DP is comfortable (2¹⁵ = 32K states)
   - N ≤ 20: Bitmask DP is feasible (2²⁰ = 1M states)
   - N ≤ 25: Bitmask enumeration only if each state is O(1) work
   - N > 25: Almost certainly not bitmask

3. **"Do I only care about presence/absence?"** — Bitmask = set of booleans. If you need counts, weights, or ordering, bitmask alone won't suffice (though you can combine it with other structures).

4. **"Would set operations help?"** — If you find yourself thinking "I need union, intersection, or complement of sets," bitmask gives you those in O(1).

5. **"Is the brute force factorial but N is small?"** — Classic sign of bitmask DP. You're compressing N! states into 2ᴺ by ignoring order.

---

## Python-Specific Interview Notes

1. **Python integers have arbitrary precision** — no overflow! A bitmask with 1000 bits works fine (though it's slow for very large masks).

2. **`~x` in Python gives `-(x+1)`**, not a fixed-width complement. If you need 32-bit NOT, use `x ^ 0xFFFFFFFF`.

3. **`bin(x).count('1')` works on all Python 3 versions.** `x.bit_count()` is cleaner but requires Python 3.10+. In interviews, mention both and ask which Python version is available.

4. **Negative numbers and right shift:** Python's `>>` does arithmetic right shift (preserves sign). For unsigned behavior, mask with `& 0xFFFFFFFF` first.

5. **Common interview trap:** Forgetting that `==` has higher precedence than `&` in Python. Write `(n & (n-1)) == 0`, NOT `n & (n-1) == 0` (the latter evaluates as `n & (True/False)`).

---

*Happy bit twiddling! 🎉*
