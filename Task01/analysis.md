# TASK 01 – Understand the Problem and Identify the Objects

## 1. Object-Oriented Analysis of the Metro Fare System

The Namma Metro Smart-Card Fare System is designed to calculate passenger fares according to the farthest zone travelled, apply peak-hour surcharges and passenger concessions, and deduct the fare from a stored-value smart card. The system must also ensure that the remaining card balance never falls below Rs 50 after a successful journey.

An object-oriented approach is suitable because the system contains identifiable entities with their own data and responsibilities. These entities can be represented as classes, combining their attributes and behaviour to improve data protection, organisation and maintainability.

### 1.1 Identification of Objects, Data and Behaviour

| Object / Class | Data (Attributes) | Behaviour (Methods / Responsibilities) |
|---|---|---|
| **SmartCard** | Card ID, stored balance in paise | Check available balance, validate payment, deduct fare and recharge the card |
| **Passenger** | Passenger ID, passenger category (regular, student or senior citizen) | Identify passenger category and determine concession eligibility |
| **Journey** | Origin, destination, farthest zone, journey time and calculated fare | Validate journey details, calculate journey fare and generate a receipt |
| **Zone** | Zone identifier: Inner, Middle or Outer | Represent a valid travel zone and provide the zone information required for fare selection |
| **FareTable** | Base fares, peak surcharge percentage and concession policy | Retrieve the applicable base fare and calculate the fare according to the defined rules |

**Justification of each object:**

1. **SmartCard:** The smart card stores the passenger's available monetary balance. It is responsible for checking whether a journey can be paid for and deducting the fare. Keeping the balance private prevents other parts of the program from modifying it directly and helps enforce the minimum remaining balance of Rs 50.

2. **Passenger:** Passenger information is maintained separately because concession eligibility depends on the passenger's category. This prevents passenger-related rules from being mixed with card balance operations and allows different passenger categories to be handled appropriately.

3. **Journey:** A journey represents an individual travel transaction. It stores the origin, destination, farthest zone and travel time required for fare calculation. Keeping these details together makes each transaction easier to validate and process.

4. **Zone:** The Zone representation identifies the valid travel categories: Inner, Middle and Outer. It helps the system select the correct fare and reject invalid zone values instead of accidentally assigning an incorrect fare.

5. **FareTable:** The FareTable centralises base fares, peak surcharge and concession rules. This avoids repeating fare values in different parts of the program and makes fare revisions easier to manage consistently.

### 1.2 Procedural Programming and Object-Oriented Programming

In a **procedural approach**, the system would use global variables or arrays to store card balances, passenger categories and fare values. Separate free functions would perform operations such as `calculateFare()`, `checkBalance()` and `deductFare()`. These functions would receive the required information as arguments and operate on the stored data.

In an **object-oriented approach**, the system is organised into classes such as `SmartCard`, `Passenger`, `Journey`, `Zone` and `FareTable`. Each class combines the data it manages with the operations related to that data.

For example, the `SmartCard` class manages its own balance and provides controlled methods for fare deduction, while the `FareTable` class manages the fare calculation rules.

The main difference is that procedural programming organises the solution around functions and shared data, whereas object-oriented programming organises it around objects that combine data and behaviour.

The object-oriented approach is more appropriate for this metro system because it separates responsibilities, protects important data and provides a structured way to extend the system when new zones, discounts or monthly passes are introduced.

### 1.3 Application of the Four Basic Principles of OOP

**1. Encapsulation**

Encapsulation combines data and the methods that operate on that data within a class. In the metro system, the `SmartCard` class can keep its balance as a private data member and provide public methods such as `deductFare()` and `recharge()`.

This prevents external functions from directly changing the balance and ensures that the minimum balance condition is checked before a fare is deducted.

**2. Abstraction**

Abstraction hides internal implementation details and exposes only the necessary operations. For example, the metro gate can call `card.deductFare(fare)` without knowing how the card checks the remaining balance or performs the deduction internally.

This reduces complexity because the gate needs to know what operation is performed, not every detail of how it is implemented.

**3. Inheritance**

Inheritance allows a derived class to reuse the common attributes and behaviour of a base class. A general `Passenger` class can contain common passenger information, while `StudentPassenger` and `SeniorPassenger` classes can inherit from it.

The derived classes can provide their respective concession eligibility rules while reusing the common passenger information.

**4. Polymorphism**

Polymorphism allows different classes to provide their own implementations of a common operation. For example, a virtual method named `calculateConcession()` can be declared in the `Passenger` class and overridden in `StudentPassenger` and `SeniorPassenger`.

The fare system can then process different passenger categories through a common interface while applying the appropriate concession behaviour.

### 1.4 Rules Every Correct Journey Must Satisfy

The following conditions must be checked to ensure that every journey is processed accurately and safely.

