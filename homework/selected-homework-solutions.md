# Some Homework Solutions

​ **בס"ד**

**Special thanks** to all the wonder students who participated in showing their code 😊

The following codes are a guide to the solution but are **not the official answers** to the exercises. Obviously, there are also more wonderful solutions that are not listed to the problems given.

## Exercise 1: [Phonetic-Search](https://github.com/cpp-exercises/phonetic-search-b)

[Netanel Albert](https://github.com/NetanelAlbert/cppEx1-phonetic-search/blob/master/PhoneticFinder.cpp)

* Code is very short and effective

## Exercise 2: [Ancestor-Tree](https://github.com/cpp-exercises/ancestor-tree-b)

[Shira Tzadok](https://github.com/shiraZadok/CPP_Ex2_ancestor-tree)

## Exercise 3: [Solver](https://github.com/cpp-exercises/solver-b)

### 1st option \(simpler\)

[Hila Shoshan](https://github.com/HilaShoshan/cpp_task3_EquationsSolver)

* This solution is more suited for the test.

### 2nd option:

[Dekel Gilboa](https://github.com/Revertigo/solver-b)

```cpp
class Variable{
    virtual void handle_imag(complex<double> &result, double a, double b, double discriminant) = 0;
}
class RealVariable : public Variable 
class ComplexVariable : public Variable

complex<double> solve(ComplexVariable c) {
        return c.solve_abc_formula();
}

double solve(RealVariable r) {
        return r.solve_abc_formula().real();
 }

void RealVariable::handle_imag(complex<double> &result, double a, double b, double discriminant) {
        throw exception(invalid_argument("There is no solution"));
}
```

* method hiding \(no polymorphism since there are no pointer and everything is on the stack\)
* inherited the ctor from the parent to prevent code duplication

## Exercise 4: [Wargame](https://github.com/cpp-exercises/wargame-b)

### 1st option: \(excellent implementation\)

[Netanel Albert](https://github.com/NetanelAlbert/cppEx4-wargame)

* Used BFS for foot soldier searching is better than checking min distance for everyone.
* use of Enums: `enum TYPE{Commander, Foot, Sniper, Paramedic};` to check types, `enum MoveDIR` to move
* ```cpp
  virtual void play(std::vector<std::vector<Soldier*>>&, std::pair<int,int>) = 0;
  ```

### 2nd option: \(very creative\)

[Dekel Gilboa](https://github.com/Revertigo/wargame-b)

* The `actuate_subordinates` function in `Soldier.ipp` uses **templates** which save the repetition of 3 times the code later on.
* in `Paramedic::heal_teammates` use of `helper_board` creates new board with additional cell on each boundary allows us not to check and throw out of bound exceptions and lengthy edge cases

![](https://i.ibb.co/GHP5H9S/inner-board.png)

## Exercise 5: [Itertools-Cfar](https://github.com/cpp-exercises/itertools-cfar-b)

### 1st option:

[Netanel Albert](https://github.com/NetanelAlbert/cppEx5_iterTools)

* usage of `value_type` is a very creative solution. 
* Works on functions, functors and lambdas
* Unfortunately, copies the container by value
* The`!=` could check for edge case when there are not the same object.

### 2nd option:

[Dekel Gilboa](https://github.com/Revertigo/itertools-cfar-b)

* copies the container by reference, more efficient
* use of `decltype` to infer the value type

