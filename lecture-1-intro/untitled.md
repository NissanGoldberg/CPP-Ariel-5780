# IO and Namespaces

## Output

The keyword `cout` will output. Use the `<<` operator to output the data following it.

```cpp
cout << "Output sentence";
```

Either write `std::cout` or use the std namespace as follows:

```cpp
#include <iostream>
using namespace std;
cout << "hello";
```

For simplicity I will always implicitly include the std namespace

The output can also be _**chained**_ into a single statement

```cpp
cout << "This " << " is a " << "single C++ statement";
```

This is useful when you mix variables and literals together.

```cpp
int age = 20;
int zipcode = 99999;
cout << "I am " << age << " years old and my zipcode is " << zipcode;
```

The `std::endl` or `endl` manipulator function \(there are no arguments\) will enter a newline and flush the output stream. The following code appears on two different lines.

```cpp
cout << "My name is Nissan" << endl;
cout << "How are you doing?" << endl;
```

## Input

The keyword `cin` or `std::cin` is for input

```cpp
int age;
cin >> age;
```

The following will enter the input into the variable _age_ when the `ENTER` key has been pressed. Notice `>>`

**Strings** are defined using `std::string` or `string` keyword and then variables' name. _**Don't forget to include string**_

```cpp
#include <string> 
std::string str1;
string str2;
```

Putting it all together we get

```cpp
string name;
cout << "What is your name? ";
cin >> name;
cout << endl << "Hello " << name << "!" << endl;
```

A little bit more difficult example is the following

```cpp
#include <iostream>
#include <string>
int main()
{
    std::string str;
    int a;
    double b;
    std::cin >> str >> a >> b;
    if(std::cin.fail())
    {
        std::cerr << "input problem\n";
        return 1;
        }
    std::cout << "I got: "<< str << ' '
    << a << ' ' << b << std::endl;
}
```

### **`valid input`**

```text
hi
4
6.7
output: I got: hi 4 6.7
```

### **`invalid input`**

```text
a
b
output: input problem   
```

## Namespaces

Namespaces allow us to group named entities that otherwise would have global scope into narrower scopes, giving them namespace scope. This allows organizing the elements of programs into different logical scopes referred to by names.

To create a namespace use the `namespace` keyword. To use the namespace created use the `::` operator

```cpp
#include <iostream> 
using namespace std; 

// Variable created inside namespace 
namespace first 
{ 
    int val = 500; 
} 


int main() 
{ 
    int val = 200; // Local variable 
    cout << first::val << '\n';  //namespace var
    return 0; 
}
```

### **`output`**

```text
500  
```

Namespaces can also be used to access functions

```cpp
#include <iostream>
using namespace std;
namespace ns  
{ 
    const double x = 100; 
    double value() {  return 2*x; } 
} 

int main() 
{ 
    // Access value function within ns 
    cout << ns::value() << endl;  
    // Access variable x directly 
    cout << ns::x << endl;        
    return 0; 
}
```

### **`output`**

```text
200 
100
```

To access a namespace without explicitly calling each the the _ns::variable_ you may do the use the `using` keyword

```cpp
using namespace std;
namespace ns
{
    const double x = 100;
    double value() {  return 2*x; }
}

using namespace ns;
int main()
{
    cout << value() << endl; 
    cout << x << endl;
    return 0;
}
```

notice `using namespace ns;` on line 8.  
This allows us to use _x_ and _value_ in the namespace without using the `::` operator

**Note:** It's actually best practice not to import the entire namespace into your program because it pollutes your namespace. This can lead to naming collisions. It's best to import only what you are using. So use `std::cout` instead of `cout`.  
A better solution is to use the namespace only where you need it, for example inside the function.

```cpp
void foo()
{
  using namespace std;
  using ns::context;
  // some implementation
};
```

