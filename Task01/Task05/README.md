# Task 5 – Write Correct Expressions with the Right Precedence

## 1. Peak Hour, Surcharge and Concession Expressions

The peak hours are 8:00–10:00 AM and 5:00–8:00 PM. The starting times are included and ending times are excluded.

```cpp
bool peak = (hour >= 8 && hour < 10) ||
            (hour >= 17 && hour < 20);

int surcharge = peak ? (baseFare * 20 / 100) : 0;

int fare = baseFare + surcharge;

// Apply the concession percentage defined by the fare policy.
fare -= (fare * concessionPercent / 100);
```

- `&&` checks both conditions in a time interval.
- `||` checks whether either peak interval is satisfied.
- The conditional operator `?:` applies the surcharge only during peak hours.
- Compound assignment `+=` or `-=` updates the fare directly.
- The concession percentage must be defined according to the applicable student or senior-citizen policy.

## 2. Operator Precedence and Associativity

Operator precedence determines the order in which operators are evaluated.

| Operator | Purpose | Associativity |
|---|---|---|
| `*`, `/`, `%` | Multiplication, division and remainder | Left to right |
| `+`, `-` | Addition and subtraction | Left to right |
| `<`, `>=` | Relational comparison | Left to right |
| `&&` | Logical AND | Left to right |
| `||` | Logical OR | Left to right |
| `?:` | Conditional operator | Right to left |
| `+=`, `-=` | Compound assignment | Right to left |
| `++` | Increment | Depends on prefix or postfix form |

Parentheses should be used to make expressions clear and avoid mistakes.

## 3. Pre-increment and Post-increment

- `x++` (post-increment) uses the current value first and increments afterwards.
- `++x` (pre-increment) increments first and then uses the updated value.

Example:
```cpp
int x = 5;
int a = x++;  // a = 5, x = 6

int y = 5;
int b = ++y;  // b = 6, y = 6
```

The expression `i = i++` should be avoided because it modifies and assigns the same variable in an unsafe and confusing way. In modern C++, this expression has undefined behaviour.

## 4. Division and Modulus with Negative Operands

C++ integer division truncates the result towards zero. The remainder has the sign of the dividend.

```cpp
10 / 3   = 3
10 % 3   = 1
-10 / 3  = -3
-10 % 3  = -1
```

## 5. Peak-Hour Expression Comparison

Correct and readable expression:

```cpp
(hour >= 8 && hour < 10) ||
(hour >= 17 && hour < 20)
```

Equivalent expression:

```cpp
hour >= 8 && hour < 10 || hour >= 17 && hour < 20
```

Both expressions produce the same result because `&&` has higher precedence than `||`. However, parentheses make the two time intervals easier to understand and reduce mistakes.

## 6. Predict the Output

**Program:**

```cpp
#include <iostream>

int main()
{
    int x = 5, y = 3;
    std::cout << x + y * 2 << ' ' << (x + y) * 2 << '\n';

    int z = x++ + ++y;
    std::cout << z << ' ' << x << ' ' << y << '\n';

    std::cout << (x > y ? x : y) << ' ' << x % 4 * 2 << '\n';
    std::cout << 10 / 3 << ' ' << 10 % 3 << ' '
              << -10 / 3 << ' ' << -10 % 3 << '\n';

    int k = 0;
    for (int i = 0; i < 10; i += 3)
        k += i;

    std::cout << k << '\n';
}
```

**Predicted Output:**

```text
11 16
9 6 4
6 4
3 1 -3 -1
18
```

## 7. Explanation of the Output

1. `x + y * 2` gives `5 + 6 = 11` because multiplication has higher precedence. `(x + y) * 2` gives `16` because parentheses are evaluated first.

2. `x++` returns 5 and then increments x to 6. `++y` increments y to 4 before returning it. Therefore, `z = 5 + 4 = 9`.

3. The conditional expression selects 6 because `x > y`. The expression `x % 4 * 2` evaluates as `(6 % 4
