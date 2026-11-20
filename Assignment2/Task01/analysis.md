# TASK 01 – Call by Value, by Reference and by Pointer

## 1. Introduction

C++ functions can receive arguments using call by value, call by reference and call by pointer. These methods determine whether a function works with a copy of a variable or can directly modify the original variable.

In the hostel fee concession system, the fee must be reduced according to the applicable concession percentage. The three parameter-passing methods are compared to identify which method updates the caller's original fee.

## 2. Three Methods of Passing Arguments

### 2.1 Call by Value

In call by value, a copy of the argument is passed to the function. Changes made to the parameter affect only the local copy. The original variable remains unchanged.

```cpp
void applyByValue(long long fee, int percent)
{
    fee -= fee * percent / 100;
}
```

### 2.2 Call by Reference

In call by reference, the parameter becomes an alias for the original variable. Any modification made to the parameter directly changes the caller's variable.

```cpp
void applyByReference(long long& fee, int percent)
{
    fee -= fee * percent / 100;
}
```

### 2.3 Call by Pointer

In call by pointer, the address of the original variable is passed. The pointer is dereferenced using `*` to modify the value stored at that address.

```cpp
void applyByPointer(long long* fee, int percent)
{
    if (fee != nullptr)
    {
        *fee -= (*fee * percent) / 100;
    }
}
```

## 3. Comparison of the Three Methods

| Method | Parameter | Effect on Original Variable | Main Characteristic |
|---|---|---|---|
| Call by value | `long long fee` | No change | Operates on a copy |
| Call by reference | `long long& fee` | Changes | Directly accesses the original variable |
| Call by pointer | `long long* fee` | Changes when valid | Uses an address and dereferencing |

Call by value is suitable when the function should not modify the caller's variable. Call by reference is useful when modification is required without copying the value. Call by pointer is useful when an address must be passed or a nullable parameter is required.

## 4. Stored Parameter and Memory Behaviour

- **Call by value:** A separate local parameter is created with a copy of the argument. Modifying it does not affect the original variable.
- **Call by reference:** The parameter refers to the original variable. Changes are reflected directly in the caller's variable.
- **Call by pointer:** The function receives an address. Dereferencing the pointer allows it to modify the original value.

A `const` reference should be used when a function needs to read a large object without modifying it. It avoids copying the object while preventing accidental modification through that parameter.

## 5. Hostel Fee Concession Implementation

The hostel fee is initially ₹45,000. A concession of 10% is applied.

### Complete C++ Program

```cpp
#include <iostream>
using namespace std;

void applyByValue(long long fee, int percent)
{
    fee -= fee * percent / 100;
}

void applyByReference(long long& fee, int percent)
{
    fee -= fee * percent / 100;
}

void applyByPointer(long long* fee, int percent)
{
    if (fee != nullptr)
    {
        *fee -= (*fee * percent) / 100;
    }
}

int main()
{
    long long fee1 = 45000;
    long long fee2 = 45000;
    long long fee3 = 45000;

    applyByValue(fee1, 10);
    cout << "After call by value: " << fee1 << '\n';

    applyByReference(fee2, 10);
    cout << "After call by reference: " << fee2 << '\n';

    applyByPointer(&fee3, 10);
    cout << "After call by pointer: " << fee3 << '\n';

    return 0;
}
```

### Expected Output

```text
After call by value: 45000
After call by reference: 40500
After call by pointer: 40500
```

### Explanation

The initial fee is ₹45,000 and the 10% concession is ₹4,500.

- Call by value changes only the local copy, so the original fee remains ₹45,000.
- Call by reference modifies the original fee to ₹40,500.
- Call by pointer modifies the original fee through its address, resulting in ₹40,500.

## 6. Predict the Output of the Given Program

### Original Program

The assignment provides a program using a value parameter, reference parameter, pointer parameter, return by reference and default arguments.

### Predicted Output

```text
1 12 13
1 100
16 13 6
```

### Explanation of Each Output Line

**Line 1: `1 12 13`**

- `a` remains 1 because it is passed by value.
- `b` becomes 12 because it is passed by reference.
- `c` becomes 13 because its address is passed through a pointer and modified using dereferencing.

**Line 2: `1 100`**

The `bigger()` function returns a reference to the larger of `a` and `c`. Since `c` is larger, the returned reference refers to `c`. Assigning 100 through that reference changes `c` directly.

**Line 3: `16 13 6`**

The `show()` function uses default arguments:

- `show(1)` uses `y = 5` and `z = 10`: result = 16.
- `show(1, 2)` uses `z = 10`: result = 13.
- `show(1, 2, 3)` uses all supplied arguments: result = 6.

## 7. Alternative Concept – Returning the Updated Fee

Instead of modifying the caller's fee through a reference, the function can return the updated fee.

```cpp
long long applyConcession(long long fee, int percent)
{
    return fee - (fee * percent / 100);
}
```

The function is called as follows:

```cpp
long long hostelFee = 45000;
hostelFee = applyConcession(hostelFee, 10);
```

This approach makes the updated value explicit and allows the result to be stored or used in another expression.

For several concessions, the returned value can be passed through successive function calls. This makes the sequence of updates clear.

## 8. Time and Space Complexity Analysis

| Method | Time Complexity | Additional Parameter Storage |
|---|---|---|
| Call by value | O(1) | One copied `long long` value |
| Call by reference | O(1) | Reference to the original value |
| Call by pointer | O(1) | One pointer containing an address |
| Return by value | O(1) | One returned `long long` value |

All methods perform a constant number of arithmetic operations, so their time complexity is O(1).

For a `long long`, copying the value requires constant space. For a large `Student` object, passing by value copies the entire object and requires additional storage proportional to its size. Passing by reference or `const` reference avoids copying the complete object and uses only a reference to it.

## 9. Design Decision

For a hostel fee concession system, returning the updated fee is a suitable design when multiple concessions must be applied in sequence. It makes the data flow explicit and avoids hidden changes to the original variable.

Call by reference is also appropriate when the purpose of the function is specifically to update the existing fee. A `const` reference is preferable when a function only needs to inspect a large object without modifying it.

## 10. Conclusion

Call by value, call by reference and call by pointer differ in how function parameters interact with the original variables. Call by value modifies only a copy, whereas reference and pointer parameters can modify the caller's original value.

The output-prediction program demonstrates these differences, along with returning a reference and using default arguments. For the hostel fee system, reference or pointer parameters can update the fee directly, while returning the updated fee provides an explicit alternative design.
