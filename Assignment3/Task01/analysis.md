# Q1 – Constructors and Constructor Overloading

## 1. Book Class Implementation

A constructor initializes an object when it is created. Different constructors are used for different book-record situations.

```cpp
#include <iostream>
#include <string>
#include <stdexcept>
using namespace std;

class Book {
    string title, author;
    int copies;

public:
    Book() : Book("", "", 0) {}

    Book(const string& t) : Book(t, "Unknown", 1) {}

    Book(const string& t, const string& a, int c)
        : title(t), author(a), copies(c) {
        if (c < 0)
            throw invalid_argument("Invalid copies");
    }

    void display() const {
        cout << title << " | " << author
             << " | Copies: " << copies << '\n';
    }
};

int main() {
    Book b1;
    Book b2("C++ Basics");
    Book b3("OOP in C++", "Bjarne", 5);

    b1.display();
    b2.display();
    b3.display();
}
```

## 2. Expected Output

```text
 |  | Copies: 0
C++ Basics | Unknown | Copies: 1
OOP in C++ | Bjarne | Copies: 5
```

A negative number of copies throws an `invalid_argument` exception, preventing an invalid Book object from being successfully constructed.

## 3. Constructor Concepts

- **Default constructor:** Creates an empty book record.
- **Parameterized constructor:** Initializes all book details.
- **Overloaded constructor:** Allows creation using different argument lists.
- **Constructor delegation:** The default and title-only constructors call the main constructor to avoid repeating initialization logic.
- **Member initializer list:** Initializes members directly before the constructor body executes.

The compiler supplies a default constructor only when no user-declared constructor prevents its implicit generation. If a parameterized constructor is declared, the compiler does not automatically provide a default constructor.

## 4. Member Initialization Order

Members are initialized in the order in which they are declared inside the class, not the order written in the initializer list.

Using initializer lists is preferred because members are initialized directly rather than first being default-constructed and then assigned.

## 5. Object States

| Object | Title | Author | Copies |
|---|---|---|---:|
| `b1` | Empty | Empty | 0 |
| `b2` | C++ Basics | Unknown | 1 |
| `b3` | OOP in C++ | Bjarne | 5 |

## 6. Explicit Constructor

The `explicit` keyword prevents unintended implicit conversions.

```cpp
class Book {
public:
    explicit Book(string title) {}
};
```

Without `explicit`, this may compile:

```cpp
Book b = "C++ Basics";
```

With `explicit`, the implicit conversion is rejected. Direct initialization remains valid:

```cpp
Book b("C++ Basics");
```

## 7. Experimental Investigation

- Removing the default constructor makes `Book books[3];` invalid if no other default constructor is available.
- Initializing members in a different order from their declaration can produce a `-Wreorder` warning.
- Passing a negative copy count must be tested to verify that the exception is thrown.

## 8. Alternative Concept – Default Arguments

A single constructor could use default arguments:

```cpp
Book(string t = "", string a = "", int c = 0);
```

Both approaches take O(1) time and O(1) object space. However, constructor delegation with overloads provides clearer interfaces for different creation situations and centralizes copy validation in one constructor.
