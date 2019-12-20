# Lecture 3: C++ introduction (cont.), Problem Session

# While loop
Pretty straightforward:
```cpp
while(condition){
    ...
}


int i = 0;
while(i < 10){
    cout << "Hello" << endl;
    ++i;
}
```

# Typedefs
```cpp
typedef long long ll;
```
Now ```ll``` is a synonym to type ```long long```, you can freely mix them:
```cpp
void f(long long a, long long b){
    ...
}

...

ll a = 2;
long long b = 3;
f(a, b);
cout << a << b;
```

# References
Reference is another name for already existing variable: it you change reference, the referenced variable is also changed:
```cpp
int a = 3;
int &ra = a;   // now a and ra is responsible for the same memory cells

ra += 2;

cout << ra << " " << a; // 5 5

a += 3;

cout << ra << " " << a; // 8 8
```
## Important Notes
1. reference cannot be left uninitialized:
```cpp
        int &ra;       // ERROR

        ...

        int a = 3;
        int &ra = a;   // OK
```
2. once you have initialized the reference, you cannot make it point to another variable, i.e:
```cpp
        int a = 3;
        int b = 4;

        int &ra = a;

        ra = b;
        // now ra and a have value of b
        // we did not make ra pointing to b
```
3. You can also pass variables as references inside functions:
```cpp
        void do_smth_on_variable(int &target){
            target *= 2;
        }

        void do_smth_on_variable2(int target){
            target *= 2;
        }

        ...

        int a = 2;
        do_smth_on_variable(a);
        cout << a; // 4

        do_smth_on_variable2(a);
        cout << a; // 4
```
In the above example ```do_smth_on_variable2``` gets a **COPY** of our original variable ```a```

**Conclusion: ALWAYS** pass containers by **REFERENCE** not by value (otherwise your container will be unnecessary copied)
```cpp
void this_function_copies_container(vector<int> v);

void this_function_does_not_copy_container(vector<int> &v);
```

# Const Reference
If you don't want your function to modify the reference passed, you can explicitly state it by using **const reference**:
```cpp
void f_without_const_reference(int &a){
    a += 2;
}

void f_with_const_reference(const int &a){
    a += 2; // ERROR, a must remain unchanged
}
```
Same thing works for containers:
```cpp
int get_sum_of_the_container(const vector<int> &v){
    int res = 0;

    for(int i = 0; i < v.size(); ++i){
        res += v[i];
    }

    return res;
}
```

**Conclusion: ALWAYS** pass things by **const reference** if you are **NOT GOING TO MODIFY** it inside the function.

**Side note:** don't pass integral types by **const reference**, just copy it, i.e:

```cpp
void f1(const int &i); // don't do it
void f2(int i);        // preferred way
```

# Debugging process
**Debugging** - process of finding bugs -> logical errors in your programs :)

You should be able to use your ide tools to debug your program - this way of doing it is **PREFERRED** over cout-debug.

(More on the lecture)

# Ranged for

```cpp
void increase_each_element_by_one(vector<int> &v){
    for(int i = 0; i < v.size(); ++i){
        ++v[i];
    }
}
```

This is not really straightforward.

What about this?

```cpp
void increase_each_element_by_one(vector<int> &v){
    for(auto &it: v){
        ++it;
    }
}
```

This code reads like this: take each element of the vector **by reference**.

Same works with const references:
```cpp
void print_container(const vector<int> &v){
    for(const auto &it: v){
        cout << it << " ";
    }
    cout << endl;
}
```
The following examples also work:
```cpp
void f1(const vector<int> &v){
    for(auto &it: v){
        // it is CONST reference
    }
}

void f2(vector<int> &v){
    for(const auto &it: v){
        // it is also const reference
    }
}
```

# Iterator Concept
python:
```python
>>> r = range(1, 3)
>>> it = iter(r)
>>> next(it)
1
>>> next(it)
2
>>> next(it)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
>>>
```

**Iterator** is an object that points to an element in the container and takes into account the structure of the container.

![text](https://blog.nelhage.com/images/indices/iterators-element.png)

```cpp
vector<int> v;
v.push_back(2);
v.push_back(3);
v.push_back(4);

vector<int>::iterator it = v.begin();

cout << *it;              // 2
it += 2;
cout << *it;              // 4
--it;
cout << *it;              // 3
it += 2;
cout << (it == v.end());  // true
cout << *it;              // ERROR
```

It is needed for a unifued interface for algorithms,
for example sorting:
```cpp
#include <algorithm>

...

vector<int> v;
v.push_back(5);
v.push_back(4);
v.push_back(3);
v.push_back(2);
v.push_back(1);

// sorting first 2 elements of the vector
sort(v.begin(), v.begin() + 2) // right endpoint is NOT included
// 4 5 3 2 1

// sorting all vector
sort(v.begin(), v.end());
// 1 2 3 4 5

// you can do it for other types, that support sorting
// for example deque

deque<double> d;
d.push_back(2);
d.push_front(4);
d.push_back(3);
// 4 2 3
sort(d.begin(), d.end());
// 2 3 4
```

# Set

Based on **red-black tree**.

Insertion, deletion, lookup - ```O(logn)```.

Elements are **ALWAYS** stored sorted.

```cpp
#include <set>

...

set<int> s;
s.insert(1);

cout << s.size();       // 1
cout << s.empty();      // false

s.insert(1);
cout << s.size();       // 1

s.clear();
cout << s.empty();      // true

s.insert(1);
s.insert(2);

s.erase(1);
s.erase(100);           // OK, won't throw error

cout << s.size();       // 1
s.insert(1);

// now s has 1 2

if(s.find(100) == s.end()){
    cout << "s does not have value 100";
}
else{
    cout << "s has value 100";
}

// finding min and max element
set<int>::iterator it = s.begin();
cout << *it;            // 1

it = s.end();
cout << *it;            // ERROR
--it;
// HAZARD: cannot do it += 1 or it += 2
cout << *it;            // 2
```

# Unordered set
Based on **hashing**.

Insertion, deletion, lookup - ```O(1)```.

Elements are **NOT** sorted.

Interface is pretty much the same:
```cpp
#include <unordered_set>

...

unordered_set<int> s;
// same as in previous example
// however s.begin() will not necessary give you an iterator to the smallest element
```
# Map
Same structure as in ```set```, but apart from key map stores a value.

All modification operations - ```O(logn)```

```cpp
#include <map>

...

map<int, int> m;
cout << m.size();   // 0
cout << m.empty();  // true

m[0] = 1;
m[1] = 2;

cout << m[0];          // 1
cout << m[1];          // 2

// HAZARD
cout << m[100];        // WILL WORK FINE
// now 100 key is inside the map!!!

// finding by key
if(m.find(200) == m.end()){
    cout << "m does not have a key 200";
}
else{
    cout << "m has a key 200";
}

// getting the smallest key
map<int, int>::iterator it = m.begin();
cout << (*it).first;    // 0
cout << (*it).second;   // 1

// getting the biggest key
it = m.end();
--it;                   // NOTE: not it -= 1
cout << (*it).first;    // 100
cout << (*it).second;   // 0
```

# Unordered_map
Same as ```map```, but uses hashing inside.

All modification operations are ```O(1)```, but keys are not sorted anymore.

Interface is pretty much the same.
