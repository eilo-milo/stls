# C++ STL `std::vector` Complete Reference Guide

A consolidated technical guide and cheatsheet covering the Standard Template Library (STL) `std::vector` in C++, based on the 3-part tutorial series by **Adel Nasim**.

---

## 1. Internal Architecture & Memory Layout

A `std::vector<T>` is a dynamically resizable sequence container stored contiguously on the heap.

### Stack vs. Heap Allocation

On the stack, the control block typically stores three internal pointers:

* `_M_start`: Points to the first allocated element.
* `_M_finish`: Points to one past the last initialized element (`size()`).
* `_M_end_of_storage`: Points to the upper boundary of the allocated memory chunk (`capacity()`).

```text
Stack Control Block:
+---------------+---------------+--------------------+
| _M_start      | _M_finish     | _M_end_of_storage  |
+-------+-------+-------+-------+---------+----------+
        |               |                 |
        v               v                 v
Heap:  [Elem 0 | Elem 1 | Elem 2] [Unused | Unused]
       |<------- size: 3 ------>|
       |<----------------- capacity: 5 --------->|
```

Because elements are contiguous in physical heap memory, they benefit from CPU L1/L2 cache locality and guarantee $O(1)$ random access through pointer arithmetic.

---

## 2. Declaration, Capacity & Initialization

### Common Initializations

```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<int> v1;                     // Empty vector (size = 0, cap = 0)
    std::vector<int> v2(5);                  // 5 elements value-initialized to 0
    std::vector<int> v3(5, 100);             // 5 elements filled with 100
    std::vector<int> v4 = {10, 20, 30, 40};  // Initializer list
    std::vector<int> v5(v4);                 // Deep copy constructor
    
    int arr[] = {1, 2, 3};
    std::vector<int> v6(arr, arr + 3);       // Range constructor from raw pointer
}
```

### Size vs. Capacity & Reallocations

* `size()`: Number of elements currently present.
* `capacity()`: Total storage currently allocated before reallocation is required.

**Reallocation Steps** (When `size() == capacity()` and a new element is appended):
1. A new contiguous block is allocated (usually $1.5\times$ to $2\times$ the previous capacity).
2. Elements are copied or moved into the new block.
3. The old memory block is freed.
4. **Iterator Invalidation**: All prior references, pointers, and iterators to the old vector memory become invalid.

### `reserve()` vs. `resize()`

* `reserve(n)`: Allocates raw memory so `capacity() >= n`. Does not change `size()` and creates no objects. Use this when the expected element count is known beforehand to avoid incremental allocations.
* `resize(n)`: Directly modifies `size()`. Expands by value-constructing new elements, or shrinks by destructing trailing elements.

```cpp
std::vector<int> v;
v.reserve(100); // cap = 100, size = 0

v.resize(3);    // cap = 100, size = 3 (elements: 0, 0, 0)
```

---

## 3. Element Access & Iteration

### Access Methods

* `v[i]`: Unchecked random access ($O(1)$). Fast, but leads to undefined behavior if out of range.
* `v.at(i)`: Bounds-checked access ($O(1)$). Throws `std::out_of_range` if `i >= v.size()`.
* `v.front()`: Reference to the first element (`v[0]`).
* `v.back()`: Reference to the last element (`v[v.size() - 1]`).
* `v.data()`: Returns a raw pointer to the underlying contiguous array (`T*`).

### Iterators & Traversal Patterns

Iterators point to memory locations within the contiguous block:

```text
  v.begin()                                           v.end()
     |                                                   |
     v                                                   v
   [ 10  |  20  |  30  |  40  |  50 ]  ... [past-the-end]
     ^                                   ^
     |                                   |
  v.rend()                            v.rbegin()
```

```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<int> v = {10, 20, 30, 40, 50};

    // 1. Classic Iterator Traversal
    for (std::vector<int>::iterator it = v.begin(); it != v.end(); ++it) {
        std::cout << *it << " ";
    }

    // 2. Reverse Iterator Traversal (Print right-to-left)
    for (auto rit = v.rbegin(); rit != v.rend(); ++rit) {
        std::cout << *rit << " "; // 50 40 30 20 10
    }

    // 3. Modern Range-based For Loop (Read-only const reference)
    for (const auto& val : v) {
        std::cout << val << " ";
    }
}
```

---

## 4. Modifiers, Deletions & Memory Compaction

### `push_back()` vs. `emplace_back()`

* `push_back(x)`: Copies or moves an already constructed object into the container.
* `emplace_back(args...)`: Forwards constructor arguments and constructs the element directly in place, avoiding redundant copy/move operations.

### Insertion and Deletion

* `v.insert(pos, val)`: Inserts `val` before iterator `pos`. Shifts subsequent elements right ($O(n)$).
* `v.erase(pos)`: Destroys element at iterator `pos`. Shifts subsequent elements left ($O(n)$).
* `v.erase(first, last)`: Destroys elements in the half-open range `[first, last)`.
* `v.pop_back()`: Destroys the final element ($O(1)$).

```cpp
std::vector<int> v = {10, 20, 50};

// Insert 30 at index 2
v.insert(v.begin() + 2, 30); // {10, 20, 30, 50}

// Erase element at index 1
v.erase(v.begin() + 1);      // {10, 30, 50}
```

### Buffer Reassignment & Reclaiming Memory

* `v.assign(count, val)`: Replaces container contents with `count` copies of `val`.
* `v.clear()`: Destroys all elements (`size()` drops to 0), but retains capacity.
* `v.shrink_to_fit()`: Non-binding request to release unused capacity so that `capacity() == size()`.
* `v1.swap(v2)`: $O(1)$ pointer swap exchanging internal buffers without element copying.

```cpp
std::vector<int> v(1000, 7);
v.clear();          // size = 0, capacity = 1000
v.shrink_to_fit();  // capacity reduced to 0
```

---

## 5. Algorithmic Complexity Table

| Operation | Method / Syntax | Time Complexity | Memory Impact |
| :--- | :--- | :--- | :--- |
| **Index Access** | `v[i]`, `v.at(i)` | $O(1)$ | None |
| **Front / Back Access** | `v.front()`, `v.back()` | $O(1)$ | None |
| **Append End** | `push_back()`, `emplace_back()` | $O(1)$ amortized | Triggers $O(n)$ reallocation when `size == capacity` |
| **Remove End** | `pop_back()` | $O(1)$ | No memory release |
| **Middle Insertion** | `insert(pos, val)` | $O(n)$ | Shifts right; reallocates if capacity exceeded |
| **Middle Deletion** | `erase(pos)` | $O(n)$ | Shifts left; invalidates subsequent iterators |
| **Clear Container** | `clear()` | $O(n)$ | Destructs elements; preserves capacity |
| **Buffer Swap** | `v1.swap(v2)` | $O(1)$ | Swaps internal pointers only |

---

## 6. Critical Rules & Pitfalls

### Avoid Invalidation Inside Loops
Modifying a vector's capacity or layout during iteration leads to dangling iterators:

```cpp
// INCORRECT:
for (auto it = v.begin(); it != v.end(); ++it) {
    if (*it == target) {
        v.push_back(100); // REALLOCATION INVALIDATES 'it'
    }
}
```

### Use the Erase-Remove Idiom
To remove elements by predicate prior to C++20 (or use `std::erase` in C++20+):

```cpp
#include <algorithm>

// Removes all instances equal to 20
v.erase(std::remove(v.begin(), v.end(), 20), v.end());
```

### Prefer `empty()` over `size() == 0`
`v.empty()` executes in $O(1)$ across all standard containers and explicitly signals semantic intent.

---
