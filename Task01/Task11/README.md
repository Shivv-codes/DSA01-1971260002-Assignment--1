# Task 11 – Recommend and Defend the Final Design

**Marks: 4**

## 1. Recommended Data Types and Constants

The final metro fare system should use the following design:

| Data / Component | Recommended type | Reason |
|---|---|---|
| Fare and balance | `long long` | Stores money exactly in paise |
| Journey count | `long long` | Supports large network totals |
| Journey hour | `int` | Stores hours from 0 to 23 |
| Peak-hour status | `bool` | Stores true or false |
| Zone | `enum class Zone` | Provides type-safe zone values |
| Fare constants | `constexpr` | Defines values in one place |
| Card | `SmartCard` class | Protects balance using encapsulation |

The minimum remaining balance should be stored as a constant:

```cpp
constexpr long long MIN_BALANCE = 5000;
```

## 2. Fare Revision

All base fares and surcharge values should be maintained in one central fare table or set of constants.

When the metro changes a fare, the corresponding value can be updated in one place. This reduces errors and avoids changing multiple expressions throughout the program.

## 3. Limitations of the Design

- The original switch-based design requires modification when a new zone is added.
- Hard-coded fares require recompilation after fare revisions.
- The original design needs additional logic for new discounts and monthly passes.
- Input validation is necessary to prevent invalid zone, hour and recharge values.

## 4. Correctness for the Whole Network

The design uses `long long` for money accumulation, avoiding the 32-bit integer overflow identified in Task 10.

The fare calculation is constant time, O(1), for each journey. The balance check is also O(1). Therefore, the design can process a large number of journeys efficiently, including the 3,000,000 journeys per day specified in the problem.

## 5. Alternative Concept – Configuration File

Instead of storing fares directly in the program, the fare table can be read from a configuration file when the gate starts.

- **Time complexity:** O(z) to read z zone fares during startup.
- **Space complexity:** O(z) to store the fare values.
- **Advantage:** Fare revisions can be made by updating the configuration file without recompiling the program.
- **Limitation:** The program must validate the file and handle missing or incorrect values.

For a small fixed fare table, compile-time constants are simple and efficient. For a large metro network with frequent fare revisions, a configuration file is more flexible.

## 6. Final Recommendation

I recommend using a `SmartCard` class with private balance, `long long` for money, `enum class` for zones and `constexpr` constants for fixed rules. The original design is accurate and efficient for the three-zone system, but it should be modified when new zones, discounts and passes are introduced. The configuration-file approach can be adopted when fare revisions become frequent.
