# Operator Overloading - Exercise

```cpp
#include <iostream>
#include <vector>
#include <algorithm>    //std::any_of
#include <numeric> //std::iota
using namespace std;

struct Predicate{
    int N;

    Predicate(int v):N(v){} //ctor

    bool operator()( const int val) const{
        return val >= N;
    }
};

int main(){
    vector<int> v1 {1,2,3,4,-1};

    std::vector<int> v2(10);
    std::iota(v2.begin(), v2.end(), 0); // creates range from 0 to 9


    int N=2;
    Predicate predicate{N};

    cout << "How many num of numbers greater or equal to "  << N << "?\n" <<
            std::count_if(v1.begin(), v1.end(), predicate) <<endl; //calls predicate() on each value of v

    cout << "How many num of numbers greater or equal to "  << N << "?\n" <<
         std::count_if(v2.begin(), v2.end(), predicate) <<endl;

    //cout << std::count_if(v.begin(), v.end(),[N](int a){ return (a >= N);}) <<endl; //lambda expression

    return 0;
}
```

```text
How many num of numbers greater or equal to 2?
3
How many num of numbers greater or equal to 2?
8
```

`std::iota` creates range from the number of the 3rd param `std::count_if` count how many times the predicate returns true

> **Note:** The better way would to be to replace our functor with a **lambda** expression

```cpp
cout << std::count_if(v.begin(), v.end(),[N](int a){ return (a >= N);}) <<endl;
```

#### Revisiting the polynomial functor

$$ax^2+bx+c$$

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <algorithm>

using namespace std;

// ax^2 + bx + c - poly
class Polynomial2 {
    double _a,_b,_c;
public:
    Polynomial2(double a):_a(a), _b(0), _c(0){  }
    Polynomial2(double a, double b, double c):_a(a), _b(b), _c(c){  }

    double operator() (double x) {
        cout << _a << "*" << x <<"^2 + "
             << _b << "*" << x <<" + "
             << _c << " = ";


        return _a*x*x + _b*x + _c;
    }

    double operator()() { return 0; }

    friend ostream& operator<< (ostream& os, const Polynomial2& p) {
        return (os << "poly2 =    "
                   << p._a << "*x^2 + "
                   <<   p._b  << "*x + "
                   <<   p._c );
    }
};
```

```cpp
int main() {
    int a=0,b=0,c=0;

    Polynomial2 p {1,2,3}

    cout << p(3) <<endl;

    return 0;
}
```

```cpp
int main() {
    int a=0,b=0,c=0;

    std::vector<Polynomial2> polies;
    for (int i = 0; i < 5; ++i)
        polies.push_back(Polynomial2(++a,++b,++c));

    for(Polynomial2& p: polies){
        cout << p <<endl;
        cout << "poly2(3) = " << p(3) << endl<<"=====\n";
    }

    return 0;
}
```

```text
poly2 =    1*x^2 + 1*x + 1
poly2(3) = 1*3^2 + 1*3 + 1 = 13
=====
poly2 =    2*x^2 + 2*x + 2
poly2(3) = 2*3^2 + 2*3 + 2 = 26
=====
poly2 =    3*x^2 + 3*x + 3
poly2(3) = 3*3^2 + 3*3 + 3 = 39
=====
poly2 =    4*x^2 + 4*x + 4
poly2(3) = 4*3^2 + 4*3 + 4 = 52
=====
poly2 =    5*x^2 + 5*x + 5
poly2(3) = 5*3^2 + 5*3 + 5 = 65
=====
```

**Same code but with color and different poly\(x\)**

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <algorithm>

using namespace std;

// ax^2 + bx + c - poly
class Polynomial2 {
    double _a,_b,_c;
public:
    Polynomial2(double a):_a(a), _b(0), _c(0){  }
    Polynomial2(double a, double b, double c):_a(a), _b(b), _c(c){  }

    double operator() (double x) {
        cout << _a << "*" << "\033[1;31m" << x << "\033[0m"<< "^2 + "
             << _b << "*" << x <<" + "
             << _c << " = ";


        return _a*x*x + _b*x + _c;
    }

    double operator()() { return 0; }

    friend ostream& operator<< (ostream& os, const Polynomial2& p) {
        return (os << "poly2 =    "
                   <<   p._a << "*x^2 + "
                   <<   p._b  << "*x + "
                   <<   p._c );
    }
};


int main() {
    int a=0,b=0,c=0;
    int x = 3;

    std::vector<Polynomial2> polies;
    for (int i = 0; i < 5; ++i)
        polies.push_back(Polynomial2(++a,++b,++c));

    for(Polynomial2& p: polies){
        cout << p <<endl  << "poly2("<< x <<") = ";
        int solution = p(x++);

        cout << "\033[1;32m" << solution <<"\033[0m" << endl<<"=====\n";
    }

    return 0;
}
```

