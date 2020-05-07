# Polymorphism

## Overview

```text
├── Fighter
│     ├── Ninja
│     ├── Zombie
│     ├── Viking 
│
```

```cpp
#include <iostream>
using std::cout, std::endl, std::string;

class Fighter{
protected:
    string name;
    int hp;
    int power;
protected:
    void applyDamageTo(Fighter& target, int damage) const {}
public:
    Fighter(const string &name, int hp, int power) : name(name), hp(hp), power(power) {}
    const string& getName() const {}
    bool isAlive() const {}
    void attack(Fighter& other) const {}
};


class Ninja: public Fighter{
public:
    Ninja(const string &name, int hp=100, int power=1) : Fighter(name, hp, power) {}
    void specialMove(Fighter& other) {  cout << "Ninja special move\n"; }
};


class Zombie: public Fighter{
public:
    Zombie(const string &name, int hp=100, int power=1) : Fighter(name, hp, power) {}
    void specialMove(Fighter& other) const {  cout << "Zombie special move\n"; }
};


void Engage(Fighter& f1, Fighter& f2){
    Fighter* p1 = &f1;
    Fighter* p2 = &f2;

    p1->attack(*p2);
    p2->attack(*p1);

    p1->specialMove(*p2);
    p2->specialMove(*p1);
}
```

```cpp
int main(){
    Ninja f1("Hanzo");
    Zombie f2("zombat");

    while (f1.isAlive() && f2.isAlive()){
        Engage(f1, f2);
    }
}
```

The following won't compile because `Fighter` doesn't have `specialMove` so in **Engage** we can't call

```cpp
p1->specialMove(*p2);
p2->specialMove(*p1);
```

## Solution

Lets add `Fighter::specialMove`

```cpp
class Fighter{
...
public:
    ...
    void specialMove(Fighter& other) const{ cout << "Fighter special move\n"; }
};
```

### What do we expect in our output?

We would expect to see

```text
Ninja special move
Zombie special move
```

but instead we get

```text
Fighter special move
Fighter special move
```

The reason is because `Fighter* p1,p2` are unaware of the `specialMove` overloaded function in **Ninja** and **Zombie**

### Solution

Lets add `virtual` before `Fighter::specialMove`

```cpp
```cpp
class Fighter{
...
public:
    ...
    virtual void specialMove(Fighter& other) const{ cout << "Fighter special move\n"; }
};
```

And now we get

```cpp
Fighter special move
Zombie special move
Fighter special move
Zombie special move
```

this allows use to call at run-time the overloaded function or in otherwords **polymorphism**

### but wait????

Why are we getting **Fighter special move** shouldn't we be **Ninja special move**?

Maybe we should add `override` to the end of our function to see what happened

```cpp
class Ninja: public Fighter{
public:
    Ninja(const string &name, int hp=100, int power=1) : Fighter(name, hp, power) {}
    void specialMove(Fighter& other)  override  {  cout << "Ninja special move\n"; }
};


class Zombie: public Fighter{
public:
    Zombie(const string &name, int hp=100, int power=1) : Fighter(name, hp, power) {}
    void specialMove(Fighter& other) const override  {  cout << "Zombie special move\n"; }
};
```

And we get this compile-time error

```text
Ninja::specialMove(Fighter&) marked override, but does not override
```

So I guess we forgot the `const` on Ninja. So I guess thats why they added `override`, one dumb mistake ruined our code.

### Lets fix it now

```cpp
class Ninja: public Fighter{
public:
    Ninja(const string &name, int hp=100, int power=1) : Fighter(name, hp, power) {}
    void specialMove(Fighter& other) const override  {  cout << "Ninja special move\n"; }
};


class Zombie: public Fighter{
public:
    Zombie(const string &name, int hp=100, int power=1) : Fighter(name, hp, power) {}
    void specialMove(Fighter& other) const override  {  cout << "Zombie special move\n"; }
};
```

And here we get:

```text
Ninja special move
Zombie special move
Ninja special move
Zombie special move
```

## Code

```cpp
#include <iostream>
using std::cout, std::endl, std::string;

class Fighter{
protected:
    string name;
    int hp;
    int power;
protected:
    void applyDamageTo(Fighter& target, int damage) const {}
public:
    Fighter(const string &name, int hp, int power) : name(name), hp(hp), power(power) {}
    const string& getName() const {}
    bool isAlive() const {}
    void attack(Fighter& other) const {}
    virtual void specialMove(Fighter& other) const { cout << "Fighter special move\n"; }
};


