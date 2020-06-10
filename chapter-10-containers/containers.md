# Containers

## Lambda Examples

### Lambda example

```cpp
#include <iostream>

template<typename F>
int foo(int x, int y, F lambda) {
    return lambda(x,y);
}

struct Multiply{
    double operator()( const double v1, const double v2 ) const{
        return v1 * v2;
    }
};

int main(){
    std::cout << foo(2,3, [](int x, int y){return x*y;}) << std::endl;
    Multiply mul;
    std::cout << foo(3,4, mul) << std::endl;
    return 0;
}
```

```text
6
12
```

Really looks like this

```cpp
template<>
int foo<__lambda_9_27>(int x, int y, __lambda_9_27 lambda){
    return lambda.operator()(x, y);
}


int main(){
  struct __lambda_9_27{
      int operator()(int x, int y) const{
          return x * y;
      }
  }

  std::cout.operator<<(foo(2, 3, __lambda_9_27{})).operator<<(std::endl);
}
```

#### Example 2

```cpp
#include <iostream>
#include <vector>
using std::cout, std::vector,std::endl;

struct Multiply{
    double operator()( const double v1, const double v2 ) const{
        return v1 * v2;
    }
};

template<typename ITR, typename F>
int acc(ITR itr, ITR end,int init, F lambda) {
    int sum = init;
    for (; itr!=end; ++itr)
        sum=lambda(sum,*(itr));
    return sum;
}

int main(){
    vector vec1{10,20,30};
    cout << acc(vec1.begin(), vec1.end(), 0, [](int x, int y){return x+y;}) << std::endl;

    vector vec2{2,3,4};
    Multiply multiply;
    cout << acc(vec2.begin(), vec2.end(), 1, multiply) << std::endl;
    return 0;
}
```

```text
60
24
```

Really looks like this:

```cpp
template<>
int acc<__gnu_cxx::__normal_iterator<int *, std::vector<int, std::allocator<int> > >, __lambda_15_46> //ITR,F
(__gnu_cxx::__normal_iterator<int *, std::vector<int, std::allocator<int> > > itr, //arg1
 __gnu_cxx::__normal_iterator<int *, std::vector<int, std::allocator<int> > > end, //arg2
 int init,                                                                         //arg3
 __lambda_15_46 lambda){                                                           //arg4

     //start of function
      int sum = init;
      for(; __gnu_cxx::operator!=(itr, end); itr.operator++())
          sum = lambda.operator()(sum, (itr).operator*());
      return sum;
}
```

### `for_each`

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
using std::cout, std::vector,std::endl,std::for_each;

int main(){
    vector nums{10,20,30};
    for_each(nums.begin(), nums.end(), [](int &n){ n++; });
    for_each(nums.cbegin(), nums.cend(), [](const int& n) { cout << n << " "; });
}
```

```text
11 21 31
```

I usually will use a **range-based** for \(`for(auto& n : nums)`\) instead of `for_each`

## Containers

### Sequence containers

Sequence containers implement data structures which can be accessed sequentially.

| name | description |
| :--- | :--- |
| **array** | static contiguous array |
| **vector** | dynamic contiguous array |
| **deque** | double-ended queue |
| **forward\_list** | singly-linked list |
| **list** | doubly-linked list |

### Associative containers

Associative containers implement **sorted** data structures that can be quickly searched \(O\(log n\) complexity\).

| name | description |
| :--- | :--- |
| **set** | collection of unique keys, sorted by keys |
| **map** | collection of key-value pairs, sorted by keys, keys are unique |
| **multiset** | collection of keys, sorted by keys |
| **multimap** | collection of key-value pairs, sorted by keys |

### Unordered associative containers

Unordered associative containers implement **unsorted** \(hashed\) data structures that can be quickly searched \(O\(1\) amortized, O\(n\) worst-case complexity\).

| name | description |
| :--- | :--- |
| **unordered\_set** | collection of unique keys, hashed by keys |
| **unordered\_map** | collection of key-value pairs, hashed by keys, keys are unique |
| **unordered\_multiset** | collection of keys, hashed by keys |
| **unordered\_multimap** | collection of key-value pairs, hashed by keys |

### Container adaptors

Container adaptors provide a different interface for sequential containers.

| name | description |
| :--- | :--- |
| **stack** | adapts a container to provide stack \(LIFO data structure\) |
| **queue** | adapts a container to provide queue \(FIFO data structure\) |
| **priority\_queue** | adapts a container to provide priority queue |

### Span \(C++20\)

| name | description |
| :--- | :--- |
| **span** | a non-owning view over a contiguous sequence of objects |

[More info](https://en.cppreference.com/w/cpp/container)

## Sequence containers

### array

Creating array

```cpp
std::array<int, 3> a1{ {1, 2, 3} }; //double curly 
std::array<int, 3> a2 = {1, 2, 3};  // never required after =
std::array<std::string, 2> a3 = { std::string("a"), "b" };
```

Element access & Capacity

```cpp
a1[0]=1
a1.at(3) // throw std::out_of_range
a2.front()
a3.back()
a3.size()
```

container operations are supported

```cpp
std::sort(a1.begin(), a1.end());
std::reverse_copy(a2.begin(), a2.end(), 
                    std::ostream_iterator<int>(std::cout, " "));
