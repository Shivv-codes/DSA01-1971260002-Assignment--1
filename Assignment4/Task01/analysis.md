# Task 1 – Class Design Review

## 1.1 Diagnose the Design (2 Marks)

The existing payroll classes have the following design deficiencies:

1. **Duplicate fields:** `Professor` and `LabAssistant` repeat `name`, `id` and `basic`.
2. **Public data members:** Anyone can directly modify employee details, reducing data protection.
3. **Inconsistent naming:** `name` and `nm`, and `id` and `code`, represent the same information.
4. **Unused field:** `da` is declared but never used in salary calculation.
5. **Magic number:** The value `0.42` is directly written in the pay calculation instead of being a named constant.
6. **Repeated pay logic:** Professor and LabAssistant repeat the same basic salary calculation.
7. **Poor extensibility:** Adding a new staff category requires modifying multiple parts of the program.

## 1.2 Repaired Class Hierarchy (3 Marks)

A common abstract base class `Staff` stores shared employee information. Derived classes implement their own `pay()` function.

```cpp
#include <iostream>
#include <string>
using namespace std;

class Staff {
protected:
    string name;
    int id;
    double basic;

public:
    Staff(string n, int i, double b)
        : name(n), id(i), basic(b) {}

    virtual double pay() const = 0;

    virtual void display() const {
        cout << name << " " << id
             << " Salary: " << pay() << '\n';
    }

    virtual ~Staff() = default;
};

class Professor : public Staff {
    double research;

public:
    Professor(string n, int i, double b, double r)
        : Staff(n, i, b), research(r) {}

    double pay() const override {
        return basic + basic * 0.42 + research;
    }
};

class LabAssistant : public Staff {
public:
    LabAssistant(string n, int i, double b)
        : Staff(n, i, b) {}

    double pay() const override {
        return basic + basic * 0.42;
    }
};

class GuestFaculty : public Staff {
    double hours, rate;

public:
    GuestFaculty(string n, int i, double h, double r)
        : Staff(n, i, 0), hours(h), rate(r) {}

    double pay() const override {
        return hours * rate;
    }
};

int main() {
    Professor p("Arun", 101, 50000, 5000);
    LabAssistant l("Bala", 102, 30000);
    GuestFaculty g("Kavi", 103, 20, 1000);

    Staff* staff[] = {&p, &l, &g};

    for (Staff* s : staff)
        s->display();
}
```

### UML Class Diagram

```text
                 Staff
        -----------------------
        # name, id, basic
        + pay() = 0
        + display()
        -----------------------
             /    |    \
            /     |     \
           /      |      \
    Professor  LabAssistant  GuestFaculty
    ---------  ------------  ------------
    research                 hours, rate
    pay()                    pay()
    pay() 
```

## 1.3 Design Defence (1 Mark)

The hierarchy avoids duplicated employee details by storing common fields in the `Staff` base class. Each derived class overrides `pay()` according to its salary rules. A new staff category can be added by creating another derived class without modifying the existing classes. This improves reusability, maintainability and runtime polymorphism.

## Alternative Concept and Complexity Analysis

A single `Staff` class could store a type field and use a `switch` statement inside `pay()`.

| Design | Pay operation | Space per object |
|---|---|---|
| Inheritance | O(1) | Common and specific fields |
| Single class with switch | O(1) | May contain unused fields |

Both approaches have O(1) time per pay calculation. However, adding a new staff category in the switch design requires modifying the type definition, the `pay()` switch and possibly other condition checks. In inheritance, a new derived class can be added without changing existing classes.