ADD IMAGE

```text
poly2 =    1*x^2 + 1*x + 1
poly2(3) = 1*3^2 + 1*3 + 1 = 13
=====
poly2 =    2*x^2 + 2*x + 2
poly2(4) = 2*4^2 + 2*4 + 2 = 42
=====
poly2 =    3*x^2 + 3*x + 3
poly2(5) = 3*5^2 + 3*5 + 3 = 93
=====
poly2 =    4*x^2 + 4*x + 4
poly2(6) = 4*6^2 + 4*6 + 4 = 172
=====
poly2 =    5*x^2 + 5*x + 5
poly2(7) = 5*7^2 + 5*7 + 5 = 285
=====
```

#### Exercise

Find in the above code how many poly\(3\) are greater than 50 $$ax^2+bx+c>50$$

> **Erase the ostream and couts**
>
> \`\`\`cpp
>
> ## include
>
> ## include
>
> ## include
>
> ## include

using namespace std;

// ax^2 + bx + c - poly class Polynomial2 { double \_a,\_b,\_c; public: Polynomial2\(double a\):\_a\(a\), \_b\(0\), \_c\(0\){ } Polynomial2\(double a, double b, double c\):\_a\(a\), \_b\(b\), \_c\(c\){ }

```text
double operator() (double x) {
    return _a*x*x + _b*x + _c;
}

double operator()() { return 0; }
```

};

struct Predicate{ int N;

```text
Predicate(int v):N(v){} //ctor

bool operator()( Polynomial2& p) const{
    return p(3) >= N;
}
```

};

int main\(\) { int a=0,b=0,c=0; int x = 3;

```text
std::vector<Polynomial2> polies;
for (int i = 0; i < 5; ++i)
    polies.push_back(Polynomial2(++a,++b,++c));


int N=50;
Predicate predicate{N};

cout << "How many num of poly(3) are greater or equal to "  << N << "?\n" <<
     std::count_if(polies.begin(), polies.end(), predicate) <<endl; //calls predicate() on each value of v

return 0;
```

}

```text

```

How many num of poly\(3\) are greater or equal to 50? 2

```text
#### Exercise

Lets add Polynomial addition
* `+`

$$ (1x^2+2x+3) + (10x^2+10x+10)$$

$$ =11x^2+12x+1
3$$

* `+=`
* `++` - pre/postfix: this will add 1 to the **c** of the polynomial

$$ ax^2+bx+(c+1)$$


