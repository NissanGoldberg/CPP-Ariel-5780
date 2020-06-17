# Smart Pointers

* `auto_ptr`
* `unique_ptr`
* `shared_ptr`

### `std::auto_ptr<T>` \(until C++17\)

* modeled class to manage dynamically allocated memory.
* provides all the interfaces provided by normal pointers with a few

  exceptions.

* during construction, it owns the memory and releases the same when it

  goes out of scope. 

#### Simple example

```cpp
#include <iostream>
#include <memory>
using std::auto_ptr, std::cout, std::endl;

class SomeClass{
public:
    SomeClass(int a = 0) : m_a(a) { }
    ~SomeClass() { cout << "Calling destructor" << m_a <<endl; }
public:
    int m_a;
};

int main(){
    auto_ptr<SomeClass> ptr(new SomeClass(5));
    cout << ptr->m_a << endl;
    return 0;
}
```

```text
5
Calling destructor5
```

We didn't have to call `delete` to deallocate memory and call the destructor.

#### Solving memory leaks and exceptions

Now take a look at the following code:

```cpp
#include <iostream>
#include <memory>
using std::auto_ptr, std::cout, std::endl;

struct SomeClass{
    int m_a;
    SomeClass(int a = 0) : m_a(a) { }
    ~SomeClass() { cout << "Calling destructor" << m_a <<endl; }
};

void foo(){
    int a = 0, b = 5, c;
    if (a == 0 )
        throw std::overflow_error("Divide by zero exception");
    c = b/a;
}

int main(){
    try {
        auto_ptr<SomeClass> ptr(new SomeClass(5));
        foo();
        cout << ptr->m_a << endl;
    }catch (...){
        cout << "Oops! something has gone wrong";
    }

    return 0;
}
```

```text
Calling destructor5
Oops! something has gone wrong
```

This is the desired result.

Here coded the same but with a **regular pointer**

```cpp
#include <iostream>
#include <memory>
using std::auto_ptr, std::cout, std::endl;

struct SomeClass{
    int m_a;
    SomeClass(int a = 0) : m_a(a) { }
    ~SomeClass() { cout << "Calling destructor" << m_a <<endl; }
};

void foo(){
    int a = 0, b = 5, c;
    if (a == 0 )
        throw std::overflow_error("Divide by zero exception");
    c = b/a;
}

int main(){
    try {
        SomeClass* ptr =new SomeClass(5);
        foo();
        cout << ptr->m_a << endl;

        delete ptr;
    }catch (...){
        cout << "Oops! something has gone wrong";
    }

    return 0;
}
```

```text
Oops! something has gone wrong
```

We see here that we **didn't call** `delete ptr` which causes a memory leak.

Now obviously we could have done some like this which would have solved our problems but still this may not be possible in every case or maybe more cumbersome

```cpp
int main(){
    SomeClass* ptr =new SomeClass(5);

    try {
        foo();
        cout << ptr->m_a << endl;
    }catch (...){
        cout << "Oops! something has gone wrong";
    }

    delete ptr;
    return 0;
}
```

### `std::unique_ptr<T>`

* `unique_ptr` follows the **exclusive ownership semantics**, i.e., at any point of time, the resource is owned by **only one** unique\_ptr.
* When unique\_ptr goes out of scope, the resource is released.
* If the resource is **overwritten** by some other resource, the **previously owned resource is released**. So it guarantees that the associated resource is released always. 

#### Example

```cpp
#include <iostream>
#include <memory>
using std::unique_ptr, std::cout, std::endl;

struct SomeClass{
    static int cntr;
    int m_a;
    SomeClass() { m_a=cntr++; }
    SomeClass(int a) : m_a(a) { cntr++; }
    ~SomeClass() { cout << "Calling destructor" << m_a <<endl; }
};

int SomeClass::cntr = 0;

int main(){

        unique_ptr<SomeClass> uniqPtr(new SomeClass());
        //std::unique_ptr<SomeClass> uniqPtr1 (uniqPtr); // NOT POSSIBLE
        unique_ptr<SomeClass> uniqPtr1 (std::move(uniqPtr));
        if (!uniqPtr)
            cout << "Uniqptr is empty now" << endl;

    return 0;
}
```

