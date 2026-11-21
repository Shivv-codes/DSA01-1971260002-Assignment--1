# Q5 – Overloading Increment, Comparison and Subscript Operators

## 1. Time Class Implementation

The `Time` class overloads prefix and postfix increment to advance the time by one second. It also overloads comparison operators.

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <stdexcept>
#include <iomanip>
using namespace std;

class Time {
    int h, m, s;

public:
    Time(int hh = 0, int mm = 0, int ss = 0)
        : h(hh), m(mm), s(ss) {}

    Time& operator++() {
        if (++s == 60) {
            s = 0;
            if (++m == 60) {
                m = 0;
                h = (h + 1) % 24;
            }
        }
        return *this;
    }

    Time operator++(int) {
        Time old = *this;
        ++(*this);
        return old;
    }

    bool operator==(const Time& t) const {
        return h == t.h && m == t.m && s == t.s;
    }

    bool operator<(const Time& t) const {
        if (h != t.h) return h < t.h;
        if (m != t.m) return m < t.m;
        return s < t.s;
    }

    void display() const {
        cout << setfill('0') << setw(2) << h << ':'
             << setw(2) << m << ':'
             << setw(2) << s << '\n';
    }
};

class Playlist {
    vector<string> tracks;

public:
    Playlist(vector<string> t) : tracks(t) {}

    string& operator[](int i) {
        if (i < 0 || i >= static_cast<int>(tracks.size()))
            throw out_of_range("Invalid track index");
        return tracks[i];
    }

    const string& operator[](int i) const {
        if (i < 0 || i >= static_cast<int>(tracks.size()))
            throw out_of_range("Invalid track index");
        return tracks[i];
    }

    int size() const {
        return static_cast<int>(tracks.size());
    }
};

int main() {
    Time t(0, 59, 59);

    t++;
    t.display();

    ++t;
    t.display();

    Playlist p({"Song A", "Song B", "Song C"});
    cout << p[1] << '\n';

    try {
        cout << p[3] << '\n';
    } catch (const out_of_range& e) {
        cout << e.what() << '\n';
    }
}
```

## 2. Expected Output

```text
01:00:00
01:00:01
Song B
Invalid track index
```

## 3. Prefix and Postfix Increment

**Prefix:** `++time` increments the object first and returns the updated object by reference.

**Postfix:** `time++` uses an additional dummy `int` parameter to distinguish it from prefix. It returns a copy of the old value.

Example:

```cpp
Time a = t++;
Time b = ++t;
```

Postfix returns the previous value, whereas prefix returns the updated value. Postfix may require an extra copy, making it less efficient for class types.

## 4. Predict the Output – Given Counter Program

```text
5 7 7
```

**Explanation:**
- Initially, `c.v` is 5.
- `a = c++` stores the old value 5 in `a`, then increments `c` to 6.
- `b = ++c` increments `c` to 7 and stores 7 in `b`.
- Final values are `a = 5`, `b = 7` and `c = 7`.

Postfix increment creates a copy of the original object.

## 5. Why Two Versions of operator[]?

The non-const version returns `string&`, allowing the caller to modify a track.

The const version returns `const string&`, allowing access without modification.

An invalid index should be handled safely. This implementation throws `std::out_of_range` instead of accessing invalid memory.

## 6. Complexity Analysis

| Operation | Time complexity |
|---|---|
| Prefix increment | O(1) |
| Postfix increment | O(1), with an extra copy |
| Time comparison | O(1) |
| Playlist access | O(1) |
| Checked playlist access | O(1), with an extra comparison |

## 7. Alternative Concept – at() Function

Instead of an overloaded checked `operator[]`, the program can use `vector::at(i)`, which throws `std::out_of_range` for invalid indices.

An unchecked `operator[]` does not perform range checking and can cause undefined behaviour when the index is invalid.

For 10,000,000 accesses, both approaches are O(1) per access. The checked version performs an additional bounds comparison. Actual timing differences should be measured using the same tests at `-O0` and `-O2`.

## 8. Conclusion

Prefix increment is generally preferred for class types because it avoids creating a temporary copy. Checked subscript access protects the playlist from invalid indices while keeping access efficient.
