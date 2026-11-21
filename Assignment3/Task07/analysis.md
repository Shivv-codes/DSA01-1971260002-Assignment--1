# Q7 – Design a Complex-Number Class from First Principles

**Marks: 10**

## Stage 1 – Interpret the Problem (2 Marks)

### 1. Data and Operations

A complex number consists of:
- Real part: \(a\)
- Imaginary part: \(b\)
- Complex representation: \(a+bj\)

The required operations are:
- Addition and subtraction.
- Multiplication and division.
- Equality comparison.
- Display in rectangular form.
- Magnitude calculation.
- Safe handling of division by zero.

### 2. Constructors Required

1. Default constructor: Initializes the complex number to 0 + 0j.
2. Single-argument constructor: Initializes only the real part.
3. Two-argument constructor: Initializes both real and imaginary parts.

### 3. Measurable Requirements

- All arithmetic operations must produce correct real and imaginary parts.
- Magnitude must be calculated correctly.
- Division by zero must be handled safely.
- Series and parallel impedance calculations must be verified.

## Stage 2 – Design (3 Marks)

### 1. Complex Class Design

```text
Complex
 |
 |-- Private:
 |     real
 |     imag
 |
 |-- Public:
       Complex()
       Complex(real)
       Complex(real, imag)
       operator+
       operator-
       operator*
       operator/
       operator==
       magnitude()
       operator<<
```

### 2. Mathematical Formulas

For two complex numbers:

\[
Z_1=a+bj,\quad Z_2=c+dj
\]

| Operator | Formula |
|---|---|
| Addition | \((a+c)+(b+d)j\) |
| Subtraction | \((a-c)+(b-d)j\) |
| Multiplication | \((ac-bd)+(ad+bc)j\) |
| Division | \(\frac{(ac+bd)+(bc-ad)j}{c^2+d^2}\) |
| Magnitude | \(\sqrt{a^2+b^2}\) |

The division operator must check whether \(c^2+d^2=0\) before performing division.

### 3. Member and Friend Operators

- Arithmetic operators are implemented as member functions.
- Equality is implemented as a member function.
- Stream insertion `<<` is implemented as a friend function because the left operand is `ostream`.

## Stage 3 – Implement and Verify (3 Marks)

### 1. Complete C++ Program

```cpp
#include <iostream>
#include <cmath>
#include <stdexcept>
#include <iomanip>
using namespace std;

class Complex {
    double real, imag;

public:
    Complex() : real(0), imag(0) {}

    Complex(double r) : real(r), imag(0) {}

    Complex(double r, double i) : real(r), imag(i) {}

    Complex operator+(const Complex& c) const {
        return Complex(real + c.real, imag + c.imag);
    }

    Complex operator-(const Complex& c) const {
        return Complex(real - c.real, imag - c.imag);
    }

    Complex operator*(const Complex& c) const {
        return Complex(real * c.real - imag * c.imag,
                       real * c.imag + imag * c.real);
    }

    Complex operator/(const Complex& c) const {
        double d = c.real * c.real + c.imag * c.imag;

        if (d == 0)
            throw domain_error("Division by zero");

        return Complex((real * c.real + imag * c.imag) / d,
                       (imag * c.real - real * c.imag) / d);
    }

    bool operator==(const Complex& c) const {
        return real == c.real && imag == c.imag;
    }

    double magnitude() const {
        return sqrt(real * real + imag * imag);
    }

    friend ostream& operator<<(ostream& out, const Complex& c) {
        out << c.real;

        if (c.imag >= 0)
            out << "+";

        out << c.imag << "j";
        return out;
    }
};

int main() {
    cout << fixed << setprecision(2);

    const double pi = acos(-1.0);
    const double f = 50.0;
    const double R = 10.0;
    const double L = 0.01;
    const double C = 0.0001;

    double w = 2 * pi * f;
    double XL = w * L;
    double XC = 1 / (w * C);

    Complex resistor(R, 0);
    Complex inductor(0, XL);
    Complex capacitor(0, -XC);

    Complex series = resistor + inductor + capacitor;
    Complex parallel = (resistor * capacitor) /
                       (resistor + capacitor);

    cout << "Series impedance: " << series << '\n';
    cout << "Series magnitude: " << series.magnitude() << '\n';

    cout << "Parallel impedance: " << parallel << '\n';
    cout << "Parallel magnitude: " << parallel.magnitude() << '\n';

    try {
        Complex zero(0, 0);
        Complex result = resistor / zero;
        cout << result << '\n';
    } catch (const domain_error& e) {
        cout << e.what() << '\n';
    }

    return 0;
}
```

