# Q4 – Specifying a Class with an Array Inside It

## 1. SavingsAccount Class

The class stores the account number, balance, ten recent transactions and the transaction count. All data members are private to protect account information.

```cpp
#include <iostream>
using namespace std;

class SavingsAccount {
    int accountNumber;
    double balance;
    double transactions[10];
    int count;

public:
    SavingsAccount(int acc, double amount)
        : accountNumber(acc), balance(amount), count(0) {}

    void deposit(double amount) {
        if (amount <= 0) return;
        balance += amount;
        addTransaction(amount);
    }

    void withdraw(double amount);

    void addTransaction(double amount) {
        if (count < 10) {
            transactions[count++] = amount;
        } else {
            for (int i = 1; i < 10; i++)
                transactions[i - 1] = transactions[i];

            transactions[9] = amount;
        }
    }

    void miniStatement() const {
        cout << "Account: " << accountNumber << '\n';
        cout << "Balance: " << balance << '\n';
        cout << "Recent transactions:\n";

        for (int i = 0; i < count; i++)
            cout << transactions[i] << '\n';
    }
};

void SavingsAccount::withdraw(double amount) {
    if (amount <= 0 || amount > balance) return;

    balance -= amount;
    addTransaction(-amount);
}

int main() {
    SavingsAccount account(101, 5000);

    account.deposit(1000);
    account.withdraw(500);
    account.deposit(200);

    account.miniStatement();
}
```

## 2. Member Functions

- `deposit()` adds money to the balance.
- `withdraw()` deducts money if sufficient balance is available.
- `miniStatement()` displays the account number, balance and stored transactions.
- `addTransaction()` stores the latest transaction.

Some functions are defined inside the class, while `withdraw()` is defined outside using the scope resolution operator `SavingsAccount::`.

## 3. Handling the 11th Transaction

When the 11th transaction arrives, the oldest transaction is discarded.

**Array shifting:**
- Each transaction is moved one position to the left.
- The newest transaction is inserted at the last position.
- Time complexity: O(k), where k is the number of stored transactions.

**Circular buffer:**
- Uses a start index to identify the oldest transaction.
- The next transaction overwrites the oldest one.
- Insertion takes O(1) time.

## 4. Alternative – Deque and Circular Buffer

A `std::deque` can use `push_back()` and `pop_front()` to maintain the latest transactions. A circular buffer uses a fixed array and an index to overwrite the oldest transaction.

| Design | Insert transaction | Space |
|---|---|---|
| Shifting array | O(k) | O(k) |
| Deque | O(1) amortized | O(k) |
| Circular buffer | O(1) | O(k) |

## 5. Design Decision – Last 50 Transactions

For 50 transactions, I recommend a circular buffer. The capacity can be changed from 10 to 50, and new transactions can be stored in constant time without shifting all existing entries.

The private array protects transaction data from direct modification by other parts of the program.
