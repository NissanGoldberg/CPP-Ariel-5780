# Technion Bash Questions

The questions and solutions can be found [here ](https://moodlearn.ariel.ac.il/mod/resource/view.php?id=1161055)or on  our moodle class website

[Bash Special Variables – MyLinuxPlace](https://www.mylinuxplace.com/bash-special-variables/)

[Bash Scripting Tutorial for Beginners - LinuxConfig.org](https://linuxconfig.org/bash-scripting-tutorial-for-beginners#h12-input-output-and-error-redirections)

## Winter 2014 Moed A - Exam 15-1-a

`exam-grades.txt`

```text
123 65
789 89
234 95
```

`hmwk-grades.txt`

```text
123 70
234 80
789 100
```

Lets sort them by ID

```text
$ cat exam-grades.txt hmwk-grades.txt | sort -n
123 65
123 70
234 80
234 95
789 100
789 89
```

* `-n` Option : To sort a file **numerically** \(here we want by ID\).
* Alphabetical order is the default \(no flags\)

### Answer

```bash
calc1:
#!/bin/bash
cat exam-grades hmwk-grade  | sort -n  | ./calc2
```

```bash
calc2
#!/bin/bash
while read line; do #read 1st line
   ls1=( $line ) #get line from exam-grades, put both values into array, ls-line split
        read line #read 2nd line
        ls2=( $line ) #get line from hmwk-grades
         (( sum=${ls1[1]}*3/4+${ls2[1]}*1/4+0 ))  # ls1[1] and ls2[1] are the grades
        echo ${ls1[0]} $sum
done
```

My Solution looks a bit different

```bash
while read id_t  g_t; do # read id into id_t, grade into g_t
    read id_h  g_h
    sum=$(( ${g_t}*3/4+${g_h}*1/4+0 ))
    echo ${id_t} $sum
done
```

## Winter 2015 Moed A - Exam 16-1-a

```bash
$ sed -n '2,$p' comb | ./calc.sh
37000 9250
```

`sed` will read from **line 2 until the end**

```bash
#! /bin/bash
(( sum=0 ))
(( count=0 ))
while read line; do
    line_split=($line)
    (( count+=1 ))
    (( sum+=${line_split[3]}+0 ))
done

(( avg=$sum/$count ))
echo "$sum" "$avg"
```

#### Part Bet

Before let review the following:

```bash
jobs=($@)
echo "$len"
for job in ${jobs[@]}; do
    echo $job
done
```

```text
$ ./avg.sh hello world shalom
3
hello
world
shalom
```

Now lets explain

* 1st arg- file name
* rest - employee types

[https://www.mylinuxplace.com/bash-special-variables/](https://www.mylinuxplace.com/bash-special-variables/)

```bash
jobs=($@) # makes an array of all the args
(( jobs_num=$#-1 )) # make len number of args-1,
#because we need to subtract the file name

for job in ${jobs[@]:1:$jobs_num}; do #look all array from 2nd arg (exclude filename) until last
    avg=`grep $job "$1" | ./calc.sh` # grep will only find the job
    # in $1 which is 1st arg which is file name 
    echo "Average salary of $job: $avg"
done
```

