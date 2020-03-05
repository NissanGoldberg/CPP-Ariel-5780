# OOP

## Classes and Structs

In C++ unlike C structs are the same as a class the only difference is that **classes** default are _**private**_ and **structs** default are _**public**_.

```cpp
class A
{ 
   int x;
};
```

Another example would be the complex numbers

```cpp
class Complex { 
  double re, im;

public:
//ctor
 Complex (double re, double im) {…}

 Complex sum (Complex b) {…}
};

int main () { 
    Complex a, b, c; 
};
```

### Inline implementation

Every function and ctor is defined in the class or struct

```cpp
#include <iostream>
using namespace std;

class Complex {
    double re, im;

public:
    Complex() {  re = im  = 999; }    // inline constructor
    Complex(double re, double im) {
        this->re = re;
        this->im = im;
    }

    string to_string() {           // inline method
        return std::to_string(re)+"+"+std::to_string(im)+"i";
    }
};

int main() {
    Complex a(1,2);
    cout << "a = " << a.to_string() << endl;
}
```

### **`output`**

```text
a = 1.000000+2.000000i
```

### Outline implementation

The functions and ctors are defined outside the class

```cpp
#include <iostream>
using namespace std;

class Complex {
    double re, im;
public:
    Complex();
    Complex(double re, double im);
    string to_string();
};

Complex::Complex() {  re = im  = 999; }

Complex::Complex(double re, double im) {
    this->re = re;
    this->im = im;
}

string Complex::to_string() {
    return std::to_string(re)+"+"+std::to_string(im)+"i";
}

int main() {
    Complex a(3,4);
    cout << "a = " << a.to_string() << endl;
}
```

### **`output`**

```text
a = 3.000000+4.000000i
```

In the outline implementation first we must delcare the prototype of each function in the class. Observe line 8

```cpp
Complex(double re, double im);
```

Then **outside** the class or struct we define the function

```cpp
Complex::Complex(double re, double im) {
    this->re = re;
    this->im = im;
}
```

```cpp
string Complex::to_string() {
    return std::to_string(re)+"+"+std::to_string(im)+"i";
}
```

Notice the the order, `return_type` `class_name::func_name`

**Note:** Inline and outline implementations _**can be mixed**_ such that one function is defined one way while another in the second way.

## Multiple Files using Outline Implementation

**TABS** Complex.hpp

```cpp
#include <iostream>
using namespace std;

class Complex {
    double re, im;

public:
    Complex() {  re = im  = 999; }    // inline constructor
    Complex(double re, double im);  // "outline" constructor

    string to_string() {           // inline method  
        return std::to_string(re)+"+"+std::to_string(im)+"i";
    }
    Complex sum(Complex b);        // "outline" method
};
```

Complex.cpp

```cpp
#include "Complex.hpp"

// "outline" constructor implementation:
Complex::Complex(double re, double im) {  
    (*this).re = re;
    this->im = im; 
}   

// "outline" method implementation:
Complex Complex::sum(Complex b) {  
    return Complex(re+b.re, im+b.im);
}
```

main.cpp

```cpp
#include "Complex.hpp"

int main() {
    Complex a(1,2);
    cout << "a = " << a.to_string() << endl;
}
```

## Static

Static members of a class are not associated with the objects of the class. Use the keyword `static` to define it static.

* Static data member
* Static member function

### Static data member

Static data members are not associated with any object. _**They exist even if no objects of the class have been defined**_. There is only one instance of the static data member in the entire program.

Static data members must be _**delared in the class**_ but _**defined \(given a value\) outside the class**_

```cpp
class X { static int n; }; // declaration (uses 'static')
int X::n = 1;              // definition (does not use 'static')
```

It is illegal to define in the class itself such as

```cpp
class X { static int n=1; }; // illegal to define in class
```

The only time we are permitted to define in the class itself is if the variable is **const**. So the following is permitted

```cpp
class X { const static int n=1; }; //const defintion
```

As said befoe they exist even if no objects have been defined. So the following is correct

```cpp
#include <iostream>
using namespace std;

struct X { static int n; }; 
int X::n = 5;              

int main() {
    cout << X::n;
    return 0;
}
```

### **`output`**

```text
5
```

### Static member functions

Static member functions are not associated with any object. When called, they have no `this` pointer.

Static member functions cannot be `virtual` or `const`

```cpp
#include <iostream>
using namespace std;

class List
{
public:
    static int getMaxSize();
    int getSize();
    static int max_size; //=1000; //error! (declare outside)
    int size=0;
};

int List::max_size=1000; //ok, in one cpp file

int List::getMaxSize() {
    // return this->size; // compile error! no this!
    return max_size; // ok
}
int List::getSize(){
    return this->size; //ok
}

int main(){
    List l;
    l.getSize();
    List::getMaxSize();
    l.getMaxSize(); //compiles ok, but bad style
}
```