### 2. Hand Verification

Given:

- Resistance \(R=10\ \Omega\)
- Inductance \(L=0.01\ H\)
- Capacitance \(C=100\ \mu F\)
- Frequency \(f=50\ Hz\)

Angular frequency:

\[
\omega=2\pi f=314.16
\]

Inductive reactance:

\[
X_L=\omega L=3.14\ \Omega
\]

Capacitive reactance:

\[
X_C=\frac{1}{\omega C}=31.83\ \Omega
\]

**Series RLC impedance:**

\[
Z=R+j(X_L-X_C)
\]

\[
Z=10-j28.69\ \Omega
\]

Magnitude:

\[
|Z|=\sqrt{10^2+(-28.69)^2}
\]

\[
|Z|\approx30.38\ \Omega
\]

**Parallel RC impedance:**

\[
Z_p=\frac{Z_R Z_C}{Z_R+Z_C}
\]

\[
Z_p\approx9.10-j2.86\ \Omega
\]

Magnitude:

\[
|Z_p|=\sqrt{9.10^2+(-2.86)^2}
\]

\[
|Z_p|\approx9.54\ \Omega
\]

### 3. Expected Output

```text
Series impedance: 10.00-28.69j
Series magnitude: 30.38
Parallel impedance: 9.10-2.86j
Parallel magnitude: 9.54
Division by zero
```

The final line is the exception message from the program.

## Stage 4 – Defend the Design (2 Marks)

### 1. Comparison with std::complex<double>

| Feature | Custom Complex class | std::complex<double> |
|---|---|---|
| Storage | Two doubles | Two doubles |
| Addition | O(1) | O(1) |
| Multiplication | O(1) | O(1) |
| Division | O(1) | O(1) |
| Implementation | Must be written manually | Already provided |
| Division by zero | Explicit exception handling | Does not provide this custom exception behaviour automatically |

### 2. Why Each Constructor and Operator Is Needed

- Default constructor initializes a zero complex number.
- Single-argument constructor supports real-only values.
- Two-argument constructor supports complete complex values.
- Arithmetic operators simplify impedance calculations.
- Equality operator compares two complex numbers.
- Stream insertion operator displays impedance in rectangular form.

### 3. Remaining Limitation

The implementation uses `double`, so floating-point rounding errors may occur. Exact equality comparison can be unreliable for calculated values. A tolerance-based comparison is more suitable for numerical calculations.

## Alternative Concept – std::complex<double>

The same impedance calculations can be implemented using:

```cpp
#include <complex>
using namespace std;

complex<double> R(10, 0);
complex<double> L(0, 3.14);
complex<double> C(0, -31.83);

complex<double> series = R + L + C;
complex<double> parallel = (R * C) / (R + C);
```

Both designs have O(1) time per arithmetic operation and use two doubles for each complex value. `std::complex<double>` reduces implementation work and is well tested. A custom class is useful when the application needs specialized validation, formatting or division-by-zero handling.

## Final Conclusion

The Complex class successfully models AC impedance using constructors and overloaded operators. It performs series and parallel calculations, displays rectangular values and handles division by zero using an exception. The custom design demonstrates operator overloading, while `std::complex<double>` is preferable for standard numerical applications.
