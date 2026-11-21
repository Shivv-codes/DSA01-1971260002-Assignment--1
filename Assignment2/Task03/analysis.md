# Q3 – Default Arguments and Function Overloading

## 1. Reason for Ambiguous Call

The following functions create ambiguity:

```cpp
charge(double weight);
charge(double weight, bool express);
charge(double weight, bool express = false,
       bool insured = false);
```

A call such as `charge(2.0)` can match more than one function because the third function also has default arguments. The compiler cannot select a unique overload.

## 2. Redesigned Function Set

The following design uses default arguments for common options and overloading for different input types.

```cpp
#include <iostream>
using namespace std;

struct Parcel {
    double weight;
};

double charge(double kg, bool express = false,
              bool insured = false) {
    double amount = kg * 50;

    if (express) amount += 30;
    if (insured) amount += 20;

    return amount;
}

double charge(int grams) {
    return charge(grams / 1000.0);
}

double charge(Parcel p) {
    return charge(p.weight);
}

int main() {
    Parcel p{2.5};

    cout << charge(2) << '\n';
    cout << charge(2.0f) << '\n';
    cout << charge(2.0, true) << '\n';
    cout << charge(2.0, false, true) << '\n';
    cout << charge(2500) << '\n';
    cout << charge(p) << '\n';
}
```

## 3. Predicted Function Selection

| Call | Selected function | Reason |
|---|---|---|
| `charge(2)` | `charge(int)` | Exact match |
| `charge(2.0f)` | `charge(double, bool, bool)` | Float converts to double |
| `charge(2.0, true)` | `charge(double, bool, bool)` | Default value used for insured |
| `charge(2.0, false, true)` | `charge(double, bool, bool)` | All arguments supplied |
| `charge(2500)` | `charge(int)` | Exact match |
| `charge(p)` | `charge(Parcel)` | Exact object match |

## 4. Expected Output

```text
100
100
130
120
125
125
```

## 5. Overload Resolution

The compiler generally prefers:
1. Exact match.
2. Promotion.
3. Standard conversion.

Default arguments are supplied from the right. Therefore, a function cannot omit a middle argument while supplying a later defaulted argument.

## 6. Alternative Concept – Parcel Structure

Instead of several overloads, one `Parcel` structure can store weight, express delivery and insurance options.

This reduces the number of overloaded functions and makes adding new options easier, but requires the caller to construct or populate a Parcel object.

## 7. Complexity Analysis

| Approach | Time per charge | Space |
|---|---|---|
| Overloading with defaults | O(1) | O(1) |
| Parcel structure | O(1) | O(1) |
| Function template | O(1) for this calculation | Depends on parameter type |

Overload resolution occurs at compile time and adds no runtime cost. The redesigned overload set avoids ambiguous calls while supporting different weight representations.
