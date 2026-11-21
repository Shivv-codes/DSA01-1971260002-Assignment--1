# Task 7 – B2: Engineer Error Conditions

## 1. Error Conditions (5 Marks)

The billing system must be tested with the following invalid inputs:

| Test | Input | Expected behaviour |
|---|---|---|
| Negative charge | `-500` | Throw `invalid_argument` |
| Claim above cover | Billed 60000, cover 50000 | Throw `ClaimError` |
| Unknown package | Invalid package code | Throw `PackageError` |
| Invalid number text | `"abc"` passed to `stod` | Throw `invalid_argument` |
| Memory allocation failure | Impossible allocation request | Catch `bad_alloc` |

## 2. Predict the Output – Given Program

```text
ok done caught negative [log too large] caught too large
```

**Explanation:**

- `100.0` is valid, so `validate()` prints `ok` and `main()` prints `done`.
- `-5.0` throws `invalid_argument("negative")`. It is caught by the outer `std::exception` handler.
- `2e6` throws `out_of_range("too large")`. The inner handler logs the error and rethrows it using `throw;`.
- The outer handler catches the rethrown exception.

Only the `out_of_range` exception is logged because `process()` specifically catches that exception type.

## 3. Complete the Given Code

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

class ClaimError : public runtime_error {
public:
    using runtime_error::runtime_error;
};

double approve(double billed, double cover) {
    if (billed < 0 || cover < 0)
        throw invalid_argument("negative amount");

    if (billed > cover)
        throw ClaimError("exceeds cover");

    return billed;
}

double settle(double billed, double cover) {
    try {
        return approve(billed, cover);
    } catch (const ClaimError& e) {
        cout << "audit: " << e.what() << '\n';
        throw;
    }
}

int main() {
    double cases[][2] = {
        {40000, 50000},
        {60000, 50000},
        {-1, 50000}
    };

    for (auto& c : cases) {
        try {
            double paid = settle(c[0], c[1]);
            cout << "approved " << paid << '\n';
        } catch (const ClaimError& e) {
            cout << "claim rejected: " << e.what() << '\n';
        } catch (const exception& e) {
            cout << "error: " << e.what() << '\n';
        }
    }
}
```

### Expected Output

```text
approved 40000
audit: exceeds cover
claim rejected: exceeds cover
error: negative amount
```

## 4. Difference Between throw; and throw e;

- `throw;` rethrows the currently handled exception and preserves its original type.
- `throw e;` throws a new exception object based on the static type of `e`. This can cause slicing when a derived exception is caught through a base-class reference.

Therefore, `throw;` is preferred when the same exception must be propagated.

## 5. Additional Error Tests

```cpp
#include <iostream>
#include <string>
#include <stdexcept>
#include <new>
using namespace std;

int main() {
    try {
        double amount = stod("abc");
        cout << amount;
    } catch (const invalid_argument& e) {
        cout << "Invalid number: " << e.what() << '\n';
    }

    try {
        int* p = new int[1000000000000000000ULL];
        delete[] p;
    } catch (const bad_alloc& e) {
        cout << "Allocation failed\n";
    } catch (const exception& e) {
        cout << "Allocation error: " << e.what() << '\n';
    }
}
```

An extremely large allocation request is not guaranteed to produce `bad_alloc` on every system; it may be rejected or handled differently by the operating system. Record the actual result.

A top-level `catch(...)` can prevent an uncaught exception from terminating the program, but it should not hide errors without logging or recovery.

## 6. Alternative Concept – std::optional

Instead of throwing exceptions for invalid claims, a function could return `std::optional<double>`. A missing value indicates rejection.

For one million claims:

| Invalid claims | Return-code/optional approach | Exception approach |
|---|---|---|
| 0% | Normal validation | Normal validation |
| 1% | Returns rejection status | Throws for invalid claims |
| 50% | Returns rejection status | Frequent exception handling |

Both approaches perform O(n) work for n claims. Exceptions can be expensive when many invalid claims occur because stack unwinding is required. `optional` stores a value or an empty state, with a small additional representation whose exact size is implementation-dependent.

**Conclusion:** Exceptions are appropriate for exceptional failures, while return values or `optional` are often preferable for frequent expected validation failures.
