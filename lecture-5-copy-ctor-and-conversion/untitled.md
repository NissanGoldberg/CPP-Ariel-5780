# Deep copying and Conversion

## Shallow and Deep Copying

An object is copied when:

1.  Constructing new object from existing
2.  Passing parameter by value
3. Returning by value
4. Assigning existing to existing.

**Cases 1-3** are handled by copy constructor;

**Case 4** is handled by assignment operator.

**By default, both do shallow copy.**

### Rule of 3

When you need to make a **deep copy** of an object, you need to define all of these:

 1. **Copy constructor** 2. **Destructor** 3. **Operator =**

### Example of Deep Copy

```cpp
//@author Erel Segal-Halevi
#include <iostream>
using namespace std;

class IntList {
private:
    int* theInts;
    uint numInts;

public:
    IntList(uint newNumInts):
            numInts(newNumInts),
            theInts(new int[newNumInts]){  }

    // conversion constructor
    IntList(const string& other):
            numInts(2),
            theInts(new int[2]){
        cout << "converting string to IntList" << endl;
    }

    IntList(const IntList& other):
    // IntList(IntList other):
            IntList(other.numInts){
        cout << "copy constructor doing deep copy" << endl;
        for (uint i=0; i<numInts; ++i)
            theInts[i] = other.theInts[i];
    }

    IntList& operator=(const IntList& other) {
        if (this==&other)
            return *this;
        cout << "assignment operator doing deep copy..." << endl;

        if (other.numInts!=this->numInts) {
            delete[] theInts;
            theInts = new int[other.numInts]; // init
            numInts = other.numInts;
        }

        for (uint i=0; i<numInts; ++i)
            theInts[i] = other.theInts[i];
        return *this;
    }

    ~IntList() {
        delete[] theInts;
    }

    int size() const {
        return numInts;
    }

    void operator=(int value) {
        cout << "filling with "<<value<<"..."<<endl;
        for (uint i=0; i<this->numInts; ++i)
            theInts[i] = value;
    }

    int operator[](uint index) const {
        return theInts[index];
    }

    int& operator[](uint index) {
        return theInts[index];
    }

    void operator()(const IntList& other) {
        cout << "hahaha" << endl;
    }
};

/**
 * If you pass a parameter by value -
 *    the compiler will call the copy ctor
 */
int sum(const IntList& list) {
    int result=0;
    for (int i=0; i<list.size(); ++i) {
        result += list[i];
    }
    // list[5] = 88;
    return result;
}
```

```cpp
int main() {
    // int a,b,c;    a = b = c; // Chaining
    IntList list1{10};
    cout << "list1[5] = " << list1[5] << endl;
    list1 = 1;
    cout << "list1[5] = " << list1[5] << endl << endl;
    cout << "sum(list1) = " << sum(list1) << endl;
    cout << "list1[5] = " << list1[5] << endl << endl;
    cout << "---" << endl;

    IntList list2{list1};     // calls copy ctor
    // IntList list2 (list1);  // calls copy ctor
    // IntList list2 = list1;  // calls copy ctor
    cout << "list1[5] = " << list1[5] << endl;
    cout << "list2[5] = " << list2[5] << endl;
    list2 = 2;
    cout << "list1[5] = " << list1[5] << endl;
    cout << "list2[5] = " << list2[5] << endl << endl;

    IntList list3{20};
    list3 = list1;   // operator=
}
```

```text
list1[5] = 0
filling with 1...
list1[5] = 1

sum(list1) = 10
list1[5] = 1

---
copy constructor doing deep copy
list1[5] = 1
list2[5] = 1
filling with 2...
list1[5] = 1
list2[5] = 2

assignment operator doing deep copy...
```

`IntList& operator=()` returning a reference allows us to chain `a=b=c`

## Conversions of types

### Implicit casting

```cpp
Vector(size_t length) // ctor
...
int sum(const Vector& v) // function
...
int i=3;
sum(i);
```

`sum(i)` is same as `sum(Vector(i))`. Did the user really wanted this?

### `explicit` keyword

Prevents undesired conversion

```cpp
explicit Vector(size_t length) // ctor
...
int sum(const Vector& v) // function
...
int i=3;
sum(i); // Won�t compile
sum(Vector{i}); // compiles
```

