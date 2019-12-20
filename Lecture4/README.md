# Lecture 4: Numbers Theory, Recursion

# Undefined Behaviour in C++

More on the lecture

# Pointers
**Pointers** represents the same concept of synonym to a variable as **references** do. However, they have some differences:
1. You can change the referenced object
2. You can left pointer uninitialized
3. **BUT** in order to access the value you must dereference it (put a *) or use **arrow operator ->**

## Basics
```cpp
int a = 2;
int *p = &a;

cout << a;  // 2
cout << *p; // 2

*p = 5;
cout << a;  // 5
cout << *p; // 5

int b = 10;
p = &b;

cout << *p; // 10
*p = 20;

cout << a;  // 5
cout << b;  // 20
cout << *p; // 20

p = nullptr;        // not p points to nothing
*p;                 // ERROR: cannot dereference a null pointer

int *p1;            // p1 is NOT nullptr, it contains trash
```
## Pointer to container
```cpp
vector<int> v{1, 2, 5, 3};

vector<int> *p = &v;
cout << (*p).size(); // 4
cout << p->size();   // 4

cout << (*p)[2];     // 5
```
## Passing pointers inside functions
```cpp
void do_smth(int *a){
    ++(*a);
}

...

int a = 2;

int *p = &a;

do_smth(p);
cout << a;    // 3

do_smth(&a);
cout << a;    // 4
```

# Fibonacci Numbers
Warm-up (for now without recursion, we will see recursive solution later).

Spoiler: nth fibonacci number can be calculated in ```O(logn)``` - but we won't talk about this today.

# Checking Whether Number is Prime
Silly solution:
```cpp
bool is_prime(int number){
    if(number == 1){
        return false;
    }

    for(int i = 2; i < number; ++i){
        if(number % i == 0){
            return false;
        }
    }

    return true;
}
```
**Complexity**: ```O(n)```

**Observation**: if ```number``` is prime, it can be represented as a product of 2 numbers ```n1```, ```n2```, such as: ```n1 <= sqrt(number)``` and ```n2 >= sqrt(number)```

This will lead us to a faster solution:
```cpp
typedef long long ll;

bool is_prime(int number){
    if(number == 1){
        return false;
    }

    // NOTICE i HAS LONG LONG TYPE!
    for(ll i = 2; i * i <= number; ++i){
        if(number % i == 0){
            return false;
        }
    }

    return true;
}
```
**Complexity**: ```O(sqrt(n))``` - MUCH GREATER than the previous one.

# Number Factorization
**Problem:** given number ```n```, factorize it, i.e:

500 = 2<sup>2</sup> * 5<sup>3</sup>

```cpp
typedef long long ll;

vector<int> factorize(int number){
    vector<int> result;

    // NOTICE i HAS LONG LONG TYPE!
    for(ll i = 2; i * i <= number; ++i){
        while(number % i == 0){
            result.push_back(i);
            number /= i;
        }
    }

    if(number > 1){
        result.push_back(number);
    }

    return result;
}
```
**Complexity:** ```O(sqrt(n))```

# Sieve of Eratosthenes
See theory on [e-maxx](https://e-maxx.ru/algo/eratosthenes_sieve).

What if we want to determine if ```i``` is prime for all ```i``` in ```[1; n]```?

```O(n * sqrt(n))``` can be too much. It turns out, that we can solve this problem more efficiently.

P.S. e-maxx code is a little bit ugly, here is my version:
```cpp
typedef long long ll;

const int N = 1000000;
bool is_prime[N];

void sieve(){
    for(int i = 0; i < N; ++i){
        is_prime[i] = true;
    }
    is_prime[0] = false;
    is_prime[1] = false;

    for(ll i = 2; i <= N; ++i){
        if(!is_prime[i]){
            continue;
        }

        for(ll j = i * i; j <= N; j+=i){
            is_prime[j] = false;
        }
    }
}

// now just run sieve() and use is_prime global array
```

**Complexity**: ```O(n * log(log(n)))```

# Least prime factor of numbers till n
See additional materials on [geeksforgeeks](https://www.geeksforgeeks.org/least-prime-factor-of-numbers-till-n/)

```cpp
    lowest_prime[2] = 2;
    lowest_prime[4] = 2;
    lowest_prime[5] = 5;
    lowest_prime[18] = 2;
    lowest_prime[25] = 5;
    lowest_prime[1155] = 3;
```

## Applications
* Factorization
* Calculation of multiplicative function (if ```c = a * b```, then ```f(c) = f(a) * f(b)```)

## Code
```cpp
typedef long long ll;

const int N = 1000000;

int lowest_prime[N];

void sieve(){
    for(int i = 2; i <= N; ++i){
        if(lowest_prime[i] != 0){
            continue;
        }
        lowest_prime[i] = i;

        for(ll j = 2 * i; j <= N; j += i){
            if(lowest_prime[j] == 0){
                lowest_prime[j] = i;
            }
        }
    }
}
```

**Complexity:** ```O(nlogn)```

# Recursion

In order recursive function to be good, it should have **exit condition**.

It means that it should return a specific value for some argument and not to make a recursive call.

```cpp
int f(int i){
    if(i == 1 || i == 2){
        return 1;
    }

    return f(i - 1) + f(i - 2);
}
```
Recursive fibonacci number function.

Do you see a problem? :) **(IT IS A BAD FUNCTION, DO NOT USE IT)**

# Recursion with memoization

More on the lecture

# Binary power
See theory on [e-maxx](https://e-maxx.ru/algo/binary_pow)

**Problem:** calculate **n<sup>m</sup>** (```n```,```m``` <= 10<sup>9</sup>)

```O(m)``` approach won't work :(

**Observation:**
* m is even: n<sup>m</sup> = (n<sup>2</sup>)<sup>m/2</sup>
* m is odd: n<sup>m</sup> = n * n<sup>m - 1</sup>

Got ```O(logm)``` solution, code is available on [e-maxx](https://e-maxx.ru/algo/binary_pow)

**Note:** As **n<sup>m</sup>** can lead to integer overflow, you should also implement it in modular arithmetic.

# Greatest Common Divisor
This topic is easy enough to learn on your own.

Materials can be found on [e-maxx](https://e-maxx.ru/algo/euclid_algorithm)

You can also read about extended gcd on [e-maxx](https://e-maxx.ru/algo/extended_euclid_algorithm)

# Нахождение степени делителя факториала
See theory on [e-maxx](https://e-maxx.ru/algo/factorial_divisors)

More on the lecture

# Problems
See problems on [vjudge](https://vjudge.net/contest/342783). Password: ```47```