class Ninja: public Fighter{
public:
    Ninja(const string &name, int hp=100, int power=1) : Fighter(name, hp, power) {}
    void specialMove(Fighter& other) const override  {  cout << "Ninja special move\n"; }
};


class Zombie: public Fighter{
public:
    Zombie(const string &name, int hp=100, int power=1) : Fighter(name, hp, power) {}
    void specialMove(Fighter& other) const override  {  cout << "Zombie special move\n"; }
};
```

```cpp
void Engage(Fighter& f1, Fighter& f2){
    Fighter* p1 = &f1;
    Fighter* p2 = &f2;

    p1->attack(*p2);
    p2->attack(*p1);

    p1->specialMove(*p2);
    p2->specialMove(*p1);
}

int main(){
    Ninja f1("Hanzo");
    Zombie f2("zombat");

    while (f1.isAlive() && f2.isAlive()){
        Engage(f1, f2);
    }
    return 0;
}
```

So we fixed our code by adding **polymorphism** using `virtual` and making sure we overrided the functions using `override`. It seems that `Fighter::specialMove` won't be used at all, **so maybe we should make it abstract?**

### Pure Virtual Function

We can make `Fighter::specialMove` pure virtual function \(or abstract\) meaning that we must implement it in order to use our class by adding `=0` :

```cpp
virtual void specialMove(Fighter& other) const = 0;
```

From now on we can't create

```cpp
Fighter p1;
```

Because now that Fighter contains a pure virtual function it is itself an **abstract class** but we can still create a pointer

```cpp
Fighter* p1;
```

So now we have the following:

```cpp
class Fighter{
...
public:
    ...
    virtual void specialMove(Fighter& other) const =0;
};


class Ninja: public Fighter{
public:
    ...
    void specialMove(Fighter& other) const override  {  cout << "Ninja special move\n"; }
};


class Zombie: public Fighter{
public:
    ...
    void specialMove(Fighter& other) const override  {  cout << "Zombie special move\n"; }
};
```

### Lets add some game features 😃

```cpp
#include <iostream>
using std::cout, std::endl, std::string;

class Fighter{
protected:
    string name;
    int hp;
    int power;
protected:
    void applyDamageTo(Fighter& target, int damage) const {
        if (target.hp-damage>0)
            target.hp-=damage;
        else
            target.hp = 0;
        cout << target.name << " was inflicted " << damage << ", current hp: " <<target.hp  << "\n";
    }
public:
    Fighter(const string &name, int hp, int power) : name(name), hp(hp), power(power) {}

    const string& getName() const { return name;}
    bool isAlive() const {return hp > 0;}

    void attack(Fighter& other) const {
        if(rand() % 6 >=3)
            applyDamageTo(other, power);
    }
    virtual void specialMove(Fighter& other) const =0;
};

class Ninja: public Fighter{
public:
    Ninja(const string &name, int hp=10000, int power=1) : Fighter(name, hp, power) {}

    void specialMove(Fighter& other) const override {
        if(rand() % 10 >=9){
            cout << "Ninja special move\n";
            applyDamageTo(other, power*5);
        }
    }
};



class Zombie: public Fighter{
public:
    Zombie(const string &name, int hp=10000, int power=1) : Fighter(name, hp, power) {}

    void specialMove(Fighter& other) const override {
        if(rand() % 10 >=8){
            cout << "Zombie special move\n";
            applyDamageTo(other, power*3);
        }
    }
};

void Engage(Fighter& f1, Fighter& f2){
    Fighter* p1 = &f1;
    Fighter* p2 = &f2;

    p1->attack(*p2);
    p2->attack(*p1);

    p1->specialMove(*p2);
    p2->specialMove(*p1);
}

