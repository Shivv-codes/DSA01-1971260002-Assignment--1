# Task 3 – Constructors in Derived Classes and Member Classes

## 3.1 Pass Values to Base Constructors (3 Marks)

Constructors in derived classes use member initializer lists to pass values to their base classes.

When virtual inheritance is used, the most-derived class is responsible for constructing the virtual base class. Therefore, `TeachingAssistant` must directly initialize `Person`.

## 3.2 Member Classes (2 Marks)

- `Address` is a member class of `Person`.
- `Course` is a member class of `Student`.
- These represent has-a relationships.

## 3.3 Complete C++ Program

```cpp
#include <iostream>
#include <string>
using namespace std;

class Address {
    string city;
    int pin;

public:
    Address(string c, int p) : city(c), pin(p) {
        cout << "Address ";
    }

    ~Address() {
        cout << "~Address ";
    }

    string str() const {
        return city + "-" + to_string(pin);
    }
};

class Course {
    string title;

public:
    Course(string t) : title(t) {
        cout << "Course ";
    }

    ~Course() {
        cout << "~Course ";
    }
};

class Person {
protected:
    string name;
    int age;
    Address address;

public:
    Person(string n, int a, string city, int pin)
        : name(n), age(a), address(city, pin) {
        cout << "Person ";
    }

    virtual ~Person() {
        cout << "~Person ";
    }
};

class Student : virtual public Person {
protected:
    long long regNo;
    Course course;

public:
    Student(string n, int a, long long r,
            string city, int pin, string c)
        : Person(n, a, city, pin),
          regNo(r), course(c) {
        cout << "Student ";
    }

    ~Student() override {
        cout << "~Student ";
    }
};

class Staff : virtual public Person {
protected:
    string department;

public:
    Staff(string n, int a, string city, int pin, string d)
        : Person(n, a, city, pin), department(d) {
        cout << "Staff ";
    }

    ~Staff() override {
        cout << "~Staff ";
    }
};

class TeachingAssistant : public Student, public Staff {
public:
    TeachingAssistant(string n, int a, long long r,
                      string city, int pin,
                      string c, string d)
        : Person(n, a, city, pin),
          Student(n, a, r, city, pin, c),
          Staff(n, a, city, pin, d) {
        cout << "TA ";
    }

    ~TeachingAssistant() override {
        cout << "~TA ";
    }
};

int main() {
    cout << "Student object: ";
    {
        Student s("Kavya", 19, 192311001,
                  "Salem", 636001, "C++");
    }

    cout << "\nStaff object: ";
    {
        Staff s("Arun", 35, "Chennai", 600001, "CSE");
    }

    cout << "\nTeachingAssistant object: ";
    {
        TeachingAssistant ta("Meena", 22, 192311002,
                             "Salem", 636001, "C++", "CSE");
    }

    cout << '\n';
}
```

## 3.4 Construction and Destruction Order (3 Marks)

The construction order is:

1. Virtual base classes.
2. Direct base classes in declaration order.
3. Member objects in declaration order.
4. Derived class constructor body.

Destruction happens in reverse order.

For the `TeachingAssistant` object, the order is:

**Construction:**

```text
Address Person Course Student Staff TA
```

**Destruction:**

```text
~TA ~Staff ~Student ~Course ~Person ~Address
```

The `Person` virtual base is constructed only once. The `Address` member is constructed as part of `Person`, and `Course` is constructed as part of `Student`.

## Predict the Output – Given Car Program

```text
Vehicle Engine Car | ~Car ~Engine ~Vehicle
```

**Explanation:**
- `Vehicle` is the base class and is constructed first.
- `Engine` is a member object and is constructed second.
- `Car` is constructed last.
- On destruction, `Car` is destroyed first, then `Engine`, then `Vehicle`.

## Complete the Given Code – Student Constructor

The required constructor is:

```cpp
Student(const std::string& n, int a, long long r,
        const std::string& city, int pin)
    : Person(n, a), regNo(r), home(city, pin) {}
```

Expected output:

```text
192311001 Kavya (19) Salem-636001
```

## 3.5 Experiment – Remove virtual

Without virtual inheritance, `TeachingAssistant` contains two separate `Person` subobjects, one through `Student` and one through `Staff`.

This causes duplicate construction and destruction messages and may make `name` or `age` ambiguous. `sizeof(TeachingAssistant)` will generally increase, but the exact difference is compiler-dependent and must be measured.

## Alternative Concept – Address Pointer

If `Person` stored `Address*` instead of an `Address` member, it would need to allocate memory using `new` and release it using `delete`.

The class would then need:
- A destructor to delete the address.
- A deep-copy constructor to allocate independent address memory.
- A copy-assignment operator to safely replace the address.

A pointer uses extra pointer storage and dynamic allocation. Direct composition is simpler and safer because the member object's lifetime is automatically managed.
