# IOStream

## IOstream

![iostream.gif](https://www.cplusplus.com/img/iostream.gif)

#### What is `std::cout`?

`std::cout` and `std::cin` are **global objects** of classes std::ostream and std::istream respectively, which they've **overloaded** operator `<<` and `>>`

```text
  cout     <<      expr  ;
  -----   ---     -------
  object   op.   argument
```

is basically this

```cpp
cout.operator<<(expr);
```

The same is true for cin

## Output stream manipulators

```cpp
#include <iostream>
#include <iomanip>
#include <math.h>
using namespace std;

int main(){
    cout << setprecision(4)
         << 1234.5 << endl
         << 12345678. << endl;

    cout << "========\n";
    cout << M_PI << endl; //still setprecision(4)

    cout << setprecision(15)
         << M_PI << endl;

    return 0;
}
```

```text
1234
1.235e+07
========
3.142
3.14159265358979
```

```cpp
 cout << M_PI << endl; //still setprecision(4)
```

Here the precision is still 4

### boolalpha

```cpp
#include <iostream>   

int main () {
  bool b = true;
  std::cout << std::boolalpha << b << '\n';
  std::cout << std::noboolalpha << b << '\n';
  return 0;
}
```

```text
true
1
```

### How does this work?

The ostream object overloads the `<< operator` for each basic type.

```cpp
00001 // Standard stream manipulators -*- C++ -*-
00035 // iomanip
00049 namespace std
00050 {
00199   struct _Setprecision { int _M_n; };
...
...
00223 
00224   template<typename _CharT, typename _Traits>
00225     inline basic_ostream<_CharT,_Traits>& 
00226     operator<<(basic_ostream<_CharT,_Traits>& __os, _Setprecision __f)
00227     { 
00228       __os.precision(__f._M_n); 
00229       return __os; 
00230     }
```

The operator returns a reference as seen here `basic_ostream<_CharT,_Traits>&` which allows combined output

### Examples

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main () {
    ofstream myfile ("example.txt");

    if (myfile.is_open()){
        myfile << "This is a line.\n";
        myfile << "This is another line.\n";
        myfile.close();
    }
    else cout << "Unable to open file";
    return 0;
}
```

```text
==example.txt==
This is a line.
This is another line.
```

or

```cpp
ofstream myfile ("example.bin", ios::out | ios::app | ios::binary);
```

These are some flags

|  |  |
| :--- | :--- |
| ios::in | Open for input operations. |
| ios::out | Open for output operations. |
| ios::binary | Open in binary mode. |
| ios::ate | Set the initial position at the end of the file. |
| ios::app | All output operations are performed at the end of the file, appending the content to the current content of the file. |
| **ios::trunc** | If the file is opened for output operations and it already existed, its previous content is deleted and replaced by the new one. |

### Closing the file

When we are finished with our input and output operations on a file we shall close it so that the os is notified and its resources become available again. For that, we call the stream's member function `close`. This member function takes flushes the associated buffers and closes the file:

```cpp
myfile.close();
```

**Once this member function is called, the stream object can be re-used to open another file**, and the file is available again to be opened by other processes.

### Lengthy Example \(can skip\)

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main(){
    string filename = "file.txt";
    ofstream outputfile;
    // creating, opening and writing/appending data to file

    outputfile.open(filename, ios::out|ios::app);

    // simple error handling for file creating/opening for writing, test if fail to open the file, do…
    if(outputfile.fail()){
        cout<<"Creating and opening file "<<filename<<" for writing\n";
        cout<<"------------------------------------------\n";
        cout<<"The "<<filename<<" file could not be created/opened!\n";
        cout<<"Possible errors:\n";
        cout<<"1.  The file does not exist.\n";
        cout<<"2.  The path was not found.\n";
        exit(1);   // just exit, 0-normal, non zero - some error
    }else{ // else, if the file can be opened, do…
        cout<<"The "<<filename<<" file was created and opened successfully!\n";
        cout<<"\nDo some file writing....\n\n";
        outputfile<<"Writing some data in this file\n";
        outputfile<<"------------------------------\n";
        cout<<"Check the "<<filename<<" file contents :-)"<<endl;
        cout<<"If the file already have had data, the new data will be appended\n";

        int sampledata; // write some integers to the file...

        for(sampledata=0; sampledata<=10; sampledata++)
            outputfile<<sampledata<<"  ";
        outputfile<<endl;

        outputfile.close();  // close the output file

        // test if fail to close the file, do the following..., simple error handling for output files closing

        if(outputfile.fail()){
            cout<<"The "<<filename<<" file could not be closed!\n";
            exit(1);
        } else  // test if successful to close the file, do the following...
            cout<<"\nThe "<<filename<<" file was closed successfully!\n";
    }

    return 0;
}
```

**file.txt** before running

```text
some data
```

**file.txt** after running

```text
some data

Writing some data in this file
------------------------------
0  1  2  3  4  5  6  7  8  9  10
```

Here we use `ios::app` to append the data

### Reading

[ostream - C++ Reference](http://www.cplusplus.com/reference/ostream/ostream/)

```cpp
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

void read_file(ifstream& myfile,string fileName){
    myfile.open(fileName);
    string line;

    if (myfile.is_open()){
        while ( getline (myfile,line) ){
            cout << line << '\n';
        }
        myfile.close();
    }else
        cout << "Unable to open file";
}

int main () {
    ifstream myfile;

    read_file(myfile,"example.txt");
    read_file(myfile,"example2.txt");

    return 0;
}
```

```text
This is a line.
This is another line.
This is a line from example 2
This is another line from example 2.
```

We can get add file name to the the ctor like we did while reading

## Sum up numbers

```text
==nums.txt==
1
10
100
```

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main() {
    int sum = 0;
    int x;
    ifstream myFile("nums.txt");

    while (myFile >> x) {
        sum = sum + x;
    }

    myFile.close();
    cout << "Sum = " << sum << endl;
    return 0;
}
```

```text
Sum = 111
```

or this

```text
==nums2.txt==
1    5
10    50
100    500
```

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main() {
    int sum1 = 0;
    int sum2 = 0;
    int x1, x2;
    ifstream myFile("nums2.txt");

    while (myFile >> x1 >> x2) {
        sum1 = sum1 + x1;
        sum2 = sum2 + x2;
    }

    myFile.close();
    cout << "Sum1 = " << sum1 << endl;
    cout << "Sum2 = " << sum2 << endl;
    return 0;
}
```

```text
Sum1 = 111
Sum2 = 555
```

or even a csv file

```text
1,5
10,50
100,500
```

```cpp
int sum1 = 0;
int sum2 = 0;
char comma;
int x1, x2;
ifstream myFile("numcsv.csv");

while (myFile >> x1 >> comma >> x2) {
    sum1 = sum1 + x1;
    sum2 = sum2 + x2;
}
```

## My exercise - Building an HTML generator

The program will receive a text file using the following syntax:

**html.txt**

```text
h1 : Nissans Website
p : Whats up guys?
img : https://static.wixstatic.com/media/934aa2_5a1889466db146c8833c5d0f01eba2da~mv2.png/v1/fill/w_590,h_570,al_c,q_85,usm_0.66_1.00_0.01/cartoon%20arms%20crossed.webp
```

* h1 - is and header
* p - is a paragraph
* img - is an image address

The program will also prompt you for the Websites title

```cpp
#include <iostream>
#include <fstream>
using namespace std;

//for string switch - https://stackoverflow.com/questions/16388510/evaluate-a-string-with-a-switch-in-c/16388594
enum HTML_Options {
    TITLE,
    H1,
    PARAGRAPH,
    IMAGE,
    NOT_VALID
};

HTML_Options resolveOption(std::string input) {
    if( input == "title" ) return TITLE;
    else if( input == "p" ) return PARAGRAPH;
    else if( input == "img" ) return IMAGE;
    else if( input == "h1" ) return H1;
    return NOT_VALID;
}

int main() {
    string key, value;
    char colon; // colon is :
    ifstream inFile("html.txt");
    ofstream newHTMLfile ("website.html");
    string title;

    cout << "Enter Website title\n";
    getline(cin, title);

    //add while myfile.open()
    newHTMLfile << "<!DOCTYPE html>\n"
                << "<html lang=\"en\">\n"
                << "<head>\n"
                << "    <meta charset=\"UTF-8\">\n"
                << "    <title>"<< title <<"</title>\n"
                << "</head>\n"
                << "<body>";


    while (inFile >> key >> colon ) {
        getline(inFile, value);
        cout << value << "\n";

        switch( resolveOption(key) ){
            case PARAGRAPH: {
                newHTMLfile << "\n<p>" << value << "</p>\n";
                break;
            }case IMAGE: {
                newHTMLfile << "\n<img src=\"" << value << "\"/>\n";
                break;
            }case H1: {
                newHTMLfile << "\n<h1>" << value << "</h1>\n";
                break;
            }default: {
                //...
                break;
            }
        }
    }

    newHTMLfile << "\n</body>\n</html>";

    inFile.close();
    newHTMLfile.close();

    return 0;
}
```

Yeah it took me a while to think about this exercise 😂

### TODO add image

### Same exercise but now with redirection

Lets remeber some standard redirection first

`>` redirects standard output of a command to a file, overwriting previous content.

```text
$ command > file
```

`>>` redirects standard output of a command to a file, appending new content to old content.

```text
$ command >> file
```

`<` redirects standard input to a command.

```text
$ command < file
```

**html.txt**

```text
Nissans Website 2
h1 : Nissans Website
p : Whats up guys?
img : https://static.wixstatic.com/media/934aa2_5a1889466db146c8833c5d0f01eba2da~mv2.png/v1/fill/w_590,h_570,al_c,q_85,usm_0.66_1.00_0.01/cartoon%20arms%20crossed.webp
p : It me from line 2
```

Notice here the title \(the first line\) doesn't have a key

```text
$ ./a.out < html.txt
```

Here we will use the `<` redirection operator

```cpp
#include <iostream>
#include <fstream>
using namespace std;

//for string switch - https://stackoverflow.com/questions/16388510/evaluate-a-string-with-a-switch-in-c/16388594
enum HTML_Options {
    TITLE,
    H1,
    PARAGRAPH,
    IMAGE,
    NOT_VALID
};

HTML_Options resolveOption(std::string input) {
    if( input == "title" ) return TITLE;
    else if( input == "p" ) return PARAGRAPH;
    else if( input == "img" ) return IMAGE;
    else if( input == "h1" ) return H1;
    return NOT_VALID;
}

int main() {
    string key, value;
    char colon; // colon is :
    ofstream newHTMLfile ("website.html");
    string title;

    getline(cin, title);

    //add while myfile.open()
    newHTMLfile << "<!DOCTYPE html>\n"
                << "<html lang=\"en\">\n"
                << "<head>\n"
                << "    <meta charset=\"UTF-8\">\n"
                << "    <title>"<< title <<"</title>\n"
                << "</head>\n"
                << "<body>";


    while (cin >> key >> colon ) {
        getline(cin, value);
        cout << value << "\n";

        switch( resolveOption(key) ){
            case PARAGRAPH: {
                newHTMLfile << "\n<p>" << value << "</p>\n";
                break;
            }case IMAGE: {
                newHTMLfile << "\n<img src=\"" << value << "\"/>\n";
                break;
            }case H1: {
                newHTMLfile << "\n<h1>" << value << "</h1>\n";
                break;
            }default: {
                //...
                break;
            }
        }
    }

    newHTMLfile << "\n</body>\n</html>";

    newHTMLfile.close();

    return 0;
}
```

The main differences are the following rows:

```cpp
getline(cin, title);
...
while (cin >> key >> colon ) {
        getline(cin, value);
`
```

Now we are using `cin`

### Some more redirection

Lets change our interface to something like this

```text
$ ./a.out < html.txt > website.html
```

Our programs

* input is now **html.txt**
* and it outputs to **website.html**

```cpp
#include <iostream>
using namespace std;

//for string switch - https://stackoverflow.com/questions/16388510/evaluate-a-string-with-a-switch-in-c/16388594
enum HTML_Options {
    TITLE,
    H1,
    PARAGRAPH,
    IMAGE,
    NOT_VALID
};

HTML_Options resolveOption(std::string input) {
    if( input == "title" ) return TITLE;
    else if( input == "p" ) return PARAGRAPH;
    else if( input == "img" ) return IMAGE;
    else if( input == "h1" ) return H1;
    return NOT_VALID;
}

int main() {
    string key, value;
    char colon; // colon is :
    string title;

    getline(cin, title);

    cout << "<!DOCTYPE html>\n"
                << "<html lang=\"en\">\n"
                << "<head>\n"
                << "    <meta charset=\"UTF-8\">\n"
                << "    <title>"<< title <<"</title>\n"
                << "</head>\n"
                << "<body>";

    while (cin >> key >> colon ) {
        getline(cin, value);

        switch( resolveOption(key) ){
            case PARAGRAPH: {
                cout << "\n<p>" << value << "</p>\n";
                break;
            }case IMAGE: {
                cout << "\n<img src=\"" << value << "\"/>\n";
                break;
            }case H1: {
                cout << "\n<h1>" << value << "</h1>\n";
                break;
            }default: {
                //...
                break;
            }
        }
    }
    cout << "\n</body>\n</html>";
    return 0;
}
```

Now we have

```cpp
cout << "<!DOCTYPE html>\n"
 ...
cout << "\n<p>" << value << "</p>\n";
```

Some other things we could do is add `ios::app` to our output stream or some other flag

## Serializing Objects

Lets try to serialize a student object

```cpp
//@author https://thispointer.com/c-how-to-read-or-write-objects-in-file-serializing-deserializing-objects/
#include <iostream>
#include <fstream>
#include <string>
#include <vector>
#include <cassert>

class Student{
public:
    std::string mName;
    std::string mId;
    std::string mPhoneNumber;

    Student(std::string id = "", std::string name = "", std::string phone = "") : mId(id), mName(name), mPhoneNumber(phone)
    {}

    bool operator==(const Student & obj){
        return (mId == obj.mId) && (mName == obj.mName) && (mPhoneNumber == obj.mPhoneNumber);
    }

    //Write the member variables to stream objects
    friend std::ostream & operator << (std::ostream &out, const Student & obj){
        out << obj.mId << "\n" <<obj.mName<<"\n"<<obj.mPhoneNumber<<std::endl;
        return out;
    }

    // Read data from stream object and fill it in member variables
    friend std::istream & operator >> (std::istream &in,  Student &obj){
        in >> obj.mId;
        in >> obj.mName;
        in >> obj.mPhoneNumber;
        return in;
    }


};

int main(){
    Student stud1("1","Jack", "4445554455");
    Student stud2("4","Riti", "4445511111");
    Student stud3("6","Aadi", "4040404011");

    // Open the File
    std::ofstream out("students.txt");

    // Write objects to file
    out<<stud1;
    out<<stud2;
    out<<stud3;

    out.close();

    // Open the File
    std::ifstream in("students.txt");

    Student student1;
    Student student2;
    Student student3;

    // Read objects from file and fill in data
    in>>student1;
    in>>student2;
    in>>student3;

    in.close();

    // Compare the Objects
    assert(stud1==student1);
    assert(stud2==student2);
    assert(stud3==student3);

    return 0;
}
```

[Read here](https://stackoverflow.com/questions/49291594/c-munmap-chunk-invalid-pointer) if we would like to serialize this in binary format

## Binary files

[Input/output with files - C++ Tutorials](http://www.cplusplus.com/doc/tutorial/files/)

#### get and put stream positioning

All i/o streams objects keep internally -at least- one internal position:

* `ifstream`, like istream, keeps an internal **get** position with the location of the element to be read in the next input operation.
* `ofstream`, like ostream, keeps an internal **put** position with the location where the next element has to be written.

Finally, fstream, keeps both, the get and the put position, like iostream

#### `seekg()` and `seekp()`

These functions allow to change the location of the **get** and **put** positions

* `seekg ( position );`
* `seekp ( position );`

or

* `seekg ( offset, direction );`
* `seekp ( offset, direction );`

#### `tellg()` and `tellp()`

These two member functions with no parameters return a value of the member type `streampos`, which is a type representing the:

* current get position \(in the case of tellg\) 
* the put position \(in the case of tellp\).

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main () {
  streampos begin,end;
  ifstream myfile ("example.bin", ios::binary);
  begin = myfile.tellg();
  myfile.seekg (0, ios::end);
  end = myfile.tellg();
  myfile.close();
  cout << "size is: " << (end-begin) << " bytes.\n";
  return 0;
}
```

```text
size is: 40 bytes.
```

Another example:

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main () {
  streampos size;
  char * memblock;

  ifstream file ("example.bin", ios::in|ios::binary|ios::ate);
  if (file.is_open()){
    size = file.tellg();
    memblock = new char [size];
    file.seekg (0, ios::beg);
    file.read (memblock, size);
    file.close();

    cout << "the entire file content is in memory\n";

    delete[] memblock;
  }
  else cout << "Unable to open file";
  return 0;
}
```

### Lets say we would like to **Serializing & Deserializing Objects**

```cpp
//@author - Nissan
// based on https://www.youtube.com/watch?v=P7XGOBoVzW4
// read here https://stackoverflow.com/questions/49291594/c-munmap-chunk-invalid-pointer
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

class GameState{
    int hp;
    int level;
public:
    GameState(int h=100,int l=1):hp(h),level(l){}

    void info(){ cout <<"hp: " << hp << " level: " << level << endl; }

    friend ostream &operator<<(ostream &os, const GameState &gs) {
        os.write((char*) &gs.hp, sizeof(int));
        os.write((char*) &gs.level, sizeof(int));
        return os;
    }

    friend std::istream& operator>>(std::istream& is, GameState &gs) {
        is.seekg(0);//start from zero
        is.read((char *) &gs.hp, sizeof(int));
        is.read((char *) &gs.level, sizeof(int));
        return is;
    }

};


int main() {
    GameState p1(95, 25);
    GameState p2;

    fstream file("person.bin", ios::binary | ios::in | ios::out | ios::trunc);
    if(!file.is_open())
        cerr << "error opening";

    file << p1;
    file >> p2;

    p2.info();
    return 0;
}
```

```text
hp: 95 level: 25
```

### More advanced example

```text
├── Figure
│     ├── Triangle (has points)
│ 
├── Point
```

```cpp
//based on https://stackoverflow.com/questions/37038909/c-read-write-class-from-to-binary-file
#include <string>
#include <fstream>
#include <iostream>

class Point{
protected:
    int x;
    int y;
public:
    Point():x(0),y(0){}
    Point(int x,int y):x(x),y(y){}

    void write(std::ostream& f){
        // We can just write out the bytes for x and y because, they are primitive types stored in the class
        f.write( (char*)&x, sizeof(x) );
        f.write( (char*)&y, sizeof(y) );
    }
    void read(std::istream& f){
        // We can just read the bytes directly into x and y because, they are primitive types stored in the class
        f.read( (char*)&x, sizeof(x) );
        f.read( (char*)&y, sizeof(y) );
    }

    int getX() const { return x;}

    int getY() const {return y;}
};
```

```cpp
class Figure{
    std::string name;
    std::string type;
public:
    Figure(){}
    Figure(std::string name,std::string type):name(name),type(type){}

    void write(std::ostream& f){
        size_t size;
        size = name.size();
        f.write( (char*)&size, sizeof(size_t) );  // size of "name" string
        f.write( (char*)name.c_str(), size );    // "name" string

        size = type.size();
        f.write( (char*)&size, sizeof(size_t) ); // size of "type" string
        f.write( (char*)type.c_str(), size );    // "type" string
    }

    void read(std::istream& f){
        size_t size;
        char *data;

        f.read( (char*)&size, sizeof(size) );
        data = new char[size+1];
        f.read( data, size );
        data[size]='\0';
        name = data;
        delete data;

        f.read( (char*)&size, sizeof(size) );
        data = new char[size+1];
        f.read( data, size );
        data[size]='\0';
        type = data;
        delete data;
    }
};
```

`write()` **We need to store the data from the string along with the size** because to restore it we need to temporarily read it somewhere before storing it in the std::string \(istream::read\(\) doesn't read directly to std::string\)

`read()` When we read the string data we need somewhere to store it because we std::string isn't a primitive type. So we

* **read** the size
* **allocate** an array
* read the data into the array,
* load the std::string
* and **delete** the array

```cpp
class Triangle: public Figure{
private:
    Point p1, p2, p3;
public:
    Triangle(){}
    Triangle(Point x,Point y,Point z,Figure f):p1(x),p2(y),p3(z),Figure(f){}

    void write(std::ostream& f){
        // First write the base class then write the members of this class
        Figure::write(f);
        p1.write(f);
        p2.write(f);
        p3.write(f);
    }
    void read(std::istream& f){
        // First read the base class then read the members of this class
        Figure::read(f);
        p1.read(f);
        p2.read(f);
        p3.read(f);
    }

    friend std::ostream &operator<<(std::ostream &os, const Triangle &tri) {
        os << "p1-> x:" << tri.p1.getX() << ", y:" << tri.p1.getY()
           << "\np2-> x:" << tri.p2.getX() << ", y:" <<tri.p2.getY()
           << "\np3-> x:" << tri.p3.getX() << ", y:" <<tri.p3.getY();
        return os;
    }
};
```

Lets explain the `write()`:

* 1st we save/write the **figure** which contains the _**name**_ and _**type**_
* 2nd we save each and every point

```cpp
class BinaryFile{
    std::string FileName;
    std::fstream File;
public:
    BinaryFile(std::string FileName) : FileName(FileName) {};

    void WriteTriangle(){
        File.open(FileName, std::ios::binary | std::ios::out);
        if(!File)
        {
            std::cerr<<"File error <"<<FileName<<">\n";
            exit(1);
        }
        Triangle trig({1,2},{3,4},{5,6},{"name","type"}); // something new
        trig.write(File);
        File.close();
    }

    Triangle ReadTriangle(){
        File.open(FileName, std::ios::binary | std::ios::in);
        if(!File){
            std::cerr<<"File error <"<<FileName<<">\n";
            exit(1);
        }
        Triangle trig; // default values
        trig.read(File);
        File.close();
        return trig;
    }
};
```

```cpp
int main(){
    BinaryFile bin("file.bin");
    bin.WriteTriangle();
    Triangle trig = bin.ReadTriangle();  // at this point trig has the values we stored

    std::cout << trig;
    return 0;
}
```

```text
p1-> x:1, y:2
p2-> x:3, y:4
p3-> x:5, y:6
```

### SFML version

```cpp
#include <SFML/Graphics.hpp>
#include <time.h>
#include <list>
#include <cmath>
using namespace sf;

const int W = 1200;
const int H = 800;


//based on https://stackoverflow.com/questions/37038909/c-read-write-class-from-to-binary-file
#include <string>
#include <fstream>
#include <iostream>

class Point{
protected:
    int x;
    int y;
public:
    Point():x(0),y(0){}
    Point(int x,int y):x(x),y(y){}

    void write(std::ostream& f){
        // We can just write out the bytes for x and y because, they are primitive types stored in the class
        f.write( (char*)&x, sizeof(x) );
        f.write( (char*)&y, sizeof(y) );
    }
    void read(std::istream& f){
        // We can just read the bytes directly into x and y because, they are primitive types stored in the class
        f.read( (char*)&x, sizeof(x) );
        f.read( (char*)&y, sizeof(y) );
    }

    int getX() const { return x;}

    int getY() const {return y;}
};

class Figure{
    std::string name;
    std::string type;
public:
    Figure(){}
    Figure(std::string name,std::string type):name(name),type(type){}

    void write(std::ostream& f){
        size_t size;

        // we need to store the data from the string along with the size
        // because to restore it we need to temporarily read it somewhere
        // before storing it in the std::string (istream::read() doesn't
        // read directly to std::string)

        size = name.size();
        f.write( (char*)&size, sizeof(size_t) );
        f.write( (char*)name.c_str(), size );

        size = type.size();
        f.write( (char*)&size, sizeof(size_t) );
        f.write( (char*)type.c_str(), size );
    }

    void read(std::istream& f){
        size_t size;
        char *data;

        // when we read the string data we need somewhere to store it
        // because we std::string isn't a primitive type.  So we read
        // the size, allocate an array, read the data into the array,
        // load the std::string, and delete the array

        f.read( (char*)&size, sizeof(size) );
        data = new char[size+1];
        f.read( data, size );
        data[size]='\0';
        name = data;
        delete data;

        f.read( (char*)&size, sizeof(size) );
        data = new char[size+1];
        f.read( data, size );
        data[size]='\0';
        type = data;
        delete data;
    }
};

class Triangle: public Figure{
private:
    Point p1, p2, p3;
public:
    Triangle(){}
    Triangle(Point x,Point y,Point z,Figure f):p1(x),p2(y),p3(z),Figure(f){}

    void write(std::ostream& f){
        // First write the base class then write the members of this class
        Figure::write(f);
        p1.write(f);
        p2.write(f);
        p3.write(f);
    }
    void read(std::istream& f){
        // First read the base class then read the members of this class
        Figure::read(f);
        p1.read(f);
        p2.read(f);
        p3.read(f);
    }


    void draw(RenderWindow &app){
        sf::ConvexShape convex;

        // resize it to 5 points
        convex.setPointCount(5);

        // define the points
        convex.setPoint(0, sf::Vector2f(p1.getX(), p1.getY()));
        convex.setPoint(1, sf::Vector2f(p2.getX(), p2.getY()));
        convex.setPoint(2, sf::Vector2f(p3.getX(), p3.getY()));

        //===add color===
        convex.setFillColor(sf::Color(150, 50, 250));

        // set a 10-pixel wide orange outline
        convex.setOutlineThickness(10);
        convex.setOutlineColor(sf::Color(250, 150, 100));
        app.draw(convex);
    }


    friend std::ostream &operator<<(std::ostream &os, const Triangle &tri) {
        os << "p1-> x:" << tri.p1.getX() << ", y:" << tri.p1.getY()
           << "\np2-> x:" << tri.p2.getX() << ", y:" <<tri.p2.getY()
           << "\np3-> x:" << tri.p3.getX() << ", y:" <<tri.p3.getY();
        return os;
    }
};

class BinaryFile{
private:
    std::string FileName;
    std::fstream File;
public:
    BinaryFile(std::string FileName) : FileName(FileName) {};

    void WriteTriangle(Triangle& trig){
        File.open(FileName, std::ios::binary | std::ios::out);
        if(!File){
            std::cerr<<"File error <"<<FileName<<">\n";
            exit(1);
        }

        trig.write(File);
        File.close();
    }



    Triangle ReadTriangle(RenderWindow &app,Text& text){
        File.open(FileName, std::ios::binary | std::ios::in);

        //if no file prompt user
        if(!File){
            std::cerr<<"File error <"<<FileName<<">\n";
            text.setString("No file to open");
            app.clear(); //clear screen
            app.draw(text);
            return Triangle({0,0},{0,0},{0,0},{"name","type"});
        }
        Triangle trig; // default values
        trig.read(File);
        File.close();
        return trig;
    }
};

Triangle generateTriangle(){
    srand ( time(0) );
    int random_num = rand() % 10;
    random_num = 3;
    if(random_num >=5){
        return Triangle({100,200},{300,400},{100,400},{"name","type"});
    }else
        return Triangle({200,600},{500,600},{600,100},{"name","type"});

}

int main(){
    srand(time(0));

    sf::ContextSettings settings;
    settings.antialiasingLevel = 8; //take away jaggies

    sf::RenderWindow app(sf::VideoMode(W, H), "SFML shapes", sf::Style::Default, settings);
    app.setFramerateLimit(60);

    //Text on window
    sf::Text text;
    text.setFillColor(sf::Color::White);
    sf::Font font;
    font.loadFromFile("arial.ttf");
    text.setFont(font);


    Triangle trig;
    trig = generateTriangle();

    BinaryFile bin("file.bin");

    /////main loop/////
    while (app.isOpen()){
        Event event;
        while (app.pollEvent(event)){
            if (event.type == Event::Closed)
                app.close();

            if (Keyboard::isKeyPressed(Keyboard::S)){
                app.clear(); //clear screen
                text.setString("Pressed S - save file");
                text.setCharacterSize(24); // in pixels, not points!

                bin.WriteTriangle(trig);
            }
            if (Keyboard::isKeyPressed(Keyboard::O)){
                app.clear(); //clear screen

                text.setString("Pressed O - open file");
                text.setCharacterSize(24); // in pixels, not points!

                trig = bin.ReadTriangle(app,text); //added app,text to display error if no file
                // at this point trig has the values we stored
                std::cout << trig;
            }
            if (Keyboard::isKeyPressed(Keyboard::G)){
                app.clear(); //clear screen
                text.setString("Pressed G - generate triangle");
                trig = generateTriangle();
            }


        }


        //////draw//////
        trig.draw(app);
        app.draw(text);
        app.display();
    }
    return 0;
}
```

```text
all: main.cpp
    g++ -c main.cpp -std=c++17 && g++ main.o -o sfml-app -lsfml-graphics -lsfml-window -lsfml-system -std=c++17
```

![](https://i.ibb.co/Wf6Rhj4/triangle-app.jpg)

#### TODO fix genertaion of triangles

### Add Fighter and Ninja

[Saving high score tutorial](https://www.youtube.com/watch?v=c4YSBHZ2gS4)

[More tutorials on iostream](https://www.tenouk.com/Module19.html)

