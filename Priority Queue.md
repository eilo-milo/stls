

---

## 1. What is a Priority Queue? (Container Adaptor)

In the C++ Standard Template Library, `std::priority_queue` is a **Container Adaptor**:

* **Priority-Based Ordering:** Unlike a standard FIFO `std::queue`, elements in a priority queue are retrieved according to priority rather than arrival time.
* **Max-Heap by Default:** By default, the element with the highest value (largest number) has the highest priority and stays at the front/top.
* **Internal Representation:** Typically implemented over a binary heap using an underlying sequence container (by default `std::vector`).
* **Restricted Interface:** Like `std::stack` and `std::queue`, it does not support random access indexing (`pq[i]`) or iterators (`begin()`, `end()`).

---

## 2. Header and Template Declarations

Include the `<queue>` header to use priority queues:

```cpp
#include <iostream>
#include <queue>
#include <vector>

int main() {
    // 1. Default: Max-Heap (stores largest value at top)
    std::priority_queue<int> max_pq;

    // 2. Min-Heap: stores smallest value at top using std::greater
    std::priority_queue<int, std::vector<int>, std::greater<int>> min_pq;
}
```

### Template Signature
```cpp
std::priority_queue<Type, Comparator Container,>
```
* `Type`: The data type of the elements.
* `Container`: The underlying container storing the heap elements (default is `std::vector<Type>`).
* `Comparator`: Strict weak ordering functor (default is `std::less<Type>`, which produces a **Max-Heap**).

---

## 3. Core Operations & Complexities

| Function | Operation | Description | Time Complexity |
| :--- | :--- | :--- | :--- |
| `push(val)` | Enqueue | Inserts an element into the priority queue and re-heaps | $O(\log n)$ |
| `emplace(args...)`| Enqueue | Constructs the element in-place directly inside the heap | $O(\log n)$ |
| `pop()` | Dequeue | Removes the highest-priority element (at the top) | $O(\log n)$ |
| `top()` | Access | Returns a reference to the highest-priority element | $O(1)$ |
| `empty()` | Check | Returns `true` if the queue is empty, `false` otherwise | $O(1)$ |
| `size()` | Size | Returns the total count of elements | $O(1)$ |

---

## 4. Code Walkthrough: Max-Heap vs. Min-Heap

### Max-Heap (Default Behavior)

```cpp
#include <iostream>
#include <queue>

int main() {
    std::priority_queue<int> pq;

    pq.push(30);
    pq.push(10);
    pq.push(50);
    pq.push(20);

    // Elements are always extracted from highest to lowest
    while (!pq.empty()) {
        std::cout << pq.top() << " "; // Output: 50 30 20 10
        pq.pop();
    }
    std::cout << "\n";
}
```

### Min-Heap (Smallest Element First)

```cpp
#include <iostream>
#include <queue>
#include <vector>

int main() {
    std::priority_queue<int, std::vector<int>, std::greater<int>> min_pq;

    min_pq.push(30);
    min_pq.push(10);
    min_pq.push(50);
    min_pq.push(20);

    // Elements are extracted from lowest to highest
    while (!min_pq.empty()) {
        std::cout << min_pq.top() << " "; // Output: 10 20 30 50
        min_pq.pop();
    }
    std::cout << "\n";
}
```

---

## 5. Custom Comparators

You can define custom sorting logic using a custom functor struct overloading `operator()`:

```cpp
#include <iostream>
#include <queue>
#include <vector>

// Custom comparator prioritizing numbers >= 50 first
struct CustomCompare {
    bool operator()(int a, int b) {
        // Return true if 'a' has lower priority than 'b'
        bool a_high = (a >= 50);
        bool b_high = (b >= 50);

        if (a_high != b_high) {
            return !a_high; // true if 'a' is not high priority (meaning 'b' comes first)
        }
        return a > b; // Tie-breaker: smaller values first
    }
};

int main() {
    std::priority_queue<int, std::vector<int>, CustomCompare> custom_pq;

    custom_pq.push(10);
    custom_pq.push(50);
    custom_pq.push(21);
    custom_pq.push(5);
    custom_pq.push(100);
    custom_pq.push(70);

    while (!custom_pq.empty()) {
        std::cout << custom_pq.top() << " ";
        custom_pq.pop();
    }
    std::cout << "\n";
}
```

---

## 6. Container Adaptor Summary

| Feature | `std::queue` | `std::priority_queue` |
| :--- | :--- | :--- |
| **Ordering** | FIFO (First In, First Out) | Priority-ordered (Max/Min Heap) |
| **Top/Front Access** | `.front()` ($O(1)$) | `.top()` ($O(1)$) |
| **Insertion** | `push()` / `emplace()` ($O(1)$) | `push()` / `emplace()` ($O(\log n)$) |
| **Removal** | `pop()` ($O(1)$) | `pop()` ($O(\log n)$) |
| **Default Container** | `std::deque` | `std::vector` |
| **Common Uses** | BFS, Print queues, buffering | Dijkstra's Algorithm, Prim's MST, Task scheduling |
