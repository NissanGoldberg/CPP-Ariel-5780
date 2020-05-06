# Inheritance

## Door Class

**object type**:

* Door

**attributes**:

* Open or close

**methods**:

* Init as closed door
* Open if possible
* Close
* State if opened

```cpp
// A generic door class (Door.h)
#pragma once
class Door {
protected:
  bool shut_;
public:
  Door(); // Constructs a shut door
  bool isOpen() const; // Is the door open?
  void open(); // Opens the door, if possible
  void close(); // Shuts the door
};
```

We used `protected` so that subclasses can use the attribute as well.

```cpp
/* Door.cpp */
#include "Door.h"
Door :: Door() : shut_(true) { }
bool Door :: isOpen() const { return !shut_; }
// By default, it's always possible to open
void Door :: open() { shut_ = false; }
void Door :: close() { shut_ = true; }
```

```cpp
/* Game.cpp */
#include "Door.h"

int main(){
  Door BigHouseDoor;
  Door BathroomDoor;
  BigHouseDoor.open();
  ...
  ...
  if ( BigHouseDoor.isOpen() )
    enterBigHouse();
  else
    lookForBackDoor();
}
```

* class: Door
* objects: BigHouseDoor, BathroomDoor

### New Feature - version 2.0

Now the manager has added another feature to implement: **track how many door at every instance of the game**

An **inexperienced** would solve the problem by using a **global** variable and increment each time we added a new door and viceversa.

* This complicates the process and can cause errors later down the road.
* This also contradicts the OOD principle

### Solution

Definition of a **static class that is a friend**. That counts the amount of doors at run and is **incremented** by the **constuctor** of each new door and **decrements** in the **destructor**.

> **Remember:** A static method is part of the class and only one instance exists and not a different one for each object

```cpp
// A generic door class (Door.h)
#pragma once
class Door {
protected:
  bool shut_;
  static int count_;
public:
  Door(); // Constructs a shut door
  bool isOpen() const; // Is the door open?
  void open(); // Opens the door, if possible
  void close(); // Shuts the door
  ~Door(); // Destructor
  // define a static class method, which return the number
  // of Door instances ( objects)
  static int getDoorCount();
};
```

Added features

* `~Door()` will decrement each time a door is removed
* the static variable `count_`
* static method `getDoorCount`, returns `count_`

> **Remember:** the static variable must be defined init in the `.cpp` file

```cpp
/* Door.cpp */
#include "Door.h"

/* defining the static members first */
int Door::count_ = 0; /* defining and initializing count_*/
int Door::getDoorCount () {
/* this method can't access shut_ !! */
  return count_;
}

Door::Door() : shut_(true) {
  count_++;
}

bool Door::isOpen() const {
  return !shut_;
}

// By default, it's always possible to open
void Door::open() { shut_ = false; }
void Door::close() { shut_ = true; }

Door::~Door() {
  count_--;
}
```

```cpp
/* Game.cpp */
#include <iostream>
#include "Door.h"
int main() {
    Door D;
    Door* Dp;
    // calling non-static methods of the object
    D.open();
    D.close();
    cout << "At start, we have " << Door::getDoorCount() << " Door(s)" << endl;
    // pay attention: when calling static method, you
    // should use the scope of the class ( Door::...)

    Dp = new Door();
    Dp->isOpen();
    cout << "After new, we have " << Door::getDoorCount() << " Door(s)" << endl;
    delete Dp;
    cout << "After delete, we have " << Door::getDoorCount() << " Door(s)" << endl;
}
```

```text
At start, we have 1 Door(s)
After new, we have 2 Door(s)
After delete, we have 1 Door(s)
```

### Inheritance - version 3.0

There are 2 ways to use a class:

* make a new **instance** of the class \(what we did until now\)
* **Inherit** from an existing class

**Question**

Which fields and methods from the base class can be accessed from the derived class?

**Answer**

`public` and `protected`

Therefore, each sub-class of _Door_ can use every method and variable of _Door_

### Example of sub-class

In a game there may be many types of doors. Here we will design a door **with a lock**

**object type**:

* LockableDoor

**attributes**:

* Open or close
* Locked or not locked

**methods**:

* Init as closed door and locked
* Open if not locked
* Close
* State if opened
* Lock
* Open lock
* State if locked

```cpp
// A lockable door class (LockableDoor.h)
#pragma once
#include "Door.h"

class LockableDoor : public Door {
protected:
    bool locked_;
public:
    LockableDoor(); // Constructs a shut door
    bool isLocked() const; // Is the door locked?
    void open(); // Opens the door, if not locked
    void lock(); // Locks the door
    void unlock(); // Unlocks the door
};
```

