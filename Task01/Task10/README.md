# Task 10 – Compare Expectations with Execution

**Marks: 4**

## 1. Network Total Calculations

The base fare is assumed to be ₹10 per journey for this comparison.

| Journeys per day | Total in rupees | Total in paise | Fits 32-bit signed `int`? |
|---:|---:|---:|---|
| 20,000 | ₹2,00,000 | 20,000,000 | Yes |
| 5,00,000 | ₹50,00,000 | 500,000,000 | Yes |
| 30,00,000 | ₹3,00,00,000 | 3,000,000,000 | No |

The integer-paise and double-rupee totals agree for this ₹10 example because the values are exactly representable at these magnitudes. However, floating-point calculations involving decimal fractions can produce small rounding differences.

For example, repeatedly adding ₹0.10 using `double` may produce a slightly different result from the exact integer-paise total.

## 2. Integer Overflow

A typical 32-bit signed `int` has a maximum value of 2,147,483,647.

At 30,00,000 journeys, the total is 3,000,000,000 paise, which exceeds this limit. Therefore, an `int` accumulator can overflow. Signed integer overflow in C++ causes undefined behaviour.

A `long long` accumulator can safely store this total.

## 3. Compiler Warnings

The program can be compiled using:

```bash
g++ -std=c++17 -Wall -Wextra -Wconversion -pedantic main.cpp -o fare
```

- `-Wall`: Enables a broad set of common compiler warnings.
- `-Wextra`: Enables additional warnings.
- `-Wconversion`: Detects potentially value-changing implicit conversions.
- `-pedantic`: Reports non-standard language extensions.

The exact warnings depend on the source code and compiler version. The final demonstration program compiled successfully; no warning messages should be invented. Any warnings from the actual build should be recorded exactly.

## 4. Alternative Concept – Accumulator Comparison

| Data type | Result at 30,00,000 journeys | Typical size | Time per addition |
|---|---|---:|---|
| `int` | Overflows in paise | 4 bytes | O(1) |
| `long long` | Stores exact total | 8 bytes | O(1) |
| `double` | Supports large values, but decimal fractions may be approximate | 8 bytes | O(1) |

All three types perform addition in constant time. `long long` is preferred for storing total money in paise because it provides a wider integer range and exact whole-paise calculations.

## 5. Conclusion

The experiment shows that `int` is insufficient for the whole-network total in paise. `long long` is the safest choice for accumulating fares, while `double` may introduce small precision errors when decimal fractions are used. Exact integer-paise arithmetic is therefore recommended for the metro fare system.
