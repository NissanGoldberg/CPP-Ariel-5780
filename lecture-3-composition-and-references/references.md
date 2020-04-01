# References

Q: How did we swap between two ints in **C** ? A: Pointers

```cpp
#include <iostream>
using namespace std;

void swap (int *a, int *b) {
    int t = *a;
    *a = *b;
    *b = t;
};

int main() {
    int a=1, b=2;
    swap(a,b);
    cout << "a: "<<a<<", b:"<<b;
}
```

In **C++** instead of using pointers we use _**references**_. To use a reference use the `&` sign before the varible name. If we replace pointer with references we will get:

```cpp
void swap (int &a, int &b) {
    int t = a;
    a = b;
    b = t;
};

int main() {
    int a=1, b=2;
    swap(a,b);
    cout << "a: "<<a<<", b:"<<b;
}
```

### References vs Pointers

Even though similar there are differences. A **pointer** can be declared as **void** but a **reference can never be void**

```cpp
int a = 10;
void* aa = &a;. //it is valid
void &ar = a; // it is not valid
```

3 differences:

* Once a reference is created, it _**cannot be later made to reference another object**_; it cannot be reseated. This is often done with pointers.
* _**References cannot be NULL**_. Pointers are often made NULL to indicate that they are not pointing to any valid thing.
* A reference _**must be initialized when declared**_. There is no such restriction with pointers

#### References are safer and easier to use:

* **Safer**: Since references must be initialized, wild references like wild pointers are unlikely to exist.
* **Easier to use**: References don’t need dereferencing operator to access the value. They can be used like normal variables. ‘&’ operator is needed only at the time of declaration

[Credits](https://www.geeksforgeeks.org/references-in-c/)

## Lvalue & Rvalue

**Lvalue** = Left Value – can appear at left side of =. = Located Value – has a fixed memory location. Examples: variables, references …

**Rvalue** = not Left Value. Numbers, temporaries

## Passing arguments by const reference

Passing by regular reference. The function may change the value but the object isn't copied.

```cpp
void foo (int &a){ ... }
```

Passing by **const** reference. The function _can't_ change the value and the object isn't copied.

```cpp
void foo (const int &a){ ... }
```

## Return a reference to variable

```cpp
#include <iostream>
using namespace std;

class Buffer{
    size_t _length;
    int *_buf;
public:
    Buffer (size_t l) :_length (l),_buf (new int [l]){}

    int& get(size_t i){
        return _buf[i];
    }
};

int main (){
    Buffer buf(5);
    buf.get(0)= 3;
    int& x = buf.get(0);
    cout << "x: " << x << endl;
    x++;
    cout << "x: " << x << endl;
}
```

```text
x: 3
x: 4
```

Notice the following:

```cpp
buf.get(0)= 3;
```

this is possible because `int& get(size_t i)` returns a reference. Also the following possible for the same reason.

```cpp
int& x = buf.get(0);
x++;
```

This is how in an oridnary array we are able to both get and set a value

```cpp
cars[0] = "Opel";
cout << cars[0];
```

## TODO page 14 in presentation

## Call chaining

```cpp
#include <iostream>
using namespace std;

struct Point{
    int _x,_y;

    Point(int x,int y):_x(x),_y(y){ }

    int& getX(){
        return _x;
    }
};

Point& add(Point& a, const Point& b) {
    a._x+=b._x;
    a._y+=b._y;
    return a;
}

int main() {
    Point p1(2,3), p2(4,5), p3(1,1);
    add(add(p1,p2),p3); // now p1 is (7,9)
    cout << add(p1,p2).getX(); // note the syntax
}
```

```text
11
```

Notice the syntax of the following line:

```cpp
add(p1,p2).getX();
```

Since the function _add_ returns _Point&_ we were able to call a function on the result.

If we were to change the _add_ function to this

```cpp
#include <iostream>
using namespace std;

class Point{
    int _x,_y;
public:
    Point(int x,int y):_x(x),_y(y){ }

    int& getX(){ return _x;}

    Point& add (const Point& b) {
        _x+=b._x; _y+=b._y;
        return *this;
    }

};

int main() {
    Point p1(2,3), p2(4,5), p3(1,1);
    p1.add(p2).add(p3); // now p1 is (7,9)
    cout << p1.add(p2).getX();
}
```

We are able to chain the result each time to achieve the same result.

```cpp
p1.add(p2).add(p3);
```

If we wanted to we could endlessly chain _add_

```cpp
p1.add(p2).add(p3).add(p1).add(p2).add(p3);
cout << p1.getX(); //result is 19
```

