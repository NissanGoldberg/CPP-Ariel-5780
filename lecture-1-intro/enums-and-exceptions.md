# Enums and Exceptions

## Plain Enum - The C way

```cpp
#include <iostream>
using namespace std;

enum Season {
    WINTER,    // = 0 by default
    SPRING,    // = WINTER + 1
    SUMMER,    // = WINTER + 2
    AUTUMN        // = WINTER + 3
};

int WINTER = 60;  // won't compile - redefinition

int main() {
    cout << WINTER;
    int curr_season = WINTER;
    curr_season += 50;
    cout << curr_season << endl;
    cout << (curr_season==0) << endl;
}
```

**1st** - This won't compile because on _**line 12**_ we are redefing winter. No variable can have a name which is already in some enumeration.

**2nd** - usually _**curr\_season += 50;**_ usually wouldn't make sense and also this could be very dangerous.

To prevent these mishaps c++ has included _enum class_. The original c-styled enum may be used if desired.

## Enum Class - The C++ way

```cpp
#include <iostream>

enum class Season {
    WINTER,    // = 0 by default
    SPRING,    // = WINTER + 1
    SUMMER,    // = WINTER + 2
    AUTUMN    // = WINTER + 3
};

int WINTER = 60;

int main() {
    using namespace std;

    cout << WINTER << endl;
    cout << int(Season::WINTER) << endl;
    Season curr_season = Season::WINTER;

    if (curr_season == Season::WINTER)
        cout << "brewwwww, its winter" << endl;
    else
        cout << "its nice weather" << endl;
}
```

#### **`output`**

```text
60
0
brewwwww, its winter 
```

The following is much more understandable.

We could also do the following to print out which month it is

```cpp
Season curr_season = Season::WINTER;
string name[4] {"winter", "spring", "summer", "autumn"};
cout << name[int(curr_season)];
```

### Safety prevention in Enum Class

The enum isn't treated like and integer like the c-styled version therefore the following won't compile

```cpp
Season curr_season = Season::WINTER;
cout << curr_season << endl;
```

This would work in the C version. In order to print the intger value wiht enum class we must cast it to an _int_

```cpp
cout << int(curr_season) << endl;
```

```text
0
```

since _**curr\_season**_ isn't an integer the **none** of the following wouldnt work either

```cpp
cout << (curr_season==0) << endl;
curr_season += 50;
int new_season = Season::SUMMER;
```

rather we can do the following

```cpp
if (curr_season == Season::WINTER)
    cout << "brewwwww, its winter" << endl;
```

Other good examples of enums

```cpp
enum class week { Sunday, Monday, Tuesday,
                  Wednesday, Thursday, Friday, Saturday };

enum class suit {club, diamonds, hearts, spades };
```

## Exceptions

To prevent early termination of a program we mush _**throw**_ and **catch** an **exception** by using the keywords `throw`, `try` and `catch`

```cpp
try{
    throw 20;
}catch (int e){
    cout << "An exception occurred. Exception Num. " << e << '\n';
}
```

#### **`output`**

```text
An exception occurred. Exception Num. 20
```

#### Throwing a String

Instead of throwing an _**int**_ we can throw a _**string**_

```cpp
// Throw a string:
double safesqrt(double x) {
    if (x<0)
        throw string(
                "x must be non-negative, but it is "
                +to_string(x));
    return std::sqrt(x);
}

int main() {
    cout << "std::sqrt(-4) = " << std::sqrt(-4) << endl;
    try {
        cout << "safesqrt(-4) = " << safesqrt(-4) << endl;
    }catch (string message) {
        cout << "   caught exception: " << message << endl;
    }

    cout << safesqrt(-9) << endl; // uncaught exception
    cout << "Program finished successfully" << endl;
}
```

#### **`output`**

```text
std::sqrt(-4) = nan
safesqrt(-4) =    caught exception: x must be non-negative, but it is -4.000000

terminate called after throwing an instance of 'std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<
char> >'
```

The function `sqrt` will return a _**nan**_ \(not a number\) if the input is below zero

```cpp
    cout << "std::sqrt(-4) = " << std::sqrt(-4) << endl;
```

```text
std::sqrt(-4) = nan
```

Our function _safesqrt_ returns a _**double**_

```cpp
double safesqrt(double x) {
```

To prevent _**terminate called after throwing...**_ if we were to only run the following

```cpp
cout << safesqrt(-9) << endl;
```

We must _**catch**_ the exception of type _**string**_ the type that was thrown on lines 4-6

```cpp
    try {
        cout << "safesqrt(-4) = " << safesqrt(-4) << endl;
    }catch (string message) {
        cout << "   caught exception: " << message << endl;
    }
```

#### Throwing a Standard Exception Object

we can use a standard expction such as `std::out_of_range`

```cpp
double safesqrt1(double x) {
    if ( x<0 )
        throw std::out_of_range{"x must be positive"};
    double result = std::sqrt(x);
    return result;
}
```

notice line 3

```cpp
 throw std::out_of_range{"x must be positive"};
```

similarly we must catch a std::exception object \(or reference to one\)

```cpp
try {
    cout << "safesqrt1(-4) = " << safesqrt1(-4) << endl;
}
catch (const std::exception& ex) {
    cout << "   caught exception: " << ex.what() << endl;
}
```

notice line 4 it is a `std::exception&` type instead of the string type before `catch (string message)`

```cpp
catch (const std::exception& ex) {
```

the `what` function will display the string given to the constructor of the object

```cpp
cout << ex.what()
```

#### Multiple handlers and Any Exception

**catch** expressions can be _**chained**_ with the relavent handler catching the exception

```cpp
try {
  throw 'a';
}
catch (int param) { cout << "int exception"; }
catch (char param) { cout << "char exception"; }
```

```text
char exception
```

Additionally we can catch **any** exception by using three dots `...`

```cpp
try {
   throw string("wow, why would you do that?!");
}
catch (int param) { cout << "int exception"; }
catch (char param) { cout << "char exception"; }
catch (...) { cout << "default exception"; }
```

```text
default exception 
```

#### Throwing an object

first create an object

```cpp
class argument_must_be_positive {};
```

next throw the object in the desired place

```cpp
throw argument_must_be_positive{};
```

and we can catch the error using ellipsis \(three dots\) `...`

```cpp
catch (...) { cout << "some exception"; }
```

