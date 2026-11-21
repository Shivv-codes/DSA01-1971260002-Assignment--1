# Q3 – Dynamic Constructors and Destructors

## 1. Dynamic Seat Map Implementation

A dynamic constructor allocates memory according to the number of rows and seats provided when an event is created. The destructor releases all allocated memory.

```cpp
#include <iostream>
using namespace std;

class Event {
    int rows, seats;
    char** map;
    int booked;

public:
    Event(int r, int s) : rows(r), seats(s), map(nullptr), booked(0) {
        if (r < 0 || s < 0)
            throw invalid_argument("Invalid dimensions");

        if (rows > 0 && seats > 0) {
            map = new char*[rows];

            int i = 0;
            try {
                for (; i < rows; i++)
                    map[i] = new char[seats]();
            } catch (...) {
                while (i > 0)
                    delete[] map[--i];
                delete[] map;
                throw;
            }
        }

        cout << "Event created\n";
    }

    bool book(int r, int s) {
        if (r < 0 || r >= rows || s < 0 || s >= seats)
            return false;

        if (map[r][s] == 'B')
            return false;

        map[r][s] = 'B';
        booked++;
        return true;
    }

    bool cancel(int r, int s) {
        if (r < 0 || r >= rows || s < 0 || s >= seats)
            return false;

        if (map[r][s] != 'B')
            return false;

        map[r][s] = '\0';
        booked--;
        return true;
    }

    ~Event() {
        for (int i = 0; i < rows; i++)
            delete[] map[i];

        delete[] map;
        cout << "Event closed. Booked: " << booked << '\n';
    }
};

int main() {
    Event e1(2, 3);

    e1.book(0, 1);
    e1.book(1, 2);
    e1.cancel(0, 1);

    {
        Event e2(1, 2);
        e2.book(0, 0);
    }

    Event* e3 = new Event(2, 2);
    e3->book(1, 1);
    delete e3;

    Event e4(0, 0);

    return 0;
}
```

## 2. Object Creation and Destruction

- **Stack object:** Its destructor runs automatically when it goes out of scope.
- **Block-scope object:** Its destructor runs when the block ends.
- **Heap object:** Its destructor runs only when `delete` is called.

Objects are destroyed in the reverse order of their construction within the same scope.

## 3. Destructor Rules

- A destructor has the same name as the class, preceded by `~`.
- It takes no arguments.
- It has no return type.
- It cannot be overloaded.
- It releases resources owned by the object.

For a dynamically allocated two-dimensional array, every row must be released using `delete[]`, followed by the row-pointer array.

Forgetting `delete` causes a memory leak. Using `delete` instead of `delete[]` for an array results in undefined behaviour.

## 4. Memory Layout

```text
Event object
 |
 |-- rows
 |-- seats
 |-- booked
 |
 |-- map
      |
      |-- Row 0 --> [ ][B][ ]
      |
      |-- Row 1 --> [ ][ ][B]
```

The row-pointer array and each row are dynamically allocated.

## 5. Predict the Output – Given Logger Program

```text
+A +B -B copyAc use -Ac +P -P end -A
```

**Explanation:**
1. `a` is created and prints `+A`.
2. `b` is created inside the block and prints `+B`.
3. At the end of the block, `b` is destroyed and prints `-B`.
4. `use(a)` passes `a` by value, creating a copy named `Ac`.
5. The function prints `use`.
6. The copied object is destroyed after the function call and prints `-Ac`.
7. The dynamically allocated object `P` is created and prints `+P`.
8. `delete p` destroys it and prints `-P`.
9. The program prints `end`.
10. Finally, `a` is destroyed and prints `-A`.

## 6. Alternative Concept – Contiguous Memory

Instead of allocating each row separately, the seat map can be allocated as one contiguous block.

The element at row `r` and seat `s` can be accessed using:

```cpp
map[r * seats + s]
```

A `std::vector<std::vector<char>>` is another alternative.

| Design | Number of allocations | Access time |
|---|---:|---|
| Row pointers | rows + 1 | O(1) |
| One contiguous block | 1 | O(1) |
| Vector of vectors | Multiple internal allocations | O(1) |

A contiguous block uses fewer allocations and avoids storing a separate row-pointer array.

## 7. Memory Analysis

Assuming one byte per seat:

| Seat map | Number of seats | Approximate seat storage |
|---|---:|---:|
| 10 × 20 | 200 | 200 bytes |
| 100 × 200 | 20,000 | 20,000 bytes |
| 1,000 × 2,000 | 2,000,000 | 2,000,000 bytes |

Actual memory usage is higher due to pointers, allocation metadata and object overhead.

## 8. Zero Rows and RAII

An event with zero rows should allocate no seat map. Its destructor must safely release the resources without accessing invalid memory.

RAII (Resource Acquisition Is Initialization) connects resource ownership to object lifetime. The constructor acquires memory and the destructor releases it automatically, reducing memory leaks.

## 9. Experimental Investigation

Compile with:

```bash
g++ -std=c++17 -Wall -Wextra -fsanitize=address -g main.cpp -o event
```

Run tests for:
- Stack, block and heap objects.
- Booking and cancelling seats.
- Zero rows.
- Correct deletion.
- A deliberately missing `delete` to observe the sanitizer report.

Record actual sanitizer output and memory measurements from execution.
