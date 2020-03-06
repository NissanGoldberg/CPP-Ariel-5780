# Exercises

## Exercise 1:

Lets Print All odd numbers from 1 until 100

{% tabs %}
{% tab title="Hide" %}
```
The solution is on the other tab
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
The solution is on the other tab
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

