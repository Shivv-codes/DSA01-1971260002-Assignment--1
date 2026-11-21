# Task 8 – B3: Virtual Functions vs Switch vs Exceptions: The Cost

## B3.1 – Correctness on Small Data (2 Marks)

The same 10 bills must be processed using:

1. Virtual functions through base-class pointers.
2. A single structure with a type field and a switch statement.

Both methods must produce the same total.

Example bill data:

| Bill type | Amount |
|---|---:|
| OutPatient | 1700 |
| InPatient | 47500 |
| DayCare | 15000 |
| Emergency | 5000 |

For these four example bills, the total is ₹69,200. The same data must be used for both implementations.

## B3.2 – Benchmark Program (2 Marks)

Use this C++ program to compare virtual dispatch and switch-based calculation.

```cpp
#include <iostream>
#include <vector>
#include <memory>
#include <chrono>
using namespace std;
using namespace chrono;

class Bill {
public:
    virtual double total() const = 0;
    virtual ~Bill() = default;
};

class OP : public Bill {
    double c, t;
public:
    OP(double a, double b) : c(a), t(b) {}
    double total() const override { return c + t; }
};

class IP : public Bill {
    double amount;
public:
    explicit IP(double a) : amount(a) {}
    double total() const override { return amount; }
};

struct SimpleBill {
    int type;
    double a, b;
};

double switchTotal(const SimpleBill& b) {
    switch (b.type) {
        case 1: return b.a + b.b;
        case 2: return b.a;
        default: return 0;
    }
}

int main() {
    const int sizes[] = {1000, 1000000, 10000000};

    for (int n : sizes) {
        vector<unique_ptr<Bill>> virtualBills;
        vector<SimpleBill> switchBills;

        virtualBills.reserve(n);
        switchBills.reserve(n);

        for (int i = 0; i < n; i++) {
            if (i % 2 == 0) {
                virtualBills.push_back(make_unique<OP>(500, 1200));
                switchBills.push_back({1, 500, 1200});
            } else {
                virtualBills.push_back(make_unique<IP>(47500));
                switchBills.push_back({2, 47500, 0});
            }
        }

        double sum1 = 0, sum2 = 0;

        auto start = steady_clock::now();
        for (const auto& b : virtualBills)
            sum1 += b->total();
        auto end = steady_clock::now();

        auto virtualTime = duration_cast<milliseconds>(end - start).count();

        start = steady_clock::now();
        for (const auto& b : switchBills)
            sum2 += switchTotal(b);
        end = steady_clock::now();

        auto switchTime = duration_cast<milliseconds>(end - start).count();

        cout << "Bills: " << n << '\n';
        cout << "Virtual total: " << sum1
             << " Time: " << virtualTime << " ms\n";
        cout << "Switch total: " << sum2
             << " Time: " << switchTime << " ms\n";
    }
}
```

Compile using:

```bash
g++ -std=c++17 -O2 -Wall -Wextra cost_comparison.cpp -o benchmark
```

Run:

```bash
./benchmark
```

## B3.3 – Cost of Exceptions (2 Marks)

The following code compares return-code validation with exception-based validation for one million claims.

```cpp
#include <iostream>
#include <stdexcept>
#include <chrono>
using namespace std;
using namespace chrono;

bool validateCode(double amount) {
    return amount >= 0;
}

bool validateException(double amount) {
    try {
        if (amount < 0)
            throw invalid_argument("negative");
        return true;
    } catch (const invalid_argument&) {
        return false;
    }
}

int main() {
    const int n = 1000000;

    for (int invalidPercent : {0, 1, 50}) {
        int invalidCount = n * invalidPercent / 100;
        volatile int accepted = 0;

        auto start = steady_clock::now();

        for (int i = 0; i < n; i++) {
            double amount = (i < invalidCount) ? -1 : 100;

            if (validateCode(amount))
                accepted++;
        }

        auto end = steady_clock::now();
        cout << invalidPercent << "% invalid, return code: "
             << duration_cast<milliseconds>(end - start).count()
             << " ms\n";

        accepted = 0;
        start = steady_clock::now();

        for (int i = 0; i < n; i++) {
            double amount = (i < invalidCount) ? -1 : 100;

            if (validateException(amount))
                accepted++;
        }

        end = steady_clock::now();
        cout << invalidPercent << "% invalid, exceptions: "
             << duration_cast<milliseconds>(end - start).count()
             << " ms\n";
    }
}
```

Compile using:

```bash
g++ -std=c++17 -O2 -Wall -Wextra cost_comparison.cpp -o benchmark
```

Run the benchmark several times and record the results.

**Do not enter invented timings.** Actual measurements depend on the processor, compiler, operating system and workload.

## B3.4 – Engineering Trade-Off (2 Marks)

| Method | Advantages | Limitations |
|---|---|---|
| Virtual functions | Extensible and supports runtime polymorphism | Virtual dispatch overhead |
| Switch statement | Simple and direct for a small fixed set | Must modify switch when adding a type |
| Exceptions | Centralized error handling | Expensive when failures occur frequently |
| Return codes | Efficient for frequent validation | Caller must check every result |

All three bill-processing approaches have O(n) overall time for n bills. The switch and virtual-call costs are O(1) per bill. Exception handling is generally suitable for unusual failures, not frequent invalid records.

## Alternative Concept – std::variant and std::visit

A `std::variant` can store one of several bill types in a single container.

```cpp
#include <variant>
#include <vector>

struct OutPatientBill {
    double consultation, tests;
};

struct InPatientBill {
    double room, surgery;
};

using BillData = variant<OutPatientBill, InPatientBill>;

double total(const BillData& b) {
    return visit([](const auto& x) -> double {
        if constexpr (is_same_v<decay_t<decltype(x)>, OutPatientBill>)
            return x.consultation + x.tests;
        else
            return x.room + x.surgery;
    }, b);
}
```

The total of n bills can be calculated in O(n). A variant stores enough space for its largest alternative plus a discriminator, with exact layout determined by the implementation.

`std::variant` provides a type-safe alternative for a fixed set of bill types, while virtual functions are more suitable when new derived types need to be introduced independently.
