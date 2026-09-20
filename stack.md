


---

## 1. What is a Stack? (Container Adaptor)

In C++ STL, `std::stack` is classified as a **Container Adaptor** (Container Adapter) [00:06].

* **Container Adaptor Concept:** It is not an independent underlying data structure built from scratch; rather, it wraps an underlying sequence container (by default `std::deque`, though `std::vector` or `std::list` can also be used) and restricts its interface to provide a specialized behavior [00:23].
* **LIFO Principle:** It strictly follows **LIFO** (Last In, First Out) [01:09].
* **Single Point of Access:** You can only push elements to and pop elements from one designated end, known as the **top** [01:37].
* **No Iterators or Indexing:** A stack does not support random access (`s[i]`), pointer arithmetic, or iterators (`begin()`, `end()`). You can only inspect the element currently residing at the top [01:46].

---

## 2. Header and Initialization

Include the `<stack>` header:

```cpp
#include <iostream>
#include <stack>
#include <vector>

int main() {
    // 1. Default stack of integers (backed by std::deque internally)
    std::stack<int> s1;

    // 2. Specifying a different underlying container (e.g., std::vector)
    std::stack<int, std::vector<int>> s2;
}
```

---

## 3. Core Stack Operations

All core member functions operate in $O(1)$ constant time:

| Function | Description |
| :--- | :--- |
| `push(val)` | Pushes an element onto the top of the stack [01:25]. |
| `emplace(args...)` | Constructs an element directly at the top in-place. |
| `pop()` | Removes the top element (returns `void`) [01:37]. |
| `top()` | Returns a reference to the top element [01:46]. |
| `empty()` | Returns `true` if the stack has no elements, `false` otherwise [06:41]. |
| `size()` | Returns the number of elements currently stored in the stack. |

---

## 4. Code Walkthrough: Push, Pop, and Inspecting Elements

Notice that `pop()` only removes the element—it does not return it [01:37]. To inspect the value before deleting it, you must call `top()` first:

```cpp
#include <iostream>
#include <stack>

int main() {
    std::stack<int> s;

    // Adding elements onto the stack
    s.push(5);   // Stack: [5] (top is 5)
    s.push(7);   // Stack: [5, 7] (top is 7)
    s.push(10);  // Stack: [5, 7, 10] (top is 10)

    std::cout << "Top element: " << s.top() << "\n"; // Outputs: 10

    // Remove the top element
    s.pop(); // Removes 10 -> Stack is now [5, 7]

    std::cout << "New top element: " << s.top() << "\n"; // Outputs: 7

    std::cout << "Stack size: " << s.size() << "\n";     // Outputs: 2
}
```

---

## 5. Printing / Traversing a Stack

Because a stack does not allow indexing or iterators, traversing or printing all elements requires extracting them one by one using a `while (!s.empty())` loop [06:41].

> **Tip:** If you do not want to destroy the original stack, pass a copy of the stack to the printing function (pass by value) [07:48].

```cpp
#include <iostream>
#include <stack>

// Passing by value creates a copy, preserving the original stack in main
void printStack(std::stack<int> s) {
    std::cout << "Stack elements (top to bottom): ";
    while (!s.empty()) {
        std::cout << s.top() << " "; // Read the top element
        s.pop();                     // Remove it to uncover the next
    }
    std::cout << "\n";
}

int main() {
    std::stack<int> myStack;

    for (int i = 1; i <= 5; ++i) {
        myStack.push(i * 10); // Pushes 10, 20, 30, 40, 50
    }

    printStack(myStack); // Output: 50 40 30 20 10

    // Original stack remains intact
    std::cout << "Original stack size: " << myStack.size() << "\n"; // Outputs: 5
}
```

---

## 6. Practical Use Cases

The stack container is commonly used in:

* **Expression Evaluation & Parsing:** Checking balanced parentheses (`()`, `{}`), syntax tree validation, or converting Infix to Postfix notation.
* **Backtracking Algorithms:** Depth-First Search (DFS), undo/redo action histories, and browser forward/back navigation stacks.
* **Call Stack Simulation:** Simulating recursive operations iteratively to prevent runtime call stack overflows.
