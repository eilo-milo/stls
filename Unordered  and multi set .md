# C++ STL `std::unordered_set` & `std::unordered_multiset` Complete Reference Guide

A consolidated technical guide and cheatsheet covering the Standard Template Library (STL) unordered set containers in C++.

---

## 1. What are Unordered Containers?

Unlike regular `std::set` (which maintains elements in sorted order using a self-balancing binary search tree / Red-Black Tree), unordered containers do not sort elements:

* **Underlying Mechanism:** They are implemented using **Hash Tables**.
* **Average Time Complexity:** Search, insertion, and deletion operations run in $O(1)$ constant time on average.
* **Worst-Case Complexity:** $O(n)$ if excessive hash collisions occur (e.g., all keys hash into the exact same bucket).

---

## 2. How the Hash Table Works: Buckets & Hash Function

* **Hash Function:** Converts an input element/key into a numeric hash code (`std::hash<T>`).
* **Modulo / Bucket Indexing:** The resulting hash code is mapped to an internal array slot called a **Bucket**:
  
  ```text
  Bucket Index = Hash(x) % Total_Buckets
  ```

* **Collision Handling (Chaining):** Each bucket acts as a linked list (or chain). If two distinct keys map to the same bucket index, they are chained together inside that bucket.
* **Load Factor:** The average number of elements stored per bucket:

  ```text
  Load Factor = size() / bucket_count()
  ```

  When the load factor exceeds `max_load_factor()`, the container automatically triggers **rehashing** (allocates more buckets and redistributes existing elements).

---

## 3. Difference Between `unordered_set` and `unordered_multiset`

| Feature | `std::unordered_set` | `std::unordered_multiset` |
| :--- | :--- | :--- |
| **Duplicates** | Disallowed (unique elements only) | Allowed (can store identical values) |
| **Ordering** | Arbitrary / Unordered | Arbitrary / Unordered |
| **Underlying Structure** | Hash Table | Hash Table |
| **Average Time Complexity** | $O(1)$ (`insert`, `find`, `erase`) | $O(1)$ (`insert`, `find`, `erase`) |

---

## 4. Header and Initialization

Include the `<unordered_set>` header to use either container:

```cpp
#include <iostream>
#include <unordered_set>

int main() {
    // 1. Unordered set (duplicates are discarded, order is arbitrary)
    std::unordered_set<int> us = {40, 10, 20, 10, 30, 20};
    // Stores: {40, 10, 20, 30} (placement determined by hash indices)

    // 2. Unordered multiset (duplicates are preserved)
    std::unordered_multiset<int> ums = {10, 20, 10, 30, 20};
    // Stores both instances of 10 and both instances of 20
}
```

---

## 5. Core Operations

### A. Insertion & Search

```cpp
#include <iostream>
#include <unordered_set>

int main() {
    std::unordered_set<int> us;

    // Insertion: O(1) average
    us.insert(50);
    us.insert(20);
    us.insert(70);

    // Searching with find(): O(1) average
    auto it = us.find(20);
    if (it != us.end()) {
        std::cout << "Found: " << *it << "\n";
    }

    // count(): returns 1 or 0 for unordered_set, or >= 0 for unordered_multiset
    std::cout << "Count of 50: " << us.count(50) << "\n";
}
```

### B. Deletion with `erase()`

* `us.erase(val)`: Deletes elements matching `val`. In `unordered_multiset`, passing a value deletes **all** instances of that value.
* `us.erase(iterator)`: Erases only the specific node pointed to by the iterator (useful in `unordered_multiset` to remove just a single duplicate instance).

```cpp
#include <iostream>
#include <unordered_set>

int main() {
    std::unordered_multiset<int> ums = {10, 10, 10, 20};

    // Erasing by iterator deletes only ONE instance of 10
    auto it = ums.find(10);
    if (it != ums.end()) {
        ums.erase(it); // Remaining: {10, 10, 20}
    }

    // Erasing by value deletes ALL remaining instances of 10
    ums.erase(10); // Remaining: {20}
}
```

---

## 6. Inspecting the Hash Table (Buckets & Rehashing)

C++ provides inspection functions to check the internal bucket arrangement and control rehashing costs:

```cpp
#include <iostream>
#include <unordered_set>

int main() {
    std::unordered_set<int> us = {14, 44, 25, 30, 77};

    // 1. Number of buckets currently allocated
    std::cout << "Bucket count: " << us.bucket_count() << "\n";

    // 2. Locate which bucket contains a specific key
    std::cout << "Element 14 is in bucket: " << us.bucket(14) << "\n";

    // 3. Inspect individual bucket sizes (chain lengths)
    for (size_t i = 0; i < us.bucket_count(); ++i) {
        std::cout << "Bucket #" << i << " has " << us.bucket_size(i) << " elements.\n";
    }

    // 4. Load factor metrics
    std::cout << "Current load factor: " << us.load_factor() << "\n";
    std::cout << "Max load factor: " << us.max_load_factor() << "\n";

    // 5. Pre-allocating buckets to prevent continuous rehashing
    us.rehash(50);   // Allocates at least 50 buckets
    us.reserve(100); // Allocates enough buckets to hold 100 elements without rehashing
}
```

---

## 7. Comparison: `std::set` vs. `std::unordered_set`

| Feature | `std::set` | `std::unordered_set` |
| :--- | :--- | :--- |
| **Ordering** | Strict weak ordering (sorted, ascending by default) | No ordering (determined by hash buckets) |
| **Underlying Structure** | Red-Black Tree (Self-balancing BST) | Hash Table (Buckets + linked chains) |
| **Average Time Complexity** | $O(\log n)$ | $O(1)$ |
| **Worst-Case Complexity** | $O(\log n)$ | $O(n)$ (excessive bucket collisions) |
| **Range Queries** | Supported (`lower_bound`, `upper_bound`) | Not supported |
| **Key Type Requirements** | Requires `operator<` (or custom comparator) | Requires `std::hash<Key>` and `operator==` |

---

## When to Use Which
* **Use `std::unordered_set`** when you need maximum lookup, insertion, and deletion throughput ($O(1)$ average) and do not care about element ordering.
* **Use `std::set`** when you require elements to stay consistently sorted, or when your algorithm relies on ordered range operations (`lower_bound`, `upper_bound`).
