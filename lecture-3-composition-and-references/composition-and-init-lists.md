# Composition and Init lists

## Composition

_**Definition:**_ Composition describes a class that references one or more objects of other classes in instance variables.

```cpp
class Line {
  Point p1, p2;
};
```

| C++ | Java |
| :--- | :--- |
| Members are objects | Members are pointers |

## Construction

**Question:** In what order are the constructors called?

```cpp
#include <iostream>
using namespace std;

class A {
public:
    A() { std::cout << "A: parameterless ctor" << endl; }
};

class B {
    A _a1,_a2;
public:
    B() { std::cout << "B: parameterless ctor" << endl;}
};

int main() {
    B b;
}
```

**Answer:**

```text
A - parameterless ctor
A - parameterless ctor
B – parameterless ctor
```

We can clearly see that the member variables are initialized _**before**_ the constructor of the object is called.

**Question:** In what order are the destructors called?

```cpp
#include <iostream>
using namespace std;

class A {
public:
    A() { std::cout << "A: parameterless ctor\n"; }
    ~A() { std::cout << "A: dtor\n";
    }
};

class B {
    A _a1,_a2;
public:
    B() { std::cout << "B: parameterless ctor";}
    ~B() { std::cout << "B: dtor\n";}
};

int main() {
    B b;
}
```

**Answer:**

```text
...
B: dtor
A: dtor
A: dtor
```

Here we can clearly see that first the objects dtor is called only then are the the member objects dtors called

## Default ctor

Now lets replace this line

```cpp
 A() { std::cout << "A: parameterless ctor\n"; }
```

with this line:

```cpp
A(int a) { std::cout << "A:  ctor with one param\n"; }
```

The following code won't complile because _**\_a1**_ and _**\_a2**_ won't be able to find their parameterless ctors'. Now this is quite a problem it _seems_ that we can't have member objects in our class if they they don't have a default ctor. The solution to this problem is an _initialization list_.

## Initialization list

As said before the member objects are initialized _**before**_ the constructor of the object is called. But with init lists we can pass values to the objects right before entering the ctor. The syntax is:

```cpp
B(int i): var_to_init(value_to_give_ctor){
```

Lets look at an example

```cpp
#include <iostream>
using namespace std;

class A {
public:
    A(int a) { cout << "A (" << a << ") " << endl;  }
};

class B {
    A _a1,_a2;
public:
    B(int i):_a1 (i), _a2(2*i){
        cout << "B cons" << std::endl;
    }
};

int main() {
    B b{2};
}
```

```text
A (2) 
A (4) 
B cons
```

Here we can see that even thought _\_a1_ and _\_a2_ will be initialized before entering our ctor, now we have control with what to initialize them.

This solves another problem because _constant variables_ must be assigned a value at the _**time of the declaration**_ without init list we wouldn't have been able to have a const variable as a member variable in our class, but now we can.

### Advantages of using initialization list

* Faster – no need for default initializaiton
* Safer – be sure that all components are ready.
* Can initialize constants and reference variables.
* Can initialize parent class.

