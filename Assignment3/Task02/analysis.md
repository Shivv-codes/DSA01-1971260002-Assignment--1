
# Q2 – Copy Constructors: Shallow and Deep Copies

## 1. Problem Explanation

A `Prescription` class stores medicine names in a dynamically allocated array.

A shallow copy copies only the pointer address. Therefore, both objects refer to the same memory. Modifying one object affects the other, and both destructors may attempt to delete the same memory.

A deep copy allocates separate memory and copies every medicine name, making the objects independent.

## 2. Complete C++ Program – Deep Copy

```cpp
#include <iostream>
#include <string>
using namespace std;

class Prescription {
    int n;
    string* medicines;

public:
    explicit Prescription(int count)
        : n(count), medicines(new string[count]) {}

    Prescription(const Prescription& p)
        : n(p.n), medicines(new string[p.n]) {
        for (int i = 0; i < n; i++)
            medicines[i] = p.medicines[i];
    }

    Prescription& operator=(const Prescription& p) {
        if (this != &p) {
            string* temp = new string[p.n];

            for (int i = 0; i < p.n; i++)
                temp[i] = p.medicines[i];

            delete[] medicines;
            medicines = temp;
            n = p.n;
        }
        return *this;
    }

    ~Prescription() {
        delete[] medicines;
    }

    void set(int i, const string& m) {
        if (i >= 0 && i < n)
            medicines[i] = m;
    }

    string get(int i) const {
        return medicines[i];
    }
};

int main() {
    Prescription p(2);
    p.set(0, "Paracetamol");
    p.set(1, "ORS");

    Prescription q = p;
    q.set(1, "Cetirizine");

    Prescription r(1);
    r = p;

    p.set(0, "Ibuprofen");

    cout << p.get(0) << ' ' << p.get(1) << " | "
         << q.get(0) << ' ' << q.get(1) << " | "
         << r.get(0) << ' ' << r.get(1) << '\n';
}
```

## 3. Expected Output

```text
Ibuprofen ORS | Paracetamol Cetirizine | Paracetamol ORS
```

The three objects have independent medicine arrays. Changes to `p` do not affect `q` or `r`.

## 4. Memory Diagrams

**Shallow copy:**

```text
Original p ─────┐
                ├──> Same medicine array
Copied q ───────┘
```

Both objects share the same dynamically allocated array. This can cause unintended changes and double deletion.

**Deep copy:**

```text
Original p ───────> Medicine Array 1

Copied q ─────────> Medicine Array 2

Assigned r ───────> Medicine Array 3
```

Each object owns separate memory.

## 5. Rule of Three

The Rule of Three states that if a class requires a user-defined destructor, copy constructor or copy assignment operator, it generally needs all three.

- **Copy constructor:** Creates an independent copy.
- **Copy assignment:** Replaces an existing object's data safely.
- **Destructor:** Releases dynamically allocated memory.

The copy constructor is called when:
1. An object is initialized from another object.
2. An object is passed by value to a function.
3. An object is returned by value when a copy is actually performed.

## 6. Self-Assignment

The condition:

```cpp
if (this != &p)
```

prevents an object from deleting its own data before copying it during an assignment such as `p = p`.

## 7. Alternative Concept – Vector

Using `std::vector<std::string>` automatically manages memory and supports deep copying.

```cpp
#include <vector>
#include <string>
using namespace std;

class Prescription {
    vector<string> medicines;
public:
    Prescription(int n) : medicines(n) {}
};
```

The compiler-generated copy operations work correctly because `vector` manages its own memory. This follows the Rule of Zero.

## 8. Complexity Analysis

For n medicine names:

| Method | Copy time | Additional space |
|---|---|---|
| Shallow copy | O(1) | O(1) |
| Deep copy | O(n) | O(n) |
| Vector copy | O(n) | O(n) |

Deep copying and vector copying require time proportional to the number of medicines. Shallow copying is faster but unsafe for independently owned dynamic memory.

## 9. Experimental Investigation

Compile using AddressSanitizer:

```bash
g++ -std=c++17 -Wall -Wextra -fsanitize=address -g main.cpp -o prescription
```

Run the program:

```bash
./prescription
```

Test both versions:
- Without a copy constructor, check for shared memory and double-free errors.
- With deep copying, verify independent values and no memory errors.
- Test `p = p`.
- Add a copy-constructor message to count copies when passing by value and by `const` reference.

Record the actual sanitizer report and copy timings for 10, 1,000 and 100,000 medicines.

## 10. Conclusion

A compiler-generated copy constructor is suitable when the class uses members that already manage their own resources, such as `std::string` and `std::vector`. When a class directly owns dynamically allocated memory, a deep-copying constructor, assignment operator and destructor are required.
