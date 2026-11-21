# Q4 – Overloading Arithmetic and Stream Operators

## 1. Money Class Implementation

The `Money` class stores the complete amount in integer paise using `long long`. This avoids floating-point errors during money calculations.

```cpp
#include <iostream>
#include <iomanip>
using namespace std;

class Money {
    long long paise;

public:
    Money(long long rupees = 0, int p = 0)
        : paise(rupees * 100 + p) {}

    Money operator+(const Money& m) const {
        return Money(0, paise + m.paise);
    }

    Money operator-(const Money& m) const {
        return Money(0, paise - m.paise);
    }

    Money& operator+=(const Money& m) {
        paise += m.paise;
        return *this;
    }

    bool operator==(const Money& m) const {
        return paise == m.paise;
    }

    bool operator<(const Money& m) const {
        return paise < m.paise;
    }

    friend ostream& operator<<(ostream& out, const Money& m) {
        long long amount = m.paise;
        if (amount < 0) {
            out << "-Rs ";
            amount = -amount;
        } else {
            out << "Rs ";
        }

        out << amount / 100 << '.'
            << setw(2) << setfill('0') << amount % 100;
        return out;
    }
};

int main() {
    Money a(120, 75), b(79, 25);
    Money c = a + b;

    cout << c << '\n';
    cout << boolalpha << (c == Money(200)) << '\n';

    Money x(0, 75), y(0, 50);
    cout << x + y << '\n';

    cout << Money(10) - Money(15) << '\n';
}
```

## 2. Expected Output

```text
Rs 200.00
true
Rs 1.25
-Rs 5.00
```

The addition of ₹0.75 and ₹0.50 carries 25 paise into one rupee, producing ₹1.25.

## 3. Operator Overloading Explanation

- `+` returns a new `Money` object without changing the original operands.
- `-` returns a new object representing the difference.
- `+=` modifies the current object and returns a reference to `*this`.
- `==` compares two amounts.
- `<` compares which amount is smaller.
- `<<` displays the amount in rupees and paise.

## 4. Why Is `<<` a Friend Function?

The left operand of `cout << amount` is an `ostream` object, not a `Money` object. Therefore, `<<` must be implemented as a non-member function.

It is declared as a friend so that it can access the private `paise` member.

## 5. Operator Translation

The expression:

```cpp
Money c = a + b;
```

is equivalent to:

```cpp
Money c = a.operator+(b);
```

## 6. Negative Subtraction

The implementation allows negative results, which can represent a credit or negative balance. In a real billing system, negative amounts should be rejected if the business rules do not permit them.

## 7. Alternative Concept – Non-Member Operators

Operators can also be implemented as friend functions. Named functions such as `add(a,b)` and `equals(a,b)` can be used instead.

| Design | Time per operation | Extra space |
|---|---|---|
| Member operators | O(1) | O(1) |
| Friend operators | O(1) | O(1) |
| Named functions | O(1) | O(1) |

A non-member operator can support expressions such as `100 + m` when an appropriate overload for the left operand is provided. A member operator of `Money` naturally supports `m + 100` only if the conversion from `100` to `Money` is permitted.

## 8. Engineering Decision

Multiplication of one `Money` object by another is generally not meaningful because the result would have squared currency units. Multiplication by a numeric quantity, such as the number of items, is more appropriate.

For accurate billing, integer-paise storage and overloaded arithmetic operators provide readable and reliable calculations.


