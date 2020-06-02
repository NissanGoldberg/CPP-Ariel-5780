# Metaprogramming and Lambdas

## Subjects

* `auto` show in **cppinsights**
* Templates with constants **\(fib,sum,array\)**
* Template specialization
* Template Metaprogramming \(TMP\) [Physics](https://github.com/erelsgl-at-ariel/cpp-5780/blob/master/09-specializations-metaprogramming/6-metaprogramming-physics/mks.cpp)
* Lambdas

There are 2 types of c++ developers:

* those who like TMP
* and those who don't

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

## Fib recursion

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

### primes \(skip this\)

```cpp
//@author: https://stackoverflow.com/questions/16761082/check-if-number-is-prime-during-compilation-in-c
#include <iostream>

template <int N, int D>
struct tmp {
    static const bool result = (N%D) && tmp<N,D-1>::result;
};

template <int N>
struct tmp<N,1> {
    static const bool result = true;
};

template <int N>
struct is_prime {
    static const bool result = tmp<N,N-1>::result;
};

int main(){
    std::cout << std::boolalpha << is_prime<7>::result;
    return 0;
}
```

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

## Lambdas

## auto

