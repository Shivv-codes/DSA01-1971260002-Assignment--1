# TASK 02 – Use Tokens, Keywords, Identifiers and Constants Correctly

## 1. Defining Fares, Surcharge and Limits

The metro fare system uses fixed fare values and operating rules. These values should be declared centrally so that any revision can be made in one place without searching through multiple sections of the program.

C++ provides `constexpr` for defining compile-time constants. It is preferred for the metro fare table because the values are known before program execution, can be checked by the compiler and can be used in constant expressions.

### 1.1 Centralised Constants and Zone Definition

```cpp
#include <array>

// Strongly typed representation of metro zones
enum class Zone {
    Inner = 0,
    Middle = 1,
    Outer = 2
};

// Centralised fare and system constants (all monetary values in paise)
constexpr int PAISE_PER_RUPEE = 100;

constexpr std::array<int, 3> BASE_FARE_PAISE = {
    10 * PAISE_PER_RUPEE,
    20 * PAISE_PER_RUPEE,
    30 * PAISE_PER_RUPEE
};

constexpr int PEAK_SURCHARGE_PERCENT = 20;
constexpr int MIN_REMAINING_BALANCE_PAISE = 50 * PAISE_PER_RUPEE;

// Peak-hour boundaries
constexpr int MORNING_PEAK_START_HOUR = 8;
constexpr int MORNING_PEAK_END_HOUR = 10;
constexpr int EVENING_PEAK_START_HOUR = 17;
constexpr int EVENING_PEAK_END_HOUR = 20;
```

The base fares are stored in integer paise to avoid floating-point rounding errors. The constants represent:

| Constant | Value | Purpose |
|---|---:|---|
| `PAISE_PER_RUPEE` | 100 | Converts rupees into paise |
| `BASE_FARE_PAISE[0]` | 1000 paise | Inner Zone fare (Rs 10) |
| `BASE_FARE_PAISE[1]` | 2000 paise | Middle Zone fare (Rs 20) |
| `BASE_FARE_PAISE[2]` | 3000 paise | Outer Zone fare (Rs 30) |
| `PEAK_SURCHARGE_PERCENT` | 20 | Peak-hour surcharge |
| `MIN_REMAINING_BALANCE_PAISE` | 5000 paise | Minimum balance after a journey |
| `MORNING_PEAK_START_HOUR` | 8 | Morning peak starts at 8 AM |
| `MORNING_PEAK_END_HOUR` | 10 | Morning peak ends at 10 AM |
| `EVENING_PEAK_START_HOUR` | 17 | Evening peak starts at 5 PM |
| `EVENING_PEAK_END_HOUR` | 20 | Evening peak ends at 8 PM |

**Justification:** Centralising these values avoids duplication and makes maintenance easier. For example, if the Inner Zone fare changes, its value can be revised in the fare table without changing every function that uses it.

The assignment does not specify the exact concession percentage for students and senior citizens. Therefore, no unsupported concession rate is included in this set of constants.

## 2. The Five Types of C++ Tokens

A token is the smallest individual unit recognised by the C++ compiler. The five categories used in the metro fare program are:

| Token Type | Examples | Explanation |
|---|---|---|
| Keywords | `class`, `enum`, `constexpr`, `int`, `return`, `if` | Reserved words that have predefined meanings in C++. |
| Identifiers | `SmartCard`, `BASE_FARE_PAISE`, `calculateFare`, `balance` | Programmer-defined names for variables, functions, classes and constants. |
| Constants / Literals | `100`, `20`, `5000`, `true`, `'A'`, `"Invalid Zone"` | Fixed values used in the program. |
| Operators | `+`, `-`, `*`, `/`, `==`, `>=`, `&&` | Symbols that perform arithmetic, comparison and logical operations. |
| Punctuators | `;`, `{}`, `()`, `[]`, `,`, `::` | Symbols used to separate statements, group expressions and organise declarations. |

**Justification:** Correct identification and use of tokens helps create readable and syntactically valid C++ programs. Each token category performs a different role in defining the fare system.

## 3. Comparison of #define, const and constexpr

C++ provides different ways to represent values that should not be modified during program execution.

### A. #define

`#define` is a preprocessor directive that replaces a named macro with its specified text before compilation.

Example:

```cpp
#define INNER_FARE_PAISE 1000
#define PEAK_SURCHARGE 20
#define MIN_BALANCE_PAISE 5000
```

**Limitations:**

- Macros do not have a C++ data type.
- They do not follow normal C++ scope rules.
- They do not receive ordinary compiler type checking.
- Their text substitution can cause unexpected results if used carelessly.

### B. const

`const` declares a typed object whose value cannot be modified through that name after initialisation.

Example:

```cpp
const int minimumBalance = 5000;
```

**Advantages:**

- It has a specific data type.
- It follows normal C++ scope and access rules.
- The compiler checks its type.
- It is suitable for values that must remain unchanged but do not necessarily need to be compile-time constants.

### C. constexpr

`constexpr` declares a value or function that can be evaluated at compile time when its requirements are satisfied.

Example:

```cpp
constexpr int INNER_FARE_PAISE = 1000;
constexpr int PEAK_SURCHARGE_PERCENT = 20;
```

**Advantages:**

- Values must satisfy compile-time constant-expression requirements.
- It provides type checking.
- It follows normal C++ scope rules.
- It can be used in compile-time calculations and constant expressions.
- It is suitable for fixed fare values and limits known before execution.

