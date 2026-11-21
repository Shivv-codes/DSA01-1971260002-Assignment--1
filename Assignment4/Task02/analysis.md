# Task 2 – From Requirement to Hierarchy

## 2.1 Identify the Relationships (2 Marks)

| Relationship | Type | Inheritance |
|---|---|---|
| Student is a Person | Is-a | Single |
| Staff is a Person | Is-a | Single |
| TeachingAssistant is a Student and Staff | Is-a | Multiple |
| ResearchScholar is a Student | Is-a | Multilevel |
| Student has a Programme | Has-a | Composition |
| Staff has a Department | Has-a | Composition |
| TeachingAssistant inherits Student and Staff | Is-a | Hybrid |

## 2.2 Resolve the Diamond Problem (2 Marks)

If both `Student` and `Staff` inherit normally from `Person`, then `TeachingAssistant` receives two separate `Person` subobjects.

This creates ambiguity when accessing members such as `name`.

Virtual inheritance solves this problem by ensuring that only one shared `Person` base subobject exists.

```cpp
class Student : virtual public Person {};
class Staff : virtual public Person {};

class TeachingAssistant : public Student, public Staff {};
```

## 2.3 Class Design (3 Marks)

```cpp
#include <iostream>
#include <string>
using namespace std;

class Person {
protected:
    string name;
    int id;
    string phone;

public:
    Person(string n, int i, string p)
        : name(n), id(i), phone(p) {}

    void display() const {
        cout << name << " " << id << " " << phone << '\n';
    }
};

class Student : virtual public Person {
protected:
    string programme;
    double cgpa;

public:
    Student(string n, int i, string p,
            string prog, double c)
        : Person(n, i, p), programme(prog), cgpa(c) {}
};

class Staff : virtual public Person {
protected:
    string department;
    double salary;

public:
    Staff(string n, int i, string p,
          string dept, double s)
        : Person(n, i, p), department(dept), salary(s) {}
};

class TeachingAssistant : public Student, public Staff {
public:
    TeachingAssistant(string n, int i, string p,
                      string prog, double c,
                      string dept, double s)
        : Person(n, i, p),
          Student(n, i, p, prog, c),
          Staff(n, i, p, dept, s) {}
};

class ResearchScholar : public Student {
    double fellowship;

public:
    ResearchScholar(string n, int i, string p,
                    string prog, double c, double f)
        : Person(n, i, p),
          Student(n, i, p, prog, c),
          fellowship(f) {}
};

int main() {
    TeachingAssistant ta("Kavya", 101, "9876543210",
                         "CSE", 8.5, "Computer Science", 25000);

    ResearchScholar rs("Arun", 102, "9876501234",
                       "Biotechnology", 9.0, 15000);

    ta.display();
    rs.display();
}
```

**Access decisions:**
- `Student` and `Staff` publicly inherit `Person`.
- `TeachingAssistant` publicly inherits both `Student` and `Staff`.
- `ResearchScholar` publicly inherits `Student`.
- `programme` and `department` are stored as member data to represent associated information.

## 2.4 Access Table (1 Mark)

For a public member of `Person`, a protected member and a private member, visibility inside `Student` is:

| Person member | Public inheritance | Protected inheritance | Private inheritance |
|---|---|---|---|
| Public | Public | Protected | Private |
| Protected | Protected | Protected | Private |
| Private | Not directly accessible | Not directly accessible | Not directly accessible |

## Predict the Output

For the given virtual inheritance program:

```text
Person Student Staff TA
```

**Explanation:** `Person` is constructed only once because both `Student` and `Staff` virtually inherit it. The virtual base is constructed first, followed by direct base classes in declaration order, and then the derived class.

Without `virtual`, two separate `Person` subobjects are created:

```text
Person Student Person Staff TA
```

This can cause ambiguity when accessing inherited `Person` members through `TeachingAssistant`.

## Alternative Concept – Composition

Instead of multiple inheritance, `TeachingAssistant` can contain separate `Student` and `Staff` objects.

```cpp
class TeachingAssistant {
    Student student;
    Staff staff;
};
```

This represents a has-a relationship rather than an is-a relationship.

| Design | Person subobjects | Access |
|---|---:|---|
| Virtual inheritance | 1 shared Person | Direct inherited access |
| Non-virtual diamond | 2 Person subobjects | May be ambiguous |
| Composition | One inside each member object | Access through member objects |

The exact `sizeof` values depend on the compiler, alignment and virtual inheritance implementation. They should be measured using `sizeof(TeachingAssistant)` on the target compiler.
