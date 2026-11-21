# Task 9 – B4: Final Engineering Decision

## B4.1 – Implement a Practical Extension (2 Marks)

The hospital now requires a new bill type called `DiagnosticBill` and a new payer called `CorporatePayer`.

Both can be added without changing the existing bill and payer classes.

### New Bill Type

```cpp
#include <string>
using namespace std;

class DiagnosticBill : public Bill {
    double scanCharge;
    double labCharge;

public:
    DiagnosticBill(const string& p, double scan, double lab)
        : Bill(p), scanCharge(scan), labCharge(lab) {}

    double total() const override {
        return scanCharge + labCharge;
    }

    string type() const override {
        return "Diagnostic";
    }
};
```

### New Payer Type

```cpp
class CorporatePayer : public Payer {
    double coverLimit;
    double copayPercent;

public:
    CorporatePayer(double limit, double copay)
        : coverLimit(limit), copayPercent(copay) {}

    double amountCovered(const Bill& b) const override {
        double eligible = min(b.total(), coverLimit);
        return eligible * (100 - copayPercent) / 100;
    }
};
```

### Demonstration

```cpp
DiagnosticBill diagnostic("Ravi", 5000, 3000);
CorporatePayer corporate(10000, 10);

cout << diagnostic.type() << ": "
     << diagnostic.total() << '\n';

cout << "Corporate covered: "
     << corporate.amountCovered(diagnostic) << '\n';
```

Expected output:

```text
Diagnostic: 8000
Corporate covered: 7200
```

The new bill and payer are added by creating derived classes. Existing classes remain unchanged.

## B4.2 – Check Resource Safety (1 Mark)

Compile the billing program with AddressSanitizer:

```bash
g++ -std=c++17 -Wall -Wextra -fsanitize=address -g billing_system.cpp -o billing
```

Run:

```bash
./billing
```

Test normal billing, invalid claims and exceptions. Verify that:

- All dynamically allocated objects are destroyed.
- Base-class pointers correctly invoke derived destructors.
- No memory leaks are reported.
- No use-after-free or double-free errors occur.

Attach the actual sanitizer output. A clean report must be obtained by executing the program; it should not be assumed.

## B4.3 – Production Decision (2 Marks)

The polymorphic design with exception handling is suitable for the hospital group because:

1. It supports different bill types through a common abstract interface.
2. Runtime polymorphism allows bills to be processed through base-class pointers.
3. New bill and payer classes can be added without changing existing classes.
4. Exceptions report invalid charges, rejected claims and missing records clearly.
5. RAII and virtual destructors help prevent resource leaks.

The system processes 200,000 bills daily across 15 hospitals. Therefore, performance testing, input validation, logging and memory-safety testing are important before production deployment.

The final decision should be supported by actual benchmark results from Task B3. The implementation should be accepted for production only after correctness, load and sanitizer tests pass.

## Alternative Concept – Configuration-Based Plug-ins

Instead of writing a new C++ class for every bill or payer type, the hospital could load billing rules from configuration files.

Example:

```text
BillType: DayCare
PackageRate: 15000

Payer: Corporate
CoverLimit: 100000
CoPayment: 10
```

The program reads these rules and calculates the amount using a common rule-processing engine.

| Comparison | C++ polymorphism | Configuration-based rules |
|---|---|---|
| Calculation time | O(1) per bill | Depends on rule lookup and interpretation |
| Extension | Add a derived class | Add or update configuration |
| Rule storage | Object data and class code | Configuration data |
| Flexibility | Requires recompilation for new classes | Rules may change without recompilation |
| Risk | Programming errors | Invalid or incorrectly configured rules |

Configuration-based rules are useful for changing rates and package prices. However, complex new billing behaviour may still require new C++ code.

## Final Engineering Conclusion

The abstract bill hierarchy, payer hierarchy, runtime polymorphism and exception-handling strategy provide a maintainable design for the hospital billing system. New bill types and payers can be added independently, while RAII and virtual destructors support safe resource management. The system is a suitable foundation for production after successful performance, validation and sanitizer testing.