If _**line 16**_ wouldn't be commented out, this would compile. We have stated before that a static function doesn't have `this` pointer

```cpp
return this->size; // compile error! no this!
```

## Arrangement of Classes in Memory

Here is the following in C++

```cpp
class Complex { 
  double re, im;

public:
//ctor
 Complex (double re, double im) {…}

 Complex sum (Complex b) {…}
};

int main () { 
    Complex a, b; 
};
```

| stack | heap |
| :--- | :--- |
| a.re |  |
| a.im |  |
| b.re |  |
| b.im |  |

### TODO exam questions

## Constructors

```cpp
class MyClass
{
public:
    MyClass();
    MyClass( int i );
    MyClass( double x, double y );
    ...
};

int main() {
    MyClass a; // Calls 1
    MyClass b {5}; // Calls 2
    MyClass c {1.0, 0.0}; // Calls 3 
}
```

* `MyClass a` calls `MyClass()` the no-arg ctor.
* `MyClass b {5}` calls `MyClass(int i)` ctor.
* `MyClass c {1.0, 0.0}` calls `MyClass( double x, double y )`

### No constructor

If no ctor is defined the member fields will be filled with random data.

```cpp
#include <iostream>
using namespace std;

class Point {
private:    // Not required. All is private by default.
    int x;
    int y;
public:
    int getX() { return x; }

    int getY() { return y; }

    string to_string(){return "["+std::to_string(x)+","+std::to_string(y)+"]";}
};

int main() {
    Point p1;
    cout << "x=" << p1.getX() <<endl;
    cout << "y=" << p1.getY() <<endl;
}
```

### **`output`**

```text
x=75 // what was sitting in memory
y=0  // what was sitting in memory
```

_**Note:**_ If the no-arg ctor would exist `Point p1` would call it.

### No arg-constructor

If no arg-ctor is defined in the following code _**the code won't compile**_.

```cpp
#include <iostream>
using namespace std;

class Point {
    int x;
    int y;

public:
    Point(int x, int y) {
        cout << "Point(int,int)" << endl;
        this->x = x;
        this->y = y;
    }
    // methods defined inline:
    int getX() { return x; }

    int getY() { return y; }

    string to_string(){return "["+std::to_string(x)+","+std::to_string(y)+"]";}
};

int main() {
    Point p1;
    cout << "x=" << p1.getX() <<endl;
    cout << "y=" << p1.getY() <<endl;
}
```

`Point p1` will look and won't find a no-arg ctor such as `Point()`. The only available ctor is `Point(int, int)` which is unsuitable.

### Functor

A functor \(or function object\) is a C++ class that acts like a function. This will be explained in greater detail later on.

```cpp
#include <iostream>
using namespace std;

class Point {
private:    // Not required. All is private by default.
    int x;
    int y;

public:
    Point() {
        cout << "Point()" << endl;
        x = 88; y = 12;
    }
    Point(int x, int y) {
        cout << "Point(int,int)" << endl;
        this->x = x;
        this->y = y;
    }
    // methods defined inline:
    int getX() { return x; }

    int getY() { return y; }

    // methods defined in the CPP file:
    void setX(int newX){x = newX;}

    void setY(int newY) {y = newY;}

    string to_string(){return "["+std::to_string(x)+","+std::to_string(y)+"]";}
};

int main() {
    Point p1();
    cout << "x=" << p1.getX() <<endl;
//    cout << "y=" << p1.getY() <<endl;
}
```

The code above will not compile because `Point p1()` will look for a functor. Instead we can replace `Point p1()` with `Point p1{}` and the code will call the no-arg ctor and print out `x=88`

If we were to change the main function to this it would compile.

```cpp
int main() {
    Point p1{};
    cout << "p1=" << p1.to_string() <<endl;

    Point p2 {11,21};
    Point p3 (22,33);
    cout << "p2 = " << p2.to_string() << endl;
    cout << "p3 = " << p3.to_string() << endl;
}
```

### **`output`**

```text
Point()
p1=[88,12]
Point(int,int)
Point(int,int)
p2 = [11,21]
p3 = [22,33]
```

## Array of Objects

```cpp
class MyClass{
public:
    MyClass();
    MyClass( int i );
    MyClass( double x, double y );
    ...
};

int main() {
    MyClass a[5]; // Calls 1 five times
    MyClass b[5] {11, 22}; // Calls 2 two times
    MyClass c[5] { {11,22}, 33}; // Calls 3 then 2
}
```

* `MyClass a` calls `MyClass()` 5 times
* `MyClass b b[5] {11, 22}` calls `MyClass(int i)` 2 times and `MyClass()` 3 times
* `MyClass c[5] {{11,22}, 33}` calls `MyClass(double x, double y);` then calls `MyClass(int i)` and finally call `MyClass()` twice

## Destructors

