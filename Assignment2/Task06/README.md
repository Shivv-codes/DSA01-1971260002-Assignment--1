# Q6 – Friend Functions and Inline Functions

## 1. Friend Functions

A friend function is not a member of a class, but it is allowed to access the class's private and protected members.

In the train journey planner, `minutesBetween()` and `earlier()` compare two `Time` objects equally. Friend functions are suitable because neither time object is treated as the main object.

## 2. Completed Program

```cpp
#include <iostream>
#include <cstdlib>
using namespace std;

class Time {
    int h, m;

public:
    Time(int hh, int mm) : h(hh), m(mm) {}

    friend int minutesBetween(const Time& a, const Time& b);
    friend bool earlier(const Time& a, const Time& b);
};

inline int minutesBetween(const Time& a, const Time& b) {
    int x = a.h * 60 + a.m;
    int y = b.h * 60 + b.m;
    return abs(x - y);
}

inline bool earlier(const Time& a, const Time& b) {
    return a.h * 60 + a.m < b.h * 60 + b.m;
}

int main() {
    Time departure(9, 45), arrival(13, 10);

    cout << minutesBetween(departure, arrival) << ' '
         << boolalpha << earlier(arrival, departure) << '\n';

    Time t1(11, 30), t2(12, 15);
    cout << minutesBetween(t1, t2) << ' '
         << earlier(t1, t2) << '\n';

    Time t3(10, 0), t4(10, 0);
    cout << minutesBetween(t3, t4) << ' '
         << earlier(t3, t4) << '\n';
}
```

## 3. Expected Output

```text
205 false
45 true
0 false
```

**Explanation:**
- From 9:45 to 13:10, the difference is 205 minutes.
- 13:10 is not earlier than 9:45.
- From 11:30 to 12:15, the difference is 45 minutes, and 11:30 is earlier.
- Equal times have a difference of zero, and neither is earlier.

## 4. Friend Function vs Member Function

| Friend function | Member function |
|---|---|
| Not a class member | Belongs to the class |
| Called like `earlier(a,b)` | Called like `a.isEarlier(b)` |
| Can access private members through friendship | Can access its own object's private members |
| Compares two objects equally | Usually operates with one object as the caller |

## 5. Inline Functions

The `inline` keyword suggests that the compiler may substitute the function body at the call site to reduce function-call overhead. It does not guarantee that the compiler will inline the function.

These functions are small and called frequently, so inline is a reasonable option. The compiler makes the final decision.

## 6. Alternative Concept – Member Functions and Getters

The comparison can also be implemented using member functions such as `a.minutesTo(b)` and `a.isEarlier(b)`, or using public getter functions.

All approaches perform a constant number of operations.

- Time complexity per comparison: O(1).
- Extra space per call: O(1).
- Encapsulation: private members are protected in both friend and member-function designs. Public data members would weaken encapsulation.

## 7. Performance Measurement

To compare performance, execute a loop of 10,000,000 calls using two builds:

```bash
g++ -std=c++17 -O0 main.cpp -o time_O0
g++ -std=c++17 -O2 main.cpp -o time_O2
```

Record the actual execution times for both builds. Do not assume inline always produces a faster result: compiler optimisation and inlining decisions can affect the measurements.

## 8. Final Decision

I recommend retaining the friend functions because both departure and arrival times are treated equally, and the functions need access to private members. Inline may be used as an optimisation suggestion, while private data members preserve encapsulation.
