# C++ STL `std::vector` Reference Guide

## Part 1: Introduction, Initialization & Memory Model

### 1. What is a Vector?
A `std::vector` is a dynamic sequence container (resizable array). Unlike built-in static arrays (`int arr[10]`) whose sizes must be known at compile time and cannot change, vectors grow and shrink dynamically as items are inserted or erased, while maintaining contiguous memory storage in the heap.

### 2. Header and Initialization Styles

```cpp
#include <iostream>
#include <vector>

int main() {
    // 1. Empty vector
    std::vector<int> v1;

    // 2. Vector with 5 elements, all default-initialized (0 for int)
    std::vector<int> v2(5);

    // 3. Vector with 5 elements, all initialized to 10
    std::vector<int> v3(5, 10);

    // 4. Initializer list (C++11)
    std::vector<int> v4 = {1, 2, 3, 4, 5};

    // 5. Copy initialization
    std::vector<int> v5(v4);
}
```

### 3. Understanding `size()` vs. `capacity()`
* `size()`: Number of elements currently present.
* `capacity()`: Total number of elements allocated in memory before reallocation is needed.
* **Reallocation Mechanism:** When capacity is exceeded, the vector allocates a larger memory block (usually double), copies/moves the existing elements over, and frees the previous block. This makes element insertion amortized $O(1)$, but reallocation itself is costly ($O(n)$).
* **Optimization:** Use `.reserve(n)` if the number of elements is known beforehand:

```cpp
std::vector<int> v;
v.reserve(100); // Allocates capacity for 100 elements without changing size()
```

---

## Part 2: Element Access, Iteration & Modifiers

### 1. Element Access & Bounds Checking
* `v[i]`: Fast subscript access, no bounds checking (can cause undefined behavior if out of bounds).
* `v.at(i)`: Safe access, throws `std::out_of_range` exception if `i >= v.size()`.
* `v.front()`: Returns reference to first element (`v[0]`).
* `v.back()`: Returns reference to last element (`v[v.size() - 1]`).

### 2. Adding & Removing Elements
* `push_back(val)`: Inserts element at the end ($O(1)$ amortized).
* `pop_back()`: Removes the last element ($O(1)$).
* `emplace_back(args...)`: Constructs the element in-place at the end, saving an extra copy/move operation.

### 3. Iterators
An iterator functions like a pointer that points to elements inside the vector:

```cpp
std::vector<int> v = {10, 20, 30, 40};

// Traditional iterator loop
for (std::vector<int>::iterator it = v.begin(); it != v.end(); ++it) {
    std::cout << *it << " ";
}

// Reverse iterator
for (std::vector<int>::reverse_iterator rit = v.rbegin(); rit != v.rend(); ++rit) {
    std::cout << *rit << " "; // 40 30 20 10
}

// Range-based for loop (C++11)
for (int val : v) {
    std::cout << val << " ";
}
```

---

## Part 3: Advanced Methods, Algorithms & Performance

### 1. Arbitrary Insertion and Deletion
* `v.insert(pos_iterator, val)`: Inserts `val` at the specified iterator position.  
  * **Complexity:** $O(n)$ because all subsequent elements must shift right.
* `v.erase(pos_iterator)` or `v.erase(start, end)`: Removes element(s) at iterator location.  
  * **Complexity:** $O(n)$ because trailing elements shift left.
* `v.clear()`: Removes all elements (`size() = 0`, but `capacity()` usually remains).
* `v.shrink_to_fit()`: Requests releasing unused capacity to match current size.

```cpp
std::vector<int> v = {10, 20, 30, 40, 50};

// Insert 99 at index 2 (before 30)
v.insert(v.begin() + 2, 99); // {10, 20, 99, 30, 40, 50}

// Erase 99
v.erase(v.begin() + 2);      // {10, 20, 30, 40, 50}

// Erase range [begin+1, begin+3) -> removes 20, 30
v.erase(v.begin() + 1, v.begin() + 3); // {10, 40, 50}
```

### 2. Using `<algorithm>` with Vectors
Vectors pair naturally with the standard `<algorithm>` library:

```cpp
#include <algorithm>
#include <vector>

std::vector<int> v = {40, 10, 50, 20, 30};

// Sorting
std::sort(v.begin(), v.end()); // {10, 20, 30, 40, 50}

// Reverse
std::reverse(v.begin(), v.end()); // {50, 40, 30, 20, 10}

// Finding an element
auto it = std::find(v.begin(), v.end(), 30);
if (it != v.end()) {
    std::cout << "Found at index: " << (it - v.begin()) << "\n";
}
```

### 3. Complexity & Trade-offs Summary

| Operation | Time Complexity | Notes |
| :--- | :--- | :--- |
| Random Access (`v[i]`, `v.at(i)`) | $O(1)$ | Direct pointer arithmetic in memory |
| Insert / Erase at end (`push_back`, `pop_back`) | $O(1)$ amortized | Requires reallocation when full |
| Insert / Erase in middle / front | $O(n)$ | Must shift elements |
| Search (Unsorted) | $O(n)$ | Linear scan |
| Search (Sorted, Binary Search) | $O(\log n)$ | Requires sorted order |

---

## Core Takeaways to Keep in Mind
* **Contiguous Memory:** Elements reside consecutively in heap memory, making vector cache-friendly and fast for traversals.
* **Avoid Frequent Reallocations:** If you know how many elements you will store, call `.reserve()` ahead of time.
* **Iterator Invalidation:** Adding or removing elements (especially via `push_back`, `insert`, or `erase`) can reallocate memory and invalidate existing iterators and pointers to elements.
