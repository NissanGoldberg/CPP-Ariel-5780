# Linux and Bash commands

```bash
$ echo hello world!
```

## File System

> _On a UNIX system, everything is a file; if something is not a file, it is a process._

![](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f3/Standard-unix-filesystem-hierarchy.svg/800px-Standard-unix-filesystem-hierarchy.svg.png)

More info can be found [here](https://www.youtube.com/watch?v=HbgzrKJvDRw&t=)

## Basic Linux commands

`pwd` - gives us the absolute path, which means the path that starts from the root.

```bash
~/temp$ pwd
/home/nissan/temp
```

`ls` - tells us which files are in the directory you are in.

```bash
~$ ls
README.md  cpp  notebooks  temp
```

The `-a` flag gives us hidden files as well.

```bash
~$ ls -a
.bashrc  .profile README.md  cpp  notebooks  temp
```

`cd` - changes directory

```bash
~$ cd cpp
~/cpp$
```

Notice on line 2 `~/cpp` , the `~` stands for the home directory and we are now in the _cpp_ directory

`mkdir` & `rmdir` - _mkdir_ makes a new folder and _rmdir_ removes a folder

```bash
$ mkdir stam
~$ ls
README.md  cpp  notebooks stam temp
$ rmdir stam
~$ ls 
README.md  cpp  notebooks temp
```

`touch` and `rm` - `touch` creates a file. `rm` deletes a file or directory.

```bash
~$ touch newfile
~$ ls
README.md  cpp  newfile  notebooks  temp
~$ rm newfile
~$ ls
README.md  cpp  notebooks  temp
```

Add the -`r` flag to `rm` to remove only a directory

```bash
~/cpp$ ls
 erel  'new folder'

~$ rm -r 'new folder'
~/cpp$ ls
erel
```

## Intermediate Commands

`echo` and `cat` - `echo` moves data, usually into files. `cat` displays the data in files.

```bash
~$ echo hello, my name is nissan >> new.txt
~$ cat new.txt
hello, my name is nissan
```

**Note:** You do not need to separate the spaces by using the backward slash here, because we put in `>>` when we finish what we need to write.

## Redirection

### Redirect output

`>` is used to redirect output or _**output to**_

```bash
$ echo "hello" > file.txt
```

### Redirect input

`<` is used to redirect input or to _**input from**_

```bash
$ cat < file.txt
hello
```

### Appending

`>>` is used to _**append**_ output to the end of the file.

```bash
$ echo "world!" >> file.txt
hello
world!
```

### Multiline

`<<` file literal or input stream literal

```bash
~$ cat << EOF >> new.txt
> how are
> you?
> EOF

~$ cat new.txt
hello, my name is nissan
how are
you?
```

Here we chose **EOF** as the literal, similiarly we could has wrote **stop\_input** or something else

```bash
~$ cat << stop_input >> new.txt
> so bored
> stop_input
~$
```

### Pipelines

Pipelines, often called pipes, is a way to chain commands and connect output from one **command** to the input of the **next command**. A pipeline is represented by the pipe character: `|`

```bash
$ ls
README.md  cpp  notebooks  temp
$ ls | wc -w
4
```

The `ls` command list the files and folders in the current folder.

The `wc` command list does a _word count_ while the -`w` flag will list the amount of words.

Here we piped the _ls_ into the the _wc_ command.

Another handy example uses the `tee` command which stores to file and displays at the same time

```bash
$ echo hello, my name is nissan | tee new.txt
hello, my name is nissan
```

A third expample would to use `grep` which searchs for words and `ps` which shows processes.

```bash
ps | grep $$
```

## Process Substitution

Instead of comparing two files like this

```bash
sort file1.txt > sorted_file1.txt
sort file2.txt > sorted_file2.txt
diff sorted_file1.txt sorted_file2.txt
```

We can do the following

```bash
diff <(sort file1.txt) <(sort file2.txt)
```

More on sorting can be found [here](https://www.geeksforgeeks.org/sort-command-linuxunix-examples/)

## Bashing

### Simple script

Let us start by creating a script named _test.sh_ and adding some text

```bash
~/cpp$ touch test.sh
~/cpp$ echo 'echo hello world' >> test.sh
```

Now we have to change give the file execute permission using the `chmod` command the `+x` is the same as `a+x` or all and gives _**execution**_ to _user, group and others_ [read here for more options](https://www.poftut.com/chmod-x-command-linux-unix/)

```bash
~/cpp$ chmod +x test.sh
```

Alternativly we can be more specific and use `chmod 755 test.sh`

```bash
~/cpp$ chmod 755 test.sh
```

the 7 represents the _**owners**_ permission the second number is the _**group**_ and the last number is the _**others**_ permission

| 1st num | 2nd num | 3rd num |
| :--- | :--- | :--- |
| owner | user | other |

The meaning of the numbers:

| 1 | 2 | 4 |
| :--- | :--- | :--- |
| read | write | execute |

If we add up the table above we get the following combinations. So 7 is **r,w,x** and 5 is **r,x**

| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| no permission | execute | write | write and execute | read | read and execute | read and write | read, write, and execute |

![](https://lh3.googleusercontent.com/proxy/MSrYZf0zzaDaW1U3DwTaoTMeIhAHH_21IFhoakoDpp1d7QgNObbuHl5REVhSP6uTa-D08dazQnut6ALO-AnQ0Xae9DMW6LhPizoI0e--)

Lets now look if the permissions have changed by using `ls -lh` , we'll see the following

```bash
$ ls -lh
total 56K
...
-rwxrwxrwx 1 nissan nissan 1.2K Mar  9 11:41 test.sh
```

Finally lets execute the file using `./` , if the file were in the parent we would use `../` instead

```bash
~$ ./test.sh
hello world
```

### Variables

Lets define a variable in our _test.sh_ script

```bash
myName="Nissan"
```

> **Important!** do not put spaces on either side of `=` when defining variable

We can do arithmetic using the `$((...))` and access the value use the `$` sign

```bash
num1=4
num2=3
num3=$((num1+num2))

echo "4 + 3 = $num3"
```

```bash
~$ ./test.sh
4 + 3 = 7
```

#### More arithmetic

```bash
rand=5
let rand+=4
echo "$rand"
```

```bash
$ ./test.sh
9
```

If we were not to use `let` the rand would be treated as a string and the output would be 54. The `let` keyword which allows for variable creation with simple arithmetic evaluation. If you try to assign a string there like `let a="hello world"` _**you'll get a syntax error.**_

```bash
rand=5
rand+=4
echo "$rand"
```

```bash
$ ./test.sh
54
```

We can also do the use the `++var_name` inside `$((...))`

```bash
rand=5
echo "$(( ++rand ))"
```

```bash
$ ./test.sh
6
```

## Functions

To define a function use the following template `foo(){return}`

```bash
getDate(){
        date
        return
}

getDate
```

```bash
$ ./test.sh
Thu Mar  5 18:56:02 IST 2020
```

More complicated function with a return would be

```bash
get_sum(){
    local num4=$1
    local num5=$2
    local num6=$3
    local sum=$((((num4-num5))+num6))
echo $sum
}

num1=5
num2=3
num3=7
sum=$(get_sum num1 num2 num3)
echo "The result is $sum"
```

```bash
$ ./test.sh
The result is 9
```

`$1` gets the first argument _num1_. `$2` gets the 2nd arg _num2_ etc. To define a local variable in a function use the keyword `local` e.g. `local num4=$1`

### IO

use `read` function for input. Add `-p` for prompt or what follows will be prompted to user

```bash
read -p "What is your name? " name
echo "Hello $name"
```

```bash
$ ./test.sh
What is your name? nissan
Hello nissan
```

Multiple inputs look like this

```bash
read -p "Enter nums: " num1 num2 num3
echo "$num1 $num3"
```

```bash
$ ./test.sh
Enter nums: 1 2 3
1 3
```

### Conditions

`((...))` is referred to as arithmetic evaluation. The template is the following `if` `((...))` `;` `then`. To Finish the _if_ bracket use `fi` which is _if_ backwards. **Note:** `$[...]` is now deprecated

```bash
read -p "How old are you? " age

if  (($age >= 18)) ; then
        echo "You can buy cigarettes"
elif (($age == 17)) ; then
        echo "You can buy cigarettes next year"
else
        echo "Boy get out of here"
fi
```

```bash
$ ./test.sh
How old are you? 21
You can buy cigarettes
```

Another example:

```bash
read -p "Enter a number : " num

if  (( ((num % 2)) ==0)) ; then
        echo "It is even"
fi

if  (( ((num > 0)) && ((num < 11)))) ; then
        echo "$num is between 1 and 10"
fi
```

```bash
$ ./test.sh
Enter a number : 8
It is even
8 is between 1 and 10
```

### Conditions and commands

```bash
read -p "Enter a file name : " file

if [ -e "$file" ] ; then
        echo "File exists"
else
        echo "File doesn't exist"
fi
```

```bash
$ touch new_file.txt
$ ./test.sh
Enter a file name : new_file.txt
File exists
```

`-e filename` - Check for file existence

#### TODO find commands with \(\(...\)\) syntax

### Strings

```bash
str="hello"
echo "String len: ${#str}"
echo "Splice str: ${str:2:4}"
```

### While Loop

```bash
num=1
while (( $num < 4)); do
        echo $(( num++ ))
done
```

```bash
$ ./test.sh
1
2
3
```

Lets do something more complicated. First lets create a file.

```bash
$ echo 2010 .25 36 >> file.txt
```

Now lets edit our script

```bash
num=1
while read num1 num2 num3; do
        printf "Num1: ${num1}\n
Num2: ${num2}\nNum3: ${num3}\n"

done < file.txt
```

```bash
$ ./test.sh
Num1: 2010
Num2: .25
Num3: 36
```

`done < file.txt` _**pipes**_ the data from _file.txt_ into the while loop

### For Loop

Simple C style loop

```bash
for (( i=0; i<3; i=i+1)); do
        echo $i
done
```

For Range

```bash
for i in {A..C}; do
        echo $i
done
```

```bash
$ ./test.sh
0
1
2
A
B
C
```

**Exercise:** Lets Print All odd numbers from 1 until 100

```bash
for i in {1..100}; do
        if  (( ((i % 2)) ==1)) ; then
            echo $i
        fi
done
```

### Arrays

```bash
some_nums=(3.14 2.718)
some_nums[2]=1.618 
echo "Phi : ${some_nums[2]}"
```

`some_nums[3]=1.618` will append to the array. To append multiple values would can do the following: `some_nums+=(1 7)`

```bash
$ ./test.sh
Phi : 1.618
```

use @ and **\#** e.g. `#some_nums[@]` to get number of elements in array

```bash
some_nums=(3.14 2.718)
some_nums[2]=1.618 
echo "len : ${#some_nums[@]}"
```

**Explanation**: If subscript is `@` or`*`, the word expands to all members of name. By prefixing `#` to variable you will find length of an array \(i.e number of elements\).

```bash
distro=("redhat" "debian" "gentoo")
## get length of $distro array
len=${#distro[@]}
## Use bash for loop 
for (( i=0; i<$len; i++ )); do echo "${distro[$i]}" ; done
```

More info can be found [here](https://wiki-dev.bash-hackers.org/syntax/arith_expr)

### Regex

### Case

Credits to: [Basic Linux Commands for Beginners](https://maker.pro/linux/tutorial/basic-linux-commands-for-beginners)

[10 Chaining Operators](https://www.tecmint.com/chaining-operators-in-linux-with-practical-examples/)

[Piping commands](https://superuser.com/questions/480599/with-regards-to-piping-commands-what-are-the-greater-than-and-less-than)

