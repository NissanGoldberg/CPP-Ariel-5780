# OOP

## Classes and Structs

In C++ unlike C structs are the same as a class the only difference is that **classes** default are _**private**_ and **structs** default are _**public**_.

```cpp
class A { 
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

#### Inline implementation

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

#### **`output`**

```text
a = 1.000000+2.000000i
```

#### Outline implementation

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

#### **`output`**

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

#### Static data member

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

#### **`output`**

```text
5
```

#### Static member functions

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

#### TODO exam questions

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

#### No constructor

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

#### **`output`**

```text
x=75 // what was sitting in memory
y=0  // what was sitting in memory
```

_**Note:**_ If the no-arg ctor would exist `Point p1` would call it.

#### No arg-constructor

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

#### Functor

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

#### **`output`**

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

Use: Called for:

* A **stack** object – when it goes out of scope.
* A **heap** object – when it is explicitly deleted.

To create a destructor use the tilda symbol before class name `~MyClass`

```cpp
#include <cstdlib> 
class MyClass { 
  char* _mem; 
public: 
  MyClass();  // constructor 
  ~MyClass(); // destructor 
}; 

MyClass::MyClass() { _mem = new char[1000];} 

MyClass::~MyClass() { delete[] _mem;}

int main(){ 
  MyClass a;
  return 0;
}
```

#### Common Errors:

1. Forgetting to write a destructor. Might cause a memory leak.
2. Calling a destructor twice. Possible reason - shallow copy.

Lets look at a memory leak using _**valgrind**_

```cpp
int main() {
    int* p = new int[1000];
    p[5] = 4;
    //delete[] p;
}
```

```text
 $ valgrind --leak-check=yes ./a.out
==15865== Command: ./a.out
==15865==
==15865== HEAP SUMMARY:
==15865==     in use at exit: 4,000 bytes in 1 blocks
==15865==   total heap usage: 2 allocs, 1 frees, 76,704 bytes allocated
==15865==
==15865== 4,000 bytes in 1 blocks are definitely lost in loss record 1 of 1
```

## Memory Allocation and Deallocation

In **C** we used `malloc` to allocate memory and `free` to deallocate memory. The problem with using _malloc_ and _free_ in C++ is when we start to use _**objects**_.

`malloc` doesn't call the constructor of the object and doesn't initialize data members either. The same goes for `free` which doesn't call the destuctor and internal data member are not freed.

In C++ we use the `new` and `delete` operators. `new` will both: 1. Allocate memory 2. Calls constructor

```cpp
Students* a = new Students[10];
```

The `delete` operator will both: 1. Call destructor 2. Free memory

```cpp
class MyClass{
public:
    MyClass(){}
};

int main(){
    MyClass* a = new MyClass;
    delete a;
}
```

### Memory Deallocation of Arrays

```cpp
int main(){
    int *a = new int[10];
    delete [] a;
}
```

> _**Note:**_ using `delete a` without `[]` may cause a segmentation fault or memory leak

#### Memory De/Allocation of Objects

```cpp
#include <iostream>
using namespace std;

class IntArray {
    int* ints;
public:
    IntArray(int num=10) {
        ints = new int[num];
    }
    IntArray(int num,int fill) {
        ints = new int[num];
        for (int i = 0; i < num ; ++i)
            ints[i] = fill;
    }
    ~IntArray() {
        delete[] ints;
    }
    void put(int index, int value) {
        ints[index] = value;
    }
    int get(int index) {
        return ints[index];
    }
};

int main() {
    IntArray a(1000,7);
    std::cout << a.get(0);
    return 0;
}
```

Here the destructor

```cpp
~IntArray() {
        delete[] ints;
    }
```

will deallocate for us when the _**main**_ functions reaches the end of its scope

If we were to replace the _**main**_ functions will the following:

```cpp
int main() {
    IntArray* b = new IntArray(100,7);
    std::cout << b->get(0);
    delete b;
    return 0;
}
```

Or in other word we place the pointer _**a**_ on the heap then we must deallocate it as well e.g. `delete a;`

> **Remember:** anything allocated must be also deallocated

We could also do the following:

```cpp
int main() {
    IntArray c[3] =  {{100,7},{50,6},{25,5}}; // the fill ctor 3 times
    std::cout << c[1].get(0) << std::endl;

    IntArray * d =  new IntArray[3]; //call default ctor 3 times
    d[1].put(0,20);
    std::cout << d[1].get(0) << std::endl;
    delete [] d;
    return 0;
}
```

```text
6
20
```

### Memory De/Allocation an Array of Objects without Default Ctor

To top it all off, lets create a array of objects on the heap and not call the default ctor as above

```cpp
int main() {
    int n = 4;
    IntArray **arr = new IntArray *[n];

    for (int i=0; i<n; ++i)
       arr[i] = new IntArray{10*(i+1),3*(i+1)};

    std::cout<< "get arr[1] index 0: " <<arr[1]->get(0) << std::endl;
    //Deallocate
    for (size_t i=0; i<n; ++i)
        delete (arr[i]);
    delete [] arr;

    return 0;
}
```

```text
get arr[1] index 0: 6
```

```cpp
MyClass **arr = new MyClass *[n];
```

Here we create an array of n pointers to _MyClass_, no constuctors where called.

```cpp
for (size_t i=0; i<n; ++i)
  arr[i] = new MyClass (i);
```

Next we will call our _fill ctor_ \(with 2 params\) and initilize each one with a different size and num to fill in. 0. size of 10 and fill in 3's 1. size of 20 and fill in 6's 2. size of 30 and fill in 9's

Now comes deallocation, each IntArray is freed individually

```cpp
 for (size_t i=0; i<n; ++i)
        delete (arr[i]);
```

And finally, we free the pointer to n IntArrays.

