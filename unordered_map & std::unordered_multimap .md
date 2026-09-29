# C++ STL `std::unordered_map` & `std::unordered_multimap` Complete Reference Guide

A consolidated technical guide and cheatsheet covering the Standard Template Library (STL) unordered map containers in C++.

---

## 1. What is an `unordered_map`?

`std::unordered_map` is an associative container that stores key-value pairs (`std::pair<const Key, Value>`):

* **Hash Table Implementation:** Unlike `std::map` (which uses a Red-Black Tree and keeps keys sorted), `unordered_map` organizes elements into buckets using a hash function.
* **Order:** Elements are not sorted; their positions depend purely on the hash values of their keys.
* **Performance:** Provides $O(1)$ constant time on average for insertion, lookup, and deletion. In the worst case (heavy hash collisions), operations can degrade to $O(n)$.
* **Key Requirements:** The key type must provide a hash function (specialization of `std::hash<Key>`) and support the equality operator `==`.

---

## 2. Header and Initialization

Include `<unordered_map>` to use it:

```cpp
#include <iostream>
#include <unordered_map>
#include <string>

int main() {
    // 1. Empty unordered_map: <Key, Value>
    std::unordered_map<std::string, int> carModels;

    // 2. Initializer list (order is arbitrary, NOT sorted)
    std::unordered_map<std::string, int> cars = {
        {"BMW", 2018},
        {"Mercedes", 2020},
        {"Audi", 2015}
    };
}
```

---

## 3. Inserting and Accessing Elements

### A. Subscript Operator `[]` vs. `.at()`

* `m[key]`:
  * If the key exists, it returns a reference to its mapped value.
  * If the key does not exist, it inserts a new entry with a default-constructed value (e.g., `0` for numbers, empty string for text).
* `m.at(key)`: Safely accesses the value; throws a `std::out_of_range` exception if the key does not exist.

### B. `.insert()` and `.emplace()`

* `m.insert({key, val})`: Inserts a key-value pair only if the key is not already present. Returns a `std::pair<iterator, bool>`.
* `m.emplace(key, val)`: Constructs the key-value pair directly in place without temporary copies.

```cpp
#include <iostream>
#include <unordered_map>
#include <string>

int main() {
    std::unordered_map<std::string, int> carYear;

    // Using operator[]
    carYear["BMW"] = 2010;
    carYear["Toyota"] = 2015;

    // Using insert
    carYear.insert({"Mercedes", 2020});

    // Attempting to insert duplicate key (ignored by insert)
    auto res = carYear.insert({"BMW", 2022});
    if (!res.second) {
        std::cout << "BMW already exists! Not replaced.\n";
    }

    // Accessing with at()
    std::cout << "Mercedes year: " << carYear.at("Mercedes") << "\n";
}
```

---

## 4. Searching and Deleting

### A. Searching: `.find()` and `.count()`

* `find(key)`: Returns an iterator to the entry if found, or `m.end()` if absent ($O(1)$ avg).
* `count(key)`: Returns `1` if the key exists, or `0` if it does not.

```cpp
auto it = carYear.find("Toyota");
if (it != carYear.end()) {
    std::cout << "Found: " << it->first << " -> " << it->second << "\n";
} else {
    std::cout << "Not found\n";
}
```

### B. Deleting: `.erase()`

* **By Key:** `carYear.erase("BMW");` — deletes the key and its value ($O(1)$ avg).
* **By Iterator:** `carYear.erase(it);` — removes the specific element at iterator `it`.

---

## 5. Iteration and Bucket Inspection

Iterators traverse the elements stored in the internal buckets:

```cpp
#include <iostream>
#include <unordered_map>
#include <string>

int main() {
    std::unordered_map<std::string, int> inventory = {
        {"Apples", 50},
        {"Bananas", 20},
        {"Oranges", 35}
    };

    // Iterating through all key-value pairs
    for (const auto& [item, qty] : inventory) {
        std::cout << item << ": " << qty << "\n";
    }

    // Hash table internal metrics
    std::cout << "Total Buckets: " << inventory.bucket_count() << "\n";
    std::cout << "Load Factor: "   << inventory.load_factor() << "\n";
    std::cout << "Max Load Factor: " << inventory.max_load_factor() << "\n";

    // Inspecting which bucket holds a specific key
    std::cout << "'Apples' is stored in bucket: " << inventory.bucket("Apples") << "\n";
}
```

---

## 6. What is `std::unordered_multimap`?

* **Allows Duplicate Keys:** Multiple values can be associated with the same key.
* **No `operator[]` or `.at()`:** Because a key can map to multiple values, `m[key]` cannot be used. You must use `.insert()` or `.emplace()`.
* **Equal Range Lookup:** To retrieve all entries matching a specific duplicate key, use `equal_range()`:

```cpp
#include <iostream>
#include <unordered_map>
#include <string>

int main() {
    std::unordered_multimap<std::string, std::string> phoneBook;

    // Inserting multiple entries for the same key
    phoneBook.insert({"Ahmed", "0101111111"});
    phoneBook.insert({"Ahmed", "0122222222"});
    phoneBook.insert({"Khaled", "0155555555"});

    // Finding all numbers for "Ahmed"
    auto range = phoneBook.equal_range("Ahmed");
    std::cout << "Numbers for Ahmed:\n";
    for (auto it = range.first; it != range.second; ++it) {
        std::cout << it->second << "\n";
    }
}
```

---

## 7. Comparative Summary: `std::map` vs. `std::unordered_map`

| Feature | `std::map` | `std::unordered_map` |
| :--- | :--- | :--- |
| **Ordering** | Sorted by key (ascending by default) | Unordered (hash order) |
| **Underlying Data Structure** | Red-Black Tree (Self-balancing BST) | Hash Table (Buckets & Chaining) |
| **Average Time Complexity** | $O(\log n)$ | $O(1)$ |
| **Worst-Case Complexity** | $O(\log n)$ | $O(n)$ (if hash collisions spike) |
| **Memory Footprint** | Moderate (node pointers: left, right, parent) | Can be higher (bucket array overhead) |
| **Range Queries (`lower_bound`)** | Supported | Not supported |
| **Key Requirement** | `<` operator (`std::less`) | `std::hash` and `==` operator |

---

## Practical Guideline
* **Use `std::unordered_map`** when lookup, insertion, and deletion speed matter most ($O(1)$ average) and ordering is irrelevant.
* **Use `std::map`** when elements must stay sorted by key, or when ordered range queries (`lower_bound`, `upper_bound`) are needed.