> **Notice:** that this is more compact that `Door.h` this is because we will reuse Door and dont have to redefine everything a second time.

```cpp
class LockableDoor : public Door
```

when we inherit using `public` before Door we can access any _public_ and _protected_ variables and methods. If not specified the default is **private** ![](https://i.ibb.co/y0YCxgS/access.jpg)

We could also inherit from multiple classes as so:

```cpp
class ClockRadio : public Clock, public Radio
```

Lets now define our `.cpp` file

```cpp
// A lockable door class (LockableDoor.cpp)
#pragma once
#include "LockableDoor.h"

LockableDoor::LockableDoor() : Door(), locked_(true) { }
bool LockableDoor :: isLocked() const { return locked_; }
void LockableDoor :: lock() { locked_ = true; }
void LockableDoor :: unlock() { locked_ = false; }

void LockableDoor :: open() {
    if (!locked_)
        Door::open(); // could use shut_=false too
}
```

and our `main.cpp`

```cpp
/* Game.cpp */
#include <iostream>
#include "LockableDoor.h"

int main() {
    Door generic; // generic.shut_ is true
    LockableDoor bathDoor; // bathDoor.shut_ and bathDoor.locked_ are true
    generic.open(); // generic.shut_ is false
    bathDoor.lock(); // bathDoor.shut_ and bathDoor.locked_ are true
    bathDoor.open(); // bathDoor.shut_ and bathDoor.locked_ are still true
    if (!bathDoor.isOpen())
        bathDoor.unlock(); // bathDoor.shut_ is true and bathDoor.locked_ is false
}
```

or in one file for simplicity

```cpp
#include <iostream>
using namespace std;

class Door {
protected:
    bool shut_;
    static int count_;
public:
    Door(); // Constructs a shut door
    bool isOpen() const; // Is the door open?
    void open(); // Opens the door, if possible
    void close(); // Shuts the door
    ~Door(); // Destructor
    // define a static class method, which return the number
    // of Door instances ( objects)
    static int getDoorCount();
};

// A lockable door class (LockableDoor.cpp)

/* defining the static members first */
int Door::count_ = 0; /* defining and initializing count_*/
int Door::getDoorCount () {
/* this method can’t access shut_ !! */
    return count_;
}

Door::Door() : shut_(true) {
    count_++;
}

bool Door::isOpen() const {
    return !shut_;
}

// By default, it's always possible to open
void Door::open() { shut_ = false; }
void Door::close() { shut_ = true; }

Door::~Door() {
    count_--;
}


class LockableDoor : public Door {
protected:
    bool locked_;
public:
    LockableDoor(); // Constructs a shut door
    bool isLocked() const; // Is the door locked?
    void open(); // Opens the door, if not locked
    void lock(); // Locks the door
    void unlock(); // Unlocks the door
};

LockableDoor::LockableDoor() : Door(), locked_(true) { }
bool LockableDoor :: isLocked() const { return locked_; }
void LockableDoor :: lock() { locked_ = true; }
void LockableDoor :: unlock() { locked_ = false; }

void LockableDoor :: open() {
    if (!locked_)
        Door::open(); // could use shut_=false too
}

int main() {
    Door generic; // generic.shut_ is true
    LockableDoor bathDoor; // bathDoor.shut_ and bathDoor.locked_ are true
    generic.open(); // generic.shut_ is false
    bathDoor.lock(); // bathDoor.shut_ and bathDoor.locked_ are true
    bathDoor.open(); // bathDoor.shut_ and bathDoor.locked_ are still true
    if (!bathDoor.isOpen()){
        bathDoor.unlock(); // bathDoor.shut_ is true and bathDoor.locked_ is false
        cout << "Bathroom door is now unlocked" << endl;
    }
}
```

### What is the size of the following?

This question was asked to me by a fellow student

```cpp
#include <iostream>
using namespace std;

class A{
private:
    int i;
};

class B : public A{
public:
    int j;
};

int main(){
   A a;
   B b;
   b.j = 100;
//   b.i = 100; won't compile
   cout << sizeof(a) << endl;
   cout << sizeof(b) << endl;
}
```

```text
4
8
```

As we can see even though **b** doesn't have access to **i** \(unless we use a setter/getter\) it will still use memory

### Inheritance: order of construction / destruction

#### Class objects are constructed from the bottom up:

1. The **base**.
2. The members \(_**may want to use init list**_\)
3. The derived class itself

#### They are destroyed in the opposite order:

1. The **derived class** itself.
2. The members.
3. The base.

> **How to rember:** Think about this, first someones parents were born and then him \(construction\). Now this makes alot of sense because our parent class probably has to initialize a few fields and only after can we use them.

#### In our LockableDoor example

1. Doors' constuctor, which inits **shut\_** and **count\_**
2. Initialize  **\_locked**
3. LockableDoor constuctor which is empty

### Base and Derived Compatibility

Lets say we have 2 functions and 2 objects

```cpp
void f1(LockableDoor d);
void f2(Door d);

Door myDoor;
LockableDoor keyedDoor;
```

Giving a derived type to a base type is compatible

```cpp
f1(keyedDoor); // is legal
f2(keyedDoor); // is legal
```

The opposite is not true

```cpp
f1(myDoor); // illegal !!!
f2(myDoor); // is legal
```

and

```cpp
myDoor = keyedDoor; // is legal, may slice object
keyedDoor = myDoor; // illegal!!!
```

`myDoor = keyedDoor` can cause object slicing, since the compiler will ignore any variables or methods not in the base type. We will explore how to solve some of the problems with **virtual methods** later on.

## Has-a and Is-a relationship

* **Is-A  relation- Interface Inheritance**:  creating a subtype of an existing class  for purpose of setting up dynamic  binding.
  * **Circle** is a subclass of **Shape** \(i.e., Is-A  relation\).
  * A **Birthday** is a subclass of **Date**.
* **Has-A relation - Code Reuse**: reusing an implementation to create a new class type.
  * Class **Clock** that inherits from class **Window** the  ability to represent itself graphically. A **stack** is _**not  really a subtype**_ or specialization of **Vector**.
  * In this case, inheritance makes implementation  easier, because there is no rewrite and debug  existing code.
  * This is called using inheritance for reuse, i.e., a  pseudo-Has-A relation.

Now here **LockableDoor is-a Door**

## Adding more Classes

![](https://i.ibb.co/BK79C16/Door-diagram.jpg)

```cpp
// A combination lock door with single Date
class CombinationLockDoor : public LockableDoor {
protected:
    const Date combination_;
public:
     // Constructs new door with combination c
     CombinationLockDoor(const Date& c);
     // Attempts to unlock the door with combination c; hides LockableDoor::unlock
     void unlock(const Date& c);
};

CombinationLockDoor::CombinationLockDoor(const Date& c)
                : lockableDoor(), combination_(c) { }

void CombinationLockDoor :: unlock(const Date& c) {
 if (c.theYear() == combination_.theYear() && c.theMonth() == combination_.theMonth() && c.theDay() == combination_.theDay())
    LockableDoor :: unlock();
 // could use locked_ = false;
}
```

### Build visual Chest

```text
├── Chest
│     ├── LockableChest
```

`Makefile`

```text
all: main.cpp
    clang++-10 -c main.cpp -std=c++17 && clang++-10 main.o -o sfml-app -lsfml-graphics -lsfml-window -lsfml-system -std=c++17
```

`main.cpp`

```cpp
#include <iostream>
#include <SFML/Graphics.hpp>
#include <time.h>
#include <list>
#include <cmath>
using namespace sf;
using namespace std;

const int W = 1200;
const int H = 800;

float DEGTORAD = 0.017453f; // Degrees TO Radians

class Animation{
public:
    float Frame, speed;
    Sprite sprite;
    std::vector<IntRect> frames;

    Animation(){}

    Animation (Texture &t, int x, int y, int w, int h, int count, float Speed){
        Frame = 0;
        speed = Speed;

        for (int i=0;i<count;i++)
            frames.push_back( IntRect(x+i*w, y, w, h)  );

        sprite.setTexture(t);
        sprite.setOrigin(w/2,h/2);
        sprite.setTextureRect(frames[0]);
    }


    void update(){
        Frame += speed;
        int n = frames.size();
        if (Frame >= n) Frame -= n;
        if (n>0) sprite.setTextureRect( frames[int(Frame)] );
    }

    bool isEnd(){
        return Frame+speed>=frames.size();
    }

};

class Chest {
protected:
    bool shut_;
    static int count_;
    float x,y,dx,dy,R,angle;
public:
    Animation anim;

    Chest(); // Constructs a shut door
    bool isOpen() const; // Is the door open?
    void open(); // Opens the door, if possible
    void close(); // Shuts the door
    ~Chest(); // Destructor
    // define a static class method, which return the number
    // of Door instances ( objects)
    static int getDoorCount();

    void settings(Animation &a,int X,int Y,float Angle=0,int radius=1){
        anim = a;
        x=X; y=Y;
        angle = Angle;
        R = radius;
    }

    void draw(RenderWindow &app){
        anim.sprite.setPosition(x,y);
        anim.sprite.setRotation(angle+90);
        app.draw(anim.sprite);

        CircleShape circle(R);
        circle.setFillColor(Color(255,0,0,170));
        circle.setPosition(x,y);
        circle.setOrigin(R,R);
        //app.draw(circle);
    }

};

// A lockable door class (LockableDoor.cpp)

/* defining the static members first */
int Chest::count_ = 0; /* defining and initializing count_*/
int Chest::getDoorCount () {
/* this method can't access shut_ !! */
    return count_;
}

Chest::Chest() : shut_(true) {
    count_++;
}

bool Chest::isOpen() const {
    return !shut_;
}

// By default, it's always possible to open
void Chest::open() { shut_ = false; }
void Chest::close() { shut_ = true; }

Chest::~Chest() {
    count_--;
}


class LockableChest : public Chest {
protected:
    bool locked_;
public:
    LockableChest(); // Constructs a shut door
    bool isLocked() const; // Is the door locked?
    void open(); // Opens the door, if not locked
    void lock(); // Locks the door
    void unlock(); // Unlocks the door
};

LockableChest::LockableChest() : Chest(), locked_(true) { }
bool LockableChest :: isLocked() const { return locked_; }
void LockableChest :: lock() { locked_ = true; }
void LockableChest :: unlock() { locked_ = false; }

void LockableChest :: open() {
    if (!locked_)
        Chest::open(); // could use shut_=false too
}

int main() {
    Chest generic; // generic.shut_ is true
    LockableChest bathDoor; // bathDoor.shut_ and bathDoor.locked_ are true
    generic.open(); // generic.shut_ is false
    bathDoor.lock(); // bathDoor.shut_ and bathDoor.locked_ are true
    bathDoor.open(); // bathDoor.shut_ and bathDoor.locked_ are still true
    if (!bathDoor.isOpen()){
        bathDoor.unlock(); // bathDoor.shut_ is true and bathDoor.locked_ is false
        cout << "Bathroom door is now unlocked" << endl;
    }



    sf::Text text;
    text.setFillColor(sf::Color::Red);
    sf::Font font;
    font.loadFromFile("door_images/arial.ttf");
    text.setFont(font);

    std::list<Chest*> doors;
    srand(time(0));

    RenderWindow app(VideoMode(W, H), "Open Chest!");
    app.setFramerateLimit(60);

    Texture t1;
//    t1.loadFromFile("door_images/doors.png");
    t1.loadFromFile("door_images/chest.png");

    //door - sprite
//    Animation sPlayer(t1, 65,0,65,96, 1, 0);
//    Animation sPlayer_go(t1, 125,0,65,96, 1, 0);

    //chest - sprite
    Animation sChestClosed(t1, 0, 0, 590, 600, 1, 0);
    Animation sChestOpen(t1, 600, 0, 590, 600, 1, 0);

    LockableChest *lockableDoor = new LockableChest();
    lockableDoor->settings(sChestOpen, 500, 500, 270, 20);
    doors.push_back(lockableDoor);


    /////main loop/////
    while (app.isOpen()){
        Event event;
        while (app.pollEvent(event)){
            if (event.type == Event::Closed)
                app.close();

            if (event.type == Event::KeyPressed)
                if (event.key.code == Keyboard::Space){}
        }


        if (Keyboard::isKeyPressed(Keyboard::C)){
            text.setString("Pressed C - closed");
            text.setCharacterSize(24); // in pixels, not points!
            lockableDoor->close();
        }
        if (Keyboard::isKeyPressed(Keyboard::L)) {
            text.setString("Pressed L - locked");
            text.setCharacterSize(24); // in pixels, not points!
            lockableDoor->lock();
        }
        if (Keyboard::isKeyPressed(Keyboard::O)){
            text.setString("Pressed O - opened");
            text.setCharacterSize(24); // in pixels, not points!
            lockableDoor->open();
        }
        if (Keyboard::isKeyPressed(Keyboard::U)){
            text.setString("Pressed U - unlocked");
            text.setCharacterSize(24); // in pixels, not points!
            lockableDoor->unlock();
        }


        app.clear(Color::White);
        app.draw(text);
        if (lockableDoor->isOpen())
            lockableDoor->anim = sChestOpen;
        else if (!lockableDoor->isOpen())
            lockableDoor->anim = sChestClosed;

        //////draw//////
//        app.draw(background);
        for(auto i:doors) i->draw(app);
        app.display();
    }
}
```

**Instructions:**

* `O` opens chest
* `C` closes chest
* `L` locks chest
* `U` unlocks chest

Needs files:

* ariel.ttf
* chest.png

[Door sprite](https://opengameart.org/content/animated-castle-doors)

[Ariel font](https://www.freefontspro.com/14454/arial.ttf)

