# Makefile

Special Thanks to Dr. Pinchas Weisberg :smile: 

Create a file by the name of _**makefile**_ or _**Makefile**_ in the following format

```text
target: prerequisites
[TAB] command
```

for example

```text
myprog: myprog.cpp myprog.h
  g++ -o myprog myprog.cpp
clean:
  rm myprog
```

The make updates a _**target**_ e.g `myprog` that depends on _**prerequisite**_ files `myprog.cpp myprog.h` if any of the prerequisite files have been modified or the target doesn't exist.

## Rules of a makefile

* By default, make starts with the first target, thus make with no arguments , reads the makefile in the current directory and processes the first rule
* But before make can process this rule, it must check if the prerequisite files depend on other files, and process the rules for them
* If some other rule is not depended on by the goal, that rule is not processed, unless you tell make to do so, e.g. `make clean`

Another example

```text
sum: main.o sum.o
  g++ –o sum main.o sum.o

main.o: main.cpp sum.h
  g++ –c main.cpp

sum.o: sum.cpp sum.h
  g++ –c sum.cpp

clean:
  rm sum main.o sum.o
```

Here the makefile will start with the target **sum**. Since **sum** is dependent on **main.o** and **sum.o** it will go to them first.

The target **main.o** will check for **main.cpp** and **sum.h**. Afterward it will run `g++ –c main.cpp` and compile **main.o**

The `clean` target isn't dependent on sum and if we were to run it we will type in `make clean`

## Makefile Variables

To clean up our code we can use variabeles. To delcare a variable type `var_name = my_var` and to use them just use the dollar sign `$(var_name)`.

For example:

```text
OBJFILES = main.o sum.o
PROGRAM = sum
CC = g++
CFLAGS = -g -std=c++11 –Wall

$(PROGRAM): $(OBJFILES)
  $(CC) $(CFLAGS) -o $(PROGRAM) $(OBJFILES)

clean:
  rm $(PROGRAM) $(OBJFILES)
```

Here we declared and used 4 different variables: _OBJFILES_, _PROGRAM_, _CC_ and _CFLAGS_.

## Exercise

Create a makefile for the following files: _**main.cpp, Point.cpp, Point.hpp, Rectangle.cpp, Rectangle.hpp, Circle.cpp Circle.hpp, Triangle.cpp Triangle.hpp**_. Rectangle, Circle and Triangle are all dependent on Point

```text
CXX=clang++-5.0
CXXFLAGS=-std=c++17

all: a.out
    ./a.out

a.out: Point.o main.o Rectangle.o Triangle.o Circle.o
    $(CXX) $(CXXFLAGS) Point.o main.o Rectangle.o Triangle.o Circle.o

Point.o: Point.cpp Point.hpp
    $(CXX) $(CXXFLAGS) --compile Point.cpp -o Point.o

Rectangle.o: Rectangle.cpp Rectangle.hpp Point.hpp
    $(CXX) $(CXXFLAGS) --compile Rectangle.cpp -o Rectangle.o

Circle.o: Circle.cpp Circle.hpp Point.hpp
    $(CXX) $(CXXFLAGS) --compile Circle.cpp -o Circle.o

Triangle.o: Triangle.cpp Triangle.hpp Point.hpp
    $(CXX) $(CXXFLAGS) --compile Triangle.cpp -o Triangle.o

main.o: main.cpp Point.hpp
    $(CXX) $(CXXFLAGS) --compile main.cpp -o main.o

clean:
    rm -f *.o a.out
```

#### Bash and Makefiles

Lets say that we only have _**main.cpp**_, _**makefile**_ and a _**s.sh**_

{% code title="makefile" %}
```bash
a.out : main.cpp Point.cpp Point.hpp
	g++ -o $@ $^ 
```
{% endcode %}

{% code title="main.cpp" %}
```cpp
#include "Point.hpp"

#include <iostream>
using namespace std;

int main() {
    Point p1;
    cout << "p1 = " << p1.to_string() << endl;

    p1.setX(10);
    p1.setY(20);
    cout << "p1 = " << p1.to_string() << endl;

    Point p2 {11,21};
    cout << "p2 = " << p2.to_string() << endl;
    return 0;
}
```
{% endcode %}

{% code title="s.sh" %}
```bash
wget -O Point.hpp https://raw.githubusercontent.com/erelsgl-at-ariel/cpp-5780/master/02-classes-constructors-destructors/4-constructors/Point.hpp && \
wget -O Point.cpp https://raw.githubusercontent.com/erelsgl-at-ariel/cpp-5780/master/02-classes-constructors-destructors/4-constructors/Point.cpp && \
make && \
echo -e "===Archiving and Compressing===\n" 
tar cvzf files.tar.gz *.cpp *.hpp *.out

mkdir program_dir && \
echo -e "===Moving files===\n" && \
mv files.tar.gz program_dir 

echo -e "===starting program===\n"
./a.out

gfast

```
{% endcode %}