```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <algorithm>

using namespace std;

// ax^2 + bx + c - poly
class Polynomial2 {
    double _a,_b,_c;
public:
    Polynomial2(double a):_a(a), _b(0), _c(0){  }
    Polynomial2(double a, double b, double c):_a(a), _b(b), _c(c){  }

    double operator() (double x) {
        return _a*x*x + _b*x + _c;
    }

    double operator()() { return 0; }

    Polynomial2& operator+=(const Polynomial2& other_p){
        this->_a += other_p._a;
        this->_b += other_p._b;
        this->_c += other_p._c;
        return *this;
    }

    friend const Polynomial2 operator+ (const Polynomial2& p1, const Polynomial2& p2);

    friend ostream& operator<< (ostream& os, const Polynomial2& p) {
        return (os << "poly2 =    "
                   << p._a << "*x^2 + "
                   <<   p._b  << "*x + "
                   <<   p._c );
    }

    // Overloading the prefix operator
    //https://en.cppreference.com/w/cpp/language/operator_incdec
    //T& T::operator++();
    Polynomial2& operator++(){
        ++_c;
        return *this;
    }

    // Overloading the postfix operator
    ////T T::operator++();
    Polynomial2 operator++(int){
        Polynomial2 temp(_a,_b,++_c);
        ++_c;
        return temp;
    }
};

// note: this function is not a member function!
const Polynomial2 operator+ (const Polynomial2& p1, const Polynomial2& p2) {
    return Polynomial2(p1._a + p2._a, p1._b + p2._b, p1._c + p1._c);
}

int main() {
    int a=0,b=0,c=0;
    int x = 3;

    std::vector<Polynomial2> polies;
    for (int i = 0; i < 5; ++i)
        polies.push_back(Polynomial2(++a,++b,++c));

    cout << "== p1+=p2 =="<<endl;
    polies[0]+=polies[1]; // 1*x^2 + 1*x + 1    +   2*x^2 + 2*x + 2
    cout << polies[0] <<"\n\n";

    cout <<"== p1 + p2 =="<<endl;
    Polynomial2 new_p1 = polies[0] + polies[1]; //  3*x^2 + 3*x + 3    +   2*x^2 + 2*x + 2
    cout << new_p1  <<"\n\n";

    cout << "== p1 + p2 + p3 =="<<endl;
    Polynomial2 new_p2 = polies[0] + polies[1] + polies[2]; //  3*x^2 + 3*x + 3    +   2*x^2 + 2*x + 2
    cout << new_p2 <<"\n\n";

    cout << "== p1++ =="<<endl;
    cout << new_p2++  <<endl;

    return 0;
}
```

```text
== p1+=p2 ==
poly2 =    3*x^2 + 3*x + 3

== p1 + p2 ==
poly2 =    5*x^2 + 5*x + 6

== p1 + p2 + p3 ==
poly2 =    8*x^2 + 8*x + 12

== p1++ ==
poly2 =    8*x^2 + 8*x + 13
```

#### SFML version

Before we begin lets install **SFML**

```text
sudo apt-get install libsfml-dev
```

To compile

```text
g++ -c main.cpp
g++ main.o -o sfml-app -lsfml-graphics -lsfml-window -lsfml-system
```

And to run

```text
./sfml-app
```

Lets now code it 😃

```cpp
Polynomial2& operator++()   //prefix operatro
Polynomial2 operator++(int) //postfix operator
Polynomial2& operator+=(const Polynomial2& other_p)
friend const Polynomial2 operator+ (const Polynomial2& p1, const Polynomial2& p2)
```