### Why constexpr is Preferred for the FareTable

`constexpr` is preferred because the base fares, surcharge percentage and minimum balance are fixed values known at compile time. It provides type safety, scope control and compile-time evaluation.

Unlike `#define`, it is a proper C++ language feature rather than simple text substitution. Compared with ordinary `const`, it explicitly requires a compile-time constant expression.

Therefore, `constexpr` provides the most suitable approach for the fixed fare table.

## 4. enum and enum class for Zones

C++ provides enumerations to represent a fixed set of related named values.

### A. Traditional enum

```cpp
enum Zone {
    Inner,
    Middle,
    Outer
};
```

A traditional `enum` introduces its enumerator names into the surrounding scope. Its values can also convert implicitly to integers, which may lead to accidental comparisons or naming conflicts.

### B. enum class

```cpp
enum class Zone {
    Inner = 0,
    Middle = 1,
    Outer = 2
};
```

An `enum class` provides scoped enumerator names and stronger type safety. Its values do not implicitly convert to integers.

For example:

```cpp
Zone selectedZone = Zone::Inner;
```

The zone must be accessed using its scope, such as `Zone::Inner`, which makes the code clearer.

### Why enum class is Safer

`enum class` is preferred because:

1. It prevents enumerator names from unnecessarily entering the surrounding scope.
2. It prevents implicit conversion of zone values to integers.
3. It reduces accidental comparisons between unrelated enumeration types.
4. It makes the program easier to read and maintain.

For the metro fare system, `enum class Zone` ensures that zone values are represented through clearly identified and strongly typed categories.

## 5. Naming Rules and Conventions for Identifiers

Identifiers are names assigned to variables, functions, classes and other program elements. They must follow C++ naming rules.

**Naming rules:**

1. An identifier can contain letters, digits and underscores.
2. It must not begin with a digit.
3. It must not be a C++ reserved keyword.
4. C++ identifiers are case-sensitive. For example, `fare` and `Fare` are different identifiers.
5. Spaces and special characters such as `@`, `#` and `-` are not permitted within an identifier.
6. Reserved identifier patterns, such as names containing double underscores, should be avoided.

**Naming conventions used in the metro system:**

| Program Element | Convention | Example |
|---|---|---|
| Constants | Uppercase with underscores | `BASE_FARE_PAISE` |
| Variables | camelCase | `cardBalance` |
| Functions | camelCase | `calculateFare()` |
| Classes | PascalCase | `SmartCard` |
| Enumerations | PascalCase | `Zone` |
| Enum values | PascalCase | `Zone::Inner` |

Meaningful names improve readability and make the program easier to understand, debug and maintain.

## 6. Alternative Concept and Complexity Analysis

### Using #define Macros Instead of constexpr and enum class

If the fare values and zone definitions were implemented using `#define` macros instead of `constexpr` constants and `enum class`, the program would use preprocessor text substitution rather than strongly typed C++ declarations.

Example:

```cpp
#define INNER_FARE_PAISE 1000
#define MIDDLE_FARE_PAISE 2000
#define OUTER_FARE_PAISE 3000

#define INNER 0
#define MIDDLE 1
#define OUTER 2
```

In this approach, the fare values and zone names are replaced by their corresponding text before compilation. Unlike `constexpr` and `enum class`, macros do not provide normal type checking or scoped enumeration values.

### Comparison

| Feature | #define Macros | constexpr and enum class |
|---|---|---|
| Type checking | No ordinary C++ type checking for macros | Stronger type checking |
| Scope | Not governed by normal C++ scope rules | Follows C++ scope rules |
| Zone safety | Values are substituted as text or integers | Distinct, strongly typed zone values |
| Storage | Macro itself occupies no storage | A constexpr value usually requires no storage when used as a compile-time constant, although storage may be required in some uses |
| Running time | No additional runtime cost from macro substitution | No additional runtime cost for compile-time constant use |
| Maintainability | Can be harder to manage in larger programs | Clearer, safer and easier to maintain |

### Time and Memory Analysis

Both approaches can provide constant-time fare access, so the time complexity of retrieving a fare remains **O(1)**.

A macro is replaced during preprocessing and does not occupy storage as a variable. A `constexpr` value is also generally evaluated at compile time and usually does not require runtime storage when used as a compile-time constant. However, storage may be required in certain cases, such as when its address is taken.

Therefore, neither approach provides a meaningful runtime speed advantage for this fare table. The main difference is type safety, scope management and maintainability.

Using `constexpr` with `enum class` is preferable because it allows the compiler to detect more programming errors and prevents invalid mixing of zone values with unrelated integers.

## 7. Conclusion

For the Namma Metro Smart-Card Fare System, `constexpr` is the preferred choice for fixed fare values, surcharge percentages and balance limits because it provides compile-time evaluation and type safety. `enum class` is preferred for representing Inner, Middle and Outer zones because it provides scoped and strongly typed values.

Although `#define` macros can represent the same numerical values without allocating storage, they do not provide normal C++ type checking or scope protection. Both approaches have effectively the same running-time complexity for fare lookup, but `constexpr` and `enum class` provide a safer, clearer and more maintainable design.

**Final Design Decision:** Use one centralised `constexpr` fare table, named `constexpr` limits and surcharge values, and `enum class Zone` throughout the metro fare program.
