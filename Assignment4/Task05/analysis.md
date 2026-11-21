# Task 5 – Pointers to Objects and the this Pointer

## 5.1 Four Ways to Access a Cart Object (3 Marks)

1. Directly using the object name.
2. Through a pointer using `->`.
3. Through a reference.
4. Through a dynamically allocated pointer using `new` and `delete`.

## Complete C++ Program

```cpp
#include <iostream>
#include <iomanip>
#include <string>
using namespace std;

class Cart {
    string items[10];
    double prices[10];
    int n = 0;
    double discount = 0;

public:
    Cart& add(string item, double price) {
        if (n < 10 && price >= 0) {
            items[n] = item;
            prices[n] = price;
            n++;
        }
        return *this;
    }

    Cart& setDiscount(double discount) {
        this->discount = discount;
        return *this;
    }

    double total() const {
        double t = 0;

        for (int i = 0; i < n; ++i)
            t += prices[i];

        return t * (100 - discount) / 100;
    }
};

int main() {
    cout << fixed << setprecision(2);

    // 1. Direct object
    Cart c;
    c.add("Rice", 60);

    // 2. Pointer using ->
    Cart* p = &c;
    p->add("Dal", 120);

    // 3. Reference
    Cart& ref = c;
    ref.add("Oil", 180);

    // 4. Dynamic object
    Cart* dynamicCart = new Cart;
    dynamicCart->add("Milk", 50);
    cout << "Dynamic cart: " << dynamicCart->total() << '\n';
    delete dynamicCart;

    // Method chaining
    c.setDiscount(10);
    cout << "Main cart: " << c.total() << '\n';
}
```

## Expected Output

```text
Dynamic cart: 50.00
Main cart: 324.00
```

## 5.2 Find the Cart with the Highest Total (2 Marks)

An array of pointers can store carts belonging to different customers.

```cpp
Cart c1, c2, c3;

c1.add("Rice", 60);
c2.add("Laptop", 500);
c3.add("Milk", 100);

Cart* carts[] = {&c1, &c2, &c3};

Cart* highest = carts[0];

for (int i = 1; i < 3; i++) {
    if (carts[i]->total() > highest->total())
        highest = carts[i];
}

cout << "Highest total: " << highest->total() << '\n';
```

Expected output:

```text
Highest total: 500.00
```

## 5.3 Role of the this Pointer (2 Marks)

- `this` is a pointer to the current object.
- `*this` represents the current object itself.
- `this->discount` accesses the class member when the parameter has the same name.
- Returning `*this` by reference allows method chaining.

If `add()` returned `Cart` instead of `Cart&`, it would return a copy of the object. Chained calls could operate on temporary copies instead of modifying the original cart.

## Comparison of Four Access Methods

| Method | Safety | Lifetime |
|---|---|---|
| Direct object | Safe when in scope | Scope-based |
| Pointer | Can be null or dangling | Depends on pointed object |
| Reference | Must refer to a valid object | Depends on referenced object |
| Dynamic pointer | Requires careful ownership | Until `delete` |

## Alternative Concept – vector of Objects

A `std::vector<Cart>` can store cart objects directly instead of storing pointers.

Finding the highest total still takes O(n) time for n carts. A vector stores the objects themselves, while an array of pointers stores only addresses.

When a vector grows and reallocates its storage, pointers and references to its elements may become invalid. Therefore, long-lived pointers into a growing vector must be handled carefully.