```text
Uniqptr is empty now
Calling destructor0
```

`std::move(uniqPtr)` allows us to transfer ownership.

as stated before the following is not allowed

```cpp
unique_ptr<SomeClass> uniqPtr(new SomeClass(100));
unique_ptr<SomeClass> uniqPtr1 (uniqPtr); // NOT POSSIBLE
//or this
unique_ptr<SomeClass> uniqPtr2 = uniqPtr; // NOT POSSIBLE
```

This is becaues `unique_ptr` copy ctor and copy assignment look like this

```cpp
 // Disable copy from lvalue.
unique_ptr(const unique_ptr&) = delete;
unique_ptr& operator=(const unique_ptr&) = delete;
```

**A sub-category is make\_unique**

```cpp
...
int main(){
    unique_ptr<SomeClass> uniqPtr = std::make_unique<SomeClass>(7);
    cout << uniqPtr->m_a <<endl;

    return 0;
}
```

```text
7
Calling destructor7
```

Here we didn't use new as before like in `uniqPtr(new SomeClass(100))` just the class name.

[Differences between std::make\_unique and std::unique\_ptr](https://stackoverflow.com/questions/22571202/differences-between-stdmake-unique-and-stdunique-ptr-with-new)

### `std::shared_ptr<T>`

* **Multiple** shared pointers can refer to a single object
* _**When**_ the **last shared pointer goes out of scope**, _memory is released automatically_. This is known by _**reference counting**_.

**Creation**

```cpp
std::shared_ptr<MyClass> sptr = std::make_shared<MyClass>(MyClass(100));
```

if created with the syntax above the `shared_ptr`releases the associated resource by calling `delete` by **default**

**Custom deletion**

If the user needs a different destruction policy, he/she is free to specify the same while constructing the shared\_ptr.

```cpp
shared_ptr<MyClass> sptr( new MyClass[5],
                          [ ](MyClass * p) { delete[] p; } );
```

Here we game a lambda expression to call `delete[]` instead

**Member functions**

* `get()` : To get the resource associated with the shared\_ptr.
* `reset()`: To yield the ownership of the associated memory block. If this is the last shared\_ptr owning the resource, then the resource is released automatically.
* `unique()`:  \(until C++20\) To know whether the resource is managed by only this shared\_ptr instance.
* `use_count()`: get number of shared resources
* operator bool: To check whether the shared\_ptr owns a memory block or not. Can be used with an if condition. `if (ptr) {`

#### Example 1

```cpp
#include <iostream>
#include <memory>
using std::shared_ptr, std::make_shared , std::cout, std::endl;

struct SomeClass{
    static int cntr;
    int m_a;
    SomeClass() { m_a=cntr++; }
    SomeClass(int a) : m_a(a) { cntr++; }
    ~SomeClass() { cout << "Calling destructor" << m_a <<endl; }
};

int SomeClass::cntr = 0;

int main(){

    shared_ptr<SomeClass> sPtr1;
    {
        shared_ptr<SomeClass> sPtr2 = std::make_shared<SomeClass>(7);
        sPtr1 = sPtr2;
        cout << "ref count: " << sPtr1.use_count() <<endl;
    }

    cout << "ref count: " << sPtr1.use_count() <<endl;

    return 0;
}
```

```text
ref count: 2
ref count: 1
Calling destructor7
```

Here we see that the **destructor** is only called when the _number of references_ goes down to 0. Also we notice that on line 22 which is `}` the ref count goes down 1.

#### Example 2

```cpp
#include <iostream>
#include <memory>
using std::unique_ptr, std::shared_ptr, std::cout, std::endl;

struct SomeClass{
    static int cntr;
    int m_a;
    SomeClass() { m_a=++cntr; }
    SomeClass(int a) : m_a(a) { ++cntr; }
    ~SomeClass() { cout << "Calling destructor" << m_a <<endl; }
};

int SomeClass::cntr = 1;

int main(){
    std::shared_ptr<SomeClass> sptr1(new SomeClass(1));
    //OtherFunc(p);
    cout << sptr1->m_a << endl;

    shared_ptr<SomeClass> sptr2 = std::make_shared<SomeClass>( 2);
    cout << sptr2->m_a << endl;

    sptr1.reset(new SomeClass(3));
    cout << sptr1->m_a << endl;
    return 0;
}
```

```text
1
2
Calling destructor1
3
Calling destructor2
Calling destructor3
```

Notice how `Calling destructor1` was called before. This is because of `reset()`

> ## **Note:** if we were to do the following `std::make_shared<SomeClass>( SomeClass(2))` we would see the dtor twice. See [here](https://stackoverflow.com/questions/41559647/class-destructor-called-twice-when-creating-a-shared-ptr)  for more info

The difference is that `std::make_shared` performs **one** heap-allocation, whereas calling the `std::shared_ptr` constructor performs **two**.

`std::shared_ptr` manages two entities:

* the control block \(stores meta data such as ref-counts, type-erased deleter, etc\)
* the object being managed

`std::make_shared` performs a single heap-allocation accounting for the space necessary for both the control block and the data. Here also we **don't** use the `new` keyword just as make\_unique

[Difference in make\_shared and normal shared\_ptr in C++](https://stackoverflow.com/questions/20895648/difference-in-make-shared-and-normal-shared-ptr-in-c)

#### Function example

```cpp
void foo(std::shared_ptr<SomeClass> p1){
    cout << p1->m_a << endl;
    cout << "ref count: " << p1.use_count() <<endl;
}

int main(){
    std::shared_ptr<SomeClass> sptr1(new SomeClass(1));
    foo(sptr1);
    cout << "ref count: " << sptr1.use_count() <<endl;

    return 0;
}
```

```text
1
ref count: 2
ref count: 1
Calling destructor1
```

**More examples**

```cpp
int main( ) {
    int* p = new int;
    shared_ptr<int> sptr1(p);
    shared_ptr<int> sptr2(p);

    return 0;
}
```

When **sptr2** goes out of scope, _**p**_ is **already destroyed** . Valgrind will warn us.

The following is okay

```cpp
int main( ) {
    shared_ptr<int> sptr1(new int);
    shared_ptr<int> sptr2(new int);

    return 0;
}
```

#### `std::weak_ptr`

Doesn't increment the reference count to a shared pointer

#### Cool polymorphic example

```cpp
//based on Nicolai Josuttis - https://www.youtube.com/watch?v=XH4xIyS9B2I
#include <iostream>
#include <vector>
#include <memory>
using std::shared_ptr, std::make_shared, std::vector, std::cout, std::endl;

struct Coord {
    int x,y;
};

struct Shape {
    virtual void move(Coord) = 0;
    virtual void draw() const = 0;
    virtual ~Shape() = default;
};

struct Circle : public Shape {
protected:
    Coord center;
    int rad;
public:
    Circle(const Coord &center, int rad) : center(center), rad(rad) {}
    virtual ~Circle() { cout << "Deleting Circle\n"; }
    void move (Coord c) override {}
    void draw() const override {
        cout << "drawing circle from: "
             << center.x << ", " << center.y
             << " with radius: " << rad <<endl;
    }
};

struct Line : Shape{
protected:
    Coord from;
    Coord to;
public:
    Line(const Coord &from, const Coord &to) : from(from), to(to) {}
    ~Line() { cout << "Deleting Line\n"; }
    void move(Coord coord) override { }
    void draw() const override {
        cout << "drawing line from: "
             << from.x << ", " << from.y
             << " to: " << to.x << ", " << to.y <<endl;
    }
};

vector<shared_ptr<Shape>> createFig(){
    vector<shared_ptr<Shape>> f;
    auto lp = make_shared<Line>(Coord{1, 2}, Coord{3, 4});
    auto cp = make_shared<Circle>(Coord{5, 5}, 7);

    f.push_back(lp);
    f.push_back(cp);
    return f;
}

int main(){
    vector<shared_ptr<Shape>> figs = createFig();

    for (auto& f : figs)
        cout << "ref count: " << f.use_count() << endl; //1 is GOOD

    for (const auto& f : figs)
        f->draw();//polymorphic

    return 0;
}
```

```text
ref count: 1
drawing line from: 1, 2 to: 3, 4
drawing circle from: 5, 5 with radius: 7
Deleting Line
Deleting Circle
```

How easy was this we didn't have to deallocate the memory ourselves.

