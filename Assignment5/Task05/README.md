# Task 5 – A5: Design Recovery and Rethrowing

## A5.1 – Where to Catch (2 Marks)

Exceptions should be caught at the level that can take meaningful action.

1. **Validation layer:** Detects invalid charges and claims and throws the appropriate exception.
2. **Billing service layer:** Logs useful technical information and rethrows the same exception using `throw;`.
3. **Application layer:** Catches the exception and displays a clear message to the user.

### Catch-and-Rethrow Example

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

void validate(double amount) {
    if (amount < 0)
        throw invalid_argument("Negative amount");
}

void process(double amount) {
    try {
        validate(amount);
    } catch (const exception& e) {
        cerr << "Log: " << e.what() << '\n';
        throw;
    }
}

int main() {
    try {
        process(-100);
    } catch (const exception& e) {
        cout << "Billing failed: " << e.what() << '\n';
    }
}
```

`throw;` rethrows the currently handled exception without changing its original type.

## A5.2 – No Leaks on Errors (2 Marks)

**Stack unwinding** is the process in which local objects are destroyed in reverse construction order when an exception leaves a scope.

RAII (Resource Acquisition Is Initialization) connects resource ownership to object lifetime. Objects such as `std::vector`, `std::string` and `std::unique_ptr` automatically release their resources when destroyed.

### Predict the Output – Given Guard Program

```text
~f ~main caught 42
```

**Explanation:**

1. `Guard m("main")` is constructed.
2. `f()` creates `Guard g("f")`.
3. `f()` throws integer `42`.
4. Stack unwinding destroys `g`, printing `~f`.
5. The exception leaves the `try` block, so `m` is destroyed, printing `~main`.
6. The `catch(int x)` handler prints `caught 42`.
7. `not printed` never appears because the exception interrupts normal execution.

## Alternative Concept – Manual Memory Release

Without RAII, every catch block would need to release every allocated resource manually.

For example:

```cpp
int* p = new int[100];

try {
    // Operations that may throw
} catch (...) {
    delete[] p;
    throw;
}
```

This becomes difficult when several resources and multiple error paths exist.

| Comparison | RAII | Manual deletion |
|---|---|---|
| Processing time | Same overall work | Same overall work |
| Resource release | Automatic during unwinding | Must be written in every required path |
| Code size | Smaller | Larger |
| Leak risk | Lower | Higher |

**Conclusion:** RAII is safer because resource cleanup happens automatically even when exceptions interrupt normal execution.
