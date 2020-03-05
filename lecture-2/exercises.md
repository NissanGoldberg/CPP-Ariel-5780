# Exercises

## Exercise 1a

Given the following code will the code compile? If not fix it. How many bytes are allocated on the stack and what is the output?

```cpp
class MyClass{
  int x,y;
public:
    MyClass(){x=1; y=2; cout << " MyClass()"<< endl;}

    MyClass( int new_x ){x= new_x; y=3; cout << " MyClass(int)"<< endl;}

    MyClass( double new_x, double new_y ){
        x= new_x; y=new_y;
        cout << " MyClass(double, double)"<< endl;
    }

    string to_string(){ 
      return "["+std::to_string(x)+","+std::to_string(y)+"]";
    }
};

int main() {
    MyClass a[4]; 
    MyClass b[4] {11, 22};
    MyClass c {11,21};
    MyClass d();
    MyClass f[5] {{11, 22}, 33, {55, 22}}; 
}
```

## Answer

## **output**

```text
 MyClass()                  //MyClass a[0]
 MyClass()
 MyClass()
 MyClass()                  //MyClass a[4]
 MyClass(int)               //MyClass b[0]
 MyClass(int)
 MyClass()
 MyClass()                  //MyClass b[4]
 MyClass(double, double)    //MyClass c
 //no output for MyClass d();
 MyClass(double, double)    //MyClass f[0]
 MyClass(int)               
 MyClass(double, double)
 MyClass()
 MyClass()                  //MyClass f[4]
```

**Memory Allocation in bytes**

## TODO check the pointers in bytes

An object takes up memory to store it’s \(non-static\) data members [allocation explanation](http://www.cyberplusindia.com/blog/index.php/2014/04/24/memory-organisation-of-objects-in-c/) size\_of\_int on 32 bit system = 4 bytes bytes: 14\* 2 times size\_of\_int = 112 bytes

## Exercise 1b

What will happen if replace `MyClass d()` with `MyClass d{}` or `MyClass d`

## Answer

We will add `MyClass()` to the output before outputs of **MyClass f\[5\]**.

_**bytes:**_ 116 bytes

## Exercise 2

