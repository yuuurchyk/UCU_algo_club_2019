# Lecture 5: Bitwise Operations, Merge Sort, Divide and Conquer

# Iterative vs. Recursive way of solving the problem

**Theorem:** <i>everything</i> that can be solved recursively can also be solved iteratively.

**Tip:** always prefer iterative approach over recursion, unless your code gets a lot messier.

# scanf, printf
Speed of reading the data is <i>very</i> important.

in general ```scanf``` work faster than ```cin```.

Especially helpful when reading <i>lots</i> of data.

## Signature:
- ```printf("format string", value1, value2, ... valueN)```
- ```scanf("format string", &value1, &value2, &valueN)``` - here you need <i>addresses</i> of the variables you want to read

```cpp
#include <cstdio>

using namespace std;

int main(){
    int a, b;

    scanf("%d %d", &a, &b);

    printf("%d %d\n", a, b);
    printf("First number: %d, second number: %d\n", a + 1, b + 2);
}
```

## Format string:
<table>
    <tr>
        <td>%d</td>
        <td>int</td>
    </tr>
    <tr>
        <td>%ll</td>
        <td>long long</td>
    </tr>
    <tr>
        <td>%ull</td>
        <td>unsigned long long</td>
    </tr>
    <tr>
        <td>%ud</td>
        <td>unsigned int</td>
    </tr>
    <tr>
        <td>%c</td>
        <td>char</td>
    </tr>
</table>

