# C++ STL `std::map` Complete Reference Guide

A consolidated technical reference and cheatsheet covering the Standard Template Library (STL) `std::map` in C++.

---

## Part 1: Theory, Core Functions & Operations

### 1. What is `std::map`?

`std::map` is an associative container that stores elements as Key-Value pairs (`std::pair<const Key, Value>`).

* **Unique Keys:** Every key must be unique; duplicate keys are not allowed.
* **Sorted by Key:** Elements are automatically maintained in sorted order based on their keys (ascending by default).
* **Underlying Implementation:** Typically implemented as a self-balancing binary search tree (specifically a Red-Black Tree).
* **Time Complexity:** Search, insertion, and deletion operations all run in logarithmic time: $O(\log n)$.

---

### 2. Header and Initialization

Include the `<map>` header to use the container:

```cpp
#include <iostream>
#include <map>
#include <string>

int main() {
    // 1. Default ascending order map: map<Key, Value>
    std::map<int, std::string> m1;

    // 2. Initializer list (C++11)
    std::map<int, std::string> students = {
        {102, "Khaled"},
        {101, "Ahmed"},
        {103, "Sarah"}
    };
    // Automatically ordered by Key: 101 -> 102 -> 103

    // 3. Descending order map
    std::map<int, std::string, std::greater<int>> m2;
}
```

---

### 3. Inserting and Accessing Elements

#### A. Subscript Operator `[]` vs. `.at()`
* `m[key]`:
  * If the key exists, it returns a reference to its mapped value (which can be read or overwritten).
  * If the key does not exist, it automatically inserts the key value-initialized (e.g., `0` for numbers, `""` for strings).
* `m.at(key)`: Safely accesses the element; throws `std::out_of_range` if the key does not exist.

#### B. `.insert()` & `.emplace()`
* `insert({key, val})`: Inserts a `std::pair`. Returns `std::pair<iterator, bool>`, where `.second` is `true` if insertion succeeded, or `false` if the key already exists (does not overwrite).
* `emplace(key, val)`: Constructs the key-value pair directly in-place, eliminating redundant copy or move operations.

```cpp
#include <iostream>
#include <map>
#include <string>

int main() {
    std::map<int, std::string> mp;

    // Method 1: Using operator[]
    mp[1] = "CS";
    mp[2] = "IT";

    // Method 2: Using insert with make_pair or initializer list
    mp.insert({3, "Engineering"});
    mp.insert(std::make_pair(4, "Medicine"));

    // Attempting to insert duplicate key with insert()
    auto res = mp.insert({1, "Software Engineering"});
    if (!res.second) {
        std::cout << "Key 1 already exists, not overwritten!\n";
    }

    // Overwriting using operator[]
    mp[1] = "Software Engineering"; // Overwrites "CS"
}
```

---

### 4. Iterating Through a Map

Every element accessed from a map iterator is a `std::pair`:
* `.first` $\rightarrow$ The Key (read-only / `const Key`)
* `.second` $\rightarrow$ The Value

```cpp
#include <iostream>
#include <map>
#include <string>

int main() {
    std::map<int, std::string> mp = {{1, "One"}, {2, "Two"}, {3, "Three"}};

    // 1. Traditional iterator loop
    for (std::map<int, std::string>::iterator it = mp.begin(); it != mp.end(); ++it) {
        std::cout << it->first << " : " << it->second << "\n";
    }

    // 2. Range-based for loop (C++11)
    for (const auto& item : mp) {
        std::cout << item.first << " -> " << item.second << "\n";
    }

    // 3. Structured binding (C++17)
    for (const auto& [key, val] : mp) {
        std::cout << key << " = " << val << "\n";
    }
}
```

---

### 5. Searching and Deleting

#### A. Searching: `find()` & `count()`
* `find(key)`: Returns an iterator to the pair if found, or `mp.end()` if absent. Runs in $O(\log n)$.
* `count(key)`: Returns `1` if key is present, `0` otherwise.