```

```text
3 2 1
```

ranged for loop is supported

```cpp
for(const auto& s: a3)
        std::cout << s << ' ';
```

Most _modifiers_ are **not** supported such as _push\_back_

### vector

* The storage of the vector is handled _**automatically**_, being expanded and contracted as needed.
* The elements are stored _**contiguously**_.
* **Element access, capacity, container operations and ranged-loops are supported**

**Creation**

```cpp
std::vector<int> v = {1, 2, 3, 4, 5, 6};
```

**Modifiers**

```cpp
v.emplace_back(7);
v.pop_back();
v.erase(v.begin());
v.erase(v.begin()+1, v.begin()+3);

print(v);
```

### deque

* deque \(double-ended queue\) is an indexed sequence container that allows **fast insertion and deletion** at both its _**beginning and its end**_ **O\(1\)**
* **not** stored **contiguously**
* Expansion of a deque is cheaper than the expansion of a std::vector
  * cons: large minimal memory cost
* Random access -  O\(1\)
* **Element access, capacity, container operations and ranged-loops are supported**

```cpp
std::deque<int> d = {7, 5, 16, 8};
d.emplace_front(10);
```

A **deque** is somewhat recursively defined: Internally it maintains a double-ended queue of chunks of fixed size. **Each chunk is a vector**, and the queue \(“map” in the graphic below\) of chunks itself is **also a vector**.

![](https://i.stack.imgur.com/QullW.png)

[Source](https://stackoverflow.com/questions/6292332/what-really-is-a-deque-in-stl)

### list

* constant time insertion and removal of elements from anywhere in the container. 
* Fast random access is **not** supported
* Usually a doubley-linked list

```cpp
std::list<int> l = { 7, 5, 16, 8 };
```

Element access

* supports mostly everthing in **vector** except for `[]`, `at`

![doubly\_linked\_list.jpg](https://www.tutorialspoint.com/data_structures_algorithms/images/doubly_linked_list.jpg)

#### Examples

```cpp
#include <iostream>
#include <vector>
#include <list>
#include <numeric>      // std::accumulate
#include <algorithm>    //std::transform

using namespace std;
int main(){
    std::vector<int> v = {1, 5, 4, 3, 2};
    std::list<double > l = {2.2, 4.4, 6.6, 8.8, 11};
    cout << std::accumulate(v.cbegin(),v.cend(),0) << endl;
    cout << accumulate(v.cbegin(),v.cend(),1,[&v](int i,int j) { return i*j;}) << endl;

    //sort v
    sort(v.begin(),v.end());

    //sum 2 containers
    vector<double> v_sum;
    std::transform(v.begin(), v.end(), l.begin(),
            std::back_inserter(v_sum),std::plus<double>()); // std::multiplies<double>()

    for(const auto& s: v_sum)
        std::cout << s << ' ';
    cout<<endl;

    return 0;
}
```

```text
15
120
3.2 6.4 9.6 12.8 16
```

More examples: `std::reduce`,`std::transform_reduce`

**Filling in numbers**

```cpp
#include <iostream>
#include <vector>
#include <list>
#include <algorithm>    //std::transform

