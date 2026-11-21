# Task 4 – Pointers to Derived Classes: Forensics

## 4.1 Locate the Defects (2 Marks)

The given bank account program contains three major defects:

1. **Non-virtual function:** `rate()` is not virtual in `Account`, so calling it through an `Account*` uses the base-class implementation and returns 3.5 even for a `SeniorAccount`.
2. **Non-virtual destructor:** Deleting a derived object through a base pointer when the base destructor is non-virtual causes undefined behaviour. The derived destructor may not run, leaving its dynamically allocated `history` array unreleased.
3. **Object slicing:** `Account copy = SeniorAccount();` copies only the base-class part. The derived-class information is lost.

## 4.2 Predict the Output

For the supplied prediction program:

```text
3.5 4 3.5
```

**Explanation:**
- `p->rate()` calls the non-virtual base function, so it returns 3.5.
- `p->vrate()` calls the virtual function at runtime, so it invokes `SeniorAccount::vrate()` and returns 4.0.
- `copy.vrate()` returns 3.5 because `copy` is a separate base-class object created by slicing.

## 4.3 Repaired C++ Program (2 Marks)

```cpp
#include <iostream>
#include <memory>
#include <vector>
using namespace std;

class Account {
public:
    virtual double rate() const {
        return 3.5;
    }

    virtual ~Account() {
        cout << "Account destroyed\n";
    }
};

class SeniorAccount : public Account {
    double* history;

public:
    SeniorAccount() : history(new double[100]{}) {}

    double rate() const override {
        return 4.0;
    }

    ~SeniorAccount() override {
        delete[] history;
        cout << "SeniorAccount destroyed\n";
    }
};

int main() {
    vector<unique_ptr<Account>> accounts;

    accounts.push_back(make_unique<Account>());
    accounts.push_back(make_unique<SeniorAccount>());

    for (const auto& a : accounts)
        cout << a->rate() << '\n';
}
```

## 4.4 Expected Output

```text
3.5
4
Account destroyed
SeniorAccount destroyed
Account destroyed
```

The senior account destructor runs first, followed by the base destructor. The ordinary account only invokes its own destructor.

## 4.5 Prevent Recurrence (1 Mark)

1. A base class used polymorphically should have a virtual destructor.
2. Use `override` in derived classes to ensure the intended virtual function is correctly overridden.

## Alternative Concept – unique_ptr

Using `std::unique_ptr<Account>` inside a vector automatically releases objects when the vector is destroyed. This avoids manually deleting raw pointers.

| Design | Processing n accounts | Memory management |
|---|---|---|
| Raw pointers | O(n) | Manual deletion |
| `unique_ptr` | O(n) | Automatic destruction |

Both approaches require O(n) time to process n accounts. Smart pointers prevent forgotten deletion, but they do not automatically fix a non-virtual base destructor or object slicing. A virtual destructor and pointer-based polymorphic storage are still required.
