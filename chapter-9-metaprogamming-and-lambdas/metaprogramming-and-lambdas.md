# index

## Subjects

* `auto` show in **cppinsights**
* Templates with constants **\(fib,sum,array\)**
* Template specialization
* Template Metaprogramming \(TMP\) [Physics](https://github.com/erelsgl-at-ariel/cpp-5780/blob/master/09-specializations-metaprogramming/6-metaprogramming-physics/mks.cpp)
* Lambdas

## auto

### vector example

![](https://i.ibb.co/9wRQvhK/auto-on-vector.png)

### list example

![](https://i.ibb.co/dDXw6f5/auto-list.png)

### Fixed vector example

Lets make up a new container called `fixed_vector`

```cpp
#include <iostream>
using std::cout;

template <typename T, int I>
class fixed_vector{
    T arr[I];
    int size;
public:
    fixed_vector() : size(0) {}

    void push_back(T t){
        if (size<I){
            arr[size] = t;
            ++size;
        }
    }

    //TODO change to T&, was lazy :)
    T operator[] (int index){
        if (index<size && index>=0)
            return arr[index];
        else // was lazy :)
            return -1;
    }
};

int main(){
    fixed_vector<int,3> fv;
    for (int i = 1; i < 10; ++i)
        fv.push_back(i);

    for (int i = 0; i < 6; ++i)
        cout << fv[i] << " ";

    return 0;
}
```

```text
1 2 3 -1 -1 -1
```

## TODO add iterators

## Specialization

Sometimes we just want to feel special 😂

```cpp
#include <iostream>
using std::cout,std::endl;

template <typename T>
struct SpecializedStruct{
    SpecializedStruct() {cout << "I'm not special :(\n";}
    void foo(){cout << "something stupid :(\n";}
};

template <>
struct SpecializedStruct<double>{
    SpecializedStruct() {
        cout << "I'm a special struct :)\n";
    }
    void foo(){cout << "something funny :)\n";}
};


int main(){
    SpecializedStruct<std::string> sp_stru;
    sp_stru.foo();
    cout << endl;

    SpecializedStruct<double> not_sp_stru;
    not_sp_stru.foo();
    return 0;
}
```

```text
I'm not special :(
something stupid :(

I'm a special struct :)
something funny :)
```

notice the emty `<>` in `template<>` on then the `struct SpecializedStruct<double>`

There are 2 types of c++ developers:

* those who like TMP
* and those who don't

## Template metaprogramming

Template metaprogramming \(TMP\) in C++ is a technique for expressing and executing arbitrary algorithms in **compile-time** using C++ templates.

TMP is a form of _**declarative programming**_ in which the logic of computation is expressed without the use of explicit control flow statements \(`if`, `else`, `for`\). Some of these languages include SQL,cypher and sparql

So remember:

* no mutability
* no virtual functions
* no RTTI,etc.

## Sum recursion

```cpp
#include <iostream>
#include <string>
using namespace std;

template <int N>
struct Sum{
    static const int value = N + Sum<N-1>::value;
};

template <>
struct Sum<1>{ //template class specialization
    static const int value = 1;
};

int main () {
    cout << Sum<4>::value << endl;
    return 0;
}
```

```text
10
```

![](https://i.ibb.co/y0zV24r/template-compile-time.png)

The run-time isn't the run-time but rather the **compiler is the run-time**.

## Fibonacci recursion

```cpp
//@author: nissan goldberg
#include <iostream>
using namespace std;

template <int N>
struct Fib{
    static const int value = Fib<N-2>::value + Fib<N-1>::value;
};

template <>
struct Fib<1>{ //template class specialization
    static const int value = 1;
};

template <>
struct Fib<0>{
    static const int value = 0;
};

int main () {
    cout << Fib<5>::value << endl;
    return 0;
}
```

```text
8
```

**VS**

```cpp
int fib(int n) { 
    if (n <= 1) 
        return n; 
    return fib(n-1) + fib(n-2); 
}

int main () {
    //0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144
    return fib(40);
}
```

![](https://i.ibb.co/17GyJGc/template-speedup.png)

### Show [C++ Insights](https://cppinsights.io/)

more than **700x speedup** \(the compilation took 2x the amout so really 350x\)

The entire recursion is done by the compiler, while the final program just contain a constant.

### Fib with tail recursion \(skip this\)

```cpp
//@author: nissan goldberg, was really cool :)
#include <iostream>
using namespace std;

template <long N,long prev ,long next>
struct Fib_tr{
    static const long value = Fib_tr<N-1,next,next+prev>::value;
};

template <long prev, long next>
struct Fib_tr<1, prev, next>{ //template class specialization
    static const long value = next;
};

template <long prev ,long next>
struct Fib_tr<0,prev, next>{
    static const long value = prev;
};

template <int N>
struct Fib{
    static const long value = Fib_tr<N-1,0,1>::value;
};


int main () {
    cout << Fib<90>::value << endl;
    return 0;
}
```

```text
1779979416004714189
```

[The first 300 Fibonacci numbers, factored](http://www.maths.surrey.ac.uk/hosted-sites/R.Knott/Fibonacci/fibtable.html) to check :\)

### Physics example

```cpp
//@author: Dr. Erel Segel Halevi
#include <cassert>
#include <iostream>
using std::cout;
using std::endl;
using std::ostream;
using std::string;

template <int m, int k, int s>
struct mks {
    double val;
public:
    mks(double val): val(val) {}
};

template <int m, int k, int s>
auto operator+(mks<m,k,s> a, mks<m,k,s> b) {
    return mks<m,k,s>(a.val+b.val);
}

template <int ma, int ka, int sa, int mb, int kb, int sb>
auto operator*(mks<ma,ka,sa> a, mks<mb,kb,sb> b) {
    return mks<ma+mb,ka+kb,sa+sb>(a.val*b.val);
}

template <int ma, int ka, int sa, int mb, int kb, int sb>
auto operator/(mks<ma,ka,sa> a, mks<mb,kb,sb> b) {
    return mks<ma-mb,ka-kb,sa-sb>(a.val/b.val);
}

template <int m, int k, int s>
ostream& operator<<(ostream& out, mks<m,k,s> a) {
    return (out << a.val);
}

using Meters=mks<1,0,0>;  // meter
using Seconds=mks<0,0,1>;    // second
using Kilograms=mks<0,1,0>;    // kilogram
using Velocity=mks<1,0,-1>;  // meter*(second)^(-1)
using Acceleration=mks<1,0,-2>;  // meter*(second)^(-2)
using Energy=mks<2,1,-2>;  // kg^2*meter*(second)^(-2)

mks<0,0,1> operator"" _sec(long double x) { return mks<0,0,1>(x); }
mks<0,0,1> operator"" _min(long double x) { return mks<0,0,1>(x*60); }
mks<0,0,1> operator"" _hour(long double x) { return mks<0,0,1>(x*3600); }

int main() {
    //double time=5;   // seconds
    //double length = 7;  // meters
    //
    //cout << time+length;   // logic error

    Meters length = 15; // 15 meters
    cout << "length = " << length << endl;
    Seconds time   = 3;  // 3 seconds
    cout << "time = " << time << endl;
    //cout << time+length;   // compilation error

    Seconds time1 = 1.0_hour + 2.0_min + 50.0_sec;
    cout << "time1 = " << time1 << endl;

    //mks<0,0,2> time2 = 5;
    //cout << time+time2;

    auto velocity = length/time;
    cout << "velocity = " << velocity << endl;

    Velocity velocity2 = 4;
    cout << "velocity2 = " << velocity2 << endl;

    auto velocity_sum = velocity+velocity2;
    cout << "velocity_sum = " << velocity_sum << endl;
    //auto v_plus_l = velocity+length; // won't compile

    Velocity c = 3e8;  // speed of light (m/s)
    Kilograms m = 5; // 5 kg uranium
    auto mc = m*c;
    auto mc2 = m*c*c;
    Energy E(0);
    E = m*c*c;
    cout << "energy = " << E << endl;
}
```

```text
length = 15
time = 3
time1 = 3770
velocity = 5
velocity2 = 4
velocity_sum = 9
energy = 4.5e+17
```

#### Use TMP \(Template Meta-Programming\) when:

* A **macro is not enough**. You need something more complex than a macro, and you need it expanded before compiled.
* Using **recursive function** with a predetermined number of loops. In this case the overhead of function calls and setting up stack variables can be avoided and runtime will significantly decrease.
* Using loops that can be unrolled at compile time. For example hash-algorithms like MD5 and SHA1 contains well-defined block processing loops that can be unrolled with TMP.
* When calculating **constant** values. If you have constants that depend on other constants in your program, they might be a candidate for TMP.
* When the program should be portable to other platforms. In this case TMP might be an alternative to macros.

  **Don't use TMP when:**

* When a macro will do. In most cases a macro will be enough. And a macro is often easier to understand than TMP.
* You want a small executable. Templates in general, and TMP in particular will in often increase the code size.
* Your program already takes a long time to compile. TMP might significantly increase compile time.
* You are on a strict deadline. As noted above the complier will be very unfriendly when working with Template Metaprograms. Unless the changes you intend to make are very simple, you might want to save yourself a few hours of banging your head in the wall.

[source](https://www.codeproject.com/Articles/3743/A-gentle-introduction-to-Template-Metaprogramming)

## Functors - Overriding Function call operator

```cpp
#include <iostream>

struct Multiply{
    double operator()( const double v1, const double v2 ) const{
        return v1 * v2;
    }
};

int main (){
    const double v1 = 3.3;
    const double v2 = 2.0;
    Multiply m;

    std::cout << v1 << " * " << v2 << " = "
              << m( v1, v2 )
              << std::endl;
}
```

```text
3.3 * 2 = 6.6
```

## Understanding Lambdas

What does a lambda really look like?

```cpp
int main(){
    auto l = []() { return 5;};
    cout << typeid(l).name() << endl;
}
```

```text
Z4mainEUlvE_
```

main- is the function, v- is void.

Here the lambda `l` is similar to the following struct:

```cpp
struct __main_lambda_0{
    auto operator()() const {
        return 5;
    }
};
```

> Remove the double underscores to compile

Now if we were to add a **int param** like this

```cpp
[](int i) { return 5+i;};
```

It would be similar to this

```cpp
struct __main_lambda_0{
    auto operator()(int i) const {
        return 5+i;
    }
};

int main(){
    auto l = [](int i) { return 5+i;};
    cout << typeid(l).name() << endl;

    cout << l(3) << endl;
}
```

```text
Z4mainEUliE_
8
```

If we add a _**capture**_ it would look like this:

```cpp
[val](int i) { return 5+i+val; };
```

It is similar to this:

```cpp
//The same as the lambda l
struct __main_lambda_0{
    int val;

    __main_lambda_0(int v):val(v){} //ctor

    auto operator()(int i) const {
        return 5 + i + val;
    }
};

int main(){
    int val = 10;
    auto l = [val](int i) { return 5+i+val; };

    //auto l = __main_lambda_0{val}; 

    cout << l(3) << endl;
}
```

First call the ctor with val, then call the functor with 3.

Now if we replace `val` with `++val` wouldn't work, here is why

```cpp
int val = 10;
auto l = [val](int i) { return 5+i+ ++val; };
//cout << l(3) << endl;
```

this is because our structs \(\) operater is **const** in `auto operator()(int i) const {`

#### mutable

Now If we add the `mutable` specifier we would remove the const

```cpp
int val = 10;
auto l = [val](int i) mutable { return 5+i+ ++val; };
cout << l(1) << endl;
```

lets use the [cpp insights](https://cppinsights.io/) to see what is really happening here.

#### auto in param

Using `auto i`:

```cpp
int val = 10;
auto l = [val](auto i) mutable { return 5+i+ ++val; };
```

Would result to this:

```cpp
struct __main_lambda_0{
    int val;

    template<typename T>
    constexpr auto operator()(T i) const {
        return 5 + i + val;
    }
};
```

### Lets Review

* Basically a lamba is an **object**.
* The _**capture list**_ is init list of the ctor.
* The _**params**_ are a **functor** that is by default const unless we use the keyword `mutable`

### Lambda syntaxes:

* `[ captures ] ( params ) -> ret { body }`
* `[ captures ] ( params ) { body }`
* `[ captures ] { body }`

**Added C++20 syntax**

* `[ captures ] <tparams>( params ) specifiers exception->  { body }`

```cpp
// generic lambda, operator() is a template with two parameters
auto glambda = []<class T>(T a, auto&& b) { return a < b; };

// generic lambda, operator() is a template with one parameter pack
auto f = []<typename ...Ts>(Ts&& ...ts) {
   return foo(std::forward<Ts>(ts)...);
};
```

#### Lambda capture

Capture Many

* `&` :  by reference
* `=` : by value

Capture individual:

* `&var_name`:  by-reference capture
* `this` :  by-reference capture of the current object

#### Mixing up captures:

```cpp
[&]{};          // OK: by-reference capture default
[&, i]{};       // OK: by-reference capture, except i is captured by copy
```

More about [lambdas](https://en.cppreference.com/w/cpp/language/lambda)

## Examples of Lambdas

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using std::vector; using std::endl; using std::cout;

int main(){
    cout << "Starting Lambdas examples \n";
    std::vector<int> v1 = { 3, 1, 7, 9 };
    std::vector<int> v2 = { 10, 2, 7, 16, 9 };

    // access v1 and v2 by reference
    auto pushinto = [&v1,&v2](const int& m){
        v1.push_back(m);
        v2.push_back(m);
    };
    // it pushes 20 in both v1 and v2
    pushinto(20);

    // access v1 by copy
    [v1]() {
        cout << "printing values, access v1 by copy\n";
        for (auto p = v1.begin(); p != v1.end(); p++)
            cout << *p << " ";
    }();
}
```

```text
Starting Lambdas examples 
printing values, access v1 by copy
3 1 7 9 20
```

Next we'll use the `find_if` function

`InputIterator find_if (InputIterator first, InputIterator last, UnaryPredicate predicate);` **Returns:** an _**iterator**_ to the **first** element in the range \[first, last\] for **which pred\(function\) returns true**

[explanation](https://www.geeksforgeeks.org/stdfind_if-stdfind_if_not-in-c/)

Lets change our _**main**_ function to find first number greater than _N_. `[N]` denotes, can access only N by value

```cpp
int main(){
  std::vector<int> v1 = { 3, 1, 7, 9,20 };
  std::vector<int> v2 = { 10, 2, 7, 16, 9,20 };

  int N = 5;
  int k = 0;
  auto p = find_if(v1.begin(), v1.end(), [N](int& i){
          //i++; this works
          return i > N;
  });
  cout << "First number greater than 5 is : " << *p << endl;
  return 0;
}
```

```text
First number greater than 5 is : 7
```

if we were to change it to this we would get an error

```cpp
auto p = find_if(v1.begin(), v1.end(), [N](int& i){
        k++;
        return i > N;
    });
    cout << "K is " << k << endl;
```

Because _**k**_ cannot be implicitly captured because no default capture mode has been specified.

Finally, lets use the `count_if` function in our main, which will **count** numbers greater than or equal to _N_.

```cpp
int main(){
    std::vector<int> v1 = { 3, 1, 7, 9, 20 };
    std::vector<int> v2 = { 10, 2, 7, 16, 9,20 };

    int count_N = count_if(v1.begin(), v1.end(),
                           [&N](int a)->bool{
                               return (a >= N);
                           });

    cout << "The number of elements greater than or equal to 5 is : "
         << count_N << endl;

    return 0;
  }
```

This code also works without spefically defining the return type `->bool` on line 6.

We could clean this up to be one line:

```cpp
    int count_N = count_if(v1.begin(), v1.end(),[=](int a){ return (a >= N);});
```

Here we also used `[=]` to access all the variables _**by value**_