using namespace std;
int main(){
//    std::srand(std::time(nullptr));
    vector<int> v(5);

    std::generate(v.begin(), v.end(), std::rand);

    for(const auto& s: v)
        std::cout << s << ' ';
    cout<<endl;

    fill(v.begin(), v.end(),1); //fills only 1 num

    for(const auto& s: v)
        std::cout << s << ' ';
    cout<<endl;

    return 0;
}
```

```text
1804289383 846930886 1681692777 1714636915 1957747793
1 1 1 1 1
```

`std::iota`-fill container with range

**Conditional**

```cpp
#include <iostream>
#include <vector>
#include <algorithm>    //std::any_of

using namespace std;
int main(){
    vector<int> v {1,2,3,4,-1};
    // Checking if any element is negative
    any_of(v.cbegin(), v.cend(), [](int x){ return x<0; }) ? cout << "There exists a negative element\n" :cout << "All are positive elements\n";

    int N=2;
    cout << count_if(v.begin(), v.end(),[N](int a){ return (a >= N);}) <<endl;
    return 0;
}
```

```text
There exists a negative element
3
```

`all_of()`, `none_of()`,`count_if`,`find_if`

## Associative Containers

### set

**Sorted** data structures that can be quickly searched \(O\(log n\) complexity\).**No duplicates**

```cpp
set<int, less<int> > s ={1,3,3,4,-1};
```

```cpp
#include <iostream>
#include <set>

using namespace std;
int main() {
    set<int, less<int> > s ={1,3,3,4,-1}; //  std::greater<int>
    s.insert(-5);
    s.erase(1);

    for(const auto& v: s)
        std::cout << v << ' '; cout<<endl;
}
```

```text
-5 -1 3 4
```

**Important!** as with any sorted container we need to give the set a _**Compare**_ class such as `less<int>` or `greater<int>` by default `Compare = std::less<T>`

### multiset

Similar to set, with an exception that **multiple elements can have same values**.

```cpp
    multiset<int, greater<int> > s ={1,3,3};
```

```cpp
#include <iostream>
#include <set>

using namespace std;
int main() {
    multiset<int, greater<int> > s ={1,3,3,-1};
    for(const auto& v: s)
        std::cout << v << ' '; cout<<endl;
}
```

```text
3 3 1 -1
```

### map

* Each element has a key value and a mapped value. 
* No two mapped values can have same key values.
* keys are sorted

```cpp
map<int, int> m;
```

```cpp
#include <iostream>
#include <map>

using namespace std;
int main() {
    map<int, int> m;

    // insert elements in random order
    m.insert(pair<int, int>(3, 60));
    m.insert(pair<int, int>(1, 40));
    m.insert(pair<int, int>(2, 30));
    m.erase(2); // remove elems with key=2

    for(const auto& v: m)
        std::cout << v.first << ": " << v.second << endl;
}
```

```text
1: 40
3: 60
```

#### Example 2

```cpp
#include <iostream>
#include <map>
#include <string>
using std::map; using std::cout; using std::string;

int main(){
    map<string, string> address_book = { std::make_pair("one","0123"),std::make_pair("two","4567") };
    address_book.insert({ "three", "7657" });

    for (auto & address_entry : address_book){
        cout << address_entry.first << " | " << address_entry.second << " |" << std::endl;
    }
    return 0;
}
```

```text
one | 0123 |
three | 7657 |
two | 4567 |
```

This also works

```cpp
map<string, string> address_book = { {"one","0123"}, 
{"two","4567"} };
```

### multimap

Multimap is similar to map with an addition that multiple elements can have same keys.

## Unordered associative containers

### unordered\_set

```cpp
unordered_set <string> us ;
```

```cpp
#include <iostream>
#include <unordered_set>

