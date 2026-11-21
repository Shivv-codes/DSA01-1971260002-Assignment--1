# Task 6 – Design Challenge: Vehicle Rental Fleet

**Marks: 13**

## Stage 1 – Interpret the Problem (2 Marks)

### 1. Entities and Data

| Entity | Data members | Behaviour |
|---|---|---|
| Vehicle | Registration number, daily rate, rental status | Rent, return, calculate charge |
| Bicycle | Vehicle details | Calculate bicycle rental charge |
| TwoWheeler | Fuel level, service due date | Calculate two-wheeler charge |
| Car | Fuel level, service due date, seating capacity | Calculate car charge |
| ElectricCar | Battery capacity, charge level, service due date | Calculate electric rental charge |
| ElectricBus | Battery capacity, charge level, service due date | Calculate electric bus charge |

### 2. Testable Requirements

1. Every vehicle must have a unique registration number and daily rental rate.
2. Every vehicle must calculate its rental charge.
3. Motor vehicles must store fuel or battery level and service due date.
4. Cars must store seating capacity.
5. Electric vehicles must calculate energy charges using kWh.
6. Vehicles must support renting, returning and listing.
7. A new vehicle type must be added without modifying existing vehicle classes.

## Stage 2 – Propose the Hierarchy (2 Marks)

### Class Hierarchy

```text
                  Vehicle (Abstract)
                ---------------------
                registrationNo
                dailyRate
                rented
                rentalCharge() = 0
                rent()
                returnVehicle()
                ---------------------
                  /    |     \
                 /     |      \
          Bicycle  TwoWheeler   Car
                                |
                           ElectricCar

                  ElectricBus
                      |
                   Vehicle
```

- **Single inheritance:** Each vehicle type inherits from `Vehicle`.
- **Hierarchical inheritance:** Multiple vehicle classes inherit from the same base class.
- **Multilevel inheritance:** ElectricCar inherits from Car in this design.

## Stage 3 – Critical Design Decisions (2 Marks)

- `Vehicle` is an abstract base class because every vehicle must implement `rentalCharge()`.
- Common details such as registration number, daily rate and rental status belong in `Vehicle`.
- `rentalCharge()` is a pure virtual function.
- `display()` is virtual to support runtime polymorphism.
- A virtual destructor ensures correct destruction through base pointers.
- The fleet is stored as an array of `Vehicle*`.
- Derived classes store their own specific data.

## Stage 4 – Design the Classes and Pseudocode (4 Marks)

### 1. Class Declarations and Implementation

