# Templates and Iterators

[Write a text-based war-game in C++](https://github.com/cpp-exercises/wargame-a)

## Template Functions

```cpp
#include <iostream>
using namespace std;

template <typename T>
void myPrint(T val){
    cout << val << endl;
}

int main() {
    myPrint<int>(5);
    myPrint("hello world");
    myPrint(10.5f);
    return 0;
}
```

```text
5
hello world
10.5
```

When the _compiler_ encounters this call to a _template function_, it uses the **template** to **automatically generate a function replacing each appearance of T** by the type passed as the actual template parameter \(int in this case\) and then calls it. This process is automatically performed by the compiler and is invisible to the programmer.

So `myPrint(10.5f)` creates `void myPrint(float val){...}`

> **Important:** T is just the name of the placeholder we could have called it U or something else.
>
> When you design or use a template you should be aware of what operations the data types you will use need to support.

Show this using MSVC in [compiler explorer](https://godbolt.org/) not the same on clang

```cpp
template <typename T>
T add(T val ){
    return val+val;
}


int add2(int val ){
    return val+2;
}

int main() {
    // return add(5);
    add<double>(2.5f);
    add<int>(2);
    return 0;
}
```

#### Another simple example

```cpp
#include <iostream>
using namespace std;

template <typename T>
T getMax (T a, T b) {
    return (a>b ? a : b);
}

int main () {
    cout << getMax(2,-1) << endl;
    cout << getMax("b","aaa") << endl;
    return 0;
}
```

```text
2
b
```

T is always the same type `T GetMax (T a, T b)` so if **a** is an _int_, **b** is also an int.

#### 2 different types

```cpp
#include <iostream>
using namespace std;

template <typename T, typename U>
T getMin (T a, U b) {
    return (a<b ? a : b);
}

int main () {
    cout << getMin(2,-10.5f) << endl;
    cout << getMin("b","aaa") << endl;
    return 0;
}
```

```text
-10
aaa
```

Even though -10.5 is smaller it was casted into T or an int since `getMin` returns T the first argument

## Template Classes

```cpp
#include <iostream>
using std::cout, std::endl;

template <typename T>
class Foo {
    T val;
public:
    T getValue(){ return val;}
    void setValue(T v){ val = v;}
};

int main(){
    Foo<int> foo;
    foo.setValue(5);
    cout << foo.getValue() << endl;
    return 0;
}
```

### Templates and header files

When you instantiate such a template class, e.g. `MyTemplate<int>`, the compiler creates the class on the spot. In order to create it, it has to see all the templated member functions \(so that it can use the templates to create actual member functions such as MyTemplate::foo\(\) \), and therefore these templated member functions must be in the header.

If the members are not in the header, the compiler will simply assume that they exist somewhere else and just create actual function declarations from the templated function declarations, **and this gives you linker errors**.

[Credits](https://stackoverflow.com/questions/5180357/why-do-c-template-definitions-need-to-be-in-the-header)

```cpp
//=== main.cpp===
#include <iostream>
#include "Foo.h"
using namespace std;

int main() {
    Foo<int> foo;
    foo.setValue(5);
    cout << foo.getValue() << endl;
    return 0;
}
```

```cpp
//=== Foo.cpp ===
#include "Foo.h"

template<typename T>
T Foo<T>::getValue() {
    return val;
}

template<typename T>
void Foo<T>::setValue(T val) {
    this->val = val;
}
```

```cpp
//=== Foo.h ===
template <typename T>
class Foo {
    T val;
public:
    T getValue();
    void setValue(T val);
};
```

```text
undefined reference to `Foo<int>::setValue(int)'
undefined reference to `Foo<int>::getValue()'
```

Won't compile! We need to put the template in the header file

```cpp
//=== Foo.h ===
template <typename T>
class Foo {
    T val;
public:
    T getValue(){
        return val;
    }

    void setValue(T val){
        this->val = val;
    }
};
```

```text
5
```

### 2 ways to go around this

#### 1st way

We can include the cpp file in our header file like this

```cpp
//=== Foo.h ===
template <typename T>
class Foo {
    T val;
public:
    T getValue();
    void setValue(T val);
};

#include "Foo.cpp"
```

```cpp
//=== Foo.cpp ===
#include "Foo.h"

template<typename T>
T Foo<T>::getValue() {
    return val;
}

template<typename T>
void Foo<T>::setValue(T val) {
    this->val = val;
}
```

notice the `#include "Foo.cpp"` at the end of our `.h` file.

#### 2nd way

or we can explicitly instantiate the template

```cpp
//=== Foo.cpp ===
#include "Foo.h"

template<typename T>
T Foo<T>::getValue() {
    return val;
}

template<typename T>
void Foo<T>::setValue(T val) {
    this->val = val;
}

template typename Foo<int>;
template typename Foo<double>;
```

Here is the important part

```cpp
template typename Foo<int>;
template typename Foo<double>;
```

### Matrix Example

```cpp
//=== Matrix.h ===
//@author: Nissan
#pragma once

#include <ostream>
using namespace std;


template <typename T>
class Matrix{
private:
    const static int MAXROWS=3;//TODO change to meta-programming
    const static int MAXCOLS=3;
    int rows;
    int cols;
public:
    T matArray[MAXROWS][MAXROWS];

    Matrix(int rows=3,int cols=3):rows(rows),cols(cols){}
    void setElement(int row, int col, T value){} //set an element of the matrix
    void fill(T); //set the matArray to what is sent

    template <typename U>
    void operator+=(Matrix<U>& otherMat){
        for (int i=0; i< rows; i++){
            for(int j=0; j< cols; j++){
                matArray[i][j] += otherMat.matArray[i][j];
            }
        }
    }

    friend std::ostream& operator<<(ostream &os, const Matrix &matrix){
    os << "type: " <<typeid(T).name() <<endl; //RTTI :)
    os << "["  << endl;
    for (int i=0; i< matrix.rows; i++){
        for(int j=0; j< matrix.cols; j++){
            os << matrix.matArray[i][j] << "\t";
        }
        os << endl;
    }
    os << "]\n" <<"=========\n";
    }

    //TODO add scalar multiplication

};



template <typename T>
void Matrix<T>::fill(T val){
    for (int i=0; i< rows; i++){
        for(int j=0; j< cols; j++){
            matArray[i][j] = val;
        }
    }
}
```

```cpp
//=== main.cpp ===
#include <iostream>
#include "Matrix.h"

using namespace std;

int main() {
    Matrix<double> floatMatrix;
    floatMatrix.fill(3.5);

    Matrix<int> intMatrix;
    intMatrix.fill(4);

    floatMatrix+=intMatrix;
    cout << floatMatrix <<endl;

    intMatrix+=floatMatrix;
    cout << intMatrix <<endl;
    return 0;
}
```

```text
type: d
[
7.5    7.5    7.5    
7.5    7.5    7.5    
7.5    7.5    7.5    
]
=========

type: i
[
11    11    11    
11    11    11    
11    11    11    
]
=========
```

## Iterators

**Iterator**: An iterator is any object that, pointing to some element in a range of elements \(such as an array or a container\), has the ability to iterate through the elements of that range.

```cpp
#include <iostream>
#include <vector>
using std::vector, std::cout, std::endl;

int main() {
    vector<int> vec = {1,2,3};
    vector<int>::iterator itr;

    for (itr=vec.begin(); itr!=vec.end() ; ++itr) {
        cout << *itr << endl;
    }

    return 0;
}
```

```text
1
2
3
```

We can dereference iterators like pointers

### So aren't iterators basically pointers?

| POINTERS | ITERATORS |
| :--- | :--- |
| A pointer hold an address in memory. | An iterator may hold a pointer, but **it may be something much more complex**. For example, an iterator can iterate over data that’s on file system, spread across many machines, or generated locally in a programmatic fashion. A good **example** is an **iterator over linked list**, the iterator will move through elements that are at nodes in the list whose addresses in RAM may be scattered |
| We can perform simple arithmetic on pointers like increment, decrement, add an integer etc. | Not all iterators allow these operations, e.g., we cannot decrement a forward-iterator, or add an integer to a nonrandom-access iterator. |
| A pointer of type T\* can point to any type T object. | An iterator is more restricted, e.g., a vector::iterator can only refer to doubles that are inside a vector container. |
| We can delete a pointer using delete | Since an iterator refers to objects in a container, unlike pointers, there’s no concept of delete for an iterator. \(The container is responsible for memory management.\) |

[Credits](https://www.geeksforgeeks.org/difference-between-iterators-and-pointers-in-c-c-with-examples/)

### Vector Example

overview of what we want

```cpp
template <typename T>
class Vector{
public:
    //Iterator class
    class Iterator{
    public:
        Iterator(const Vector<T> *vector, int nIndex) : m_pVector(pVector), m_nIndex(nIndex){}
        const T& operator*() const { return m_pVector->operator[](m_nIndex); }
        Iterator& operator++(){ ++m_nIndex; return *this; }
        bool operator!=(const Iterator &other) const { return m_nIndex != other.m_nIndex; } 
    private:
        const Vector<T> *m_pVector;
        int m_nIndex = -1;
    };

//vectors methods
...
template <typename T>
typename Vector<T>::Iterator Vector<T>::begin() const{
    return Vector<T>::Iterator{ this, 0 };
}

template <typename T>
typename Vector<T>::Iterator Vector<T>::end() const{
    return Vector<T>::Iterator{ this, m_nSize };
}

}
```

* `begin()` and `end()` belong to the **Vector** class
* `operator!=` to check that the iterator din't end up at `iter.end`
* `operator++` increment iterator to point to the next value 
* `operator*` access the iterator values

Lets Start!!!

```cpp
//@author https://codereview.stackexchange.com/questions/202157/basic-iterator-supporting-vector-implementation
#pragma once

#include <stdexcept>

template <typename T>
class Vector{
public:
    /* The iterator */
    class Iterator{
    public:
        Iterator(const Vector<T> *vector, int nIndex);
        const T &operator*() const;
        Iterator& operator++();
        bool operator!=(const Iterator &other) const;
    private:
        const Vector<T> *m_pVector;
        int m_nIndex = -1;
    };

public:
    // constructors
    Vector() = default;
    explicit Vector(int nSize);

    ~Vector();

    void insert(const T &value);

    int size() const;
    const T &operator[](int nIndex) const;

    Iterator begin() const;
    Iterator end() const;

private:
    T *m_pData = nullptr;
    int m_nSize = 0;
    int m_nCapacity = 0;
};

/*
 * Vector methods
 **/
template <typename T>
Vector<T>::Vector(int nCapacity)
{
    m_nCapacity = nCapacity;
    m_pData = new T[m_nCapacity];
}

template <typename T>
Vector<T>::~Vector()
{
    delete m_pData;
    m_nSize = 0;
    m_nCapacity = 0;
}

template <typename T>
void Vector<T>::insert(const T &value)
{
    if (m_nSize == m_nCapacity)
    {
        if (m_nCapacity == 0)
            m_nCapacity = 1;

        m_nCapacity *= 2;

        // allocate 2x larger memory
        auto pNewMemory = new T[m_nCapacity];

        // copy data to there
        for (auto idx = 0; idx < m_nSize; ++idx)
            pNewMemory[idx] = m_pData[idx];

        delete m_pData;
        m_pData = pNewMemory;
    }

    // insert the new element
    m_pData[m_nSize] = value;
    ++m_nSize;
}

template <typename T>
int Vector<T>::size() const
{
    return m_nSize;
}

template <typename T>
const T &Vector<T>::operator[](int nIndex) const
{
    if (nIndex >= m_nSize)
        throw std::out_of_range("Index out of range");

    return m_pData[nIndex];
}

template <typename T>
typename Vector<T>::Iterator Vector<T>::begin() const
{
    return Vector<T>::Iterator{ this, 0 };
}

template <typename T>
typename Vector<T>::Iterator Vector<T>::end() const
{
    return Vector<T>::Iterator{ this, m_nSize };
}

/*
 * Iterator methods
 **/
template <typename T>
Vector<T>::Iterator::Iterator(const Vector<T> *pVector, int nIndex)
        : m_pVector(pVector)
        , m_nIndex(nIndex)
{
}

template <typename T>
const T &Vector<T>::Iterator::operator*() const
{
    return m_pVector->operator[](m_nIndex);
}

template <typename T>
typename Vector<T>::Iterator &Vector<T>::Iterator::operator++()
{
    ++m_nIndex;
    return *this;
}

template <typename T>
bool Vector<T>::Iterator::operator!=(const Vector<T>::Iterator &other) const
{
    return m_nIndex != other.m_nIndex;
}
```

```cpp
//@author https://codereview.stackexchange.com/questions/202157/basic-iterator-supporting-vector-implementation
#include <iostream>
#include "Vector.h"

int main(){
    Vector<int> vector;

    try{
        vector.insert(8);
        vector.insert(3);
        vector.insert(1);
        vector.insert(7);
        vector.insert(2);
    }
    catch (const std::exception &e){
        std::cerr << e.what() << std::endl;
    }

    for (Vector<int>::Iterator itr=vector.begin(); itr!=vector.end() ; ++itr) {
        std::cout << *itr << std::endl;
    }

    return 0;
}
```

```text
8
3
1
7
2
```

Lets see how cool this code really is :\)

```cpp
#include <iostream>
#include "Vector.h"

struct Point{
    int x,y;
    Point(int x=0, int y=0) : x(x), y(y) {}

    friend std::ostream &operator<<(std::ostream &os, const Point &point) {
        os << "x: " << point.x << ", y: " << point.y;
        return os;
    }
};

int main(){
    Vector<Point> vector;
    int i=0,j=1;

    for (int i = 0; i < 20 ; ++i,++j)
        vector.insert(Point{i,j});

    Vector<Point>::Iterator itr=vector.begin();

    for (; itr!=vector.end() ; ++itr) {
        std::cout << *itr << std::endl;
    }

    return 0;
}
```

```text
x: 0, y: 1
x: 1, y: 2
x: 2, y: 3
...
x: 19, y: 20
```

Lets add some functionality.

* This will copy from one vector to another `Vector<Point> vec2(vector.begin(),vector.end());`
* or basically `Vector(Vector<T>::Iterator begin, Vector<T>::Iterator end);`

```cpp
public:
explicit Vector(Vector<T>::Iterator begin, Vector<T>::Iterator end,int);
...

template <typename T>
Vector<T>::Vector(Vector<T>::Iterator itr, Vector<T>::Iterator end, int cap) :m_nCapacity(cap+1),m_pData(new T[cap+1]){
    m_nSize = 0;
    for (; itr!=end ; ++itr)
        insert(*itr);
}
```

And now

```cpp
int main(){
    Vector<Point> vector;
    int i=0,j=1;

    for (int i = 0; i < 20 ; ++i,++j)
        vector.insert(Point{i,j});

    Vector<Point>::Iterator itr=vector.begin();

    Vector<Point> vec2(vector.begin(),vector.end(),20);

    Vector<Point>::Iterator itr2=vec2.begin();
    for (; itr2!=vec2.end() ; ++itr2) {
        std::cout << "vec2-> "<< *itr2 << std::endl;
    }

    return 0;
}
```

```text
vec2-> x: 0, y: 1
vec2-> x: 1, y: 2
...
vec2-> x: 19, y: 20
```

### Making our own `count_if`

`count_if` - receives the beggining and the end iterators. Additionaly we give it a **predicate** or Struct with an overloaded parathesis operator.

```cpp
#include <iostream>
#include "Vector.h"
using std::cout, std::endl;


template <typename T>
struct LargerThanX{
    int num;
    LargerThanX(int n):num(n){}

    bool operator()(T iter){
        return iter>num;
    }
};

template <typename T>
int count_if(typename Vector<T>::Iterator itr, typename Vector<T>::Iterator end, LargerThanX<T> predicate){
    int ans=0;
    for (; itr!=end ; ++itr)
        if (predicate(*itr))
            ++ans;

    return ans;
}

int main(){
    Vector<int> vec;
    int i=0;

    for (int i = 0; i < 20 ; ++i)
        vec.insert(i);

    int ans = count_if(vec.begin(), vec.end(), LargerThanX<int>(5));
    cout << ans << endl;

    return 0;
}
```

```text
14
```

Try to implement the following functions: `any_of` -&gt; returns bool if some is true