int main(){
    Ninja f1("Hanzo");
    Zombie f2("zombat");

    while (f1.isAlive() && f2.isAlive()){
        Engage(f1, f2);
    }
    cout << (f1.isAlive() ? f1.getName()  : f2.getName()) << " won" << std::endl;
    return 0;
}
```

```text
Ninja special move
zombat was inflicted 5, current hp: 1333
zombat was inflicted 1, current hp: 1332
Hanzo was inflicted 1, current hp: 6
Hanzo was inflicted 1, current hp: 5
Zombie special move
Hanzo was inflicted 3, current hp: 2
zombat was inflicted 1, current hp: 1331
Hanzo was inflicted 1, current hp: 1
Hanzo was inflicted 1, current hp: 0
zombat won
```

And now we have a working game 😃

### Adding group matches \(not on test\)

* Here we are going to add 2 teams of 3 and let them fight each other
* Each fighter will switch randomly and fight another oponent each turn

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using std::cout, std::endl, std::string, std::vector;

class Fighter{
protected:
    string name;
    int hp;
    int power;
protected:
    void applyDamageTo(Fighter& target, int damage) const {
        if (target.hp-damage>0)
            target.hp-=damage;
        else
            target.hp = 0;
        cout << target.name << " was inflicted " << damage << ", current hp: " <<target.hp  << "\n";
    }
public:
    Fighter(const string &name, int hp, int power) : name(name), hp(hp), power(power) {}

    const string& getName() const { return name;}
    bool isAlive() const {return hp > 0;}

    void attack(Fighter& other) const {
        if(rand() % 6 >=3)
            applyDamageTo(other, power);
    }
    virtual void specialMove(Fighter& other) const =0;

    ~Fighter() { cout << "Destroying fighter\n";}
};

class Ninja: public Fighter{
public:
    Ninja(const string &name, int hp=10000, int power=1) : Fighter(name, hp, power) {}

    ~Ninja() { cout << "Destroying Ninja\n";}

    void specialMove(Fighter& other) const override {
        if(rand() % 10 >=9){
            cout << "Ninja special move\n";
            applyDamageTo(other, power*5);
        }
    }

};



class Zombie: public Fighter{
public:
    Zombie(const string &name, int hp=10000, int power=1) : Fighter(name, hp, power) {}

    ~Zombie() { cout << "Destroying Zombie\n";}

    void specialMove(Fighter& other) const override {
        if(rand() % 10 >=8){
            cout << "Zombie special move\n";
            applyDamageTo(other, power*3);
        }
    }
};

class Viking: public Fighter{
public:
    Viking(const string &name, int hp=10000, int power=1) : Fighter(name, hp, power) {}

    ~Viking() { cout << "Destroying Viking\n";}

    void specialMove(Fighter& other) const override {
        if(rand() % 10 >=8){
            cout << "Viking special move\n";
            applyDamageTo(other, power*3);
        }
    }
};

void Engage(Fighter& f1, Fighter& f2){
    Fighter* p1 = &f1;
    Fighter* p2 = &f2;

    p1->attack(*p2);
    p2->attack(*p1);

    p1->specialMove(*p2);
    p2->specialMove(*p1);
}

bool alive_predicate(Fighter* f){
    return f->isAlive();
}

int main(){
    vector<Fighter*> team1 = {
            new Ninja ("Hanzo"),
            new Zombie ("zombat"),
            new Viking ("Thor")
    };

    vector<Fighter*> team2 = {
            new Ninja ("Kotaro"),
            new Zombie ("bobzie"),
            new Viking ("Loki")
    };

    while (std::any_of(team1.begin(), team1.end(), alive_predicate) && std::any_of(team2.begin(), team2.end(), alive_predicate) ){
        //shuffle the group to fight different oponents
        std::random_shuffle(team1.begin(), team1.end());
        //partition alive and dead
        std::partition(team1.begin(), team1.end(), alive_predicate);

        std::random_shuffle(team2.begin(), team2.end());
        std::partition(team2.begin(), team2.end(), alive_predicate);

        for (int i=0; i< team1.size(); ++i){
            Engage(*team1[i],*team2[i]);
            cout << "------------" << endl;
        }
        cout << "============" << endl;

    }
    cout << (std::any_of(team1.begin(), team1.end(), alive_predicate) ? "team1 "  : "team2 " ) << " wins" << std::endl;

    return 0;
}
```

```text
bobzie was inflicted 1, current hp: 0
...
============
Kotaro was inflicted 1, current hp: 1
Zombie special move
Kotaro was inflicted 3, current hp: 0
------------
Zombie special move
Hanzo was inflicted 3, current hp: 127
------------
Thor was inflicted 1, current hp: 48
Viking special move
Loki was inflicted 3, current hp: 0
------------
============
team1  wins

Process finished with exit code 0
```

Here all of team1 \(Kotaro,bobzie and Loki\) were defeated.

