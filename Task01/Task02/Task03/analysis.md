# TASK 03 – Choose Data Types and Check Type Compatibility

## 1. Introduction

Selecting suitable data types is essential for the accuracy, reliability and memory efficiency of the metro smart-card fare system. The system must store fares, card balances, journey times, zone numbers and journey counts. An unsuitable data type can cause precision loss, integer overflow or incorrect calculations.

The data types are selected based on the nature of each value, its expected range and the operations performed on it.

## 2. Selection and Justification of Data Types

| System Value | Selected Data Type | Justification |
|---|---|---|
| Fare amount | `long long` | Stores monetary values as integer paise without floating-point rounding errors. |
| Smart-card balance | `long long` | Supports accurate additions and deductions and provides a large range. |
| Peak-hour status | `bool` | Represents whether a journey occurs during peak hours (`true` or `false`). |
| Passenger category code | `char` | Can store a single-character category code, if required. |
| Journey hour and minute | `int` | Suitable for storing whole-number time components. |
| Zone number | `int` | Stores numerical zone identifiers. An `enum class` can alternatively represent named zones. |
| Journey count | `unsigned long long` | Represents large non-negative journey counts. |
| Concession percentage | `int` | Stores a whole-number percentage. |
| Approximate calculations | `float` | Suitable for calculations where lower floating-point precision is acceptable. |
| Display and measurement calculations | `double` | Provides greater floating-point precision than `float`. |
| Automatically deduced values | `auto` | Allows the compiler to determine the variable type from its initial value. |

**Note:** `auto` is a type-deduction keyword, not a separate fundamental data type. For example:

```cpp
auto baseFare = 1000LL;
```

The compiler deduces `baseFare` as `long long`.

For passenger categories with multiple named values, an `enum class` is preferable to using arbitrary character codes because it provides clearer and safer category representation.

## 3. Data Type Sizes and Integer Ranges

The `sizeof` operator determines the memory occupied by a data type on the system. The `<climits>` header provides the minimum and maximum limits of integer types.

The following program measures the sizes and ranges on the actual compiler being used.

### Program: data_types.cpp

```cpp
#include <iostream>
#include <climits>
using namespace std;

int main()
{
    cout << "DATA TYPE SIZES" << endl;

    cout << "bool: " << sizeof(bool) << " bytes" << endl;
    cout << "char: " << sizeof(char) << " bytes" << endl;
    cout << "int: " << sizeof(int) << " bytes" << endl;
    cout << "unsigned int: " << sizeof(unsigned int) << " bytes" << endl;
    cout << "long long: " << sizeof(long long) << " bytes" << endl;
    cout << "unsigned long long: "
         << sizeof(unsigned long long) << " bytes" << endl;
    cout << "float: " << sizeof(float) << " bytes" << endl;
    cout << "double: " << sizeof(double) << " bytes" << endl;

    cout << "\nINTEGER RANGES" << endl;

    cout << "CHAR_MIN: " << static_cast<int>(CHAR_MIN) << endl;
    cout << "CHAR_MAX: " << static_cast<int>(CHAR_MAX) << endl;
    cout << "INT_MIN: " << INT_MIN << endl;
    cout << "INT_MAX: " << INT_MAX << endl;
    cout << "UINT_MAX: " << UINT_MAX << endl;
    cout << "LLONG_MIN: " << LLONG_MIN << endl;
    cout << "LLONG_MAX: " << LLONG_MAX << endl;
    cout << "ULLONG_MAX: " << ULLONG_MAX << endl;

    return 0;
}
```

### 3.1 Typical Sizes and Ranges

The following are common values on modern systems. The actual results must be verified using the program above.

| Data Type | Typical Size | Typical Range or Precision |
|---|---:|---|
| `bool` | 1 byte | `false` or `true` |
| `char` | 1 byte | Usually -128 to 127, or 0 to 255 depending on signedness |
| `int` | 4 bytes | -2,147,483,648 to 2,147,483,647 |
| `unsigned int` | 4 bytes | 0 to 4,294,967,295 |
| `long long` | 8 bytes | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |
| `unsigned long long` | 8 bytes | 0 to 18,446,744,073,709,551,615 |
| `float` | 4 bytes | Approximately 6–7 significant decimal digits |
| `double` | 8 bytes | Approximately 15–16 significant decimal digits |

The sizes shown are typical implementation values, not universal guarantees. The program's output is the evidence for the system used.

### Evidence 1 – Data Type Sizes and Ranges

Insert the actual screenshot of the successfully executed `data_types.cpp` program here.

**Screenshot:** `evidence/sizeof-output.png`

## 4. Why Money Should Be Stored in Integer Paise Instead of Double Rupees

Money should be represented using integer paise rather than `double` rupees because floating-point numbers cannot represent every decimal fraction exactly in binary.

For example, a value such as ₹0.10 may be stored as a nearby binary approximation. Repeated additions can therefore introduce small rounding errors.

Storing money as integer paise avoids this problem for whole-paise transactions.

Examples:

- ₹10 = 1,000 paise
- ₹20 = 2,000 paise
- ₹30 = 3,000 paise
- ₹36 = 3,600 paise

Advantages of integer-paise representation:

1. Monetary values are stored as exact whole numbers of paise.
2. Addition and subtraction do not introduce floating-point rounding errors.
3. Fare deductions and balance updates remain accurate, provided the integer type does not overflow.
4. `long long` supports large monetary totals.

Therefore, `long long` is selected for fare and balance values stored in paise.

## 5. Type Compatibility Rules in C++

