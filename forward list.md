

---

## 1. What is `std::forward_list`?

Introduced in C++11, `std::forward_list` implements a singly linked list.

* **Memory Efficiency:** Unlike `std::list` (doubly linked list), which stores two pointers per node (`next` and `prev`), `forward_list` stores only one pointer (`next`) per element [08:03].
* **One-Way Traversal:** Iterators can only move forward (`++it`). Backward traversal is not supported (`--it` is invalid) [02:23].
* **No `size()` Method:** To maximize performance and keep memory overhead minimal, `forward_list` does not store an internal size counter. Calculating its length requires manually traversing the container in $O(n)$ time.
* **Front-Only Access:** It does not provide `push_back()` or `pop_back()` because locating the final node takes $O(n)$ without a tail pointer. Operations are centered strictly around the front [06:25].

---

## 2. Header and Initialization

Include `<forward_list>` to use the container:

```cpp
#include <iostream>
#include <forward_list>

int main() {
    // 1. Empty forward list
    std::forward_list<int> fl1;

    // 2. Initializer list
    std::forward_list<int> fl2 = {10, 20, 30, 40};

    // 3. 5 elements, all initialized to 100
    std::forward_list<int> fl3(5, 100);

    // 4. Assigning values
    std::forward_list<int> fl4;
    fl4.assign({1, 2, 3, 4, 5});
}
```

---

## 3. Modifiers at the Front ($O(1)$)

Because links only point forward, operations at the beginning run in constant time [08:35]:

* `push_front(val)`: Adds an element to the head [06:25].
* `pop_front()`: Removes the element from the head.
* `front()`: Accesses the first element (there is no `back()`).

```cpp
#include <iostream>
#include <forward_list>

int main() {
    std::forward_list<int> fl;

    fl.push_front(30); // {30}
    fl.push_front(20); // {20, 30}
    fl.push_front(10); // {10, 20, 30}

    fl.pop_front();    // Removes 10 -> {20, 30}
}
```

---

## 4. Insertion and Deletion: The `_after` Rule

In a singly linked list, to insert or erase a node at a given position, you must update the pointer of the preceding node. For this reason, standard `insert()` and `erase()` are replaced with `_after` operations [11:34]:

* `insert_after(it, val)`: Inserts `val` immediately after the position pointed to by iterator `it` [16:27].
* `erase_after(it)`: Erases the element immediately after `it`.
* `before_begin()`: Returns an iterator pointing to the position before the first element, allowing insertion at the very front via `insert_after(fl.before_begin(), val)`.

```cpp
#include <iostream>
#include <forward_list>
#include <iterator>

int main() {
    std::forward_list<int> fl = {10, 20, 40, 50};

    // Insert 30 between 20 and 40
    auto it = fl.begin(); // points to 10
    ++it;                 // points to 20

    fl.insert_after(it, 30); // {10, 20, 30, 40, 50}

    // Erase the element right after 30 (which is 40)
    ++it;                    // points to 30
    fl.erase_after(it);      // {10, 20, 30, 50}

    // Insert at the very beginning using before_begin()
    fl.insert_after(fl.before_begin(), 5); // {5, 10, 20, 30, 50}
}
```

---

## 5. Specialized Algorithms and Member Functions

Like `std::list`, `std::forward_list` provides specialized member functions optimized for in-place node operations:

```cpp
#include <iostream>
#include <forward_list>

int main() {
    std::forward_list<int> fl = {40, 10, 30, 10, 20, 50, 10};

    // 1. Sort (O(n log n))
    fl.sort(); // {10, 10, 10, 20, 30, 40, 50}

    // 2. Remove all occurrences of a value
    fl.remove(10); // {20, 30, 40, 50}

    // 3. Remove by condition (remove_if with lambda)
    // Removes elements less than or equal to 25
    fl.remove_if([](int n) { return n <= 25; }); // {30, 40, 50}

    // 4. Reverse the list
    fl.reverse(); // {50, 40, 30}

    // 5. Splice after: transfers elements from another forward_list in O(1)
    std::forward_list<int> other = {100, 200};
    fl.splice_after(fl.before_begin(), other); // inserts 'other' at the beginning
}
```

---

## 6. Comparison: `std::forward_list` vs. `std::list`

| Feature | `std::forward_list` | `std::list` |
| :--- | :--- | :--- |
| **Data Structure** | Singly linked list (1 pointer per node) | Doubly linked list (2 pointers per node) |
| **Memory Footprint** | Smallest possible for linked structures | Higher (holds `next` and `prev`) |
| **Iterator Direction** | Forward only (`++it`) | Bidirectional (`++it`, `--it`) |
| **End Operations** | None (`push_back` / `pop_back` not supported) | Supported in $O(1)$ |
| **Insert / Erase Style** | `insert_after()`, `erase_after()` | `insert()`, `erase()` |
| **Size Tracking** | No `.size()` (avoids storing extra counter) | Has `.size()` in $O(1)$ |

---

## Summary Rule
Use `std::forward_list` when you need a linked list representation with node-based insertion and splicing, but want to minimize memory overhead to that of a raw C-style linked list.
