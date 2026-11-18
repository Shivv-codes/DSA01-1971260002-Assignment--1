# TASK 04 – Control Implicit Conversions

## 1. Introduction

Implicit type conversion occurs when C++ automatically converts a value from one data type to another without an explicit instruction from the programmer. These conversions take place during arithmetic operations, assignments, comparisons and other expressions.

In a metro smart-card fare system, understanding type conversions is important because unintended conversions can cause incorrect calculations, loss of precision and unexpected comparison results.

This task examines integer division, integer-to-double conversion, signed and unsigned comparisons, narrowing conversions, Boolean conversion and the use of `static_cast`.

## 2. Identification of Implicit Conversions

### 2.1 Integer Division and Integer-to-Double Conversion

Consider the following statements:

```cpp
int a = 7, b = 2;
double c = a / b;
double d = static_cast<double>(a) / b;
```

In the first expression, both operands are integers. Therefore, integer division is performed.

\[
7/2=3
\]

The fractional part is discarded. The resulting integer value 3 is then implicitly converted to `double` during assignment, producing 3.0.

In the second expression, `static_cast<double>(a)` explicitly converts `a` into a double. The other operand, `b`, is implicitly converted to double during the arithmetic operation.

\[
7.0/2.0=3.5
\]

Therefore:

- `c = 3.0`
- `d = 3.5`

**Observation:** Converting the result of integer division into `double` does not restore the discarded fractional part. The conversion must happen before division.

### 2.2 Character and Integer Conversion

Consider:

```cpp
char ch = 'A' + 2;
std::cout << ch << ' ' << ch + 1 << '\n';
```

An ordinary character literal such as `'A'` has type `int` in C++.

On the ASCII-compatible system used for this experiment:

1. `'A'` has the integer value 65.
2. The expression `65 + 2` produces 67.
3. The integer value 67 is converted to `char` during assignment, producing `'C'`.
4. In `ch + 1`, the `char` variable is promoted to `int` before addition.
5. The result is 68.

Therefore, the output is:

```text
C 68
```

**Observation:** A character variable is promoted to an integer during arithmetic. Consequently, `ch + 1` produces a numerical result rather than another character.

### 2.3 Integer-to-Boolean Conversion

Consider:

```cpp
bool flag = 5;
std::cout << flag << ' ' << std::boolalpha << flag << '\n';
```

When an integer is converted to `bool`:

- Zero becomes `false`.
- Any non-zero value becomes `true`.

Since 5 is non-zero, `flag` becomes `true`.

By default, Boolean values are displayed as 1 or 0. The `std::boolalpha` manipulator changes the output to `true` or `false`.

Therefore, the output is:

```text
1 true
```

### 2.4 Signed and Unsigned Comparison

Consider:

```cpp
unsigned int u = 1;
int s = -1;

std::cout << (s < u ? "s < u" : "s >= u") << '\n';
```

Here, a signed integer (`int`) is compared with an unsigned integer (`unsigned int`).

During the comparison, the usual arithmetic conversions convert the signed value to an unsigned value because both operands have the same rank and the unsigned type is used as the common comparison type.

On a system with a 32-bit `unsigned int`, the value -1 is converted to:

\[
2^{32}-1=4,294,967,295
\]

The comparison becomes:

\[
4,294,967,295<1
\]

This condition is false. Therefore, the conditional operator selects the second string.

Output:

```text
s >= u
```

When compiled using GCC with warning options such as `-Wall -Wextra`, the compiler can issue a warning similar to:

```text
warning: comparison of integer expressions of different signedness
```

The exact wording depends on the compiler version and warning settings.

**Observation:** Comparing signed and unsigned values without checking their ranges can produce unexpected results, particularly when a negative signed value is converted to unsigned.

## 3. Predicted Output Before Execution

The predicted output of the given program is:

```text
3 3.5
C 68
1 true
s >= u
```

## 4. Explanation of Each Line of the Actual Output

| Output | Explanation |
|---|---|
| `3 3.5` | Integer division produces 3, which is then converted to double. Explicit conversion before division produces 3.5. |
| `C 68` | The character literal `'A'` has integer value 65 on the target encoding. Addition produces 67, which is converted to `char`. The variable `ch` is promoted to `int` in `ch + 1`, producing 68. |
| `1 true` | The integer 5 is implicitly converted to Boolean `true`. Default Boolean output is 1, while `std::boolalpha` displays `true`. |
| `s >= u` | The signed value -1 is converted to an unsigned value during comparison. The comparison is false on the tested 32-bit unsigned implementation. |

The predicted output matches the expected output on the specified implementation. The experiment demonstrates how implicit conversions can affect arithmetic, character handling, Boolean values and comparisons.

## 5. Narrowing Conversions and Brace Initialisation

