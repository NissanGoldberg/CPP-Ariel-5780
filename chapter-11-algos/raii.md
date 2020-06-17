# RAII

## RAII

**Resource Acquisition Is Initialization** or **RAII**, is a C++ programming technique which binds the life cycle of a resource that must be acquired before use to the lifetime of an object. Examples:

* allocated heap memory
* thread of execution
* open socket, open file
* locked mutex, disk space, database connection—anything that exists in limited supply 

[Resource Acquisition is Initialisation \(RAII\) Explained — Tom Dalling](https://www.tomdalling.com/blog/software-design/resource-acquisition-is-initialisation-raii-explained/)

### Non-RAII approach

```cpp
#include <iostream>
using std::cout, std::endl,std::string,;

struct Resource{
    Resource()  { cout << "Resource created\n"; }
    ~Resource() { cout << "Resource destroyed\n"; }
};

int main (){
    Resource *r1 = new Resource;
    //maybe forget to delete or throw error
    delete r1;
    return 0;
}
```

```text
Resource created
Resource destroyed
```

maybe we'll forget to delete or throw an error

we could use smart-pointers

```cpp
int main (){
    std::unique_ptr<Resource> r1(new Resource);
    return 0;
}
```

```text
Resource created
Resource destroyed
```

### RAII

```cpp
#include <iostream>
#include <fstream>
#include <sstream>
using std::cout, std::endl,std::string,std::cin,std::ifstream;

class OpenFile{
private:
    ifstream file;
    string filename;
public:
    OpenFile(const string fname):filename(fname){
        file.open(filename);
    }

    ~OpenFile(){
        cout << "deleting resource\n";
        file.close();
    }

    std::string readLine() {
        std::ostringstream oss;
        string line;
        while ( getline (file,line) )
            oss << line <<"\n";
        return oss.str();
    }
};

int main (){
    OpenFile file("example.txt");
    cout << file.readLine() << endl;
    return 0;
}
```

```text
This is a line.
This is another line.

deleting resource
```

There are **3 parts** to an RAII class:

* The resource is **relinquished in the destructor** \(e.g. closing a file\)
* Instances of the class are **stack** allocated
* **Optional:** The resource is aquired in the constructor \(e.g. opening a file\).

When it comes to opening and closing files, `std::fstream` already has an RAII type of design because it closes itself in its destructor.

