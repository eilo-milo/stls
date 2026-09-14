
---

## 1. What is `std::pair`?

In C++ STL, `std::pair` is a utility class template that couples together two heterogeneous (or identical) values into a single entity.

* **Header:** Defined in the `<utility>` header (and included transitively with containers like `<vector>` or `<map>`).
* **Member Variables:** A pair provides two direct public member variables:
  * `first`: Stores the first element.
  * `second`: Stores the second element.
* **Primary Uses:**
  * Returning two related values from a function without declaring a dedicated `struct` or `class`.
  * Serving as the fundamental element type for associative containers like `std::map` (where every node stores a `std::pair<const Key, Value>`).
  * Representing coordinate pairs (e.g., $(x, y)$), weighted edges in graph algorithms, or simple key-value associations.

---

## 2. Initialization & Construction

There are multiple ways to construct and initialize pairs:

```cpp
#include <iostream>
#include <utility>
#include <string>

int main() {
    // 1. Default initialization (0, empty string, etc.)
    std::pair<int, std::string> p1;

    // 2. Direct initialization
    std::pair<int, std::string> p2(1, "Adel");

    // 3. Using std::make_pair (deduces types automatically)
    auto p3 = std::make_pair(2, "Nasim");

    // 4. Initializer list / Uniform initialization (C++11)
    std::pair<std::string, double> p4 = {"Laptop", 999.99};

    // Accessing members
    std::cout << p2.first << " : " << p2.second << "\n"; // 1 : Adel
}
```

---

## 3. Comparison Operators

Pairs provide overloaded relational and comparison operators (`==`, `!=`, `<`, `<=`, `>`, `>=`):

* **Lexicographical Ordering:**
  1. Two pairs are compared by their `.first` element first.
  2. If the `.first` elements are equal, comparison falls back to their `.second` elements.
* **Impact on Sorting:** When storing pairs in containers like `std::vector<std::pair<T1, T2>>`, invoking `std::sort()` automatically orders by `.first` ascending, using `.second` as the tie-breaker.

```cpp
std::pair<int, int> p1 = {1, 10};
std::pair<int, int> p2 = {1, 20};
std::pair<int, int> p3 = {2, 5};

// p1 < p2 is true (1 == 1, but 10 < 20)
// p2 < p3 is true (1 < 2)
```

---

## 4. Common Patterns & Code Examples

### A. Returning Multiple Values from a Function

```cpp
#include <iostream>
#include <utility>

// Function finding min and max simultaneously
std::pair<int, int> getMinMax(int a, int b) {
    if (a < b) return {a, b};
    return {b, a};
}

int main() {
    std::pair<int, int> res = getMinMax(45, 12);
    std::cout << "Min: " << res.first << ", Max: " << res.second << "\n";
}
```

### B. Vectors of Pairs

Combining `std::vector` and `std::pair` is common in data manipulation and competitive programming:

```cpp
#include <iostream>
#include <vector>
#include <utility>
#include <string>
#include <algorithm>

int main() {
    std::vector<std::pair<int, std::string>> students;

    // Adding pairs
    students.push_back(std::make_pair(95, "Sarah"));
    students.push_back({88, "Ahmed"});
    students.emplace_back(91, "Omar"); // Constructs pair in-place

    // Sorting (sorts by grade ascending, then name)
    std::sort(students.begin(), students.end());

    // Iterating
    for (const auto& item : students) {
        std::cout << "Score: " << item.first << " - Name: " << item.second << "\n";
    }
}
```

### C. Nested Pairs (Pair of Pairs / 3D Entities)

When three or more grouped values are needed before introducing `std::tuple`:

```cpp
#include <iostream>
#include <utility>
#include <string>

int main() {
    // Storing (ID, (Name, Salary))
    std::pair<int, std::pair<std::string, double>> employee;

    employee.first = 101;
    employee.second.first = "Khalid";
    employee.second.second = 5500.50;

    std::cout << employee.first << " -> " 
              << employee.second.first << " ($" 
              << employee.second.second << ")\n";
}
```

---

## 5. Summary Cheat Sheet

| Feature | Details |
| :--- | :--- |
| **Header** | `<utility>` |
| **Members** | `.first`, `.second` |
| **Creation** | Direct constructor, `{val1, val2}`, or `std::make_pair(val1, val2)` |
| **Sorting Behavior** | Lexicographical (compares `.first` first, then breaks ties using `.second`) |
| **Primary Use Cases** | Returning two values, map entries, $(x, y)$ coordinates, weighted graph edges |

---

`std::pair` offers a zero-overhead, lightweight way to group coupled values before working with associative containers like `std::map`.
