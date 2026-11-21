# Task 1 – A1: Design the Polymorphic Bill Hierarchy

## A1.1 – Identify the Common Interface (2 Marks)

All hospital bills share the following information:

- Patient name or ID.
- Bill number.
- Common operations to calculate the total, identify the bill type and print bill details.

Every bill must provide:

1. `total()` – calculates the total bill amount.
2. `type()` – identifies the type of bill.
3. `print()` – displays the bill details.

## A1.2 – Design the Hierarchy (3 Marks)

The abstract base class `Bill` contains common patient information and pure virtual functions. The derived classes implement their own billing calculations.

### UML Class Diagram

```text
                  <<abstract>>
                      Bill
              ---------------------
              # patient
              # billNumber
              ---------------------
              + total() = 0
              + type() = 0
              + print()
              + virtual destructor
              ---------------------
                  /   |   |   \
                 /    |   |    \
                /     |   |     \
       OutPatient InPatient DayCare Emergency
```

### Function Classification

| Function | Classification |
|---|---|
| `total()` | Pure virtual |
| `type()` | Pure virtual |
| `print()` | Virtual |
| Destructor | Virtual |
| Derived `total()` | Overridden |
| Derived `type()` | Overridden |

## A1.3 – Process All Bills Together (2 Marks)

### SIMATS Standard Pseudocode

```text
ALGORITHM CalculateDailyTotal
INPUT bills, n
SET dailyTotal = 0

FOR i = 0 TO n - 1
    dailyTotal = dailyTotal + bills[i].total()
    bills[i].print()
END FOR

PRINT dailyTotal
END ALGORITHM
```

The array contains base-class pointers to different derived bill objects. Runtime polymorphism calls the correct `total()` function for each bill.

## Alternative Concept – Type Field and Switch

A single `Bill` structure could store a type code and use a switch statement:

```cpp
double total(const Bill& b) {
    switch (b.type) {
        case 1: return b.consultation + b.tests;
        case 2: return b.days * b.roomRate + b.procedures;
        case 3: return b.packagePrice;
        case 4: return b.baseCharge + b.timeCharge;
        default: return 0;
    }
}
```

| Comparison | Inheritance | Type field and switch |
|---|---|---|
| Processing n bills | O(n) | O(n) |
| One bill calculation | O(1) | O(1) |
| Object storage | Common and derived data | May contain unused fields |
| Adding a bill type | Add a derived class | Modify switch and related code |

**Conclusion:** The abstract base-class design is more extensible because new bill types can be added without changing existing billing classes.
