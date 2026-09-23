# C++ STL `std::set` Complete Reference Guide

A consolidated technical guide and cheatsheet covering the Standard Template Library (STL) `std::set` in C++.

---

## 1. What is an Associative Container?

Unlike sequence containers (`vector`, `deque`, `list`), `std::set` is an associative container:

* **Automatic Sorting:** Elements are automatically maintained in sorted order (ascending by default) upon insertion.
* **Unique Values:** No duplicates are allowed. Inserting an existing value is ignored.
* **Underlying Structure:** Typically implemented using a self-balancing binary search tree (specifically a Red-Black Tree).
* **Time Complexity:** Search, insertion, and deletion operations all run in $O(\log n)$ logarithmic time.
* **Read-Only / Constant Elements:** You cannot directly modify elements stored inside a set because mutating an element's value in place would corrupt the tree's sorted invariants. To "edit" a value, you erase the old element and insert the new one.

---

## 2. Header and Initialization

Include the `<set>` header to use it:

```cpp
#include <iostream>
#include <set>

int main() {
    // 1. Default ascending set
    std::set<int> s1;

    // 2. Initialized with values (duplicates are automatically filtered)
    std::set<int> s2 = {40, 10, 20, 10, 30, 20}; // Stored as: {10, 20, 30, 40}

    // 3. Descending order set using std::greater<T>
    std::set<int, std::greater<int>> s3 = {10, 50, 20, 40}; // Stored as: {50, 40, 20, 10}
}
```

---

## 3. Inserting Elements

When inserting, elements are automatically placed into their correct sorted position:

```cpp
std::set<int> s;

s.insert(50);
s.insert(20);
s.insert(40);
s.insert(20); // Duplicate: will NOT be added

// Elements in set: 20, 40, 50
```

### Return Value of `insert()`

The `insert()` function returns a `std::pair<iterator, bool>`:
* `.first`: An iterator pointing to the element (either newly inserted or the existing duplicate).
* `.second`: A `bool` flag indicating whether the insertion succeeded (`true` if inserted, `false` if the element was already present).

```cpp
auto res = s.insert(20);
if (!res.second) {
    std::cout << "20 already exists in the set!\n";
}
```

---

## 4. Searching: `find()` and `count()`

Instead of using the generic `std::find()` algorithm from `<algorithm>` (which runs in linear time $O(n)$), always use the set’s member function `find()`, which leverages the tree structure to run in $O(\log n)$:

```cpp
std::set<int> s = {10, 20, 30, 40, 50};

// 1. find(): returns an iterator to the item, or s.end() if not found
auto it = s.find(30);
if (it != s.end()) {
    std::cout << "Found: " << *it << "\n";
} else {
    std::cout << "Not found\n";
}

// 2. count(): returns 1 if present, 0 if absent (since duplicates are disallowed)
if (s.count(20)) {
    std::cout << "20 exists\n";
}
```

---

## 5. Deleting Elements: `erase()`

`std::set` provides three distinct ways to remove elements:

### A. Erase by Value ($O(\log n)$)

```cpp
std::set<int> s = {10, 20, 30, 40, 50};
s.erase(30); // Removes 30 directly
```

### B. Erase by Iterator Position ($O(1)$ amortized)

```cpp
auto it = s.find(20);
if (it != s.end()) {
    s.erase(it); // Erases the element at iterator 'it'
}
```

### C. Erase by Range `[first, last)`

```cpp
// Erase all elements from 20 up to (but not including) 50
auto start = s.find(20);
auto end = s.find(50);

s.erase(start, end);
```

---

## 6. Lower Bound and Upper Bound

`std::set` provides built-in bound functions executing in $O(\log n)$:

* `lower_bound(x)`: Returns an iterator to the first element that is $\ge x$ (greater than or equal to $x$).
* `upper_bound(x)`: Returns an iterator to the first element that is $> x$ (strictly greater than $x$).

```cpp
#include <iostream>
#include <set>

int main() {
    std::set<int> s = {10, 20, 30, 40, 50, 60, 70};

    // lower_bound: >= 30
    auto it1 = s.lower_bound(30);
    std::cout << "Lower bound of 30: " << *it1 << "\n"; // 30

    // upper_bound: > 30
    auto it2 = s.upper_bound(30);
    std::cout << "Upper bound of 30: " << *it2 << "\n"; // 40
}
```

---

## 7. Set Summary Cheat Sheet

| Feature | Description |
| :--- | :--- |
| **Order** | Automatically sorted (default: ascending / `std::less<T>`) |
| **Duplicates** | Not allowed (all keys are strictly unique) |
| **Lookup / Insert / Erase** | $O(\log n)$ (backed by Red-Black Tree) |
| **Iterators** | Bidirectional (`++it`, `--it`), elements are `const` (read-only) |
| **Key Member Functions** | `insert()`, `erase()`, `find()`, `count()`, `lower_bound()`, `upper_bound()` |
