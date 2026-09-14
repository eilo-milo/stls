

---

## 1. What is a Deque?

* `std::deque` stands for **Double-Ended Queue** .
* **Core Difference from Vector:** While a `std::vector` is optimized for inserting/removing at the back in $O(1)$, inserting at the front is expensive ($O(n)$) because all existing elements must shift right .
* **Deque Advantage:** Provides $O(1)$ constant time insertion and deletion at both the front and the back .
* **Internal Structure:** Unlike vectors which use a single contiguous block of heap memory, a deque consists of multiple fixed-size contiguous chunks (pages) indexed by a central pointer map. This architecture allows it to expand in both directions without copying all elements upon reallocation.

---

## 2. Header and Initialization

To use deque, include the `<deque>` header :

```cpp
#include <iostream>
#include <deque>

int main() {
    // 1. Empty deque of characters
    std::deque<char> d1;

    // 2. Initialized with values
    std::deque<char> d2 = {'b', 'c', 'd'};

    // 3. Deque with 5 elements, all initialized to 10
    std::deque<int> d3(5, 10);
}
```

---

## 3. Double-Ended Modifiers: push & pop

Deque provides balanced front and back insertion and removal methods :

| Action | Front ($O(1)$) | Back ($O(1)$) |
| :--- | :--- | :--- |
| **Insert** | `push_front(val)` [03:17] | `push_back(val)`  |
| **Delete** | `pop_front()` | `pop_back()` [06:34] |

### Example

```cpp
#include <iostream>
#include <deque>

int main() {
    std::deque<char> dq;

    dq.push_back('B');  // ['B']
    dq.push_back('C');  // ['B', 'C']
    dq.push_front('A'); // ['A', 'B', 'C']
    dq.push_back('D');  // ['A', 'B', 'C', 'D']

    // Removal
    dq.pop_front();     // Removes 'A' -> ['B', 'C', 'D']
    dq.pop_back();      // Removes 'D' -> ['B', 'C']
}
```

---

## 4. Element Access & Bounds Checking

Just like `std::vector`, `std::deque` supports constant-time random access :

* `dq[i]`: Direct index access (fast, no bounds checking).
* `dq.at(i)`: Safe indexed access (throws `std::out_of_range`).
* `dq.front()`: Accesses the first element.
* `dq.back()`: Accesses the last element.

```cpp
#include <iostream>
#include <deque>

int main() {
    std::deque<int> dq = {10, 20, 30};

    std::cout << dq[0] << "\n";       // 10
    std::cout << dq.at(1) << "\n";    // 20
    std::cout << dq.front() << "\n";  // 10
    std::cout << dq.back() << "\n";   // 30
}
```

---

## 5. Iterators and Traversal

Iterating through a deque works identically to a vector using forward iterators, reverse iterators, or range-based loops :

```cpp
#include <iostream>
#include <deque>

int main() {
    std::deque<char> dq = {'A', 'B', 'C', 'D'};

    // 1. Forward iterator
    for (std::deque<char>::iterator it = dq.begin(); it != dq.end(); ++it) {
        std::cout << *it << " "; // A B C D
    }
    std::cout << "\n";

    // 2. Reverse iterator (from right to left)
    for (std::deque<char>::reverse_iterator rit = dq.rbegin(); rit != dq.rend(); ++rit) {
        std::cout << *rit << " "; // D C B A
    }
    std::cout << "\n";

    // 3. Range-based for loop
    for (char ch : dq) {
        std::cout << ch << " ";
    }
}
```

---

## 6. Insertion and Deletion at Arbitrary Positions

Elements can be inserted and removed anywhere in the deque using iterators :

```cpp
#include <iostream>
#include <deque>

int main() {
    std::deque<int> dq = {10, 20, 30, 40};

    // Insert 99 before index 2 (before 30)
    dq.insert(dq.begin() + 2, 99); // {10, 20, 99, 30, 40}

    // Erase element at index 2
    dq.erase(dq.begin() + 2);      // {10, 20, 30, 40}

    // Erase a range [begin+1, begin+3) -> removes index 1 and 2
    dq.erase(dq.begin() + 1, dq.begin() + 3); // {10, 40}

    // Clear all elements
    dq.clear();
}
```

---

## 7. Vector vs. Deque Comparison

| Feature | `std::vector` | `std::deque` |
| :--- | :--- | :--- |
| **Front Insertion/Removal** | $O(n)$ (shifts all elements) | $O(1)$ (`push_front` / `pop_front`) |
| **Back Insertion/Removal** | $O(1)$ amortized | $O(1)$ (`push_back` / `pop_back`) |
| **Random Access (`[]`)** | $O(1)$ (direct pointer math) | $O(1)$ (small overhead due to chunk map lookup) |
| **Memory Contiguity** | 100% contiguous array | Chunks of contiguous arrays |
| **Reallocation Overhead** | Copies/moves entire array when full | Allocates a new chunk without copying old ones |
| **Reserve Capacity** | Has `.capacity()` and `.reserve()` | No `.reserve()` or `.capacity()` |

---

## Quick Rule of Thumb
* **Use `std::vector`** by default when you mostly add/remove at the end and need the fastest sequential traversal with minimal memory overhead.
* **Use `std::deque`** when your algorithm requires frequent insertions and deletions from both ends efficiently.
