# Linux and Bash commands

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
$echo "hello" > file.txt
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

Pipelines, often called pipes, is a way to chain commands and connect output from one command to the input of the next. A pipeline is represented by the pipe character: `|`

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

Credits to: [Basic Linux Commands for Beginners](https://maker.pro/linux/tutorial/basic-linux-commands-for-beginners)

[10 Chaining Operators](https://www.tecmint.com/chaining-operators-in-linux-with-practical-examples/)

[Piping commands](https://superuser.com/questions/480599/with-regards-to-piping-commands-what-are-the-greater-than-and-less-than)