```cpp
auto it = mp.find(2);
if (it != mp.end()) {
    std::cout << "Found key 2 with value: " << it->second << "\n";
} else {
    std::cout << "Key not found\n";
}
```

#### B. Deleting: `erase()`
* **By Key:** `mp.erase(key)` — Deletes the matching key-value node in $O(\log n)$.
* **By Iterator:** `mp.erase(it)` — Removes the element at the specified iterator in $O(1)$ amortized time.
* **By Range:** `mp.erase(start_it, end_it)` — Erases the half-open range `[start_it, end_it)`.

---

### 6. Bounds: `lower_bound()` & `upper_bound()`

* `lower_bound(k)`: Returns an iterator to the first element whose key is $\ge k$.
* `upper_bound(k)`: Returns an iterator to the first element whose key is $> k$.

```cpp
std::map<int, std::string> mp = {{10, "A"}, {20, "B"}, {30, "C"}, {40, "D"}};

auto lb = mp.lower_bound(20); // Points to (20, "B")
auto ub = mp.upper_bound(20); // Points to (30, "C")
```

---

## Part 2: Practical Applications & Custom Comparators

### 1. Practical Example 1: Student Grade Lookup

```cpp
#include <iostream>
#include <map>
#include <string>

int main() {
    std::map<std::string, double> studentGrades = {
        {"Ali", 78.5},
        {"Mohamed", 90.0},
        {"Mahmoud", 88.0},
        {"Youssef", 94.2}
    };

    std::string searchName;
    std::cout << "Enter student name: ";
    std::cin >> searchName;

    auto it = studentGrades.find(searchName);
    if (it != studentGrades.end()) {
        std::cout << "Student: " << it->first << " | Grade: " << it->second << "\n";
    } else {
        std::cout << "Student not found!\n";
    }
}
```

---

### 2. Practical Example 2: Frequency Counter

A standard pattern in data analysis and competitive programming is tracking occurrences with `freq[x]++`:
* If `x` has not been seen yet, `std::map` default-inserts `x` with value `0`.
* The `++` operator then increments that value to `1`.
* If `x` exists, it directly increments the counter.

```cpp
#include <iostream>
#include <vector>
#include <map>

int main() {
    std::vector<int> numbers = {5, 2, 5, 1, 1, 1, 4, 2, 5};

    std::map<int, int> frequency;

    // Count frequency of each number in O(N log K)
    for (int num : numbers) {
        frequency[num]++;
    }

    // Print frequencies (automatically sorted by key)
    std::cout << "Element Frequency:\n";
    for (const auto& [num, count] : frequency) {
        std::cout << "Number " << num << " appeared " << count << " time(s)\n";
    }
}
```

---

### 3. Practical Example 3: Custom Comparator for Sorting

By default, `std::map` sorts keys ascending (`std::less<Key>`). A custom comparator struct can override this behavior:

```cpp
#include <iostream>
#include <map>
#include <string>

// Custom comparator to sort integer keys in descending order
struct CustomCompare {
    bool operator()(const int& a, const int& b) const {
        return a > b; // Descending order
    }
};

int main() {
    std::map<int, std::string, CustomCompare> customMap;

    customMap[1] = "One";
    customMap[10] = "Ten";
    customMap[5] = "Five";

    // Keys print in descending order: 10, 5, 1
    for (const auto& [k, v] : customMap) {
        std::cout << k << " -> " << v << "\n";
    }
}
```

---

## Summary Cheat Sheet

| Feature | `std::map` |
| :--- | :--- |
| **Structure** | Key-Value pairs (`std::pair<const Key, Value>`) |
| **Ordering** | Always sorted by Key ($O(\log n)$ per insertion) |
| **Key Uniqueness** | Unique keys only (duplicates disallowed) |
| **Direct Access** | `m[key]` (inserts if missing) or `m.at(key)` (throws if missing) |
| **Common Use Cases** | Dictionaries, frequency tables, indexing, caching sorted key data |
