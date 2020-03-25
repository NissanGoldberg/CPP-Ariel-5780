# More Linux

`&` after the command is for running it in the background. `&&` lets you do something based on whether the previous command completed successfully `||` which is the logical or, and also `;` which is just a separator which doesn't care what happend to the command before.

```text
$ false || echo "Oops, fail"
Oops, fail

$ true || echo "Will not be printed"
$  

$ true && echo "Things went well"
Things went well

$ false && echo "Will not be printed"
$

$ false ; echo "This will always run"
This will always run
```

`\` allows us to use the next line

```text
$ uname \
> -r
4.4.0-18362-Microsoft
```

## `export`

The `export` command provides the ability to update the current shell session about the change you made to the exported variable

`export -p` view all variables

`export name[=value]`

```bash
$ export -p | grep "EDITOR"
$ export EDITOR=/usr/bin/vim

$ echo $EDITOR
declare -x EDITOR="/usr/bin/vim"
```

Another cool thing we can do is instead of running each time our compiled program like this `./a.out` we can do this \(_**it may be dangerous, dont try this at home**_\)

```bash
$ export PATH=$PATH:.
$ a.out
Hello world!
```

To remove an exported variable we use `unset`:

```bash
unset variable_name
```

## `alias`

**Aliases** are like custom shortcuts used to represent a command. They are only stored in the current shell instance and will not be saved later on.

#### Creating an alias

```bash
$ alias shortName="your custom command here"
```

**example 1:**

```bash
$ lh
lh: command not found
$ alias lh="ls -lh"
$ lh
total 0
drwxrwxrwx 1 nissan nissan 4.0K Mar 24 09:47 download_dir
```

**example 2:**

```bash
$ alias get7z="wget -O nissans_7z.exe https://www.7-zip.org/a/7z1900-x64.exe"
$ get7z && ls
nissans_7z.exe
```

**example 3:** start shortcutting everything. Imagine that instead of write `git clone` or `git push` we could do something like this

```bash
$ alias gc="git clone"
$ alias ga="git add *.cpp *.hpp makefile *.md"
$ alias gs="git status"
$ alias gcd="git checkout develop"
$ alias gcm="git checkout master"
$ alias gfast="git add *.cpp *.hpp makefile *.md && git commit -m \"fast commit\" && git push"

