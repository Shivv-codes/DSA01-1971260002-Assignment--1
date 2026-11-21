# Task 9 – Implement and Test the Fare Logic

**Marks: 4**

## 1. C++ Implementation

The program uses integer paise to avoid floating-point errors. Peak hours are 8:00–10:00 AM and 5:00–8:00 PM. A journey is accepted only if at least ₹50 remains after deducting the fare.

```cpp
#include <iostream>
#include <iomanip>
#include <string>
#include <sstream>
using namespace std;

const long long MIN_BALANCE = 5000;

long long getFare(int zone, int hour) {
    if (hour < 0 || hour > 23) return -1;

    long long fare;
    switch (zone) {
        case 1: fare = 1000; break;
        case 2: fare = 2000; break;
        case 3: fare = 3000; break;
        default: return -1;
    }

    bool peak = (hour >= 8 && hour < 10) ||
                (hour >= 17 && hour < 20);

    if (peak) fare += fare * 20 / 100;
    return fare;
}

bool travel(int zone, int hour, long long balance,
            long long &fare) {
    fare = getFare(zone, hour);

    if (fare < 0 || balance - fare < MIN_BALANCE)
        return false;

    return true;
}

bool recharge(long long &balance, long long amount) {
    if (amount < 0) return false;
    balance += amount;
    return true;
}

int main() {
    cout << fixed << setprecision(2);

    cout << "FARE TEST RESULTS\n";
    cout << "Zone  Hour  Balance  Fare  Result\n";

    int zones[] = {1, 2, 3, 1, 2, 1, 1, 1};
    int hours[] = {9, 14, 17, 10, 20, 8, 10, 14};
    long long balances[] = {
        20000, 20000, 20000, 20000,
        20000, 6200, 20000, 20000
    };

    long long fare;

    for (int i = 0; i < 8; i++) {
        bool ok = travel(zones[i], hours[i],
                         balances[i], fare);

        cout << zones[i] << "     " << hours[i] << "     "
             << balances[i] / 100.0 << "    ";

        if (fare >= 0)
            cout << fare / 100.0 << "   ";
        else
            cout << "Invalid  ";

        cout << (ok ? "Accepted" : "Rejected") << '\n';
    }

    long long balance = 10000;
    cout << "\nFIVE JOURNEY TEST\n";

    int z[] = {1, 2, 3, 1, 2};
    int h[] = {9, 10, 17, 20, 14};
    long long total = 0;

    for (int i = 0; i < 5; i++) {
        if (travel(z[i], h[i], balance, fare)) {
            balance -= fare;
            total += fare;
        }
    }

    cout << "Total fare: Rs " << total / 100.0 << '\n';
    cout << "Final balance: Rs " << balance / 100.0 << '\n';

    cout
