# Casting

5 different casts:

* static
* dynamic
* reinterpet
* const
* C-style

```cpp
#include <iostream>

class Fighter{};
class Ninja: public Fighter{};
class Zombie: public Fighter{};

int main(){
    Ninja* ninja = new Ninja;
    Fighter* f1 = ninja;//implicit cast, upcast is fine
    Ninja* n = f1; //can't go back, error

    delete ninja;
    return 0;
}
```

`Ninja* n = f1` is an _**compile time error**_ and can't go back why? Because **f1** could have been a **Zombie** and not a **Ninja**. Look what happens here

```cpp
int main(){
    Ninja* ninja = new Ninja;
    Fighter* f1 = ninja;//implicit cast, upcast is fine
    Fighter* f2 = new Zombie; 

    Ninja* n = f2; // NOT OK!!!!!
    return 0;
}
```

```text
invalid conversion from Fighter* to Ninja*
```

So basically we must **cast**

```cpp
int main(){
    Ninja* ninja = new Ninja;
    Fighter* f1 = ninja;//implicit cast, upcast is fine
    Fighter* f2 = new Zombie;

    Ninja* n = (Ninja*)f2;
    return 0;
}
```

`(Ninja*)f2` is casting. But this is **DANGEROUS!**. If we try to access data members or functions that Ninja has and not Zombie it results in unexpected behaviour.

### Dynamic Cast

```cpp
#include <iostream>

class Fighter{};
class Ninja: public Fighter{};
class Zombie: public Fighter{};

int main(){
    Ninja* ninja = new Ninja;
    Fighter* f1 = ninja;//implicit cast, upcast is fine
    Fighter* f2 = new Zombie;

    Ninja* n = dynamic_cast<Ninja*>(f2);
    return 0;
}
```

```text
cannot dynamic_cast f2 (of type class Fighter*) to type class Ninja* (source type is not polymorphic)
```

**source type is not polymorphic**

**Remember!** Dynamic Casting only polymorphic types

Lets add a virtual function to **Fighter**

```cpp
class Fighter{
public:
    virtual void getName(){}
};
```

and now well get this

```cpp
#include <iostream>
using std::cout;
class Fighter{
public:
    virtual void getName(){}
};
class Ninja: public Fighter{};
class Zombie: public Fighter{};

int main(){
    Ninja* ninja = new Ninja;
    Fighter* f1 = ninja;//implicit cast, upcast is fine
    Fighter* f2 = new Zombie;

    if(Ninja* n = dynamic_cast<Ninja*>(f1))
        cout << "f1 is ninja\n";
    else
        cout << "f1 isn't ninja\n";


    if(Ninja* n = dynamic_cast<Ninja*>(f2))
        cout << "f2 is ninja\n";
    else
        cout << "f2 isn't ninja\n";
    return 0;
}
```

```text
f1 is ninja
f2 isn't ninja
```

It know by looking at **RTTI**.

### Static Cast

Let look at 3 examples

#### 1st Example

```cpp
int main(){
    char c = 10;       // 1 byte
    int *p = (int*)&c; // 4 bytes

    *p = 512; // run-time error: stack corruption
    std::cout << *p;
    return 0;
}
```

```text
0
```

or

```text
Segmentation fault
```

to prevent this we can use `static_cast<>()` which gives you a **compile time** checking ability, C-Style cast doesn't.

```cpp
int main(){
    char c = 10;       // 1 byte
    int *q = static_cast<int*>(&c); // compile-time error
    return 0;
}
```

```text
 error: invalid static_cast from type char* to type int*
```

#### 2nd Example

The following should result in an error but doesn't

```cpp
struct A {};

int main(){
    A* a = new A;
    int i = 10;
    a = (A*) (&i); // NO ERROR! Undesired!
    return 0;
}
```

Now if we change this to a `static_cast`

```cpp
struct A {};

int main(){
    A* a = new A;
    int i = 10;
    a = static_cast<A*>(&i); 
    return 0;
}
```

```text
invalid static_cast from type int* to type A*
```

#### 3rd Example

The following should result in an error but doesn't

```cpp
struct A {};
struct B : A {};

struct C {};

int main(){
    A* b = new B;
    B* b2 = static_cast<B*>(b); // NO ERROR! SMART!
    C* c = (C*)(b); // NO ERROR! undesired!
    return 0;
}
```

Now if we change this to a `static_cast`

```cpp
struct A {};
struct B : A {};

struct C {};

int main(){
    A* b = new B;
    B* b2 = static_cast<B*>(b); // NO ERROR! SMART!
    C* c = static_cast<C*>(b); 
    return 0;
}
```

```text
invalid static_cast from type A* to type C*
```

[Credits](https://stackoverflow.com/questions/1609163/what-is-the-difference-between-static-cast-and-c-style-casting)

## reinterpret\_cast

* It is used to **convert one pointer of another pointer of any type**, no matter either the class is related to each other or not.
* It **does not check** if the pointer type and data pointed by the pointer is same or not.

![great-power.jpg?w=640](https://diaryofarabbi.files.wordpress.com/2014/07/great-power.jpg?w=640)

```cpp
#include <iostream>
using std::cout, std::endl;

struct Stru{
    int x;
    int y;
    char c;
    bool b;
};

int main(){
    Stru s;
    s.x = 10;   s.y = 20;   s.c='a';  s.b=true;

    int* p = reinterpret_cast<int*>(&s);
    cout << *p <<endl;
    ++p;
    cout << *p <<endl;
    ++p;
    char* c = reinterpret_cast<char*>(p);
    cout << *c <<endl << std::boolalpha;
    cout << *(reinterpret_cast<bool*>(++c)) << endl;

    return 0;
}
```

```text
10
20
a
true
```

[Credits](https://www.youtube.com/watch?v=JBvEfB_F66o)

Here each time before we cast we have to know the **exact** type. This is useful is we want to do really low level work.

If we where to mess up lets say like here

```cpp
#include <iostream>
using std::cout, std::endl;

struct Stru{
    int x;
    int y;
    char c;
    bool b;
};

int main(){
    Stru s;
    s.x = 10;   s.y = 20;   s.c='a';  s.b=true;

    int* p = reinterpret_cast<int*>(&s);
    cout << *p <<endl;
    ++p;
    cout << *p <<endl;
    ++p;
    cout << *p <<endl;
    ++p;
    cout << *p <<endl;

    return 0;
}
```

```text
10
20
353
-1274109696
```

Here we didn't get **a** or **1** \(true\)

### C-style

Tries to do the following casts, in this order:

* const\_cast \(1st\)
* static\_cast
* static\_cast followed by const\_cast
* reinterpret\_cast
* reinterpret\_cast followed by const\_cast \(last\)

### RTTI typeid \(Run-time type info\)

* `dynamic_cast`
* `typeid`

`typeid(obj)` or `typeid(obj).name()`

```cpp
bool areSameType(Fighter& f1, Fighter& f2){
    if (typeid(f1)== typeid(f2))
        return true;
    return false;
}
```

Here we added a function that checks if two _**fighters**_ **are the same**

Seems better suited if we don't want to cast and only want to compare.

* Has overhead and is slower since this runs at **runtime**