using namespace std;
int main() {
    unordered_set <string> uset ;

    uset.insert("code") ;
    uset.insert("in") ;
    uset.insert("c++") ;
    uset.insert("is") ;
    uset.insert("fast") ;

    for(const auto& v: uset)
        std::cout << v << " "; cout<<endl;
    return 0;
}
```

```text
fast is code c++ in
```

### unordered\_map

```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

int main(){
    unordered_map<string, double> umap;

    umap["PI"] = 3.14;
    umap["root2"] = 1.414;
    umap.insert(make_pair("e", 2.718));

    for (auto x : umap)
        cout << x.first << ": " << x.second << endl;

    umap.find("e") == umap.end() ? cout << "e: not found\n" : cout << "e: found\n";
    return 0;
}
```

```text
e: 2.718
PI: 3.14
root2: 1.414
e: found
```

#### Span \(C++20\)

The following code helps us with one-off errors.

```cpp
#include <iostream>
#include <span>

using namespace std;

void printSpan(std::span<int> s){
    for (auto& i : s)
        cout << i < " "; cout <<endl;
    //std::find_if(s.begin(), s.end(),some_predicate);
}
int main() {
    int a[5]={0,1,2,3,4,5};
    printSpan(a);
    return 0;
}
```

Besides of ranged-based for loops, we also have `find_if` etc but without container overhead. Pretty cool 😎

#### Valarray

* [std:: valarray](https://www.geeksforgeeks.org/std-valarray-class-c/) and [here](https://en.cppreference.com/w/cpp/numeric/valarray)

**Difference between std::pair and std::tuple with only two members?**

* `std::pair` can only **have two values** - not zero, one, three or more
* It's a bit easier to get the contents of a pair  than a tuple. You have to use a function call in the tuple case: `get<0>(t)`, while the pair case is just a member field \(`p.first`,`p.second`\)

### Unpacking

```cpp
#include <iostream>
#include <tuple>
using namespace std;

auto f_pair() {
    return pair(5,"hello");
//    return tuple(5,"hello");
}

int main() {
    auto f = f_pair; // f is a pointer to function
    auto [i,j] = f(); 
    cout << i << " " << j << endl;

    auto [x,y] = f_pair();
    cout << x << " " << y << endl;
}
```

```text
5 hello
5 hello
```

`auto [x,y] = f_pair()` Creates this implicitly

```cpp
  std::pair<int, const char *> __f_pair13 = f_pair();
  std::tuple_element<0, std::pair<int, const char *> >::type& x = std::get<0UL>(__f_pair13);
  std::tuple_element<1, std::pair<int, const char *> >::type& y = std::get<1UL>(__f_pair13);
```

**Unpacking Structed Bindings**

Allows unpacking any data structure whose size is **known** during **compile time**

```cpp
#include <iostream>
using namespace std;

struct Packed {
    int x;
    string y;
    float z;
};

int main() {
    Packed p;
    p.x =1; p.y="nissan"; p.z=3.14;
    auto & [ x, y, z ] = p; // access by reference
    cout << x << " " << y << " " << z << endl;
}
```

```text
1 nissan 3.14
```

[Credits](https://dominikberner.ch/structured-bindings/)

#### `basic_string` and `string`

A `std::string` is an instantiation of the `std::basic_string` template with a type of **char**

```cpp
#include <iostream>
using namespace std;

ostream& operator<< (ostream& out, basic_string<int> s) {
    for (int i: s)
        out << i << " ";
    return out;
}

int main() {
    basic_string<int> si({1,2,3,4,5,6});
    si.replace(3,6, basic_string<int>{7,8,9});
    cout << si << endl;

    basic_string<char> si2({'n','i','s','s','a','n'});
    cout << si2 << endl;
}
```

```text
1 2 3 7 8 9 
nissan
```

without overloading the global `operator<<` we would have been able to print ints

