# C++ STL `std::queue` Complete Reference Guide

A consolidated technical reference and cheatsheet covering the Standard Template Library (STL) `std::queue` in C++.

---

## 1. What is a Queue? (Container Adaptor)

In the C++ Standard Template Library, `std::queue` is a **Container Adaptor** (Container Adapter).

* **FIFO Principle:** It strictly adheres to the **FIFO** (First In, First Out) rule—the first item added to the queue is the first one to be removed.
* **Two Ends (Front & Back):**
  * Elements are inserted (**enqueue**) at the back (also called rear or tail).
  * Elements are removed (**dequeue**) from the front (also called head).
* **No Direct Indexing or Iterators:** Just like `std::stack`, `std::queue` does not permit random access (`q[i]`), pointer arithmetic, or iterators (`begin()`, `end()`).

---

## 2. Underlying Sequence Containers

Because `std::queue` requires operations at both ends (pushing at the back and popping from the front), it can wrap containers that support both `push_back()` and `pop_front()` in constant time:

* **Default:** `std::deque`
* **Alternative:** `std::list`
* **Why `std::vector` fails:** `std::vector` cannot be used as an underlying container for `std::queue` because it lacks a member function `pop_front()` (removing from the front in a vector requires shifting all elements in $O(n)$).

```cpp
#include <iostream>
#include <queue>
#include <deque>
#include <list>

int main() {
    // 1. Default queue (backed by std::deque)
    std::queue<int> q1;

    // 2. Explicitly backed by std::list
    std::queue<int, std::list<int>> q2;

    // 3. Explicitly backed by std::deque
    std::queue<int, std::deque<int>> q3;
}
```

---

## 3. Core Queue Operations ($O(1)$)

All core queue operations execute in $O(1)$ constant time:

| Function | Operation | Description |
| :--- | :--- | :--- |
| `push(val)` | Enqueue | Inserts an element at the back. |
| `emplace(args...)` | Enqueue | Constructs the element directly at the back in-place (avoids copies). |
| `pop()` | Dequeue | Removes the element from the front (returns `void`). |
| `front()` | Access | Returns a reference to the oldest element (at the front). |
| `back()` | Access | Returns a reference to the newest element (at the back). |
| `empty()` | Check | Returns `true` if empty, `false` otherwise. |
| `size()` | Size | Returns the total count of elements. |
| `swap()` | Utility | Swaps the contents of two queues. |

---

## 4. Code Walkthrough: Enqueue, Dequeue, and Inspecting

```cpp
#include <iostream>
#include <queue>

int main() {
    std::queue<int> q;

    // 1. Adding elements to the back (Enqueue)
    q.push(60); // Queue: [60]         (Front: 60, Back: 60)
    q.push(50); // Queue: [60, 50]     (Front: 60, Back: 50)
    q.push(40); // Queue: [60, 50, 40] (Front: 60, Back: 40)

    std::cout << "Back element: "  << q.back()  << "\n"; // 40
    std::cout << "Front element: " << q.front() << "\n"; // 60

    // 2. Removing elements from the front (Dequeue)
    q.pop(); // Removes 60 -> Queue: [50, 40]
    std::cout << "New front element: " << q.front() << "\n"; // 50

    q.pop(); // Removes 50 -> Queue: [40]
    std::cout << "New front element: " << q.front() << "\n"; // 40
}
```

---

## 5. Printing / Traversing a Queue

Because there are no indices or iterators, printing or processing all items requires reading `front()` and popping elements until `empty()` is true.

> **Tip:** Pass by value (`std::queue<int> q`) to operate on a copy and preserve the original container in the caller scope.

### Using a `while` Loop

```cpp
#include <iostream>
#include <queue>

void printQueue(std::queue<int> q) {
    while (!q.empty()) {
        std::cout << q.front() << " "; // Read the front item
        q.pop();                       // Remove to reveal next element
    }
    std::cout << "\n";
}

int main() {
    std::queue<int> q;
    q.emplace(60);
    q.emplace(50);
    q.emplace(40);

    printQueue(q); // Output: 60 50 40
}
```

### Alternative using a `for` Loop

```cpp
for (int i = 0; !q.empty(); ++i) {
    std::cout << q.front() << "\n";
    q.pop();
}
```

---

## 6. Stack vs. Queue Comparison

| Feature | `std::stack` | `std::queue` |
| :--- | :--- | :--- |
| **Logic** | LIFO (Last In, First Out) | FIFO (First In, First Out) |
| **Insertion Point** | Top (`push`) | Back (`push`) |
| **Removal Point** | Top (`pop`) | Front (`pop`) |
| **Access Methods** | `.top()` | `.front()`, `.back()` |
| **Valid Adaptees** | `deque`, `vector`, `list` | `deque`, `list` (**NOT** `vector`) |
| **Typical Use Cases** | DFS, recursion, undo operations | BFS, task scheduling, print queues |
