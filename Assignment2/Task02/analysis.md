# Q2 – Return by Reference and Its Dangers

## 1. Program Using Return by Reference

A function can return a reference to an array element. This allows the caller to modify the original element directly.

```cpp
#include <iostream>
using namespace std;

int& highest(int marks[], int n) {
    int index = 0;

    for (int i = 1; i < n; i++)
        if (marks[i] > marks[index])
            index = i;

    return marks[index];
}

int& dangerous() {
    int x = 50;
    return x;  // Dangling reference
}

int main() {
    int marks[] = {75, 89, 92, 80};

    cout << "Highest: " << highest(marks, 4) << '\n';

    highest(marks, 4) += 2;

    cout << "After moderation: "
         << highest(marks, 4) << '\n';
}
```

## 2. Expected Output

```text
Highest: 92
After moderation: 94
```

The returned reference refers directly to the highest array element. Therefore, adding 2 modifies the original array.

## 3. Returning a Local Variable

The function `dangerous()` returns a reference to its local variable `x`. The variable is destroyed when the function ends. Therefore, the returned reference becomes dangling.

Using this reference causes undefined behaviour. A compiler may issue a warning about returning a reference to a local variable.

## 4. Reference, Const Reference and Copy

| Method | Behaviour |
|---|---|
| `int&` | Allows modification of the original element |
| `const int&` | Allows reading but prevents modification through the reference |
| `int` | Returns an independent copy |

## 5. Changed Condition – Vector

When a `std::vector` grows and reallocates memory, references and pointers to its old elements may become invalid. Therefore, a reference obtained before vector growth should not be used after a possible reallocation.

## 6. Alternative Concept – Returning an Index or Pointer

**Returning an index:**

```cpp
int highestIndex(const int marks[], int n) {
    int index = 0;
    for (int i = 1; i < n; i++)
        if (marks[i] > marks[index])
            index = i;
    return index;
}
```

Usage:

```cpp
marks[highestIndex(marks, 4)] += 2;
```

**Returning a pointer:** The function can return `int*` pointing to the highest array element, and the caller can modify it using `*pointer`.

## 7. Complexity Analysis

| Method | Finding maximum | Extra space |
|---|---|---|
| Reference | O(n) | O(1) |
| Index | O(n) | O(1) |
| Pointer | O(n) | O(1) |

Returning an index is a safer option when the underlying container may grow because the index can be checked again after reallocation. A pointer or reference may become invalid.
