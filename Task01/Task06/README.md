# Task 6 – Design the Control Structures

## 1. Selection of Control Structures

The fare system uses different control structures for decision-making and processing journeys.

- **if–else:** Used to check peak hours, validate journeys and verify whether the remaining balance is at least ₹50.
- **switch:** Used to select the base fare according to the zone (Inner, Middle or Outer).
- **Conditional operator (?:):** Can be used for simple decisions, such as applying a surcharge during peak hours.
- **for loop:** Suitable for processing a fixed collection of journeys.
- **Range-based for loop:** Preferred when processing every journey in an array because it is simple and readable.
- **while loop:** Useful when journeys are entered continuously until a stopping condition is reached.
- **break:** Terminates a loop or switch when required.
- **continue:** Skips an invalid journey and proceeds to the next one.

A default case in a switch handles unknown or unmatched zone values.

## 2. SIMATS Standard Algorithmic Pseudocode

**Algorithm: Calculate Metro Fare**

**Input:** Zone, peak-hour status and smart-card balance.

**Output:** Fare and updated balance or journey denial.

1. START
2. INPUT zone, peak status and balance.
3. SET baseFare = 0.
4. SELECT zone:
   - CASE Inner: baseFare = 1000 paise.
   - CASE Middle: baseFare = 2000 paise.
   - CASE Outer: baseFare = 3000 paise.
   - DEFAULT: DISPLAY "Invalid zone" and STOP.
5. IF peak status is TRUE:
   - surcharge = baseFare × 20 / 100.
   - fare = baseFare + surcharge.
   ELSE:
   - fare = baseFare.
6. IF balance − fare is greater than or equal to 5000 paise:
   - balance = balance − fare.
   - DISPLAY fare and updated balance.
   ELSE:
   - DISPLAY "Denied (low balance)".
7. STOP.

## 3. Completed C++ Program

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

enum class Zone { Inner, Middle, Outer };

constexpr int BASE_PAISE[] = {1000, 2000, 3000};
constexpr int PEAK_SURCHARGE_PERCENT = 20;
constexpr long long MIN_BALANCE_PAISE = 5000;

long long fare(Zone z, bool peak)
{
    long long base = 0;

    switch (z) {
        case Zone::Inner:  base = BASE_PAISE[0]; break;
        case Zone::Middle: base = BASE_PAISE[1]; break;
        case Zone::Outer:  base = BASE_PAISE[2]; break;
        default: return -1;
    }

    if (peak)
        base += base * PEAK_SURCHARGE_PERCENT / 100;

    return base;
}

bool canTravel(long long balance, long long f)
{
    return f >= 0 && balance - f >= MIN_BALANCE_PAISE;
}

int main()
{
    long long balance = 10000;

    struct Trip {
        Zone zone;
        bool peak;
    };

    Trip trips[] = {
        {Zone::Inner, false},
        {Zone::Outer, true},
        {Zone::Middle, false}
    };

    for (const Trip& t : trips) {
        long long f = fare(t.zone, t.peak);

        if (canTravel(balance, f)) {
            balance -= f;
            cout << "Fare Rs " << f / 100 << '.'
                 << setw(2) << setfill('0') << f % 100
                 << setfill(' ');
        } else {
            cout << "Denied (low balance)";
        }

        cout << " balance Rs " << balance / 100 << '\n';
    }
}
```

## 4. Expected Output

```text
Fare Rs 10.00 balance Rs 90
Fare Rs 36.00 balance Rs 54
Denied (low balance) balance Rs 54
```

**Explanation:**

- Inner zone off-peak fare = ₹10. Balance = ₹90.
- Outer zone peak fare = ₹30 + 20% surcharge = ₹36. Balance = ₹54.
- Middle zone fare = ₹20. The remaining balance would be ₹34, which is below ₹50. Therefore, the journey is denied.

## 5. Peak-Hour Boundary Testing

Peak hours are from 8 AM to before 10 AM and from 5 PM to before 8 PM.

| Time | Expected result | Reason |
|---|---|---|
| 8:00 AM | Peak | Start time included |
| 10:00 AM | Off-peak | End time excluded |
| 5:00 PM | Peak | Start time included |
| 8:00 PM | Off-peak | End time excluded |

Correct condition:

```cpp
bool peak = (hour >= 8 && hour < 10) ||
            (hour >= 17 && hour < 20);
```

## 6. Alternative Concept – Array Lookup Instead of Switch

The base fare can also be selected using an array indexed by the zone.

```cpp
constexpr long long FARE[] = {1000, 2000, 3000};

long long base = FARE[static_cast<int>(z)];
```

| Factor | Switch statement | Array lookup |
|---|---|---|
| Time complexity | O(1) | O(1) |
| Space | No additional fare array lookup storage | O(1), three fare entries |
| Selection | Multiple cases | Direct indexing |
| Fourth zone | Add another case | Add another array entry |

Both methods provide constant-time fare selection. An array makes fare revisions and adding zones easier, while a switch provides explicit handling for each zone and a default case.

**Conclusion:** A range-based for loop, switch statement and if–else conditions provide clear and reliable control flow. Integer paise calculations prevent floating-point rounding errors, while the minimum-balance condition prevents invalid journeys.
