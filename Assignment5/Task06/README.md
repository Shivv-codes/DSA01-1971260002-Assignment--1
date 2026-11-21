# Task 6 – B1: Implement and Validate Polymorphic Billing

## B1.1 – Complete the Code (2 Marks)

The following program implements four bill types: OutPatient, InPatient, DayCare and Emergency.

```cpp
#include <iostream>
#include <string>
using namespace std;

class Bill {
protected:
    string patient;

public:
    explicit Bill(const string& p) : patient(p) {}

    virtual double total() const = 0;

    virtual string type() const {
        return "Bill";
    }

    void print() const {
        cout << type() << " " << patient
             << ": " << total() << '\n';
    }

    virtual ~Bill() = default;
};

class OutPatient : public Bill {
    double consultation, tests;

public:
    OutPatient(const string& p, double c, double t)
        : Bill(p), consultation(c), tests(t) {}

    double total() const override {
        return consultation + tests;
    }

    string type() const override {
        return "OP";
    }
};

class InPatient : public Bill {
    int days;
    double perDay, surgery;

public:
    InPatient(const string& p, int d, double rate, double s)
        : Bill(p), days(d), perDay(rate), surgery(s) {}

    double total() const override {
        return days * perDay + surgery;
    }

    string type() const override {
        return "IP";
    }
};

class DayCare : public Bill {
    double packagePrice;

public:
    DayCare(const string& p, double price)
        : Bill(p), packagePrice(price) {}

    double total() const override {
        return packagePrice;
    }

    string type() const override {
        return "DayCare";
    }
};

class Emergency : public Bill {
    double baseCharge, timeCharge;

public:
    Emergency(const string& p, double base, double time)
        : Bill(p), baseCharge(base), timeCharge(time) {}

    double total() const override {
        return baseCharge + timeCharge;
    }

    string type() const override {
        return "Emergency";
    }
};

int main() {
    Bill* bills[] = {
        new OutPatient("Ravi", 500, 1200),
        new InPatient("Meena", 3, 2500, 40000),
        new DayCare("Arun", 15000),
        new Emergency("Kavi", 3000, 2000)
    };

    double sum = 0;

    for (Bill* b : bills) {
        b->print();
        sum += b->total();
    }

    cout << "Total " << sum << '\n';

    for (Bill* b : bills)
        delete b;
}
```

### Expected Output

```text
OP Ravi: 1700
IP Meena: 47500
DayCare Arun: 15000
Emergency Kavi: 5000
Total 69200
```

## B1.2 – Compare with Design (1 Mark)

The implementation follows the abstract `Bill` hierarchy designed in A1. Each derived class overrides `total()` and `type()`. The base pointer array processes different bill objects using runtime polymorphism.

## B1.3 – Prove Runtime Binding (2 Marks)

If `virtual` is removed from `total()`, the base-class function is no longer polymorphic. In this program, `total()` is pure virtual, so removing `virtual` while retaining `= 0` causes a compilation error.

If a normal base implementation is provided instead, calls through `Bill*` use the base implementation rather than the derived calculation.

## B1.4 – Prove the Virtual Destructor (2 Marks)

The base destructor is:

```cpp
virtual ~Bill() = default;
```

If it is made non-virtual, deleting derived bill objects through `Bill*` results in undefined behaviour. Run the program with AddressSanitizer and compare destructor messages. The exact sanitizer report depends on the compiler and runtime.

## Alternative Concept – Templates

Bills could be stored in separate containers and processed using templates.

```cpp
template <typename T>
double calculateTotal(const T& bill) {
    return bill.total();
}
```

Template-based processing avoids virtual dispatch, but separate containers are needed for different bill types. It becomes harder to keep all bill types in one heterogeneous list.

| Comparison | Virtual functions | Templates |
|---|---|---|
| Per-bill calculation | O(1) | O(1) |
| Runtime dispatch | Yes | No |
| Storage | One base-pointer list | Separate typed containers |
| Heterogeneous list | Directly supported | Requires additional design |


