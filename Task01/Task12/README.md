# Task 12 – Re-evaluate and Adapt the Design

**Marks: 8**

## Changed Requirements

The metro introduces:
- A fourth zone, Airport, with a base fare of ₹60.
- A 10% weekend discount that does not combine with the 20% peak surcharge.
- A monthly pass that provides unlimited journeys in two selected zones.

**Design decision: MODIFY the original design.**

## A. Impact Analysis – 2 Marks

| Original component | Required modification |
|---|---|
| Zone constants | Add `Airport` to the zone enumeration |
| Fare constants | Add Airport fare of ₹60 |
| Fare lookup | Update the switch or use a fare array |
| Peak expression | Retain the existing peak-hour condition |
| Discount expression | Add a separate weekend discount rule |
| `SmartCard` class | Retain private balance and balance validation |
| Monthly pass | Introduce a `MonthlyPass` class |

The original three-zone fare logic is insufficient because it does not include Airport, weekend discounts or monthly passes.

## B. Evaluation of the Original Design – 2 Marks

The original switch-based fare logic can be extended by adding another case for Airport. However, every new zone requires modifying the switch statement.

The original `SmartCard` class can still manage balance and fare deduction, but it needs to work with the new fare calculation and monthly pass.

**Worked examples:**

1. Airport off-peak weekday journey = ₹60.
2. Airport peak weekday journey = ₹60 + 20% = ₹72.
3. Airport weekend journey = ₹60 − 10% = ₹54.
4. A weekend journey during peak hours still costs ₹54 because the weekend discount does not combine with the peak surcharge.
5. A passenger with a valid monthly pass for Inner and Middle zones travels in those zones without paying an individual journey fare.

Therefore, the original design can be extended, but modification is necessary.

## C. Adaptation / Redesigned Implementation – 2 Marks

The revised design uses a fare array, a separate weekend discount rule and a `MonthlyPass` class.

```cpp
#include <iostream>
#include <array>
using namespace std;

enum class Zone { Inner, Middle, Outer, Airport };

constexpr long long MIN_BALANCE = 5000;
constexpr array<long long, 4> FARES = {
    1000, 2000, 3000, 6000
};

bool isPeak(int hour) {
    return (hour >= 8 && hour < 10) ||
           (hour >= 17 && hour < 20);
}

long long calculateFare(Zone zone, int hour, bool weekend) {
    int index = static_cast<int>(zone);

    if (index < 0 || index >= 4 || hour < 0 || hour > 23)
        return -1;

    long long fare = FARES[index];

    if (weekend)
        fare -= fare * 10 / 100;
    else if (isPeak(hour))
        fare += fare * 20 / 100;

    return fare;
}

class MonthlyPass {
    Zone first, second;
    bool active;

public:
    MonthlyPass(Zone a, Zone b, bool valid)
        : first(a), second(b), active(valid) {}

    bool covers(Zone zone) const {
        return active && (zone == first || zone == second);
    }
};

class SmartCard {
    long long balance;

public:
    SmartCard(long long amount) : balance(amount) {}

    bool travel(Zone zone, int hour, bool weekend,
                const MonthlyPass &pass) {
        if (pass.covers(zone)) {
            cout << "Pass journey: No fare deducted\n";
            return true;
        }

        long long fare = calculateFare(zone, hour, weekend);

        if (fare < 0 || balance - fare < MIN_BALANCE) {
            cout << "Journey rejected\n";
            return false;
        }

        balance -= fare;
        cout << "Fare: Rs " << fare / 100.0
             << " Balance: Rs " << balance / 100.0 << '\n';
        return true;
    }
};

int main() {
    SmartCard card(20000);

    MonthlyPass pass(Zone::Inner, Zone::Middle, true);

    card.travel(Zone::Airport, 14, false, pass);
    card.travel(Zone::Airport, 9, false, pass);
    card.travel(Zone::Airport, 9, true, pass);
    card.travel(Zone::Inner, 12, false, pass);

    return 0;
}
```

## D. Evidence-Based Defence – 2 Marks

The revised design supports all four zones and separates the weekend discount from the peak surcharge.

| Test | Expected fare / result |
|---|---|
| Airport, weekday off-peak | ₹60 |
| Airport, weekday peak | ₹72 |
| Airport, weekend peak | ₹54 |
| Inner zone with valid pass | No fare deducted |
| Middle zone with valid pass | No fare deducted |
| Outer zone without pass | Normal fare calculation |

**Advantages:**
- A fare array makes adding another zone easier.
- Weekend and peak rules are separated, preventing both discounts from being applied together.
- The `MonthlyPass` class keeps pass information separate from card balance.
- The `SmartCard` class continues to protect the minimum balance.

**Trade-offs:**
- The fare array must be updated when a zone is added.
- The pass requires validity and expiry-date management in a real system.
- The program needs additional checks for invalid zone and time values.

## Final Decision

The original design should be **modified rather than completely redesigned**. The revised system retains the useful `SmartCard` class, introduces an array-based fare table, applies weekend discounts separately from peak surcharges and adds a `MonthlyPass` class. This design is more flexible and can accommodate the new metro requirements.