```cpp
#include <SFML/Graphics.hpp>
#include <time.h>
#include <list>
#include <iostream>
#include <cmath>

using namespace sf;
using namespace std;

const int W = 800;
const int H = 800;


class Polynomial2 {
    double _a,_b,_c;
public:
    Polynomial2(double a):_a(a), _b(0), _c(0){  }
    Polynomial2(double a, double b, double c):_a(a), _b(b), _c(c){  }

    double operator() (double x) {
        return _a*x*x + _b*x + _c;
    }

    double operator()() { return 0; }

    Polynomial2& operator+=(const Polynomial2& other_p){
        this->_a += other_p._a;
        this->_b += other_p._b;
        this->_c += other_p._c;
        return *this;
    }

    friend const Polynomial2 operator+ (const Polynomial2& p1, const Polynomial2& p2);

    friend ostream& operator<< (ostream& os, const Polynomial2& p) {
        return (os << "poly2 =    "
                   << p._a << "*x^2 + "
                   <<   p._b  << "*x + "
                   <<   p._c );
    }

    // Overloading the prefix operator
    //https://en.cppreference.com/w/cpp/language/operator_incdec
    //T& T::operator++();
    Polynomial2& operator++(){
        ++_c;
        return *this;
    }

    // Overloading the postfix operator
    ////T T::operator++();
    Polynomial2 operator++(int){
        Polynomial2 temp(_a,_b,++_c);
        ++_c;
        return temp;
    }
};


int main(){

    sf::RenderWindow window(sf::VideoMode(W, H), "Hello world!");
    window.setFramerateLimit(30);
//    ImGui::SFML::Init(window);

//https://www.youtube.com/watch?v=QyAjRULZkHw
//https://www.sfml-dev.org/tutorials/2.0/graphics-vertex-array.php
    sf::Vertex line_y[] = {
                    sf::Vertex(sf::Vector2f(W/2, 0)),
                    sf::Vertex(sf::Vector2f(W/2, H))
            };
    sf::Vertex line_x[] = {
            sf::Vertex(sf::Vector2f(0, H/2)),
            sf::Vertex(sf::Vector2f(W, H/2))
    };

//    line_x[0].color = sf::Color::Blue;


//    line_y.rotate(90);

//    shape.setFillColor(sf::Color::Green);
    int num_of_points = 200;
    sf::VertexArray points(sf::Points, num_of_points);
    Polynomial2 poly {1,3,-4};
//    Polynomial2 poly {0,3,-4}; //line
    int fx_points[W];
    int half_screen = W/2;

    //calculating and drawing points
    for (int i = 0; i < num_of_points ; ++i) {
        fx_points[i] = poly(i-num_of_points/2);
        std::cout << i-num_of_points/2 << ": "<< fx_points[i] << std::endl;
        points[i].position = sf::Vector2f(i+W/2, fx_points[i]*-1+H/2);//SFML is y is opposite
    }

    //2nd polynom
    int fx_points2[W];
    sf::VertexArray points2(sf::Points, num_of_points);
    Polynomial2 poly2 {0,3,-4};
    //calculating and drawing points
    for (int i = 0; i < num_of_points ; ++i) {
        fx_points2[i] = poly2(i-num_of_points/2);
        std::cout << i-num_of_points/2 << ": "<< fx_points2[i] << std::endl;
        points2[i].position = sf::Vector2f(i+W/2, fx_points2[i]*-1+H/2);//SFML is y is opposite
    }



    sf::Clock deltaClock;
    /////main loop/////
    while (window.isOpen())
    {
        sf::Event event;
        while (window.pollEvent(event)){
            if (event.type == Event::Closed)
                window.close();

        }

        //////draw//////
        deltaClock.restart();

        window.clear();
        window.draw(points);
        window.draw(points2);
        window.draw(line_y, 2, sf::Lines);
        window.draw(line_x, 2, sf::Lines);

        window.display();
    }

    return 0;
}
```

We should also normalize but we are going to keep it simple $$1/max*fx*Height$$

### Polynomial 3

$$ax^3+bx^2+cx+d$$

Add:

* Integrals: **prefix,postfix**
* Derivatives:  **prefix,postfix**

Our definition of integration $$\int (bx^2 + cx +d ) dx = \frac{bx^3}{3} + \frac{cx^2}{2} +dx +0$$

```cpp
class Polynomial3 {
    double _a,_b,_c,_d;
public:
    Polynomial3(double a):_a(a), _b(0), _c(0),_d(0){  }
    Polynomial3(double a, double b, double c, double d):_a(a), _b(b), _c(c),_d(d){  }

    double operator() (double x) {
        return _a*x*x*x + _b*x*x + _c*x+_d;
    }

    double operator()() { return 0; }

    Polynomial3& operator+=(const Polynomial3& other_p){
        this->_a += other_p._a;
        this->_b += other_p._b;
        this->_c += other_p._c;
        this->_d += other_p._d;
        return *this;
    }

    friend const Polynomial3 operator+ (const Polynomial3& p1, const Polynomial3& p2);

    friend ostream& operator<< (ostream& os, const Polynomial3& p) {
        return (os << "poly2 =    "
                   << p._a  << "*x^3 + "
                   << p._b  << "*x^2 + "
                   << p._c  << "*x + "
                   << p._d );
    }

    // Overloading the prefix operator,     & T::operator++();
    Polynomial3& operator++(){
        if(_a!=0) //would be better to throw error
            return *this;

        _a = _b/3;
        _b = _c/2;
        _c = _d;
        _d = 0; //should be c

        return *this;
    }

    // Overloading the postfix operator,    T T::operator++();
    Polynomial3 operator++(int){
        if(_a!=0) //would be better to throw error
            return *this;

        Polynomial3 temp(_b/3, _c/2,_d , 0);

        _d = _c; //should be c
        _c = 2*_b;
        _b = 3*_a;
        _a = 0;

        return temp;
    }


    // Overloading the prefix operator,     & T::operator--();
    Polynomial3& operator--(){
        _d = _c; //should be c
        _c = 2*_b;
        _b = 3*_a;
        _a = 0;

        return *this;
    }

    // Overloading the postfix operator,    T T::operator--();
    Polynomial3 operator--(int){
        Polynomial3 temp(0, 3*_a,2*_b , _c);

        _a = 0;
        _b = 3*_a;
        _c = 2*_b;
        _d = _c; //should be c

        return temp;
    }

};
```

