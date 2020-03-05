# Overloading and Linking

## Function Overloading

Overloading a function is having the same name for multiple functions but with different arguments.

**C doesn't support** function **overloading** so the following code won't compile.

```c
void foo() { printf ("foo()\n"); }

void foo(int n) { printf ("foo(%d)\n", n); }
```

C++ does support function overloading so the above does compile. Using the _gcc_ compiler we can see what happens now.

```cpp
void foo() { std::cout << __PRETTY_FUNCTION__ << std::endl; }
void foo(int n) { std::cout << __PRETTY_FUNCTION__ << std::endl; }

int main() {
    foo(12);
    foo();
}
```

### **`output`**

```text
void foo(int)
void foo()
```

## Default parameters

Default params can be use in next to the arg type. If the value was not given the default will be what was specified

```cpp
#include <iostream>
void foo(int n=5) { std::cout << n << std::endl; }

int main(){
    foo();
    foo(12);
}
```

### **`output`**

```text
5
12
```

But how does the compiler know which function to call?

```cpp
#include <iostream>
using namespace std;

void power(int a, unsigned int b) { cout << "power(int a, unsigned int b)" << endl;}

void power(int a, int b) { cout << "power(int a, int b)" << endl; }

int main() {
     power(2,3);
     power(2, -3);
}
```

### **`output`**

```text
power(int a, int b)
power(int a, int b)
```

Notice that here we were able to confuse the compiler. `power(2, -3)` should have called the _uint_ function

### Overload resolution

_Overload resolution_ is the way the compilers knows which function to call \(but its not perfect\).

* Step 1: Find all functions with _**same name**_
* Step 2: functions with the correct number of args
* Step 3: choose function with best matching arguments

## Linking

### TODO

## doctest.h

A simple tester written by _**Viktor Kirilov**_.  
Here is a simple example

```cpp
#include "doctest.h"
#include "Factorial.hpp"

TEST_CASE("testing the factorial function") {
    CHECK(factorial(2) == 2);
    CHECK(factorial(3) == 6);
    CHECK(factorial(4) == 999);
}
```

### TODO

