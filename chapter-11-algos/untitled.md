# Algorithms

[Credits- 105 STL Algorithms in Less Than an Hour](https://www.youtube.com/watch?v=bFSnXNIsK4A)

#### Sort Example

![](https://i.ibb.co/zQfL29p/sort-highlighted.png)

The code examples will probably be given on the test.

**1st way - Lambda**

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
using std::vector, std::cout, std::endl;

int main() {
    std::vector<int> s = {5, 7, 4, 2, 8, 6, 1, 9, 0, 3};
    std::sort(s.begin(), s.end(), [](int a, int b) {return a > b;});

    for (const auto a : s)
        cout << a << " ";
}
```

```text
9 8 7 6 5 4 3 2 1 0
```

**2nd way - Functor \(Function object\)**

```cpp
struct my_functor {
    bool operator() (int i,int j) { return (i<j);}
};

int main(){
    vector<int> v {1,2,3,4,-1};
    sort(v.begin(), v.end(),my_functor{});

    for (vector<int>::iterator it=v.begin(); it!=v.end(); ++it)
        cout << ' ' << *it;
}
```

```text
-1 1 2 3 4
```

**3rd way - function**

```cpp
bool my_compare (int i,int j) { return (i<j); }

int main(){
    vector<int> v {1,2,3,4,-1};
    sort(v.begin(), v.end(),my_compare);
    for (auto it=v.begin(); it!=v.end(); ++it)
        cout <<  *it  << ' ';
}
```

```text
-1 1 2 3 4
```

#### Now will `sort` work on a `list` or a `deque`?

**Hint**: look at the iterator type

{% tabs %}
{% tab title="Hide" %}
```
The solution is on the other tab.
```
{% endtab %}

{% tab title="Solution" %}
```cpp
On a deque: yes, since it has random access.
On a list: no
```
{% endtab %}
{% endtabs %}



### Lets Start 🤩

## Land of Permutations

### Province of Heaps

* `make_heap`
* `push_heap`
* `pop_heap`

Max Heap ![Max heap](https://simpledevcode.files.wordpress.com/2015/07/smtku.png)

#### `std::make_heap`

[make\_heap - C++ Reference](http://www.cplusplus.com/reference/algorithm/make_heap/)

```cpp
using std::vector, std::cout, std::endl;
int main() {
    vector<int> s = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
    std::make_heap(s.begin(), s.end());

    for (const auto a : s)
        cout << a << " ";
}
```

```text
9 8 6 7 4 5 2 0 3 1
```

#### `std::push_heap`

[push\_heap - C++ Reference](http://www.cplusplus.com/reference/algorithm/push_heap/)

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
using std::vector, std::cout, std::endl;

int main() {
    vector<int> h = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
    std::make_heap(h.begin(), h.end());

    h.push_back(99);
    std::push_heap (h.begin(),h.end());

    for (const auto a : h)
        cout << a << " ";
}
```

```text
99 9 6 7 8 5 2 0 3 1 4
```

#### `std::pop_head`

[pop\_heap - C++ Reference](http://www.cplusplus.com/reference/algorithm/pop_heap/)

```cpp
using std::vector, std::cout, std::endl;

int main() {
    vector<int> v = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
    std::make_heap(v.begin(), v.end());
    std::cout << "max heap BEFORE pop : " << v.front() << '\n';

    std::pop_heap (v.begin(),v.end());
    v.pop_back();
    std::cout << "max heap AFTER pop : " << v.front() << '\n';

    for (const auto a : v)
        cout << a << " ";
}
```

```text
max heap BEFORE pop : 9
max heap AFTER pop : 8
8 7 6 3 4 5 2 0 1
```

**Heap Sort**

sorting using `pop_head`

```cpp
using std::vector, std::cout, std::endl;

int main() {
    vector<int> v = {0, 1, 2, 3, 4, 5};
    std::make_heap(v.begin(), v.end());

    for (int i = 0; i < v.size(); ++i)
        std::pop_heap (v.begin(),v.end()-i);

    for (const auto a : v)
        cout << a << " ";
}
```

```text
0 1 2 3 4 5
```

### Shore of Sorting

* `sort`
* `partial_sort`
* `nth_element`
* `sort_heap`
* `inplace_merge`

#### `std::sort`

[sort - C++ Reference](http://www.cplusplus.com/reference/algorithm/sort/)

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
using std::vector, std::cout, std::endl;

int main() {
    std::vector<int> s = {5, 7, 4, 2, 8, 6, 1, 9, 0, 3};
    std::sort(s.begin(), s.end(), [](int a, int b) {return a > b;});

    for (const auto a : s)
        cout << a << " ";
}
```

```text
9 8 7 6 5 4 3 2 1 0
```

#### `std::nth_element`

[nth\_element - C++ Reference](http://www.cplusplus.com/reference/algorithm/nth_element/)

![](https://i.ibb.co/RQjPT5T/nth-element.png)

```cpp
int main() {
    vector<int> v = {0, 1, 2, 3, 4, 5, 7, 6, 8, 9};
    std::nth_element (v.begin(), v.begin()+5, v.end());

    for (const auto a : v)
        cout << a << " ";
}
```

```text
1 0 4 3 2 5 7 6 8 9
```

#### `std::inplace_merge`

[inplace\_merge - C++ Reference](http://www.cplusplus.com/reference/algorithm/inplace_merge/)

Takes two sorted partitions and combines them to one sorted partition

![](https://i.ibb.co/h7DQQwp/inplace-merge.png)

```cpp
int main() {
    vector<int> v = {0, 2, 4, 6, 8, 1, 3, 5, 10};
    std::inplace_merge  (v.begin(), v.begin()+5, v.end());

    for (const auto a : v)
        cout << a << " ";
}
```

```text
0 1 2 3 4 5 6 8 10
```

### Partioning

* `std::partition`
* `std::partition_point`

[partition\_point - C++ Reference](http://www.cplusplus.com/reference/algorithm/partition_point/)

Take any element that fulfills the predicate \(the blues\) and divide into two groups \(blues and whites\)

![](https://i.ibb.co/X7PbKMK/partioning.png)

The first white is the _**partition point**_

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
using std::vector, std::cout, std::endl;

int main() {
    vector<int> v = {0, 2, 4, 6, 8, 1, 3, 5, 10};
    int N = 5;
    auto upper = std::partition(v.begin(), v.end(), [N](const auto& x){return x <= N;});

    cout  << "lower: ";
    for (auto it=v.begin(); it!=upper; ++it)
        cout  << *it  << " ";

    cout  << "\nupper: ";
    for (auto it=upper; it!=v.end(); ++it)
        cout  << *it  << " ";
}
```

```text
lower: 0 2 4 5 3 1 
upper: 8 6 10
```

### Other Permutations

* `std::rotate`
* `std::shuffle`
* `std::next_permutation`
* `std::prev_permutation`

[rotate - C++ Reference](http://www.cplusplus.com/reference/algorithm/rotate/)

[shuffle - C++ Reference](http://www.cplusplus.com/reference/algorithm/shuffle/)

[next\_permutation - C++ Reference](http://www.cplusplus.com/reference/algorithm/next_permutation/)

[prev\_permutation - C++ Reference](http://www.cplusplus.com/reference/algorithm/prev_permutation/)

## Secret Runes

Algos we can combine with other algos to create new algos

* stable
  * `stable_sort` -  `std::stable_sort` preserves the original order for equal elements, while `std::sort` doesn't.
  * `stable_partition` - while partitioning keep relative order
* is
  * `is_sorted`
  * `is_partitioned`
  * `is_heap`
* is\_until
  * `is_sort_until`
  * `is_partitioned_until`
  * `is_heap_until`

#### `std::stable_sort`

[stable\_sort - C++ Reference](http://www.cplusplus.com/reference/algorithm/stable_sort/)

```cpp
int main() {
    vector<double> v = {3.14, 1.41, 2.72, 4.67, 1.73, 1.32, 1.62, 2.58};
    std::stable_sort (v.begin(), v.end(), [](int i, int j){ return int(i)< int(j); });
    for (const auto i : v)
        cout  << i  << " ";
}
```

```text
1.41 1.73 1.32 1.62 2.72 2.58 3.14 4.67
```

Here we cast the doubles to ints: `int(i)< int(j)`

![](https://i.ibb.co/gDpmMtM/stable-sort.png)

#### `std::is_sorted`

[is\_sorted - C++ Reference](http://www.cplusplus.com/reference/algorithm/is_sorted/)

**Returns true** if the range is sorted into ascending order.

```cpp
int main() {
    std::array<int,4> arr {2,4,1,3};
    do {
        std::prev_permutation(arr.begin(),arr.end());
        cout << "arr:";
        for (int& x:arr) std::cout << ' ' << x; cout << '\n';
    } while (!std::is_sorted(arr.begin(),arr.end()));
}
```

```text
arr: 2 3 4 1
arr: 2 3 1 4
...
arr: 1 2 3 4
```

#### `std::is_sorted_until`

[is\_sorted\_until - C++ Reference](http://www.cplusplus.com/reference/algorithm/is_sorted_until/)

Find first unsorted element in range

```cpp
int main() {
    std::array<int,4> arr {2,4,1,3};
    auto it=std::is_sorted_until(arr.begin(),arr.end());
    cout << *it << endl;
}
```

```text
1
```

## Land of Queries

### Numerical Algos

* `count`
* `accumulate`
* `transform`
* `reduce` \(c++17, can run in parrallel\)
* `partial_sum`
* `inclusive_scan` \( can run in parrallel\)
* `exclusive_scan`
* `inner_product`
* `adjacent_difference`
* `sample`

#### `std::count`

[count - C++ Reference](http://www.cplusplus.com/reference/algorithm/count/)

```cpp
int main() {
    int arr[] {1, 2, 1, 2, 2};
    cout << "2 appears: " << std::count (arr, arr+5, 2) << " times";
}
```

```text
2 appears: 3 times
```

#### `std::accumulate`

[accumulate - C++ Reference](http://www.cplusplus.com/reference/numeric/accumulate/)

```cpp
#include <numeric>
using std::vector, std::cout, std::endl;

int main() {
    vector<int>v {1,2,3,4,5};
    cout << "sum is: " << std::accumulate (v.begin(), v.end(), 0) << endl;
    cout << "x+3*y:  " << std::accumulate (v.begin(), v.end(), 0,[](int x, int y) {return x+3*y;});
}
```

```text
sum is: 15
x+3*y:  45
```

$$sum=\sum_{i=1}^{5} i = 15$$ $$x+3*y=1*3 + 2*3 + 3*3 + 4*3 + 5*3=\sum_{x=1}^{5} 3*x=45$$

#### `std::partial_sum`

[partial\_sum - C++ Reference](http://www.cplusplus.com/reference/numeric/partial_sum/)

```cpp
int main() {
    vector<int>v {1,2,3,4,5}, result(5);
    std::partial_sum (v.begin(), v.end(), result.begin());

    for (const auto i : result)
        cout  << i  << " ";
}
```

```text
1 3 6 10 15
```

$$x[i]=\sum_{n=0}^{i} x[n]$$

#### `std::sample`

[std::sample - cppreference.com](https://en.cppreference.com/w/cpp/algorithm/sample)

Selects n elements from the sequence **without replacement** such that each possible sample has equal probability of appearance

```cpp
int main(){
    std::string in = "abcdefgh", out;
    std::sample(in.begin(), in.end(), std::back_inserter(out),5, std::mt19937{std::random_device{}()});
    std::cout << "five random letters out of " << in << ", result : " << out << '\n';
}
```

```text
five random letters out of abcdefgh, result : bcfgh
```

or

```cpp
int main() {
    vector<int>v {1,2,3,4,5,6,7,8,9,10}, result; //result doesn't need allocation, back_inserter will push_back automatically
    std::sample(v.begin(), v.end(), std::back_inserter(result),5, std::mt19937{std::random_device{}()});

    for (const auto i : result)
        cout  << i  << " ";
}
```

```text
1 5 6 7 9
```

### Querying a property - 1 range

* `all_of`
* `any_of`
* `none_of`

#### `std::any_of`

[any\_of - C++ Reference](http://www.cplusplus.com/reference/algorithm/any_of/)

```cpp
int main() {
    vector<int>v {1,2,3,4,5,6,7,-8,9,10};
    cout << (std::any_of(v.begin(), v.end(),[](int i){return i<0;}) ? "there's a negatives" : "no negative");
}
```

```text
there's a negatives
```

#### `std::all_of`

```cpp
int main() {
    vector<int>v {1,2,3,4,5,6,7,8,9,10};
    cout << (std::all_of(v.begin(), v.end(), [](int i){return i>=0;}) ? "all are positive" : "there's a negative");
}
```

```text
all are positive
```

### Querying a property - 2 ranges

* `equal`
* `lexicographical_compare`
* `mismatch`

### Searching a value

* **unsorted**
  * `find`
  * `adjacent_find`
* **sorted**
  * `equal_range`
  * `lower_bound`
  * `upper_bound`
  * `binary_search`

## Territory of Movers

* `move`
* `copy`
* `swap_ranges`
* `move_backward`
* `copy_backward`

#### `std::copy`

```cpp
int main() {
    int arr[] = {1,2,3,4,5,6,7,8,9,10};
    int result[5];
    std::copy(arr, arr + 5, result);

    for(const auto i : result)
        cout << i << " ";
}
```

```text
1 2 3 4 5
```

**Example 2**

```cpp
#include <vector>
#include <deque>
#include <iostream>
#include <iterator>
#include <algorithm>
int main(){
    std::vector<int> v{1,2,3,4,5};
    std::deque<int> d;
    std::copy(v.begin(), v.end(), std::front_inserter(d));
    for(int n : d)
        std::cout << n << ' ';
}
```

```text
5 4 3 2 1
```

* A `std::front_insert_iterator` which can be used to add elements to the **beginning** of the container that supports a `push_front` operation
* A `std::back_insert_iterator` which can be used to add elements to the **end** of the container that supports a `push_back` operation

#### `std::move`

```cpp
int main() {
    std::vector<std::string> old_v = {"air","water","fire","earth"};
    std::vector<std::string> new_v (4);
    std::move ( old_v.begin(), old_v.begin()+4, new_v.begin() ); //old_v.begin()+4==old.end()

    cout << "old_v, size: " << old_v.size() << endl;
    for(const auto i : old_v)
        cout << i << " "; cout << endl;

    cout << "new_v\n";
    for(const auto i : new_v)
        cout << i << " ";
}
```

```text
old_v, size: 4

new_v
air water fire earth
```

**Example 2**

```cpp
int main() {
    std::vector<std::string> old_v = {"air","water","fire","earth"};
    std::vector<std::string> new_v;
    new_v = std::move (old_v);

    cout << "old_v, size: " << old_v.size() << endl;
    for(const auto i : old_v)
        cout << i << " "; cout << endl;

    cout << "new_v\n";
    for(const auto i : new_v)
        cout << i << " ";
}
```

```text
old_v, size: 0

new_v
air water fire earth
```

Notice the size is 0.

For more about `std::move` look at R-value references and move constructors.

#### std::copy\_backward

![](https://i.ibb.co/ynp4jtF/copy-backward.png)

## Value Modifiers

* `fill`
* `generate`
* `iota`
* `replace`

![](https://i.ibb.co/N1Pn6k3/modifiers.png)

#### `std::generate`

```cpp
int main(){
    vector<int> v(5);
    std::generate(v.begin(), v.end(), std::rand);
    for (auto i1: v)
        std::cout << i1 << " ";
}
```

```text
1804289383 846930886 1681692777 1714636915 1957747793
```

#### `std::generate_n`

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <time.h>
#include <map>
using std::vector, std::string, std::cout;

class Point{
    int x;
    int y;
public:
    Point(int ix = 0 , int iy = 0) :x(ix), y(iy) {} //use only this construction
    int getX() const { return x; }
    int getY() const { return y; }
    friend std::ostream& operator<< (std::ostream& ostr, const Point& p) { ostr << p.x << "  " << p.y << "\n"; return ostr; }
};


Point&& genPoint(){
    srand(time(0));
    return std::move(Point(rand() % 100, rand() % 100));
}

int main() {
    vector<Point> v2(5);
    std::generate_n(v2.begin(), 5, genPoint);
    for (auto i1 : v2) {
        cout << i1 << " ";
    }
}
```

```text
39  42
 39  42
 39  42
 39  42
 39  42
```

## Structure Changers

* `remove`
* `unique`

#### `std::remove`

```cpp
int main() {
    vector<int> v = { 1, 1, 3, 3, 3, 10, 1, 3, 3, 7, 7, 8 }, i;
    auto it_end = std::remove(v.begin(), v.end(),3); //remove 3's

    for (auto it = v.begin(); it != it_end; ++it)
        cout << *it << " "; cout << endl;

    for (auto i : v)
        cout << i << " ";
}
```

```text
1 1 10 1 7 7 8 
1 1 10 1 7 7 8 3 3 7 7 8
```

#### `std::unique`

```cpp
int main() {
    vector<int> v = { 1, 1, 3, 3, 3, 10, 1, 3, 3, 7, 7, 8 }, i;
    auto it_end = std::unique(v.begin(), v.begin() + 12);// Now v becomes {1 3 10 1 3 7 8 * * * * *} , * means undefined

    for (auto it = v.begin(); it != it_end; ++it)
        cout << *it << " "; cout << endl;

    for (auto i : v)
        cout << i << " ";
}
```

```text
1 3 10 1 3 7 8 
1 3 10 1 3 7 8 3 3 7 7 8
```

Notice it **didn't get rid of all the duplicates** and return a set but rather got rid of each duplicate adjacent to another.

![std-unique.jpg](https://i.ibb.co/dQDF9tW/std-unique.jpg)

## `*_COPY`

* `remove_copy`
* `unique_copy`
* `reverse_copy`
* `rotate_copy`
* `replace_copy`
* `partition_copy`
* `partial_sort_copy`

#### `std::remove_copy`

**Example 1:**

```cpp
int main () {
    int myints[] = {10,20,30,30,20,10,10,20};                             // 10 20 30 30 20 10 10 20
    std::vector<int> new_vector (8);
    auto end_it = std::remove_copy (myints,myints+8,new_vector.begin(),20);// 10   30 30    10 10  0 0 0

    for (auto it=new_vector.begin(); it!=end_it; ++it)
        std::cout << ' ' << *it;
}
```

```text
10 30 30 10 10
```

**Example 2:**

```cpp
int main () {
    int myints[] = {10,20,30,30,20,10,10,20};                // 10 20 30 30 20 10 10 20
    std::vector<int> new_vector (8);
    std::remove_copy (myints,myints+8,new_vector.begin(),20);// 10    30 30    10 10  0 0 0

    for (const auto i : new_vector)
        cout << i << ' ';
}
```

```text
10 30 30 10 10 0 0 0
```

## `*_IF`

* `find_if`
* `find_if_not`
* `count_if`
* `remove_if`
* `remove_copy_if`
* `replace_if`
* `replace_copy_if`
* `copy_if`

#### `std::count_if`

```cpp
int main(){
    vector<int> v {1,2,3,4,-1};
    int N=2;
    cout << count_if(v.begin(), v.end(),[N](int a){ return (a >= N);}) <<endl;
}
```

```text
3
```

## transform

```cpp
int main(){
    std::vector<int> foo;
    std::vector<int> bar(6);
    for (int i=1; i<6; i++)
        foo.push_back (i*10);// foo: 10 20 30 40 50

    int N = 2;
    std::transform (foo.begin(), foo.end(), bar.begin(), [N](int i) { return i+=N; }); // bar: 12 22 32 42 52
    std::transform (foo.begin(), foo.end(), bar.begin(), foo.begin(), std::plus<int>());
    for (auto i : foo)
        cout << i << " ";
}
```

`foo.begin(), std::plus`: foo is the output and `std::plus` adds together its two arguments, we could have use a lambda instead

