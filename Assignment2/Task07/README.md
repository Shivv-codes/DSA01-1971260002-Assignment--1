# Q7 – Diagnose, Redesign and Validate

## Selected Program: SavingsAccount Mini-Statement (Q4)

### A. Diagnose the Limitation – 2 Marks

The original SavingsAccount class stores the last 10 transactions in a fixed array.

When the 11th transaction arrives, the program shifts all existing transactions one position to the left to remove the oldest transaction.

This creates a limitation because every new transaction may require moving several array elements.

For k stored transactions:
- Insertion using shifting: O(k).
- Storage: O(k).
- Increasing the history from 10 to 50 requires changing the array capacity and related logic.

### B. Define the Design Objective – 2 Marks

The objective is to redesign transaction storage so that:
1. The latest 50 transactions are retained.
2. The oldest transaction is automatically overwritten when the array is full.
3. Adding a transaction takes O(1) time.
4. The account balance and transaction data remain private.

### C. Design the Revised Solution – 4 Marks

I will use a circular buffer with a fixed array of 50 transactions.

The `next` index identifies the position where the next transaction will be stored. After insertion, the index moves forward and wraps around to zero when it reaches the capacity.

**Revised class design:**

```text
SavingsAccount
 |
 |-- Private:
 |    accountNumber
 |    balance
 |    transactions[50]
 |    count
 |    next
 |
 |-- Public:
      deposit()
      withdraw()
      addTransaction()
      miniStatement()
```

**Revised C++ Program:**

```cpp
#include <iostream>
using namespace std;

class SavingsAccount {
    int accountNumber;
    double balance;
    double transactions[50];
    int count;
    int next;

public:
    SavingsAccount(int acc, double amount)
        : accountNumber(acc), balance(amount),
          count(0), next(0) {}

    void addTransaction(double amount) {
        transactions[next] = amount;
        next = (next + 1) % 50;

        if (count < 50)
            count++;
    }

    void deposit(double amount) {
        if (amount <= 0) return;

        balance += amount;
        addTransaction(amount);
    }

    void withdraw(double amount) {
        if (amount <= 0 || amount > balance)
            return;

        balance -= amount;
        addTransaction(-amount);
    }

    void miniStatement() const {
        cout << "Account: " << accountNumber << '\n';
        cout << "Balance: " << balance << '\n';
        cout << "Recent transactions:\n";

        int start = (count == 50) ? next : 0;

        for (int i = 0; i < count; i++) {
            int index = (start + i) % 50;
            cout << transactions[index] << '\n';
        }
    }
};

int main() {
    SavingsAccount account(101, 5000);

    for (int i = 1; i <= 55; i++)
        account.deposit(100);

    account.miniStatement();

    return 0;
}
```

### D. Predict Before Implementing – 1 Mark

For 55 deposits of ₹100:
- Initial balance = ₹5,000.
- Total deposited = ₹5,500.
- Final balance = ₹10,500.
- Only the latest 50 transactions are stored.
- The first five transactions are overwritten.

The mini-statement should display 50 transactions, each worth ₹100.

### E. Implement and Validate the Redesign – 2 Marks

The original and redesigned versions can be tested using the same 55 deposits.

| Measurement | Original shifting array | Circular buffer |
|---|---:|---:|
| Capacity | 10 transactions | 50 transactions |
| Insertion when full | O(k) | O(1) |
| Storage | O(k) | O(k) |
| Transactions retained after 55 deposits | 10 | 50 |
| Balance after 55 deposits | ₹10,500 | ₹10,500 |

The redesigned version retains more transaction history and avoids shifting elements during insertion.

### F. Defend the Final Design – 2 Marks

The circular buffer is preferable because it:
- Stores the latest 50 transactions.
- Overwrites the oldest transaction automatically.
- Performs insertion in O(1) time.
- Uses fixed memory without dynamic allocation.
- Keeps account data private through encapsulation.

**Trade-offs:**
- The circular buffer requires an additional index.
- Displaying transactions in chronological order requires index calculations.
- The original shifting array is simpler when the history is very small.

## Final Conclusion

The original fixed-array design is suitable for a small transaction history, but shifting becomes inefficient as the capacity grows. The circular-buffer redesign improves insertion from O(k) to O(1), retains the latest 50 transactions and maintains the same account balance. Therefore, the redesigned solution better satisfies the expanded mini-statement requirement.
