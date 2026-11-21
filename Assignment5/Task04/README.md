# Task 4 – A4: Design the Exception Strategy

## A4.1 – Identify the Errors (2 Marks)

The hospital billing system may encounter these errors:

| Error | Exception |
|---|---|
| Negative consultation or room charge | `BillingError` |
| Insurance claim exceeds policy cover | `ClaimError` |
| Unknown government package | `PackageError` |
| Missing patient record | `PatientNotFoundError` |
| Invalid numeric input | `std::invalid_argument` |
| Value outside allowed range | `std::out_of_range` |
| Memory allocation failure | `std::bad_alloc` |

### Exception Hierarchy

```text
              std::exception
                    |
             std::runtime_error
                    |
                BillingError
               /     |      \
        ClaimError PackageError PatientNotFoundError
```

### C++ Exception Classes

```cpp
#include <stdexcept>
using namespace std;

class BillingError : public runtime_error {
public:
    using runtime_error::runtime_error;
};

class ClaimError : public BillingError {
public:
    using BillingError::BillingError;
};

class PackageError : public BillingError {
public:
    using BillingError::BillingError;
};

class PatientNotFoundError : public BillingError {
public:
    using BillingError::BillingError;
};
```

## A4.2 – Specify Exceptions (2 Marks)

| Function | May throw? | Reason |
|---|---|---|
| `calculateBill()` | Yes | Invalid charges |
| `approveClaim()` | Yes | Claim exceeds cover |
| `findPatient()` | Yes | Patient not found |
| `loadPackage()` | Yes | Unknown package |
| Constructor allocating memory | Yes | Allocation failure |
| Destructor | No | Must release resources safely |
| `display()` | Preferably no | Should not perform risky operations |

`noexcept` should be used only when a function is designed not to throw.

Example:

```cpp
~Bill() noexcept = default;
```

Destructors should not throw because an exception during stack unwinding can cause `std::terminate()`, ending the program.

## Alternative Concept – Return Codes

Errors can instead be represented using an enumeration:

```cpp
enum class Status {
    Success,
    NegativeCharge,
    ClaimExceeded,
    UnknownPackage
};
```

A function can return a status and use an output parameter for the result.

| Comparison | Exceptions | Return codes |
|---|---|---|
| Normal path | No explicit error checks at every call | Caller checks status |
| Error path | Stack unwinding and handler search | Return status to caller |
| Extra storage | Exception runtime information | Status and possibly error message |
| Code | Centralized catch blocks | Explicit checks at each call |

Both approaches can have O(1) normal-path work for a fixed calculation. Exceptions are useful for exceptional failures; return codes are useful for frequent, expected validation outcomes.