```cpp
#include <iostream>
#include <string>
#include <iomanip>
using namespace std;

class Vehicle {
protected:
    string regNo;
    double dailyRate;
    bool rented;

public:
    Vehicle(string r, double rate)
        : regNo(r), dailyRate(rate), rented(false) {}

    virtual double rentalCharge(int days) const = 0;

    bool rent() {
        if (rented) return false;
        rented = true;
        return true;
    }

    double returnVehicle(int lateDays) {
        if (!rented || lateDays < 0)
            return -1;

        rented = false;
        return rentalCharge(1) * 0 + lateDays * 200;
    }

    virtual void display() const {
        cout << regNo << " Rate: " << dailyRate
             << " Rented: " << boolalpha << rented << '\n';
    }

    virtual ~Vehicle() = default;
};

class Bicycle : public Vehicle {
public:
    Bicycle(string r, double rate) : Vehicle(r, rate) {}

    double rentalCharge(int days) const override {
        return dailyRate * days;
    }
};

class TwoWheeler : public Vehicle {
    double fuelLevel;
    string serviceDate;

public:
    TwoWheeler(string r, double rate, double fuel, string date)
        : Vehicle(r, rate), fuelLevel(fuel), serviceDate(date) {}

    double rentalCharge(int days) const override {
        return dailyRate * days + 50;
    }

    void display() const override {
        Vehicle::display();
        cout << "Fuel: " << fuelLevel
             << " Service: " << serviceDate << '\n';
    }
};

class Car : public Vehicle {
protected:
    double fuelLevel;
    string serviceDate;
    int seats;

public:
    Car(string r, double rate, double fuel,
        string date, int s)
        : Vehicle(r, rate), fuelLevel(fuel),
          serviceDate(date), seats(s) {}

    double rentalCharge(int days) const override {
        return dailyRate * days + 100;
    }

    void display() const override {
        Vehicle::display();
        cout << "Fuel: " << fuelLevel
             << " Seats: " << seats
             << " Service: " << serviceDate << '\n';
    }
};

class ElectricCar : public Car {
    double batteryCapacity;
    double chargeLevel;
    double kwhRate;

public:
    ElectricCar(string r, double rate, double fuel,
                string date, int seats,
                double capacity, double charge, double energyRate)
        : Car(r, rate, fuel, date, seats),
          batteryCapacity(capacity),
          chargeLevel(charge), kwhRate(energyRate) {}

    double rentalCharge(int days) const override {
        double energyUsed = batteryCapacity *
                            (100 - chargeLevel) / 100;

        return dailyRate * days + energyUsed * kwhRate;
    }

    void display() const override {
        Car::display();
        cout << "Battery: " << batteryCapacity
             << " kWh, Charge: " << chargeLevel << "%\n";
    }
};

// New type added without modifying existing classes.
class ElectricBus : public Vehicle {
    double batteryCapacity;
    double kwhRate;
    string serviceDate;

public:
    ElectricBus(string r, double rate, double capacity,
                double energyRate, string date)
        : Vehicle(r, rate), batteryCapacity(capacity),
          kwhRate(energyRate), serviceDate(date) {}

    double rentalCharge(int days) const override {
        return dailyRate * days +
               batteryCapacity * 0.2 * kwhRate;
    }

    void display() const override {
        Vehicle::display();
        cout << "Battery: " << batteryCapacity
             << " kWh Service: " << serviceDate << '\n';
    }
};

int main() {
    cout << fixed << setprecision(2);

    Bicycle b("BIKE01", 100);
    TwoWheeler t("BIKE02", 200, 5, "2026-12-01");
    Car c("CAR01", 1000, 30, "2026-12-10", 5);

    ElectricCar e("EV01", 1500, 0, "2026-12-15",
                   5, 60, 50, 10);

    ElectricBus bus("BUS01", 3000, 200, 10, "2026-12-20");

    Vehicle* fleet[] = {&b, &t, &c, &e, &bus};

    cout << "VEHICLE LIST\n";

    for (Vehicle* v : fleet)
        v->display();

    cout << "\nRENTAL CHARGES FOR 2 DAYS\n";

    for (Vehicle* v : fleet)
        cout << v->rentalCharge(2) << '\n';

    cout << "\nRENTAL TEST\n";

    if (c.rent())
        cout << "Car rented successfully\n";

    cout << "Late charge: " << c.returnVehicle(2) << '\n';

    return 0;
}
```

### 2. Pseudocode – Renting a Vehicle

```text
ALGORITHM RentVehicle
INPUT vehicle

IF vehicle is already rented
    PRINT "Vehicle unavailable"
ELSE
    SET vehicle.rented = TRUE
    PRINT "Vehicle rented successfully"
END IF

END ALGORITHM
```

### 3. Pseudocode – Returning a Vehicle

```text
ALGORITHM ReturnVehicle
INPUT vehicle, lateDays

IF vehicle is not rented OR lateDays < 0
    PRINT "Invalid return"
ELSE
    SET vehicle.rented = FALSE
    CALCULATE lateCharge = lateDays * 200
    PRINT lateCharge
END IF

END ALGORITHM
```

## Stage 5 – Implement and Demonstrate (3 Marks)

### Expected Demonstration

The program demonstrates:

- Creation of bicycle, two-wheeler, car and electric car objects.
- Listing all vehicles using base-class pointers.
- Runtime polymorphism through `rentalCharge()`.
- Renting a car successfully.
- Returning the car with a two-day late charge.
- Adding an ElectricBus without changing the existing vehicle classes.

For the car, the late charge is:

\[
2 \times ₹200 = ₹400
\]

The ElectricBus class is added by deriving it from `Vehicle` and implementing its own `rentalCharge()` and `display()` functions.

## Alternative Concept – Single Vehicle Class with Switch

A single `Vehicle` class could store a type code and calculate charges using a `switch` statement.

Example:

```cpp
double charge(int type, int days) {
    switch (type) {
        case 1: return 100 * days;
        case 2: return 200 * days + 50;
        case 3: return 1000 * days + 100;
        default: return 0;
    }
}
```

| Comparison | Inheritance | Single class with switch |
|---|---|---|
| Charge calculation | O(1) | O(1) |
| Storage | Common and derived fields | May contain unused fields |
| New vehicle type | Add a derived class | Modify switch and related logic |
| Extensibility | High | Lower |
| Runtime polymorphism | Supported | Not required |

Both designs have O(1) time per charge calculation. A derived object may contain a vtable pointer for virtual dispatch, while a single class may waste memory storing fields unused by some vehicle types.

## Final Engineering Decision

I recommend the abstract `Vehicle` base class with derived vehicle types. It separates common information from vehicle-specific behaviour, supports runtime polymorphism and allows new types such as ElectricBus to be introduced without modifying existing vehicle classes.