$ gfast
1 file changed, 2 deletions(-)
To https://github.com/NissanGoldberg/teaching_cpp.git
```

#### Listing aliases

```bash
$ alias
...
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'
```

#### Removing aliases

```bash
$ unalias alias_name
```

## Creating Permanent Aliases

To permanently save our aliases we need to edit the `~/.bashrc` file

> **Be very careful when editing this file**!

Open the file,edit and save the file. Then type to execute the changes

```text
source ~/.bashrc
```

`source` is the same as `.`

## `export`  vs  `alias`

One difference between the two is that _**aliases are only a shell feature**_. Environment variables are inherited by all subprocesses \(unless deliberately cleared\).

## `cp`

`cp` can copy files or folders. there are mainly **3** cases:

1. **Two file names** - copies one file to the next file

   ```bash
   $ ls
   a.txt

   $ cp Src_file Dest_file && ls
   a.txt b.txt
   ```

2. **One or more arguments** - copy file/s to directory

   ```bash
   $ ls
   a.txt  b.txt  new_dir

   $ cp a.txt b.txt new_dir && ls new_dir
   a.txt b.txt
   ```

   or

```bash
$ cp *.txt new_dir
```

1. **Two directory names** - copies all files of the source directory to the destination directory

   ```bash
   $ cp -R Src_directory Dest_directory
   ```

   the `-R` flag is to copy recursively

**Flags**

* **-a** : Preserve the specified attributes such as directory an file mode, ownership, timestamps, if possible additional attributes: context, links, xattr, all.
* **-v** : Verbose output.
* **-r** : Copy directories recursively.
* **-i** : Interactive, will ask if to overwrite

Instead of `cp` you can also use `rsync`

## `mv`

`mv` can move files or rename them. there are mainly **2** cases:

Here we rename file

```text
mv [file_name] [new_file_name]
```

Here we copy files to directory

```bash
mv [filename] [dest-dir]
```

**Flags**

* **-n** : prevent overwriting
* **-i** : Interactive, will ask if to overwrite
* **-f** : forcefully overwrite
* **-u** : move only when the file is newer

## `wget` command

The `wget` command allows us to download files

**Syntax:** `wget` url

```bash
$ wget https://github.com/cpp-exercises/phonetic-search-a/blob/master/Demo.cpp
$ ls
Demo.cpp
```

#### Multiple files

`wget` _**url1**_ _**url2**_

**flags**

* `-O` \(**uppercase**\) : the output name of download. `-O new_name`

  ```bash
  $ wget -O nissans_7z.exe https://www.7-zip.org/a/7z1900-x64.exe 
  $ ls
  nissans_7z.exe
  ```

* `-b` : download in background. `-b log_file_name`

## `curl` command

Very good at testing **REST** api and also downloads.

```bash
curl url_name
```

Will fetch the html page

**flags**

* `-o`,`--output` : the output name of download. `-O new_name`

  ```bash
  $ curl -o nissans_7z.exe https://www.7-zip.org/a/7z1900-x64.exe 
  $ ls
  nissans_7z.exe
  ```

download into specific folder

```bash
$ mkdir download_dir
$ curl -o download_dir/my_demo.cpp https://github.com/cpp-exercises/phonetic-search-a/blob/master/Demo.cpp
$ ls download_dir/  
my_demo.cpp
```

## `curl` vs `wget`

* `wget`'s major strong side compared to `curl` is its ability to download recursively.
* `curl` supports `FTP`, `FTPS`, `HTTP`, `HTTPS`, `SCP`, `SFTP`, `TFTP`, `TELNET`, `DICT`, `LDAP`, `LDAPS`, `FILE`, `POP3`, `IMAP`, `SMTP`, `RTMP` and `RTSP`.
* `wget` supports `HTTP`, `HTTPS` and `FTP`.
* `curl` builds and runs on more platforms than `wget`.
* `curl` offers **upload** and sending capabilities. `wget` only offers plain HTTP POST support.

  [Credits](https://unix.stackexchange.com/questions/47434/what-is-the-difference-between-curl-and-wget)

## Benchmarking

The **simple way** to benchmark is to use the `time` command

```bash
$ time get7z
real    0m7.472s
user    0m0.031s
sys     0m0.094s
```

* **real** \(wall clock time\) is the time from start to finish of the call. It is the time from the moment you hit the `Enter` key until the moment the `wget` command is completed.
* **user** - amount of CPU time spent in user mode.
* **system** or **sys** - amount of CPU time spent in kernel mode

```bash
$ time ./a.out
```

#### More reliable benchmarking

Lets run _a.out_ which will perfom a for loop 1 billion times

```bash
perf stat -r 10 -d <your app and arguments>
```

The `-r 10` will run your app 10 times and do statistics over it. `-d` outputs some more data, such as cache misses.

#### Extreme benchmarking

```bash
sudo chrt -f 99 perf stat -ddd <benchmark>
```

`sudo chrt -f 99` runs your benchmark in FIFO real-time class with priority 99, which makes your process the top priority process and avoids context switching

## Compression and Archiving

* **.tar**  : uncompressed archive file
* **.zip** :  \(usually\) compressed archive file
* **.gz** : file \(archive or not\) compressed using gzip

### gzip

**Compression:** `gzip` _filname_

```bash
$ gzip my_file.txt && ls
my_file.txt.gz
```

**Decompress:**

```bash
$ gunzip my_file.txt.gz && ls
my_file.txt
```

### tar

if we have 2 files: _**file1.txt**_, _**file2.txt**_ we can archive them like this

```bash
$ tar cvf my_new_archive.tar file1.txt file2.txt
```

or for all _**txt**_ files

```bash
$ tar cvf my_new_archive.tar *.txt
```

**Explanation:** of `cvf`

`c`- means **create**

`-v`, `--verbose` - display output in terminal

`f` - file options

#### To extract

```bash
$ tar xvf my_new_archive.tar && ls
file1.txt
file2.txt
```

`x` - extract

### Archiving and Compressing

```bash
$ tar cvzf files.tar.gz file*.txt
$ ls
file1.txt  file2.txt  file3.txt  files.tar.gz
```

**`-z`**, `--gzip`, `--gunzip` : This option tells **tar** to read or write archives through **gzip**. This option should be used, for example, when operating on files with the extension _**.tar.gz**_.

#### Decompressing

```bash
$ tar xvzf files.tar.gz
file1.txt
file2.txt
file3.txt
```

## TODO - git

