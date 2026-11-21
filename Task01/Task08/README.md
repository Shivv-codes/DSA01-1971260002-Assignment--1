# Task 8 – Read and Print Data Safely

## 1. Reading Input Safely Using cin

The `cin` object is used to read input from the user. If the user enters letters instead of numbers, `cin.fail()` detects the input error.

- `cin.fail()` checks whether an input operation has failed.
- `cin.clear()` resets the error state of the input stream.
- `cin.ignore()` removes invalid characters from the input buffer.

**C++ Program:**

```cpp
#include <iostream>
#include <limits>
using namespace std;

int main()
{
    int hour;

    cout << "Enter hour: ";

    while (!(cin >> hour) || hour < 0 || hour > 23) {
        cout << "Invalid input. Enter hour (0-23): ";
        cin.clear();
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
    }

    cout << "Valid hour: " << hour << '\n';
    return 0;
}
```

This program validates the entered hour and allows the user to enter it again if the input is invalid.

## 2. Formatting Money Using iomanip

The `<iomanip>` header provides formatting manipulators.

| Manipulator | Purpose |
|---|---|
| `setw()` | Sets the minimum output width |
| `setfill()` | Fills empty positions with a specified character |
| `fixed` | Displays floating-point values in fixed notation |
| `setprecision()` | Sets the number of decimal places |

**Example:**

```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main()
{
    double fare = 36.0;

    cout << fixed << setprecision(2);
    cout << "Fare: Rs " << fare << '\n';

    return 0;
}
```

**Output:**
```text
Fare: Rs 36.00
```

## 3. Printing a Journey Report

A journey report displays the fare and remaining smart-card balance clearly.

```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main()
{
    int fare = 3600;
    int balance = 5400;

    cout << "Journey Report\n";

    cout << "Fare: Rs " << fare / 100 << '.'
         << setw(2) << setfill('0') << fare % 100 << '\n';

    cout << "Balance: Rs " << balance / 100 << '.'
         << setw(2) << setfill('0') << balance % 100 << '\n';

    return 0;
}
```

**Output:**
```text
Journey Report
Fare: Rs 36.00
Balance: Rs 54.00
```

## 4. Alternative Concept – getline() and stoi()

Instead of using `cin >>`, the program can read a complete line using `getline()` and convert it into an integer using `stoi()` inside a try-catch block.

```cpp
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string input;

    cout << "Enter hour: ";
    getline(cin, input);

    try {
        size_t pos;
        int hour = stoi(input, &pos);

        if (pos != input.size() || hour < 0 || hour > 23)
            throw invalid_argument("Invalid hour");

        cout << "Valid hour: " << hour << '\n';
    }
    catch (const exception&) {
        cout << "Invalid input\n";
    }

    return 0;
}
```

## 5. Complexity Comparison

| Factor | cin with validation | getline() with stoi() |
|---|---|---|
| Time complexity | O(L) | O(L) |
| Extra space | Input buffer | O(L) string buffer |
| Invalid input handling | clear() and ignore() | try-catch exception |
| Recovery | Reset stream and retry | Catch error and request input again |

Here, L represents the length of the input line.

Both approaches require O(L) time to process an input line. The `getline()` approach requires additional string-buffer space, while `cin` uses stream input buffering.

## Conclusion

Safe input handling prevents invalid values from entering the metro fare system. Using `cin.fail()`, `cin.clear()` and `cin.ignore()` allows recovery from incorrect input. The `<iomanip>` library helps display fares and balances accurately and neatly.