```cpp
int main(){
    Polynomial3 p{0,2,3,4};
    cout << ++p << endl;
    cout << --p << endl;
}
```

```text
poly2 =    0.666667*x^3 + 1.5*x^2 + 4*x + 0
poly2 =    0*x^3 + 2*x^2 + 3*x + 4
```

$$\int (0x^3+2x^2+3x+4)dx = \frac{2x^3}{3} + \frac{3x^2}{2} +4x +0$$

$$f' (\frac{2x^3}{3} + \frac{3x^2}{2} +4x +0) = 0x^3+2x^2+3x+4$$

#### SFML version

```cpp
#include <SFML/Graphics.hpp>
#include <time.h>
#include <list>
#include <iostream>
#include <cmath>

using namespace sf;
using namespace std;

const int W = 800;
const int H = 800;


class Polynomial3 {
    double _a,_b,_c,_d;
public:
    Polynomial3(double a):_a(a), _b(0), _c(0),_d(0){  }
    Polynomial3(double a, double b, double c, double d):_a(a), _b(b), _c(c),_d(d){  }

    double operator() (double x) {
        return _a*x*x*x + _b*x*x + _c*x+_d;
    }

    double operator()() { return 0; }

    Polynomial3& operator+=(const Polynomial3& other_p){
        this->_a += other_p._a;
        this->_b += other_p._b;
        this->_c += other_p._c;
        this->_d += other_p._d;
        return *this;
    }

    friend const Polynomial3 operator+ (const Polynomial3& p1, const Polynomial3& p2);

    friend ostream& operator<< (ostream& os, const Polynomial3& p) {
        return (os << "poly2 =    "
                   << p._a  << "*x^3 + "
                   << p._b  << "*x^2 + "
                   << p._c  << "*x + "
                   << p._d );
    }

    // Overloading the prefix operator,     & T::operator++();
    Polynomial3& operator++(){
        if(_a!=0) //would be better to throw error
            return *this;
            //throw out_of_range("Cannot integrate more than 3rd degree: ");

        _a = _b/3;
        _b = _c/2;
        _c = _d;
        _d = 0; //should be c

        return *this;
    }

    // Overloading the postfix operator,    T T::operator++();
    Polynomial3 operator++(int){
        if(_a!=0) //would be better to throw error
            return *this;
            // //throw out_of_range("Cannot integrate more than 3rd degree: ");

        Polynomial3 temp(_b/3, _c/2,_d , 0);

        _d = _c; //should be c
        _c = 2*_b;
        _b = 3*_a;
        _a = 0;

        return temp;
    }


    // Overloading the prefix operator,     & T::operator--();
    Polynomial3& operator--(){
        _d = _c; //should be c
        _c = 2*_b;
        _b = 3*_a;
        _a = 0;

        return *this;
    }

    // Overloading the postfix operator,    T T::operator--();
    Polynomial3 operator--(int){
        Polynomial3 temp(0, 3*_a,2*_b , _c);

        _a = 0;
        _b = 3*_a;
        _c = 2*_b;
        _d = _c; //should be c

        return temp;
    }

};


int main(){

    sf::RenderWindow window(sf::VideoMode(W, H), "Hello world!");
    window.setFramerateLimit(30);
//    ImGui::SFML::Init(window);

//https://www.youtube.com/watch?v=QyAjRULZkHw
//https://www.sfml-dev.org/tutorials/2.0/graphics-vertex-array.php
    sf::Vertex line_y[] = {
            sf::Vertex(sf::Vector2f(W/2, 0)),
            sf::Vertex(sf::Vector2f(W/2, H))
    };
    sf::Vertex line_x[] = {
            sf::Vertex(sf::Vector2f(0, H/2)),
            sf::Vertex(sf::Vector2f(W, H/2))
    };

//    line_x[0].color = sf::Color::Blue;


//    line_y.rotate(90);

//    shape.setFillColor(sf::Color::Green);
    int num_of_points = 200;
    sf::VertexArray points(sf::Points, num_of_points);
    Polynomial3 poly {0,2,3,4};
//    Polynomial3 poly {0,3,-4}; //line
    int fx_points[W];
    int half_screen = W/2;


    for (int i = 0; i < num_of_points ; ++i) {
        fx_points[i] = poly(i-num_of_points/2);
        std::cout << i-num_of_points/2 << ": "<< fx_points[i] << std::endl;
        int temp = fx_points[i]*-1+H/2;
        int temp2 = fx_points[i]*-1+H/2;
//        cout << temp2 << endl;
        points[i].position = sf::Vector2f(i+W/2, fx_points[i]*-1+H/2);//SFML is y is opposite
    }

    //2nd polynom
    int fx_points2[W];
    sf::VertexArray points2(sf::Points, num_of_points);
    Polynomial3 poly2 = --poly;
    //calculating and drawing points
    for (int i = 0; i < num_of_points ; ++i) {
        fx_points2[i] = poly2(i-num_of_points/2);
//        std::cout << i-num_of_points/2 << ": "<< fx_points2[i] << std::endl;
        points2[i].position = sf::Vector2f(i+W/2, fx_points2[i]*-1+H/2);//SFML is y is opposite
    }


    sf::Clock deltaClock;
    /////main loop/////
    while (window.isOpen())
    {
        sf::Event event;
        while (window.pollEvent(event)){
            if (event.type == Event::Closed)
                window.close();

        }

        //////draw//////
        deltaClock.restart();

        window.clear();
        window.draw(points);
        window.draw(points2);
        window.draw(line_y, 2, sf::Lines);
        window.draw(line_x, 2, sf::Lines);

        window.display();
    }

    return 0;
}
```

