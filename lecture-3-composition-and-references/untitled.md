# Date Class - Tirgul

{% tabs %}
{% tab title="Date.h" %}
```cpp
#include <cstdio>

#ifndef DATE_H
#define DATE_H
class Date{
private:
    int day_;
    int month_;
    int year_;
public:
    Date(int,int,int);
    int theDay() const;
    int theMonth() const;
    int theYear() const;
    void advance(); // A method to change the date
};
#endif // DATE_H
```
{% endtab %}

{% tab title="Date.cpp" %}
```cpp
 #include "Date.h"

Date::Date(int day, int month, int year) :
        day_(day), month_(month), year_(year) { }

//Date :: Date() :
//        day_(31), month_(12), year_(2000) { }

int Date :: theDay() const {
    return day_;
}

int Date ::theMonth() const {
    return month_;
}

int Date :: theYear() const {
    return year_;
}

void Date :: advance() {
    day_ ++; // advance the day
    switch (month_) {
        case 1: case 3: case 5:
        case 7: case 8: case 10:
            // January, March, May, July, August, October
            if (day_ == 32 ) {
                day_ = 1;
                month_ ++;
            } break;
        case 12: // December
            if (day_ == 32) {
                day_ = 1;
                month_ = 1;
                year_ ++;
            } break;
        case 4: case 6: case 9: case 11:
            // April, June, September, November
            if (day_ == 31) {
                day_ = 1;
                month_ ++;
            } break;
        case 2: // February
            if (day_>29 || day_==29 && (year_%4>0
                                        || year_%100==0) && year_%400>0) {
                day_ = 1;
                month_ ++;
            }
    }
}
```
{% endtab %}

{% tab title="main.cpp" %}
```cpp
 int main() {
// defining a Data Object
    Date HerBirthDay(19,8,1976);
    printf("Her birthday is on %d / %d / %d \n",
           HerBirthDay.theDay( ) , HerBirthDay.theMonth( ),
           HerBirthDay.theYear( ) );
    HerBirthDay.advance( );
    printf("On %d / %d / %d she'll be very happy\n",
           HerBirthDay.theDay( ) , HerBirthDay.theMonth( ),
           HerBirthDay.theYear( ) );
// HerBirthDay.day_ ++ ; // illegal: day_ is private
// HerBirthDay.month_=8;// illegal: month_ is private
    return 0;
}
```
{% endtab %}
{% endtabs %}

### Function Prototype- הצהרה

We can see the our _**Date.h**_ only contains _prototypes_ of our class.

### Const member function

```cpp
int theDay() const;
int theMonth() const;
int theYear() const;
```

Here we also notice that the functions above end with `const`. `const` ensures us that the functions **will not change the object** but rather read only its values

### Implementing our Class

```cpp
int Date::theDay() const {
   return day_;
}
```

In _**Date.cpp**_ we implemented our **member funtion**.

We use the `::` or scope resolution operator to Define funtions in our class.

### Constuctors

1. doesnt return \(not even void\)
2. its name is similar to classes
3. better to initinize with _**init lists**_

```cpp
Date::Date(int day, int month, int year) :
       day_(day), month_(month), year_(year) { }
```

### Public & Private

```cpp
class Date{
private:
   int day_;
public:
   int theDay() const;
```

`theDay()` is **public** which mean anyone can call and access it. While `day_` is **private** which means only our class can access \(or call when needed\). _**Friend classes/methods**_ can also access or call or private member functions or variables.

> Member variables are **almost always private**

## Overloading

If if were to replace our main with the following would the code compile?

```cpp
int main() {
    Date yesterday;
    Date array_of_dates[100];
    return 0;
}
```

**NO!!!** We don't have a default constructor. We overloaded it.

Lets add a default ctor

## Tabs

Date.h

{% tabs %}
{% tab title="Date.h" %}
```cpp
class Date{
public:
    Date(); //default ctor
    Date(int,int,int);
}
```
{% endtab %}

{% tab title="Date.cpp" %}
```cpp
#include "Date.h"

Date :: Date() :
        day_(31), month_(12), year_(2000) { }

Date::Date(int day, int month, int year) :
       day_(day), month_(month), year_(year) { }
```
{% endtab %}

{% tab title="main.cpp" %}
```cpp
int main() {
    Date hisBirthDay(19,8,1996); // calls 3 arg ctor
    Date yesterday; // calls param-less ctor
    Date array_of_dates[100]; // calls param-less ctor 100 times
    return 0;
}
```
{% endtab %}
{% endtabs %}

The compiler knows which to ctor to call by the number of arguments in the parameter and their types.

## Watch out from this!

The following is incorrect

```cpp
int main() {
    Date someday(); 
    return 0;
}
```

The compiler believes this is a _**declaration of a function**_ who returns a Date.

### Overloading our `advance` funcion

**TABS**

Date.h

{% tabs %}
{% tab title="Date.h" %}
```cpp
 class Date{
public:
    void advance();
    void advance(int k);//advance k days
```
{% endtab %}

{% tab title="Date.cpp" %}
```cpp
void Date :: advance() {
  for(int i=1;i<=k; ++i)
    advance();
}
```
{% endtab %}

{% tab title="main.cpp" %}
```cpp
int main() {
    Date hisBirthDay(19,8,1996);
    hisBirthDay.advance(3); //22-8-96
    hisBirthDay.advance();
    return 0;
}
```
{% endtab %}
{% endtabs %}

### Default args

We can change

```cpp
class Date{
public:
  Date(int,int,int);
```

to this

```cpp
class Date{
public:
  Date(int day=31,int month=12,int year=2000);
```

We can only give default args to the **end** of our functions/ctors so the following is incorrect:

```cpp
class Date{
public:
  Date(int day=31,int month=12,int year);
```

### Const

```cpp
int main() {
  const Date newYear99(1,1,1999);
  Date someday;

  newYear99 = someday; // illegal. newYear is const.
  newYear99.advance(); // illegal. advance is non-const func.
  newYear99.theDay(); // ok. theDay is a const func.

  someday = Date(21,4,98); // ok
  someday.advance(); // ok
  someday = newYear99; // ok. A non-const copy is created
}
```

`newYear99.advance()` can't be called because

```cpp
class Date{
public:
  void advance();
  int theDay() const;
```

isn't a const function but `newYear99.theDay()` may be called for it is a const function.

### Pointer to a const

```cpp
int main(){
  const Date constDate(1,1,1999);
  Date normDate;
  const Date* constPointer;
  Date* normPointer;

  normPointer = &normDate; //ok
  constPointer = &normDate; //ok
  normPointer->advance(); //ok
  //constPointer->advance(); //illegal – even if we're actually
  //not pointing at const

  constPointer->theDay(); //ok

  //normPointer = &constDate; //illegal!
  constPointer = &constDate; //ok
  constPointer->theDay(); //ok
}
```

A pointer to a const _**can change**_ itself to another address to the following is legal:

```cpp
constPointer = &normDate;
constPointer = &constDate;
```

But it **can't change the value** to what it points to so this is illegal

```cpp
constPointer->advance();
```

### const const

```cpp
int main(){
  Date* const cpoint = new Date(1,1,1999); //the address is const
  const Date* const ccpoint = new Date(1,1,1998);
//address and value are const
  cpoint->advance(); //ok
  ccpoint->advance(); //illegal
}
```

Here _**cpoint**_ address can't be changed but the values can. While _**ccpoint**_ can't change either

## TODO pg. 43 onwards

