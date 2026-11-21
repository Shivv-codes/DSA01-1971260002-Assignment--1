# Q5 – Static Members and Arrays of Objects

## 1. Static Data Members

A static data member belongs to the entire class rather than an individual object. Therefore, only one shared copy exists regardless of how many ticket objects are created.

For the bus ticket system:
- `issued` is static because all tickets share the same issued-seat count.
- `CAPACITY` is a class-wide constant.
- `seat` and `name` are non-static because they belong to individual tickets.

## 2. Predict the Output – Given Program

```text
101 103 104 4
```

**Explanation:**
- `next` begins at 100.
- Three objects are created in the array, receiving IDs 101, 102 and 103.
- The extra object receives ID 104.
- `issued()` returns `next - 100`, which is 4.

## 3. Complete the Code

```cpp
#include <iostream>
#include <string>
using namespace std;

class BusTicket {
    static int issued;
    static const int CAPACITY = 3;

    int seat;
    string name;

public:
    explicit BusTicket(const string& n)
        : seat(0), name(n) {
        if (issued < CAPACITY)
            seat = ++issued;
    }

    static int count() {
        return issued;
    }

    void print() const {
        cout << name << ": "
             << (seat ? "Seat " + to_string(seat)
                      : string("Waiting"))
             << '\n';
    }
};

int BusTicket::issued = 0;

int main() {
    BusTicket trip[4] = {
        BusTicket("Asha"),
        BusTicket("Bala"),
        BusTicket("Chitra"),
        BusTicket("Dev")
    };

    for (const BusTicket& t : trip)
        t.print();

    cout << "Issued: " << BusTicket::count() << '\n';
}
```

## 4. Expected Output

```text
Asha: Seat 1
Bala: Seat 2
Chitra: Seat 3
Dev: Waiting
Issued: 3
```

## 5. Static Member Function

A static member function belongs to the class and can be called using the class name. It does not have a `this` pointer because it is not associated with a particular object.

Therefore, it cannot directly access non-static members such as `seat` or `name`.

## 6. Arrays of Objects and Constructor Calls

An array of four `BusTicket` objects creates four objects. Each constructor executes once. The first three receive seats, and the fourth enters the waiting list.

If tickets are created inside a function called twice, the static counter continues from its previous value because it is shared and is not reset when the function returns.

## 7. Alternative Concept – Global Counter or TicketOffice

A global variable can store the issued count, but it is less encapsulated. A separate `TicketOffice` object can manage the counter and be passed to each ticket.

| Design | Time per ticket | Counter storage |
|---|---|---|
| Static member | O(1) | One per class |
| Global variable | O(1) | One global counter |
| TicketOffice object | O(1) | One per office |

A `TicketOffice` object is more flexible when two buses need independent seat counters. A static member is suitable when all tickets share one common capacity and counter.
