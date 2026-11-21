# Task 7 – From Procedural to Object-Oriented

## 1. Procedural Approach

In procedural programming, the balance is stored in a separate variable and functions are used to check and deduct the fare.

```cpp
#include <iostream>
using namespace std;

const long long MIN_BALANCE = 5000;

bool canTravel(long long balance, long long fare)
{
    return balance - fare >= MIN_BALANCE;
}

bool deduct(long long& balance, long long fare)
{
    if (fare < 0 || !canTravel(balance, fare))
        return false;

    balance -= fare;
    return true;
}

int main()
{
    long long balance = 10000;
    long long fare = 2000;

    if (deduct(balance, fare))
        cout << "Journey allowed. Balance: " << balance << '\n';
    else
        cout << "Journey denied.\n";
}
```

**Output:**
```text
Journey allowed. Balance: 8000
```

## 2. Object-Oriented Approach

In OOP, the `SmartCard` class encapsulates the balance as a private data member. Public member functions control access and prevent invalid deductions.

```cpp
#include <iostream>
using namespace std;

class SmartCard {
private:
    long long balance;
    static constexpr long long MIN_BALANCE = 5000;

public:
    SmartCard(long long b) : balance(b) {}

    bool canTravel(long long fare) const
    {
        return fare >= 0 && balance - fare >= MIN_BALANCE;
    }

    bool deduct(long long fare)
    {
        if (!canTravel(fare))
            return false;

        balance -= fare;
        return true;
    }

    long long getBalance() const
    {
        return balance;
    }
};

int main()
{
    SmartCard card(10000);

    if (card.deduct(2000))
        cout << "Journey allowed. Balance: "
             << card.getBalance() << '\n';
    else
        cout << "Journey denied.\n";
}
```

**Output:**
```text
Journey allowed. Balance: 8000
```

## 3. Comparison of Procedural and Object-Oriented Approaches

| Feature | Procedural | Object-Oriented |
|---|---|---|
| Data storage | Separate variables | Private class members |
| Operations | Free functions | Member functions |
| Encapsulation | Not directly enforced | Achieved using private members |
| Data protection | Depends on function usage | Controlled through public methods |
| Maintenance | Rules may be repeated | Rules are centralised in the class |

The OOP approach is more suitable because the balance and its operations are grouped together. If the minimum balance changes, it can be updated in one place inside the class.

## 4. Alternative Concept – Public Data Member

If the balance were public instead of private, any part of the program could directly modify it.

Example:

```cpp
class SmartCard {
public:
    long long balance;
};

int main()
{
    SmartCard card;
    card.balance = -500;
}
```

This allows an invalid negative balance without using the `deduct()` function.

**Complexity Analysis:**

- Both private and public data-member approaches require O(1) time for balance access and deduction.
- Both require O(1) space per card.
- There is no significant time or memory difference.
- However, a public balance breaks encapsulation and allows other parts of the program to violate the minimum-balance rule.

## Conclusion

The object-oriented approach using a private balance, `canTravel()` and `deduct()` is safer and easier to maintain. It protects the smart card from invalid balance modifications and ensures that at least ₹50 remains after every successful journey.