A narrowing conversion occurs when a value is converted into a type that cannot represent the original value without losing information.

For example:

```cpp
double value = 3.7;
int x = value;
```

The fractional part is discarded, and `x` becomes 3.

C++ brace initialisation helps prevent unintended narrowing conversions.

Example:

```cpp
int x{3.7};
```

This statement produces a compile-time error because converting 3.7 from `double` to `int` through list initialisation is a narrowing conversion.

If the conversion is intentional, an explicit cast can be used:

```cpp
int x = static_cast<int>(3.7);
```

Here, the result is 3, but the fractional part is lost.

**Importance:** Brace initialisation detects certain narrowing conversions at compile time, while `static_cast` makes an intentional conversion explicit. However, an explicit cast does not guarantee that information will be preserved.

## 6. Use of static_cast and When Explicit Conversion Is Needed

`static_cast` is used to explicitly convert a value from one compatible type to another.

Example:

```cpp
int a = 7;
double result = static_cast<double>(a) / 2;
```

The integer is explicitly converted to double before division, producing 3.5.

Explicit conversions are useful when:

1. Floating-point division is required instead of integer division.
2. A conversion is intentional and should be clearly visible.
3. The programmer wants to avoid relying on unexpected implicit conversions.
4. Potentially narrowing conversions need to be identified and controlled.

However, explicit casts must be used carefully because they can still cause loss of information.

## 7. Corrected Program Using Explicit Conversions and Brace Initialisation

The following program makes important conversions explicit and avoids the unsafe signed/unsigned comparison.

### Program: `type_conversion.cpp`

```cpp
#include <iostream>

int main()
{
    int a{7};
    int b{2};

    // Integer division followed by conversion to double
    double c{static_cast<double>(a / b)};

    // Explicit conversion before division
    double d{
        static_cast<double>(a) /
        static_cast<double>(b)
    };

    std::cout << c << ' ' << d << '\n';

    // Character arithmetic and explicit conversion
    char ch{static_cast<char>('A' + 2)};

    std::cout << ch << ' '
              << static_cast<int>(ch) + 1 << '\n';

    // Explicit integer-to-Boolean conversion
    bool flag{static_cast<bool>(5)};

    std::cout << flag << ' '
              << std::boolalpha << flag << '\n';

    unsigned int u{1};
    int s{-1};

    // Check the signed value before converting it to unsigned
    bool condition =
        (s >= 0) &&
        (static_cast<unsigned int>(s) < u);

    std::cout << (condition ? "s < u" : "s >= u") << '\n';

    return 0;
}
```

The expression `int x{3.7};` is deliberately not included in the executable program because it causes a compile-time error. It can be tested separately to demonstrate narrowing prevention.

The corrected program produces:

```text
3 3.5
C 68
1 true
s >= u
```

The corrected signed/unsigned comparison first checks whether `s` is non-negative. Only then is it converted to unsigned. This prevents a negative value from being interpreted as a very large unsigned value.

## 8. Alternative Concept and Complexity Analysis

An alternative solution is to write conversions explicitly using `static_cast` and use brace initialisation wherever possible instead of relying on implicit conversions.

| Parameter | Implicit Conversion Approach | Explicit Conversion Approach |
|---|---|---|
| Conversion | Performed automatically by the compiler | Specified using `static_cast` |
| Readability | May hide conversion behaviour | Makes conversions more visible |
| Narrowing detection | Some unintended conversions may go unnoticed | Brace initialisation rejects narrowing conversions |
| Type safety | Depends on expression and conversion rules | Helps the programmer control conversions |
| Running time | O(1) per conversion | O(1) per conversion |
| Memory usage | No additional storage required | No additional storage required |

Explicit conversions generally do not change the asymptotic running time or memory usage. These conversions are handled by the compiler and normally require no additional runtime storage.

Brace initialisation can catch certain narrowing errors during compilation rather than allowing unintended values to be produced during execution.

For example:

```cpp
int x{3.7};
```

is rejected during compilation, whereas:

```cpp
int x = 3.7;
```

allows the conversion and discards the fractional part.

Therefore, explicit conversions and brace initialisation improve code clarity and help prevent unintended conversion errors without changing the overall algorithmic complexity.

## 9. Conclusion

Implicit conversions are an important feature of C++, but they can produce unexpected results when different data types are used in the same expression.

The experiment demonstrates integer division, integer-to-double conversion, character arithmetic, integer-to-Boolean conversion and signed-to-unsigned comparison. It also demonstrates how narrowing conversions can be prevented using brace initialisation.

Using `static_cast` makes intentional conversions clearer, while brace initialisation helps detect certain errors at compile time. These practices improve the correctness, readability and reliability of C++ programs and are particularly important in a fare calculation system where accurate monetary values and comparisons are essential.