1. **Valid zone:** The journey must use one of the defined zones: Inner, Middle or Outer. An invalid zone must be rejected.

2. **Correct base fare:** The fare must depend on the farthest zone travelled:
   - Inner Zone – Rs 10
   - Middle Zone – Rs 20
   - Outer Zone – Rs 30

3. **Peak-hour surcharge:** A 20% surcharge must be applied during the specified peak periods: 8–10 AM and 5–8 PM. For consistent boundary handling, this design treats the starting time as inclusive and the ending time as exclusive. Therefore, exactly 10 AM and 8 PM are treated as off-peak.

4. **Minimum remaining balance:** A stored-value journey must be permitted only if the balance remaining after fare deduction is at least Rs 50.

5. **No negative balance:** The smart-card balance must never become negative.

6. **Failed journey:** If a journey fails validation or does not satisfy the minimum balance requirement, the card balance must remain unchanged.

7. **Valid recharge:** Negative recharge amounts must be rejected so that the stored balance cannot be reduced through an invalid recharge operation.

8. **Accurate monetary calculation:** All monetary calculations should use integer paise instead of floating-point rupee values to avoid rounding errors. One rupee is equal to 100 paise.

9. **Passenger concession:** Student and senior citizen concessions must be applied according to the approved concession policy.

**Justification:** These rules prevent incorrect fare deductions, invalid transactions and negative balances. They also ensure consistent fare calculation and protect the financial accuracy of the smart-card system.

### 1.5 Alternative Concept and Complexity Analysis

If the fare system were implemented using a purely procedural approach instead of classes such as `SmartCard`, `Journey` and `FareTable`, global variables or arrays could store card balances and fare values. Separate free functions would calculate fares, validate balances and perform deductions.

**Example of a procedural solution:**

```cpp
const int MIN_BALANCE = 5000; // Rs 50 in paise

int balances[1000];
int fares[3] = {1000, 2000, 3000};

// zone: 0 = Inner, 1 = Middle, 2 = Outer
int calculateFare(int zone) {
    return fares[zone];
}

bool canTravel(int balance, int fare) {
    return balance - fare >= MIN_BALANCE;
}

void deductFare(int cardIndex, int fare) {
    if (canTravel(balances[cardIndex], fare)) {
        balances[cardIndex] -= fare;
    }
}
```

This example uses integer paise for monetary values. The fare array stores the three base fares, while separate functions calculate the fare, check whether the minimum balance condition is satisfied and deduct the fare only when the condition is met. In a complete implementation, the zone index and other inputs would also be validated before accessing the arrays.

#### Complexity Comparison

| Operation | Procedural Approach | Object-Oriented Approach |
|---|---|---|
| Fare lookup | O(1) | O(1) |
| Balance validation | O(1) | O(1) |
| Fare deduction | O(1) | O(1) |
| Memory per card | O(1) | O(1) |

Both approaches have **O(1) time complexity per journey** because fare lookup, balance validation and deduction require a fixed number of operations. Similarly, the memory required to store the balance and basic information for one card is **O(1)** in both approaches.

Therefore, object-oriented programming does not automatically make fare calculation faster or reduce the asymptotic memory required per card.

#### Effect of Changing a Fare Rule

For example, suppose the minimum remaining balance is increased from Rs 50 to Rs 60.

In a procedural implementation, if the minimum balance is stored in one global constant and all relevant functions use that constant, the value can be changed in one place. However, if the rule is repeated directly in multiple functions, every occurrence must be identified and updated.

In an object-oriented implementation, the minimum-balance condition can be centralised within the `SmartCard` class. Updating that class's rule changes the validation behaviour for all cards that use it.

Similarly, changes to base fares can be centralised in the `FareTable` class rather than repeated throughout the program.

Thus, both approaches can be designed to require only a small change when a rule is centralised. The advantage of the object-oriented approach is that the relevant data and rules are organised within their responsible classes, improving encapsulation, consistency and maintainability as the system grows.

### 1.6 Conclusion and Assumptions

The object-oriented approach is suitable for the Namma Metro Smart-Card Fare System because it separates the responsibilities of cards, passengers, journeys, zones and fare calculations. It supports encapsulation, abstraction, inheritance and polymorphism while making the system easier to maintain and extend.

Although procedural and object-oriented approaches have the same O(1) time complexity per journey and O(1) memory requirement per card, the object-oriented design provides a clearer organisation of data and behaviour and better protection of important system rules.

**Assumptions:**

- The fare is determined by the farthest zone reached during the journey.
- Peak-hour intervals use an inclusive starting boundary and an exclusive ending boundary for consistent time validation.
- Monetary values are stored in integer paise, where 100 paise equals Rs 1.
- The assignment states that students and senior citizens receive concessions but does not specify the concession percentage. Therefore, the actual rate must be confirmed before implementation and is not assumed here.