**Notice the power that polymorphism gives us.** We are fighting using 6 different objects of 3 different classes and using the same functions to do so. The `Engage` function will allow us to fight regardless of which type of **sub-Fighter** your are.

```cpp
void Engage(Fighter& f1, Fighter& f2){
    Fighter* p1 = &f1;
    Fighter* p2 = &f2;

    p1->attack(*p2);
    p2->attack(*p1);

    p1->specialMove(*p2);
    p2->specialMove(*p1);
}
```

We could scale this up to 50 versus 50 matches easily

### Wait!!!!

**We forgot to deallocate \(delete\) the memory!!!!** Well we can add the following to our main

```cpp
int main(){
    ...
    for (int i=0; i< team1.size(); ++i){
        delete team1[i];
        delete team2[i];
    }
    return 0;
}
```

But we get

```text
Destroying fighter
Destroying fighter
Destroying fighter
Destroying fighter
Destroying fighter
Destroying fighter
```

So it seems we are **calling the Fighters destructor** but not each sub-Fighters. This is not what we wanted.

Lets take a closer look here

```cpp
class Fighter{
public:
    ...
    ~Fighter() { cout << "Destroying fighter\n";}
};

class Ninja: public Fighter{
public:
    ...
    ~Ninja() { cout << "Destroying Ninja\n";}
};


class Zombie: public Fighter{
public:
   ...
    ~Zombie() { cout << "Destroying Zombie\n";}

};

class Viking: public Fighter{
public:
    ...
    ~Viking() { cout << "Destroying Viking\n";}
};
```

### Solution

Lets add `virtual` to **Fighter**

```cpp
class Fighter{
public:
    ...
    virtual ~Fighter() { cout << "Destroying fighter\n";}
};

class Ninja: public Fighter{
public:
    ...
    ~Ninja() { cout << "Destroying Ninja\n";}
};
...
```

```text
============
team1  wins
Destroying Zombie
Destroying fighter
Destroying Ninja
Destroying fighter
Destroying Ninja
Destroying fighter
Destroying Zombie
Destroying fighter
Destroying Viking
Destroying fighter
Destroying Viking
Destroying fighter

Process finished with exit code 0
```

Notice that we also are calling the fighters **destructor**.

**Rember:**

### Inheritance: order of construction / destruction

#### Class objects are constructed from the bottom up:

1. The **base**.
2. The members \(_**may want to use init list**_\)
3. The derived class itself

#### They are destroyed in the opposite order:

1. The **derived class** itself.
2. The members.
3. The base.

