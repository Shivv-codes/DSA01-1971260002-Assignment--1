# Task 2 – A2: Understand Runtime Binding

## A2.1 – Early and Late Binding (2 Marks)

**Early binding:** The function call is resolved at compile time. Non-virtual functions use early binding.

**Late binding:** The function call is resolved at runtime according to the actual object type. Virtual functions use late binding.

Example:

```cpp
Bill* b = new OutPatientBill(...);
b->total();
```

If `total()` is virtual, the program calls `OutPatientBill::total()` at runtime.

## A2.2 – Virtual Table (2 Marks)

A virtual table (vtable) stores function addresses used for runtime polymorphism. A compiler commonly maintains a vtable for each polymorphic class.

### Conceptual Vtables

```text
Bill vtable
-----------------
Bill::total()
Bill::type()
Bill::~Bill()

OutPatientBill vtable
-----------------
OutPatientBill::total()
OutPatientBill::type()
OutPatientBill::~OutPatientBill()

InPatientBill vtable
-----------------
InPatientBill::total()
InPatientBill::type()
InPatientBill::~InPatientBill()
```

### Runtime Call Flow

```text
Bill* b
   |
   v
OutPatientBill object
   |
   v
Virtual table pointer
   |
   v
OutPatientBill vtable
   |
   v
OutPatientBill::total()
```

The exact vtable implementation is compiler-dependent; this diagram represents the usual implementation model.

## A2.3 – Virtual Destructor (1 Mark)

A virtual destructor is required because derived bill objects may be deleted through base-class pointers.

```cpp
virtual ~Bill() = default;
```

Without a virtual destructor, deleting a derived object through a base pointer causes undefined behaviour. The derived destructor may not execute, resulting in resource leaks.

## Predict the Output

Given the program in the assignment:

```text
500 ~OPBill ~Bill
```

**Explanation:**
1. `b->show()` calls the non-virtual `show()` function.
2. Inside `show()`, `total()` is virtual, so `OPBill::total()` executes and prints `500`.
3. `delete b` calls the derived destructor first, printing `~OPBill`.
4. The base destructor executes next, printing `~Bill`.

Removing `virtual` from `~Bill` makes deletion through `Bill*` undefined behaviour. The sanitizer may report a problem, but the exact diagnostic depends on the compiler and runtime.

## Alternative Concept – Dynamic Casting

Without virtual `total()`, the program could use `dynamic_cast` to identify each derived bill and call its corresponding function.

```cpp
if (auto* op = dynamic_cast<OPBill*>(b))
    cout << op->total();
else if (auto* ip = dynamic_cast<InPatientBill*>(b))
    cout << ip->total();
```

For k bill types, this may require up to k type checks per bill. A virtual call selects the correct implementation directly.

Both approaches process n bills in O(n) overall when the number of types is fixed. Polymorphic objects commonly carry an implementation-specific vtable pointer, which adds some per-object storage.