More about format string on [cplusplus.com](http://www.cplusplus.com/reference/cstdio/scanf/).

## Example
Read the following into 2D char array:
```
abcde
dguwq
ajskd
```

```cpp
const int N = 3, M = 5;

char arr[N][M];

char c;
for(int i = 0; i < N; ++i){
    for(int j = 0; j < M; ++j){
        scanf("%c", &c);
        arr[i][j] = c;
    }
    // HAZARD!!! READING THE LEFTOVER '\n'
    scanf("%c", &c);
}
```

## Might be useful when
Dealing with whitespaces (because ```cin``` skips them by default)

## Reading until the end of the file

What if you don't know, how many integers you need to read?

### cin approach
```cpp
int number;
while(cin >> number){
    // do_stuff
}
```

What happened?

As we have learnt, ```cin >> number``` returns ```cin```, which is then implicitly casted to ```bool```. When it's casted to ```bool``` it returns ```true``` when the reading operation went successfully and ```false``` otherwise.

### scanf approach

```cpp
int number;
while(scanf("%d", &number) == 1){
    // do_stuff
}
```

What happened?

It turns out that ```scanf``` returns number of arguments, that were successfully read.

Another example:

```cpp
int a;
char b;
while(scanf("%d %c", &a, &b) == 2){
    // do stuff
}
```

## Microsoft Visual Studio scanf, printf

You can have the following error when using scanf/printf while using visual studio ide or compiler:

```
'scanf': This function or variable may be unsafe. Consider using scanf_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details.
```

Here is the **fix**: add it to the first line of your program:

```cpp
#define _CRT_SECURE_NO_WARNINGS
```

Everything should work fine.

# Prefix sums

More on the lecture.

Additional info on [geeksforgeeks](https://www.geeksforgeeks.org/prefix-sum-array-implementation-applications-competitive-programming/).

# Merge Sort
See tutorial on [geeksforgeeks](https://www.geeksforgeeks.org/merge-sort/).

![merge sort visualization](https://www.geeksforgeeks.org/wp-content/uploads/Merge-Sort-Tutorial.png)

**Asymptotics:**
```
T(n) = 2 * T(n / 2) + O(n)
```
We can use [Master Theorem](https://en.wikipedia.org/wiki/Master_theorem_(analysis_of_algorithms)) to prove that:
```
T(n) = O(nlogn)
```

Merge sort is a classical illustration of **[divide and conquer](https://en.wikipedia.org/wiki/Divide-and-conquer_algorithm)** technique, which consist of the following steps:

1. Split your problem in smaller **subproblems**
2. Solve smaller problems **recursively**
3. **Combine** the results of smaller subproblems into the result of the main problem

# Conting Inversions
Example of divide and conquer technique.

See tutorial on [geeksforgeeks](https://www.geeksforgeeks.org/counting-inversions/).

## Code
```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;

int n;
vector<int> nums;
ll inv_count;

void merge_sort(vector<int> &v, int l, int r){
    if(l >= r)
        return;

    int mid = (l + r) / 2;

    // [l; mid] [mid + 1, r]

    merge_sort(v, l, mid);
    merge_sort(v, mid + 1, r);

    vector<int> tmp;

    int i = l;
    int j = mid + 1;

    while(i <= mid && j <= r){
        if(v[j] < v[i]){
            inv_count += (mid - i + 1);
            tmp.push_back(v[j]);
            ++j;
        }
        else{
            tmp.push_back(v[i]);
            ++i;
        }
    }
    while(i <= mid){
        tmp.push_back(v[i]);
        ++i;
    }
    while(j <= r){
        tmp.push_back(v[j]);
        ++j;
    }

    for(int i = 0; i < tmp.size(); ++i)
        v[l + i] = tmp[i];
}

int main(){
    // read nums vector
    n = nums.size();
    inv_count = 0;

    merge_sort(nums, 0, n - 1);

    cout << inv_count << endl;
}
```

# Bitwise Operations

## Individual Bit Manipulation:
1. Determine the value of ith bit (bit are numbered from 0 from the right):
```cpp
bool determine_bit_value(int number, int bit){
    return (number & (1 << bit)) > 0;
}
```
```cpp
bool determine_bit_value(int number, int bit){
    return ((number >> bit) & 1);
}
```
2. Set particular bit to 1
```cpp
void set_to_one(int &number, int bit){
    number |= (1 << bit);
}
```
3. Set particular bit to 0
```cpp
void set_to_zero(int &number, int bit){
    number &= (~(1 << bit));
}
```
4. Toggle particular bit
```cpp
void toggle(int &number, int bit){
    number ^= (1 << bit);
}
```

### Operations Priority
While manipulating with bits, **ALWAYS** put parentheses!!!
```cpp
int number1 = 2;
int bit1 = 1;

int number2 = 3;
int bit2 = 2;

if(number1 & (1 << bit1) && number2 & (1 << bit2)) //???

if(((number1 & (1 << bit1)) > 0) && ((number2 & (1 << bit2)) > 0)) //OK
```

## Number of bits problem
**Problem:** for all ```i``` in ```[1; n]``` (n < 10<sup>7</sup>) count number of ```1``` bits in ```i```.

Example: 73<sub>10</sub> = 1001001<sub>2</sub> ---> count(73) = 3

```cpp
const int N = 10000000;
int count[N];

int main(){
    count[0] = 0;
    for(int i = 1; i <= N; ++i){
        count[i] = count[i & (i - 1)] + 1;
    }
    // horray
}
```

P.S. can we apply this approach to bigger numbers?

**Problem**: can you suggest an effective set for integer numbers from ```1``` to ```10```?

## Bitset
Container for storing sequence of bits. Documentation can be found on [cppreference](https://en.cppreference.com/w/cpp/utility/bitset).

```cpp
#include <bitset>

using namespace std;

int main(){
    bitset<10> a; // 10 must be COMPILE TIME CONSTANT
    // bitset of 10 zero bits

    bitset<10> b;

    cout << a.test(0) << endl; // false
    b.set(0) // set 0th bit to true

    cout << b.count() << endl; // number of 1 bits in b

    cout << (a == b) << endl; // false
    // NOTE: sizes of a and b should be the same

    b.set(1);
    a.set(1);

    b.reset(0); // set 0th bit to false

    cout << (a != b) << endl; // false

    a.flip(1); // toggle 1th bit

    a ^= b;
    b |= a;
    b &= a;

    bitset<10> c = ((a >> 2) ^ b);

    c.set(); // set all bits to 1
}

```

## Note
You should remember that 2<sup>n</sup> > sum([2<sup>i</sup> for i in range(n)]), which will be useful while solving some problems.

**For example**: given an array with ```n``` integers (```n``` < 10<sup>6</sup>, a<sub>i</sub> < 10<sup>9</sup>), find a pair of numbers a<sub>i</sub>, a<sub>j</sub> with the largest possible (a<sub>i</sub> & a<sub>j</sub>)

# Problems
See problems on [vjudge](https://vjudge.net/contest/345933).
Password: ```47```