[Credits](https://www.youtube.com/watch?v=4Vvc1YurUYA)

### TODO add photo uml of inheritance w/o inheritance

### Inheritance and Composition

A car is not an engine; **it has one**. And a coffee machine has a grinder and a brewing unit, but it is none of them. The car and the coffee machine integrate an engine, grinder and brewing unit via their external APIs to compose a higher level of abstraction and provide more significant value to their users.

You can do the same in software development when you design a class to keep a reference to an object and to use it in one or more of its methods.

The main reason to use _**composition**_ is that it allows you to **reuse** code **without** modeling an is-a association as you do by using inheritance. It enables you to reuse code by modeling a **has-a association** between objects.

[What is Composition](https://stackify.com/oop-concepts-composition/)

### What we want?

We want to use a weapon or subclass of weapon as a black box without know the inter workings. We can also change weapons easily.

## Lets start

```cpp
struct Attributes{
    int hp,speed,power;
    Attributes(int hp, int speed, int power) : hp(hp), speed(speed), power(power) {}
};


class Weapon{
    string name;
public:
    Weapon(const string &name) : name(name) {}

    const string &getName() const {return name;}
    virtual int calculateDamage(const Attributes& attr) const =0;
};
```

> Remember: add `virtual` to `calculateDamage`

Now lets add weapon to our **Fighter** class

```cpp
class Fighter{
    Weapon* pWeapon = nullptr;
protected:
    string name;
    Attributes attr;
protected:
    void applyDamageTo(Fighter& target, int damage) const {
        if (target.attr.hp-damage>0)
            target.attr.hp-=damage;
        else
            target.attr.hp = 0;
        cout << target.name << " was inflicted " << damage << ", current hp: " <<target.attr.hp  << "\n";
    }
public:
    Fighter(const string &name, int hp, int speed, int power, Weapon* w= nullptr) :
                    name(name),
                    attr(hp,speed,power),
                    pWeapon(w){}

    const string& getName() const { return name;}
    bool isAlive() const {return attr.hp > 0;}

    void attack(Fighter& other) const {
            cout << name << " attacks " << other.name << " with his " << pWeapon->getName() << endl;
            applyDamageTo(other, pWeapon->calculateDamage(attr));
    }

    void giveWeapon(Weapon* pNewWeapon){
        delete pWeapon;
        pWeapon = pNewWeapon;
    }

    virtual void specialMove(Fighter& other) const =0;

    virtual ~Fighter() {
        delete pWeapon;
        cout << "Destroying fighter\n";
    }
};
```

**Changes:**

* Added `Attributes attr`
* in `applyDamageTo` everything was changed to `target.attr.hp` accordingly
* Added `pWeapon` arg to **constructor**
* `attack` function has changed to `applyDamageTo(other, pWeapon->calculateDamage(attr));`
* add `giveWeapon` function
* add `delete pWeapon` to destructor

Add the weapons sub-classes

```cpp
class Fists : public Weapon{
public:
    Fists(const string &name="fists") : Weapon(name) {}

    int calculateDamage(const Attributes &attr) const override {
        int num = rand() % 3;
        if(num >=1) {
            return attr.power + num;
        }
    }
};

class Sword : public Weapon{
public:
    Sword(const string &name="sword") : Weapon(name) {}

    int calculateDamage(const Attributes &attr) const override {
        int num = rand() % 10;
        if(num >=8) {
            return attr.power + num;
        }
    }
};

class Bat : public Weapon{
public:
    Bat(const string &name="bat") : Weapon(name) {}

    int calculateDamage(const Attributes &attr) const override {
        int num = rand() % 6;
        if(num >=3) {
            return attr.power + num;
        }
    }
};
```

Change our **fighters** subclasses by adding weapons in the param of our constructor

```cpp
class Ninja: public Fighter{
public:
    Ninja(const string &name, int hp=10000,int speed=3, int power=1, Weapon* pWeapon= nullptr)
        : Fighter(name, hp,speed, power,pWeapon) {}
    ...
}
```

our main also looks simialar expect for the fact that we give each Figher a weapon

```cpp
int main(){
    vector<Fighter*> team1 = {
            new Ninja ("Hanzo", new Sword),
            new Zombie ("zombat", new Fists),
            new Viking ("Thor", new Bat)
    };

    vector<Fighter*> team2 = {
            new Ninja ("Kotaro", new Sword),
            new Zombie ("bobzie", new Fists),
            new Viking ("Loki", new Bat)
    };
    ...
}
```

```text
============
Thor attacks Loki with his bat
Loki was inflicted 2, current hp: 111
Loki attacks Thor with his bat
Thor was inflicted 6, current hp: 0
Viking special move
Thor was inflicted 3, current hp: 0
------------
zombat attacks Kotaro with his fists
Kotaro was inflicted 0, current hp: 0
Kotaro attacks zombat with his sword
zombat was inflicted 1, current hp: 0
Zombie special move
Kotaro was inflicted 3, current hp: 0
------------
Hanzo attacks bobzie with his sword
bobzie was inflicted 4, current hp: 0
bobzie attacks Hanzo with his fists
Hanzo was inflicted 0, current hp: 0
------------
============
team2  wins
Destroying Viking
Destroying fighter
...
```

### Final Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using std::cout, std::endl, std::string, std::vector;

struct Attributes{
    int hp,speed,power;
    Attributes(int hp, int speed, int power) : hp(hp), speed(speed), power(power) {}
};


class Weapon{
    string name;
public:
    Weapon(const string &name) : name(name) {}

    const string &getName() const {return name;}
    virtual int calculateDamage(const Attributes& attr) const =0;
};

class Fists : public Weapon{
public:
    Fists(const string &name="fists") : Weapon(name) {}

    int calculateDamage(const Attributes &attr) const override {
        int num = rand() % 3;
        if(num >=1) {
            return attr.power + num;
        }
    }
};

class Sword : public Weapon{
public:
    Sword(const string &name="sword") : Weapon(name) {}

    int calculateDamage(const Attributes &attr) const override {
        int num = rand() % 10;
        if(num >=8) {
            return attr.power + num;
        }
    }
};

class Bat : public Weapon{
public:
    Bat(const string &name="bat") : Weapon(name) {}

    int calculateDamage(const Attributes &attr) const override {
        int num = rand() % 6;
        if(num >=3) {
            return attr.power + num;
        }
    }
};




class Fighter{
    Weapon* pWeapon = nullptr;
protected:
    string name;
    Attributes attr;
protected:
    void applyDamageTo(Fighter& target, int damage) const {
        if (target.attr.hp-damage>0)
            target.attr.hp-=damage;
        else
            target.attr.hp = 0;
        cout << target.name << " was inflicted " << damage << ", current hp: " <<target.attr.hp  << "\n";
    }
public:
    Fighter(const string &name, int hp, int speed, int power, Weapon* w= nullptr) :
                    name(name),
                    attr(hp,speed,power),
                    pWeapon(w){}

    const string& getName() const { return name;}
    bool isAlive() const {return attr.hp > 0;}

    void attack(Fighter& other) const {
            cout << name << " attacks " << other.name << " with his " << pWeapon->getName() << endl;
            applyDamageTo(other, pWeapon->calculateDamage(attr));
    }
    void giveWeapon(Weapon* pNewWeapon){
        delete pWeapon;
        pWeapon = pNewWeapon;
    }

    virtual void specialMove(Fighter& other) const =0;

    virtual ~Fighter() {
        delete pWeapon;
        cout << "Destroying fighter\n";
    }
};

class Ninja: public Fighter{
public:
    Ninja(const string &name,Weapon* pWeapon= nullptr, int hp=10000,int speed=3, int power=1) : Fighter(name, hp,speed, power,pWeapon) {}

    ~Ninja() { cout << "Destroying Ninja\n";}

    void specialMove(Fighter& other) const override {
        if(rand() % 10 >=9){
            cout << "Ninja special move\n";
            applyDamageTo(other, attr.power*5);
        }
    }

};



class Zombie: public Fighter{
public:
    Zombie(const string &name, Weapon* pWeapon= nullptr, int hp=10000,int speed=3, int power=1) : Fighter(name, hp,speed, power,pWeapon) {}

    ~Zombie() { cout << "Destroying Zombie\n";}

    void specialMove(Fighter& other) const override {
        if(rand() % 10 >=8){
            cout << "Zombie special move\n";
            applyDamageTo(other, attr.power*3);
        }
    }
};

class Viking: public Fighter{
public:
    Viking(const string &name,Weapon* pWeapon= nullptr, int hp=10000,int speed=3, int power=1) : Fighter(name, hp,speed, power,pWeapon) {}

    ~Viking() { cout << "Destroying Viking\n";}

    void specialMove(Fighter& other) const override {
        if(rand() % 10 >=8){
            cout << "Viking special move\n";
            applyDamageTo(other, attr.power*3);
        }
    }
};

void Engage(Fighter& f1, Fighter& f2){
    Fighter* p1 = &f1;
    Fighter* p2 = &f2;

    p1->attack(*p2);
    p2->attack(*p1);

    p1->specialMove(*p2);
    p2->specialMove(*p1);
}

bool alive_predicate(Fighter* f){
    return f->isAlive();
}

int main(){
    vector<Fighter*> team1 = {
            new Ninja ("Hanzo", new Sword),
            new Zombie ("zombat", new Fists),
            new Viking ("Thor", new Bat)
    };

    vector<Fighter*> team2 = {
            new Ninja ("Kotaro", new Sword),
            new Zombie ("bobzie", new Fists),
            new Viking ("Loki", new Bat)
    };

    while (std::any_of(team1.begin(), team1.end(), alive_predicate) && std::any_of(team2.begin(), team2.end(), alive_predicate) ){
        //shuffle the group to fight different oponents
        std::random_shuffle(team1.begin(), team1.end());
        //partition alive and dead
        std::partition(team1.begin(), team1.end(), alive_predicate);

        std::random_shuffle(team2.begin(), team2.end());
        std::partition(team2.begin(), team2.end(), alive_predicate);

        for (int i=0; i< team1.size(); ++i){
            Engage(*team1[i],*team2[i]);
            cout << "------------" << endl;
        }
        cout << "============" << endl;

    }
    cout << (std::any_of(team1.begin(), team1.end(), alive_predicate) ? "team1 "  : "team2 " ) << " wins" << std::endl;

    for (int i=0; i< team1.size(); ++i){
        delete team1[i];
        delete team2[i];
    }
    return 0;
}
```

