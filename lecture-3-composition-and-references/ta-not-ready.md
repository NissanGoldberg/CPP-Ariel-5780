# TA - not ready

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

## Exercise 2 - Makefiles

create a makefile using variable for the following files: _main.cpp,sum.cpp, sum.hpp_

## TODO

## Exercise 3 - LinkedList

Create a linked list class with the following functions: _add\(int\), remove\(int\), get\(int\), contains\(int\)_. Properly divide the code into cpp and hpp files. How much memory does your code take?

## TODO

```cpp
#include <iostream>
using namespace std;

struct Node{
    int data;
    Node *next;
};

class List{
private:
    Node *head, *tail;
public:
    List(){
        head= nullptr;
        tail= nullptr;
    }

    void createnode(int value){
        Node *temp=new Node;
        temp->data=value;
        temp->next= nullptr;
        if(head==NULL){
            head=temp;
            tail=temp;
            temp=NULL;
        }else{
            tail->next=temp;
            tail=temp;
        }
    }

    void display(){
        Node *temp=new Node;
        temp=head;
        cout << "===== Displaying Nodes =====" << endl;
        while(temp!=NULL){
            cout<<temp->data<<"\t";
            temp=temp->next;
        }
        cout << endl;
    }


    void insert_start(int value){
        Node *temp=new Node;
        temp->data=value;
        temp->next=head;
        head=temp;
    }
};

int main() {
    List * list = new List();
    list->createnode(1);
    list->createnode(2);
    list->insert_start(3);

    list->display();
    return 0;
}
```

```text
===== Displaying Nodes =====
3    1    2
```

