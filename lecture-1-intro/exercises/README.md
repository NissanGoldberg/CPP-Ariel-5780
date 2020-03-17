# Exercises

## Exercise 1:

Lets Print All odd numbers from 1 until 100

{% tabs %}
{% tab title="Hide" %}
```
The solution can be found on the other tab.
```
{% endtab %}

{% tab title="Bash" %}
```bash
for i in {1..100}; do
        if  (( ((i % 2)) ==1)) ; then
            echo $i
        fi
done
```
{% endtab %}
{% endtabs %}

## Exercise 2:

Print average

```text
input
4
1
2
9
8
output
5.000
```

The '4' in the first line indicates that there are four integers whose average is to be computed. The average = \(1 + 2 + 9 + 8\)/4 = 20/4 = 5.000 \(correct to three decimal places\)

**Solution 1**: Piping

{% tabs %}
{% tab title="Hide" %}
```
The solution can be found on the other tab.
```
{% endtab %}

{% tab title="Bash" %}
```bash
sum=0
read num_or_args
for (( i=0; i<num_or_args; i=i+1)); do
        read num
        ((sum+=num))
done
echo "print($sum/$num_of_args)" | python3
```
{% endtab %}
{% endtabs %}

**Solution 2**: if python isn't installed

```bash
sum=0
read num_or_args
for (( i=0; i<num_or_args; i=i+1)); do
        read num
        ((sum+=num))
done
echo "$sum $num_or_args" | awk '{printf "%.3f \n", $1/$2}'
```

## Exercise 3a: Bash and GREP

Write a program a program that will receive a filename as an input. The user will then be prompted to enter how many lines to show and the string to search for. After so the user will be prompted to enter if he would wish to output the search to a file. The output should be like the following:

```bash
$ ./test.sh file.txt
how many lines to show 5
Enter REGEX: as a
Would you like to output to file (y/N): y
file name to output : file4.txt
16:and decided airs it has assumed, may have been only a noble puerilism
18:and again understood WHAT has actually sufficed for the basis of such
45:spoke of them; indeed one might ask, as a physician: "How did such a
54:furthest goals. As a matter of fact, the European feels this tension as
164:a renunciation of life, a negation of life. TO RECOGNISE UNTRUTH AS A
succesfully wrote to file4.txt
```

**Solution**:

{% tabs %}
{% tab title="Hide" %}
```
The solution can be found on the other tab.
```
{% endtab %}

{% tab title="Bash" %}
```bash
# check if filename was enter
if [ $1 != "" ]; then
    read -p "how many lines to show " start
    read -p "Enter REGEX: " regex
    read -p "Would you like to output to file (y/N): " answer

    if [ $answer != "y" ] || [ $answer != "Y" ]; then
        read -p "file name to output : " output_file
        grep -i -n "$regex" $1 | head -n $start
        grep -i -n "$regex" $1 | head -n $start >> $output_file
        echo "succesfully wrote to $output_file"
    else
        echo "grep -i -n $regex $1 | head -n $start"
        grep -i -n "$regex" $1 | head -n $start
    fi
else
    echo "no filename"
fi
```
{% endtab %}
{% endtabs %}

## Exercise 3b: Extended GREP - Part II

Add the following functionality, if the passing argument is "many" the program will prompt how may files to accept. Afterwards the user will enter each file name and the results will be returned. **You must use a for loop**. Here is an example of our extended GREP

```bash
$ ./test.sh many
how files would you like to search for: 2
enter file name 0: file.txt
enter file name 1: file2.txt
file.txt
file2.txt
how many lines to show 3
Enter REGEX: as a
```

**Solution**:

{% tabs %}
{% tab title="" %}
```
The solution can be found on the other tab.
```
{% endtab %}

{% tab title="Bash" %}
```bash
# check if filename was enter
if [ $1 != "many" ]; then
    read -p "how many lines to show " start
    read -p "Enter REGEX: " regex
    read -p "Would you like to output to file (y/N): " answer

    if [ $answer != "y" ] || [ $answer != "Y" ]; then
        read -p "file name to output : " output_file
        grep -i -n "$regex" $1 | head -n $start
        grep -i -n "$regex" $1 | head -n $start >> $output_file
        echo "succesfully wrote to $output_file"
    else
        echo "grep -i -n $regex $1 | head -n $start"
        grep -i -n "$regex" $1 | head -n $start
    fi

# multiple file option
else
    read -p "how files would you like to search for: " num_of_files
  # empty array
    list_of_files=()
    for (( i=0; i<num_of_files; i=i+1)); do
        read -p "enter file name $i: " file_name
        list_of_files[i]=$file_name
    done

    for each in "${list_of_files[@]}"; do
      echo "$each"
    done

    read -p "how many lines to show " start
    read -p "Enter REGEX: " regex

    for each in "${list_of_files[@]}"; do
      echo "grep -i -n $regex $each | head -n $start"
      grep -i -n "$regex" $each | head -n $start >> temp.txt
    done

    cat temp.txt
    rm temp.txt

fi
```
{% endtab %}
{% endtabs %}