C++ allows different data types to participate in the same expression through implicit type conversions and promotions.

### 5.1 Integer and Double Conversion

When an `int` and a `double` are used in an arithmetic expression, the integer is converted to `double` before the operation.

Example:

```cpp
int a = 10;
double b = 2.5;

double result = a + b;
```

The integer value 10 is converted to 10.0, and the result is 12.5.

### 5.2 Integer Division

When two integers are divided, C++ performs integer division. The fractional part is discarded.

Example:

```cpp
int a = 7;
int b = 2;

double result = a / b;
```

The result is 3.0, not 3.5, because the division is performed using integer operands before the result is assigned to `double`.

To obtain the fractional result, an explicit conversion is required:

```cpp
double result = static_cast<double>(a) / b;
```

The result is 3.5.

### 5.3 Integer and Boolean Conversion

When an integer is converted to `bool`:

- Zero becomes `false`.
- Any non-zero value becomes `true`.

Example:

```cpp
int balance = 100;
bool hasBalance = balance;
```

Here, `hasBalance` becomes `true`.

When a Boolean value is printed normally, it appears as 1 for `true` and 0 for `false`. Using `boolalpha` displays the words `true` and `false`.

These conversion rules must be considered carefully to prevent unexpected results in fare calculations and balance validation.

## 6. Integer Ranges and Daily Collection Analysis

The `<climits>` header provides the limits of integer data types.

For a typical system with a 32-bit signed `int`:

- `INT_MIN` = -2,147,483,648
- `INT_MAX` = 2,147,483,647

The metro network is expected to grow to 3,000,000 journeys per day.

The maximum base fare is ₹30. During peak hours, a 20% surcharge applies.

Maximum fare before concessions:

₹30 + 20% of ₹30 = ₹36

Maximum daily collection:

3,000,000 × ₹36 = ₹108,000,000

Converting this amount into paise:

₹108,000,000 × 100 = 10,800,000,000 paise

This exceeds the typical maximum value of a 32-bit signed integer, which is 2,147,483,647.

Therefore, `int` is insufficient to store the maximum possible daily collection in paise. Using `long long` resolves this overflow problem because it supports a much larger range.

This calculation assumes every journey pays the maximum peak fare and does not receive a concession. It is therefore a conservative upper-bound capacity calculation.

## 7. Alternative Concept: Double Rupees vs Long Long Paise

An alternative solution is to store monetary values using `double` in rupees instead of `long long` in paise.

| Parameter | `double` in Rupees | `long long` in Paise |
|---|---|---|
| Representation | Floating-point | Integer |
| Typical memory per value | 8 bytes | 8 bytes |
| Time per addition | O(1) | O(1) |
| Space per value | O(1) | O(1) |
| Monetary precision | Approximate | Exact for whole-paise values |
| Rounding error | Possible | No floating-point rounding error |

Both methods have constant-time addition, O(1), and typically use 8 bytes per value on common modern systems.

However, `double` uses approximate binary floating-point representation. Repeated additions can accumulate rounding differences.

The integer-paise approach avoids this problem for transactions expressed in whole paise and is therefore more appropriate for a fare collection system.

## 8. Measuring Floating-Point Rounding Error

The following program compares repeated addition using `double` with integer-paise addition. It measures the difference after increasing numbers of additions.

### Program: precision_test.cpp

```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main()
{
    const int N = 10000000;

    double doubleTotal = 0.0;
    long long paiseTotal = 0;

    int checkpoints[] = {1000, 100000, 1000000, 10000000};
    int nextCheckpoint = 0;

    cout << fixed << setprecision(12);
    cout << "NUMBER OF ADDITIONS | ABSOLUTE ERROR" << endl;

    for (int i = 1; i <= N; i++)
    {
        doubleTotal += 0.10;
        paiseTotal += 10;

        if (i == checkpoints[nextCheckpoint])
        {
            double exactRupees =
                static_cast<double>(paiseTotal) / 100.0;

            double error = doubleTotal - exactRupees;

            if (error < 0)
                error = -error;

            cout << i << " | " << error << endl;

            nextCheckpoint++;
        }
    }

    cout << "\nFinal double total: "
         << setprecision(17) << doubleTotal << endl;

    cout << "Exact integer-paise total: "
         << paiseTotal << " paise" << endl;

    return 0;
}
```

### 8.1 Explanation of the Experiment

The program adds ₹0.10 repeatedly using two methods:

- `doubleTotal` stores the accumulated amount as a floating-point value.
- `paiseTotal` stores each transaction as 10 integer paise.

The expected exact total is calculated from the integer-paise value. At each checkpoint, the absolute difference between the two totals is measured.

The actual measured errors must be recorded from the compiler output. Floating-point error does not necessarily increase monotonically at every checkpoint, so the measured values should be reported rather than assumed.

### Evidence 2 – Precision Comparison

Insert the actual screenshot of the successfully executed `precision_test.cpp` program here.

**Screenshot:** `evidence/precision-output.png`

## 9. Conclusion

Appropriate data type selection is essential for the accuracy and reliability of the metro smart-card fare system. `long long` is suitable for storing monetary values in integer paise because it avoids floating-point rounding errors and provides sufficient range for large collections.

The `sizeof` operator and `<climits>` header help verify memory requirements and integer limits on the actual system. Type compatibility rules explain how C++ handles mixed expressions involving integers, floating-point values and Boolean values.

The daily collection calculation demonstrates that a 32-bit `int` is insufficient for the maximum network collection in paise. The precision experiment further demonstrates why integer-based monetary calculations are preferable to floating-point storage.