#### Adding the `[] operator`

```cpp
const int operator[](uint index) const {
        if (index >= 4)
            throw out_of_range("Array index out of bounds: "+to_string(index));

        switch (index) {
            case 0:
                return _d;
            case 1:
                return _c;
            case 2:
                return _b;
            case 3:
                return _a;
        }

    }
```

```cpp
int main(){
    Polynomial3 p{0,45,3,4};
    cout << p << endl;
    cout << p[2] << endl;

    return 0;
}
```

```text
poly2 =    0*x^3 + 45*x^2 + 3*x + 4
45
```

### Thinking exercise

Lets say we would like to add `< operator` what would you do?

```cpp
bool operator<(const Polynomial3 & lhs, const Polynomial3 & rhs)
```

I suggest we we start we can **compare the a of ax^3** and

return the lowest . If the they have the same the compare the b of ax^2.

Now we are able to sort our polynimials

#### If extra time

* Convert polynomial to array type instead of \_a,\_b
* Deep copy

#### Inline

**Without inline**

When the program executes the function call instruction

* the CPU stores the memory address of the instruction following the function call
* copies the arguments of the function on the stack
* and finally transfers control to the specified function.

The CPU then executes the function code

* stores the function return value in a predefined memory location/register
* and returns control to the calling function.

**With inline**

When the inline function is called whole code of the inline function gets inserted or substituted at the point of inline function call. This substitution is performed by the C++ compiler at compile time

> **Remember:** inlining is only a _**request**_ to the compiler, not a command **Note:** all the functions defined inside the class are implicitly inline.

#### Syntax

use the `inline` keyword before the function declaration

```cpp
inline return-type function-name(parameters){
    // function code
}
```

**Example**

```cpp
#include <iostream>

using namespace std;

inline int Max(int x, int y) {
   return (x > y)? x : y;
}

// Main function for the program
int main() {
   cout << "Max (20,10): " << Max(20,10) << endl;
   cout << "Max (0,200): " << Max(0,200) << endl;
   cout << "Max (100,1010): " << Max(100,1010) << endl;

   return 0;
}
```

```text
Max (20,10): 20
Max (0,200): 200
Max (100,1010): 1010
```

