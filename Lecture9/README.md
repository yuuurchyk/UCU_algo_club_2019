# Lecture 9: SQRT Decomposition, Mo's Algorithm, Segment Tree

# Warmup

## Problem
You are given an integer array ```arr``` of size ```n```. Every element of the array is given in advance.

There are ```m``` queries of the form ```(i, j), 0 <= i <= j < n```. Your task is to find the **sum** of ```arr``` elements with indexes from ```i``` to ```j```.

```n, m``` <= 10<sup>6</sup>

```arr[i]``` <= 10<sup>9</sup>

## Example
```
    n = 6
    arr = [0, -2, 5, 2, -1, 4]

    Query (0, 1) ---> -2
    Query (2, 3) ---> 7
    Query (3, 5) ---> 5
    Query (0, 0) ---> 0
```

## Hint
* use the fact, that every ```arr[i]``` never changes and use some preprocessing

## Solution
prefix sums

## Complexity
```O(n + m)```

# SQRT decomposition

Read tutorial on [e-maxx](https://e-maxx.ru/algo/sqrt_decomposition).

Read tutorial on [geeksforgeeks](https://www.geeksforgeeks.org/sqrt-square-root-decomposition-technique-set-1-introduction/).

## Problem
You are given an integer array ```arr``` of size ```n```. Every element of the array is given in advance.

There are ```m``` queries of 2 types:
1. ```(i, j)```     ---> get sum of ```arr[k], k in [i; j]```
2. ```(i, new_val)``` ---> set ```arr[i] = new_val```

You can find this problem online on [algotester](https://algotester.com/en/ArchiveProblem/Display/40148).

## Example
```
    n = 6
    arr = [0, -2, 5, 2, -1, 4]

    Query (0, 1) ---> -2
    Change (1, 2)
    Query (0, 1) ---> 2
    Query (2, 3) ---> 7
    Query (1, 3) ---> 9
    Query (3, 5) ---> 5
```

## Idea
split an array in multiple **blocks**. Calculate sum of elements of each block and keep them up-to date (i.e. update on each query if necessary).

![Blocks image](https://media.geeksforgeeks.org/wp-content/uploads/sqrt_root.png)

Let the size of block be ```k``` (we will talk about what ```k``` to choose later).

## So what?

Solving query **1.**

![Query 1](https://media.geeksforgeeks.org/wp-content/uploads/sqrt_root2.png)

Solving query **2.** is straightforward: update block value and array value.

## Complexity
1. Query of type **1.**: ```O(2 * k + (n / k))```
2. Query of type **2.**: ```O(1)```

## What k to choose?
It is not hard to prove that choosing ```k = sqrt(n)``` will minimize complexity of solving query **1.**, which will become ```O(sqrt(n))```

Overall complexity: ```O(m * sqrt(n))```

## Note:
You don't need to calculate square root of ```n``` explicitly. Square root of **maximum** ```n``` will be just enough.

It means, that if ```n``` <= 10<sup>6</sup>, then you can have blocks of fixed size ```sqrt(1 000 000) = 1000```.

# SQRT decomposition: add on subarray

## Task
You are given an integer array ```arr``` of size ```n```. Every element of the array is given in advance.

2 types of queries:
1. ```(i, j)```     ---> get sum of ```arr[k], k in [i; j]```
2. ```(i, j, value)```     ---> for every ```k in [i; j]``` set ```arr[k] += value```

## Solution
For each block hold an integer ```a``` - how much should we add on this block (more on the lecture).

# Online vs Offline task
In a nutshell:

## Offline Tasks
All the queries are given in advance in input

It means, that you can reorder the queries as you like (to reduce time complexity of your algorithm).

Be aware, that you need to answer the queries in the **ORIGINAL** order.

## Online Tasks
All the queries are **NOT** given in advance: in order to solve query ```i``` you should have an answer to query ```i - 1```.

# MO's algorithm
Suitable only for **OFFLINE** tasks.

Read tutorial [here](https://blog.anudeep2011.com/mos-algorithm/).

## Problem
[B, Little Elephant and Array](https://codeforces.com/contest/220/problem/B).

Please, read the statement before moving to the next section.

## Approach
If we know the answer for ```[l; r]``` - can we quickly calculate the answer for ```[l; r + 1]```?

It turns out we can:
```cpp
const int kMaxN = 100000;
int n;
int arr[kMaxN];

int ans, l, r; // all of these will be filled in the process
unordered_map<int, int> cnt;

void incr(){
    ++r;
    
    ++cnt[arr[r]];

    if(cnt[arr[r]] == arr[r])
        ++ans;
    if(cnt[arr[r]] == arr[r] + 1)
        --ans;
}
```

So, we can get from ```[l; r]``` to ```[l; r + 1]``` in **constant** time.

We can write similar functions to transition to the other queries:
* ```[l; r]``` ---> ```[l; r + 1]```
* ```[l; r]``` ---> ```[l + 1; r]```
* ```[l; r]``` ---> ```[l - 1; r]```
* ```[l; r]``` ---> ```[l; r - 1]```

## Silly Approach

Solve each query by going multiple times from the previous one:

Example:
```
    Query 1: (3, 5)
    Query 2: (1, 4)

    Transitions will look like follows:
    (3, 5) --> (2, 5) --> (1, 5) --> (1, 4)
```

It's runtime is quite bad (you need ```O(n)``` to get to the other query, which will lead to ```O(n * m)``` solution).

## MO's algorithm
What if we reorder the queries and use the same approach?

Quote from [Anudeep's blog](https://blog.anudeep2011.com/mos-algorithm/#disqus_thread):
```
How does the final order look like?

All the queries are first ordered in ascending order of their block number
(block number is the block in which its opening falls).
Ties are ordered in ascending order of their R value.

For example consider following queries and assume we have 3 blocks each of size 3.

{0, 3} {1, 7} {2, 8} {7, 8} {4, 8} {4, 4} {1, 2}

Let us re-order them based on their block number.
{0, 3} {1, 7} {2, 8} {1, 2} {4, 8} {4, 4} {7, 8}

Now let us re-order ties based on their R value.
{1, 2} {0, 3} {1, 7} {2, 8} {4, 4} {4, 8} {7, 8}
```

As in SQRT decomposition, block has size ```sqrt(n)```

## Complexity?
The final complexity consist of 2 parts:
1. queries, left boundaries of which lies **the same block**
2. queries, left boundaries of which are in neighbouring blocks (there will be ```sqrt(n)``` such pairs)

### Complexity analysis - inside on 1 block
* as the right bound is sorted, it will advance maximum ```n``` times.
* as the left boundaries lie in the same block, each time it will move max on ```sqrt(n)```

### Complexity analysis - neighbouring blocks
* right bound will advance max ```n``` times
* left bound advances max on ```2 * sqrt(n)```, as it lies in the neighbouring block

Final complexity: ```O(n * sqrt(n) + m * sqrt(n))```

## Solution of the problem - full code piece:

```cpp
#include <bits/stdc++.h>

using namespace std;

const int kMaxN = 100000;
const int kBlockSize = 320;

int arr[kMaxN];

int n, m;

int blocknum(int i){
    return i / kBlockSize;
}

struct Query{
    int id = 0;
    int l = 0;
    int r = 0;
    int res = 0;
};
vector<Query> queries;

bool mo_order(const Query &lhs, const Query &rhs){
    int lb = blocknum(lhs.l);
    int rb = blocknum(rhs.l);

    if(lb < rb)
        return true;
    if(lb > rb)
        return false;

    return lhs.r < rhs.r;
}
bool cmp_id(const Query &lhs, const Query &rhs){
    return lhs.id < rhs.id;
}

int res;
unordered_map<int, int> counter;
int l, r;

void incl(){
    ++l;
    --counter[arr[l - 1]];

    if(counter[arr[l - 1]] == arr[l - 1])
        ++res;
    if(counter[arr[l - 1]] == arr[l - 1] - 1)
        --res;
}
void decl(){
    --l;
    ++counter[arr[l]];

    if(counter[arr[l]] == arr[l])
        ++res;
    if(counter[arr[l]] == arr[l] + 1)
        --res;
}
void incr(){
    ++r;
    ++counter[arr[r]];

    if(counter[arr[r]] == arr[r])
        ++res;
    if(counter[arr[r]] == arr[r] + 1)
        --res;
}
void decr(){
    --r;
    --counter[arr[r + 1]];

    if(counter[arr[r + 1]] == arr[r + 1])
        ++res;
    if(counter[arr[r + 1]] == arr[r + 1] - 1)
        --res;
}

void solve(){
    l = queries[0].l;
    r = queries[0].r;

    for(int i = l; i <= r; ++i)
        ++counter[arr[i]];
    for(const auto &it: counter){
        int number = it.first;
        int occurances = it.second;
        if(number == occurances)
            ++res;
    }
    queries[0].res = res;

    for(int i = 1; i < queries.size(); ++i){
        while(r < queries[i].r)
            incr();
        while(l > queries[i].l)
            decl();
        while(l < queries[i].l)
            incl();
        while(r > queries[i].r)
            decr();
        queries[i].res = res;
    }
}

int main(){
    cin >> n >> m;
    for(int i = 0; i < n; ++i)
        cin >> arr[i];
    for(int i = 0; i < m; ++i){
        int l, r; cin >> l >> r;
        --l;
        --r;

        Query q;
        q.l = l;
        q.r = r;
        q.id = i;
        queries.push_back(q);
    }

    sort(queries.begin(), queries.end(), &mo_order);
    solve();
    sort(queries.begin(), queries.end(), &cmpid);
    for(const auto &it: queries)
        cout << it.res << endl;
    return 0;
}
```

# Segment Tree

Read tutorial on [e-maxx](http://e-maxx.ru/algo/segment_tree).

## Problem
You are given an integer array ```arr``` with ```n``` elements.

You should handle ```m``` queries of 2 types:
1. ```(i, j)``` ---> ```min(arr[i], arr[i + 1], ... arr[j - 1], arr[j])``` (minimum of subarray)
2. ```(i, new_val)``` ---> set ```arr[i] = new_val```

It's **online** task.

Example:
```
    arr = [1, 4, 2, -1, 8, 9, -6, 2]

    Query(0, 2) ---> 1
    Query(0, 7) ---> -6
    Set(0, -100)
    Query(0, 3) ---> -100
    Query(2, 4) ---> -1
```

## Idea
Build the following tree:

![Example of segment tree](./Materials/SegmentTree/Structure.jpg)

Yellow nodes represent our array. Value in other nodes are ```minimum``` among left and right sons. Hence, each node is responsible for minimum on some subarray.

There are ```~2n``` nodes in this tree.

**Build complexity:** ```O(n)```

## Solving query 1

![Solving the particular query](./Materials/SegmentTree/SolvingQuery.jpg)

Start from the root. If it exactly cover subarray we are looking for, return value of the root.

Else, go to left and right sons, and repeat.

Here, nodes that are splitted are covered in green, while blue ones are terminal (subarray they are responsible for is in query subarray).

## Solving query 2
Straightforward: go to the particular element and update each node on the way.

## Complexity
On each level of the tree there will be max **4** nodes affected (more on emaxx).

Hence, the complexity of each query will be ```O(logn)```

## How to code it?
Use the following node numbering:

![Nodes numbering](./Materials/SegmentTree/NodesNumbering.jpg)

### Observation
For every node ```i``` its left son has value ```2 * i + 1``` and right son has value ```2 * i + 2```.

### Conclusion
You just need an array instead of coding a tree :)

### What size of array should I have?
for ```n``` nodes you should have ```4 * n``` elements array.

Why? Consider the following corner case:

![Corner case](./Materials/SegmentTree/MaxNodesNumber.jpg)

### Final Code Piece

Solution to the [minimum](http://algotester.com/en/ArchiveProblem/Display/40148) problem on algotester:

```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;

const int kMaxN = 111111;

int n, m;
int arr[kMaxN + 10];
int tree[kMaxN * 4 + 10];

void maintain(int v){
    tree[v] = tree[2 * v + 1] + tree[2 * v + 2];
    // HERE YOU CAN WRITE YOUR SEGMENT TREE LOGIC
    // it can be either min, max, sum or other stuff
}

void build(int v, int tl, int tr){
    if(tl == tr){
        tree[v] = arr[tl];
        return;
    }

    int tm = (tl + tr) / 2;
    build(2 * v + 1, tl, tm);
    build(2 * v + 2, tm + 1, tr);

    maintain(v);
}

void update(int v, int tl, int tr, int i, int val){
    if(tl == tr){
        tree[v] = val;
        return;
    }

    int tm = (tl + tr) / 2;
    if(i <= tm)
        update(2 * v + 1, tl, tm, i, val);
    else
        update(2 * v + 2, tm + 1, tr, i, val);

    maintain(v);
}

int get_sum(int v, int tl, int tr, int l, int r){
    if(l > r)
        return 0;
    if(tl == l && tr == r)
        return tree[v];

    int tm = (tl + tr) / 2;

    return (
        get_sum(2 * v + 1, tl, tm, l, min(r, tm)) +
        get_sum(2 * v + 2, tm + 1, tr, max(tm + 1, l), r)
    );
}

int main(){
    cin >> n >> m;
    for(int i = 0; i < n; ++i)
        cin >> arr[i];
    build(0, 0, n - 1);
    for(int i = 0; i < m; ++i){
        string query_type; cin >> query_type;

        if(query_type == "query"){
            int l, r; cin >> l >> r;
            --l; --r;
            cout << get_sum(0, 0, n - 1, l, r) << endl;
        }
        else{
            int i, val;
            cin >> i >> val;
            --i;
            update(0, 0, n - 1, i, val);
        }
    }

    return 0;
}
```

# Implicit Segment Tree

## Problem
[Monkey and Apple-trees](https://www.e-olymp.com/en/problems/7488).

Please, read the problem statement before moving on to the next section.

## Idea
Build the tree **on the fly**: start with only the root node and split the node only when it's needed (more on the lecture).

# Other Problems on Segment Tree
[E, Lucky Queries](http://codeforces.com/contest/145/problem/E?locale=ru)