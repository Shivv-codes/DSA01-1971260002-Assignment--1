# Q6 – The Rules of Operator Overloading

## 1. Text Class Implementation

The `Text` class stores a dynamically allocated character array. It implements a constructor, copy constructor, copy assignment operator and destructor to manage memory safely.

```cpp
#include <iostream>
#include <cstring>
using namespace std;

class Text {
    char* data;

public:
    Text(const char* str = "") {
        data = new char[strlen(str) + 1];
        strcpy(data, str);
    }

    Text(const Text& t) {
        data = new char[strlen(t.data) + 1];
        strcpy(data, t.data);
    }

    Text& operator=(const Text& t) {
        if (this != &t) {
            char* temp = new char[strlen(t.data) + 1];
            strcpy(temp, t.data);

            delete[] data;
            data = temp;
        }
        return *this;
    }

    ~Text() {
        delete[] data;
    }

    Text operator+(const Text& t) const {
        char* temp = new char[strlen(data) + strlen(t.data) + 1];

        strcpy(temp, data);
        strcat(temp, t.data);

        Text result(temp);
        delete[] temp;
        return result;
    }

    bool operator==(const Text& t) const {
        return strcmp(data, t.data) == 0;
    }

    bool operator!=(const Text& t) const {
        return !(*this == t);
    }

    friend ostream& operator<<(ostream& out, const Text& t) {
        return out << t.data;
    }
};

int main() {
    Text a("Hello ");
    Text b("World");
    Text c = a + b;

    cout << c << '\n';
    cout << boolalpha << (a == b) << '\n';
    cout << (a != b) << '\n';

    a = a;
    Text empty;
    cout << "Empty: " << empty << '\n';

    Text longMessage("This is a longer SMS message");
    cout << longMessage << '\n';
}
```

## 2. Expected Output

```text
Hello World
false
true
Empty:
This is a longer SMS message
```

The self-assignment `a = a` is safe because the assignment operator checks whether both objects are the same before replacing the allocated memory.

## 3. Operators That Cannot Be Overloaded

The following operators cannot be overloaded in C++:

| Operator | Meaning |
|---|---|
| `::` | Scope resolution |
| `.` | Member access |
| `.*` | Pointer-to-member access |
| `?:` | Conditional operator |
| `sizeof` | Size operator |

## 4. Rules of Operator Overloading

1. At least one operand must be a user-defined type.
2. Operator precedence cannot be changed.
3. Operator associativity cannot be changed.
4. The number of operands (arity) cannot be changed.
5. New operators cannot be created.
6. The operators `=`, `[]`, `()` and `->` must be overloaded as member functions.

## 5. Why operator= Must Be a Member

The assignment operator must be a member function because it modifies the left-hand object and is subject to the C++ operator-overloading rules.

The self-assignment check prevents the object from deleting its own data before copying it.

## 6. When Should an Operator Be a Friend?

A friend function is useful when an operator needs access to private members but the left operand is not the class object.

For example, `<<` must be a non-member function because its left operand is `ostream`. Declaring it as a friend allows it to access the private character array.

## 7. Alternative Concept – std::string

Instead of manually allocating and releasing character arrays, `std::string` can manage memory automatically.

```cpp
#include <string>
using namespace std;

string a = "Hello ";
string b = "World";
string c = a + b;
```

This follows the Rule of Zero because the standard string class manages its own resources.

Another alternative is implementing `operator+` using `operator+=` on a copy:

```cpp
Text operator+(Text a, const Text& b) {
    a += b;
    return a;
}
```

This requires implementing `operator+=` and managing the copy of the left operand.

## 8. Complexity Analysis

Let the lengths of the three strings be n1, n2 and n3.

For:

```cpp
Text result = a + b + c;
```

The expression is evaluated as `(a + b) + c`.

- First concatenation copies n1 + n2 characters.
- Second concatenation copies n1 + n2 + n3 characters.
- Total character-copying work is O(n1 + n2 + n3).
- Temporary objects and buffers require additional memory proportional to the combined string lengths.

Using `std::string` also requires work proportional to the number of characters copied, but it provides automatic memory management and well-tested operations.

## 9. Experimental Investigation

Compile using AddressSanitizer:

```bash
g++ -std=c++17 -Wall -Wextra -fsanitize=address -g main.cpp -o text
```

Test:
- Empty strings.
- Long messages.
- `a = a` self-assignment.
- `a + b + c` concatenation.
- Copy construction and copy assignment.

Compare the custom `Text` class with `std::string` for 100,000 concatenations. Record actual execution times and sanitizer output.

## 10. Conclusion

Implementing the `Text` class demonstrates dynamic memory management, deep copying, operator overloading and the Rule of Three. For ordinary applications, `std::string` is preferable because it manages memory automatically. A custom class is useful when specialized memory management or custom behaviour is required.
