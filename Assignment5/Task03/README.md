# Task 3 – A3: Design with Abstract Classes

## A3.1 – Payer Interface (3 Marks)

An abstract class `Payer` provides a common interface for calculating the amount covered by different payment methods.

### Class Design

```text
                  <<abstract>>
                      Payer
              ---------------------
              + amountCovered(Bill&) = 0
              ---------------------
                  /      |       \
                 /       |        \
             SelfPay  Insurance  GovernmentScheme
                       |              |
                  coverLimit      packageRates
                  coPayment
```

### C++ Class Declarations

```cpp
#include <algorithm>
#include <string>
#include <stdexcept>
using namespace std;

class Bill {
public:
    virtual double total() const = 0;
    virtual string type() const = 0;
    virtual ~Bill() = default;
};

class Payer {
public:
    virtual double amountCovered(const Bill& b) const = 0;
    virtual ~Payer() = default;
};

class SelfPay : public Payer {
public:
    double amountCovered(const Bill& b) const override {
        return b.total();
    }
};

class Insurance : public Payer {
    double coverLimit;
    double coPaymentPercent;

public:
    Insurance(double limit, double copay)
        : coverLimit(limit), coPaymentPercent(copay) {
        if (limit < 0 || copay < 0 || copay > 100)
            throw invalid_argument("Invalid insurance policy");
    }

    double amountCovered(const Bill& b) const override {
        double eligible = min(b.total(), coverLimit);
        return eligible * (100 - coPaymentPercent) / 100;
    }
};

class GovernmentScheme : public Payer {
    double packageRate;

public:
    explicit GovernmentScheme(double rate)
        : packageRate(rate) {}

    double amountCovered(const Bill&) const override {
        return packageRate;
    }
};
```

## A3.2 – Extension (2 Marks)

A new corporate payer can be added by deriving another class from `Payer`.

```cpp
class CorporatePayer : public Payer {
    double discountPercent;

public:
    explicit CorporatePayer(double d) : discountPercent(d) {}

    double amountCovered(const Bill& b) const override {
        return b.total() * (100 - discountPercent) / 100;
    }
};
```

The existing `Payer`, `SelfPay`, `Insurance` and `GovernmentScheme` classes do not need modification.

## Alternative Concept – Function Pointers or std::function

Instead of creating a payer class hierarchy, a policy function can calculate the covered amount.

```cpp
#include <functional>

using Policy = function<double(const Bill&)>;

Policy corporatePolicy = [](const Bill& b) {
    return b.total() * 0.90;
};
```

| Comparison | Abstract Payer | Function object |
|---|---|---|
| Call time | O(1) | O(1) |
| Policy data | Stored in derived object | Captured or stored in function object |
| Extension | Add derived class | Add a new function |
| Reuse | Common class interface | Flexible callable interface |

**Conclusion:** The abstract payer hierarchy is suitable when different payer types have their own data and business rules. Function objects are useful for simpler interchangeable policies.
