---
date: 2009-11-12T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1311129817"
published: true
unlisted: true
status: publish
tags:
- bash
- c++
- c99
- java
- pascal
- perl
- php
- prime
- python
- ruby
- SPOJ
title: 'SPOJ Problem 2: Prime Generator (PRIME1)'
type: post
url: /2009/11/12/spoj-problem-2-prime-generator/
---

Problem: <a href='http://www.spoj.com/problems/PRIME1/'>Prime Generator</a>

<blockquote>
Peter wants to generate some prime numbers for his cryptosystem. Help him! Your task is to generate all prime numbers between two given numbers!
</blockquote>

<strong>Concept</strong>
The idea behind every solution here (with some variation) is to generate all the prime numbers that could be factors of numbers up to the maximum endpoint 1 billion. That square root happens to be around 32000. Using this array, do a bounded Sieve of Eratosthenes only in the range requested. In languages like php and python, it turns out that it's more efficient to build an associative array and check if the index is set than it is to generate a huge boolean array.

<strong>Code</strong>

<strong>C++</strong>

```cpp
/// PRIME1 - C++ (g++)
// AC Time: 2.52s
// NOTE: I am aware that the use of vector and set actually
//  makes this code run _slower_
// I used vector and set simply as a way of practicing STL
#include <iostream>
#include <cmath>
#include <vector>
#include <set>
using namespace std;

int main() {
    vector<int> primes;
    primes.push_back(2);

    for (int i = 3; i <= 32000; i+=2) {
        bool isprime = true;
        int cap = sqrt(i) + 1;

        vector<int>::iterator p;
        for (p = primes.begin(); p != primes.end(); p++) {
            if (*p >= cap) break;
            if (i % *p == 0) {
                isprime = false;
                break;
            }
        }
        if (isprime) primes.push_back(i);
    }

    int T,N,M;

    cin >> T;

    for (int t = 0; t < T; t++) {
        if (t) cout << endl;

        cin >> M >> N;
        if (M < 2) M = 2;

        int cap = sqrt(N) + 1;

        set<int> notprime;
        notprime.clear();

        vector<int>::iterator p;
        for (p = primes.begin(); p != primes.end(); p++) {

            if (*p >= cap) break;
            int start;

            if (*p >= M) start = (*p)*2;
            else start = M + ((*p - M % *p) % *p);

            for (int j = start; j <= N; j += *p) {
                notprime.insert(j);
            }
        }

        for (int i = M; i <= N; i++) {
            if (notprime.count(i) == 0) {
                cout << i << endl;
            }
        }

    }
    return 0;
}
```

<strong>C99</strong>

```c
// PRIME1 - C99 (gcc)
// AC Time: 0.07s
#include <stdio.h>
#include <string.h>
#include <stdbool.h>
#include <math.h>

int main() {
    int primes[4000];
    int numprimes = 0;

    primes[numprimes++] = 2;
    for (int i = 3; i <= 32000; i+=2) {
        bool isprime = true;
        int cap = sqrt(i)+1;
        for (int j = 0; j < numprimes; j++) {
            if (primes[j] >= cap) break;
            if (i % primes[j] == 0) {
                isprime = false;
                break;
            }
        }
        if (isprime) primes[numprimes++] = i;
    }

    int T,N,M;
    scanf("%d",&T);

    for (int t = 0; t < T; t++) {
        if (t) printf("\n");
        scanf("%d %d",&M,&N);
        if (M < 2) M = 2;

        int cap = sqrt(N) + 1;

        bool isprime[100001];
        memset(isprime,true,sizeof(isprime));

        for (int i = 0; i < numprimes; i++) {
            int p = primes[i];

            if(p >= cap) break;

            int start;

            if (p >= M) start = p*2;
            else start = M + ((p - M % p) % p);

            for (int j = start; j <= N; j += p) {
                isprime[j - M] = false;
            }
        }

        int start = (M % 2)?M:M+1;

        if (M == 2) {
            printf("2\n");
        }
        for (int i = start; i <= N; i+=2) {
            if (isprime[i-M]) printf("%d\n",i);
        }
    }
    return 0;
}
```
<strong>PHP</strong>

```php
<?
// PRIME1 - PHP
// AC Time: 5.73

$primes = array(2);
$numprimes = 1;

for ($i = 3; $i <= 32000; $i+=2) {
    $isprime = true;
    $cap = sqrt($i)+1;

    for ($j = 0; $j < $numprimes; ++$j) {
        if ($j >= $cap) break;
        if (!($i % $primes[$j])) {
            $isprime = false;
            break;
        }
    }
    if ($isprime) $primes[$numprimes++] = $i;
}

fscanf(STDIN,"%d",$T);

$output = "";

for ($t = 0; $t < $T; $t++) {
    if ($t) $output .= "\n";

    fscanf(STDIN,"%d %d",$M,$N);
    if ($M < 2) $M = 2;

    $isprime = array();

    $cap = sqrt($N)+1;

    $i = 0;
    while($i < $numprimes) {
        $p = $primes[$i];

        if ($p >= $cap) break;

        if ($p >= $M) {
            $start = $p*2;
        } else {
            $start = $M + (($p - $M % $p) % $p);
        }

        for ($j = $start; $j <= $N; $j += $p) {
            $isprime[$j - $M] = false;
        }

        ++$i;
    }

    for ($i = $M; $i <= $N; ++$i) {
        if (!isset($isprime[$i-$M])) {
            $output .= "$i\n";
        }
    }
}

echo $output;
?>
```
<strong>Python</strong>

```python
# PRIME1 - Python
# AC Time: 3.10s
from math import sqrt
primes = [2]

for i in range(3,32000,2):
    isprime = True

    cap = sqrt(i)+1

    for j in primes:
        if (j >= cap):
            break
        if (i % j == 0):
            isprime = False
            break
    if (isprime):
        primes.append(i)

T = input()
output = ""
for t in range(T):

    if (t > 0):
        output += "\n"

    M,N = raw_input().split(' ')
    M = int(M)
    N = int(N)
    cap = sqrt(N)+1

    if (M < 2):
        M = 2

    isprime = [True]*100001

    for i in primes:
        if (i >= cap):
            break

        if (i >= M):
            start = i*2
        else:
            start = M + ((i - M % i)%i)

        # The two below, obscure lines create a continuous
        #  block of false elements in order to set all
        #  elements correspnding to numbers divisible by i
        #  in isprime to be false
        # In turns out that this runs substantially faster
        #  than setting the elements individually using loops
        falseblock = [False] * len(isprime[start-M:N+1-M:i]);
        isprime[start-M:N+1-M:i] = falseblock

    for i in range(M,N+1):
        if (isprime[i-M] == True):
            output += str(i) + "\n"

print output[:-1]
```

<strong>Bash</strong>

```bash
# PRIME1 Incomplete - BASH
# This is not a complete solution
# The speed of bash parsing makes getting an
#  AC submission infeasible

# The following code is a working prime generator
# Giving enough time, it will output all prime
#  numbers from 0 to 32000
# ..which is the first step in the solution to PRIME1

let PRIMES[0]=2
let NUMPRIMES=1
for i in {3..32000}; do
    let ISPRIME=1

    #for j in {0..$NUMPRIMES}; do
    for (( j = 0;j<$NUMPRIMES;j++ )); do
        let CURPRIME=${PRIMES[$j]}
        if [[ $CURPRIME*$CURPRIME -gt $i ]]; then
            break
        fi

        let MOD=$[$i % $CURPRIME]
        if [ $MOD -eq 0 ]; then
            let ISPRIME=0
            break
        fi
    done

    if [ $ISPRIME -eq 1 ]; then
        echo $i
        let PRIMES[NUMPRIMES]=$i
        let NUMPRIMES=NUMPRIMES+1
    fi
done
</pre>
```


<strong>Ruby</strong>

```ruby
# PRIME1 - Ruby
# AC Time: 4.65s
#
# While I would have liked to use more
# Ruby idioms such as for p in primes
# or primes.each do |p| (used once),
# the difference in runtime between
# the use of loops like these and while
# loops was non-negligible

primes = [2]

3.step(32000,2) do |i|
    isprime = true
    cap = Math.sqrt(i) + 1

    primes.each do |p|
        if (p >= cap)
            break
        end

        if (i % p == 0)
            isprime = false
            break
        end
    end

    if isprime
        primes << i
    end
end
numprimes = primes.length

T = gets.to_i


output = ""
t = 0
while t < T
    print "\n" if t > 0
    line = gets.split(" ")
    m = line[0].to_i
    n = line[1].to_i

    m = 2 if m < 2

    cap = Math.sqrt(n) + 1

    notprime = {}

    i = 0
    while i < numprimes
        p = primes[i]
        i+=1
        if (p >= cap)
            break
        end

        if (p >= m)
            start = p*2
        else
            start = m + ((p - m % p)%p)
        end

        j = start
        while j <= n
            notprime[j] = true
            j += p
        end
    end

    i = m
    while (i <= n)
        if (notprime[i] == nil)
            print i,"\n"
        end
        i+=1
    end
    t += 1
end
```

<strong>Java</strong>

```java
// PRIME1 - Java
// AC Time: 2.20

import java.io.*;
import java.util.*;
import java.lang.Math.*;

public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);

        int[] primes = new int[4000];
        int numprimes = 0;

        primes[numprimes++] = 2;
        for (int i = 3; i <= 32000; i+=2) {
            boolean isprime = true;
            double cap = Math.sqrt(i) + 1.0;

            for (int j = 0; j < numprimes; j++) {
                if (j >= cap) break;
                if (i % primes[j] == 0) {
                    isprime = false;
                    break;
                }
            }
            if (isprime) primes[numprimes++] = i;
        }


        int T,N,M;

        T = in.nextInt();

        for (int t = 0; t < T; t++) {
            if (t > 0) System.out.println("");

            M = in.nextInt();
            N = in.nextInt();

            if (M < 2) M = 2;

            boolean[] isprime = new boolean[100001];
            for (int j = 0; j < 100001; j++) {
                isprime[j] = true;
            }

            for (int i = 0; i < numprimes; i++) {
                int p = primes[i];
                int start;

                if (p >= M) start = p*2;
                else start = M + ((p - M % p)%p);

                for (int j = start; j <= N; j += p) {
                    isprime[j - M] = false;
                }
            }

            for (int i = M; i <= N; i++) {
                if (isprime[i-M]) System.out.println(i);
            }
        }
    }
}
```

<strong>Perl</strong>

```perl
# PRIME1 - Perl
# AC Time: 4.28s
@primes = (2);

for ($i = 3; $i <= 32000; $i+=2) {
    $isprime = 1;
    $cap = sqrt($i) + 1;
    foreach $p (@primes) {
        if ($p >= $cap) {
            last;
        }
        if ($i % $p == 0) {
            $isprime = 0;
            last;
        }
    }
    if ($isprime == 1) {
        push(@primes,$i)
    }
}


$T = <STDIN>;

for ($t = 0; $t < $T; $t++) {
    if ($t > 0) {
        print "";
    }

    $in = <STDIN>;
    @line = split(/ /,$in);
    $M = $line[0];
    $N = $line[1];

    if ($M < 2) {
        $M = 2;
    }

    $cap = sqrt($N) + 1;

    @isprime = ((1) x 100001);

    foreach $p (@primes) {
        if ($p >= $cap) {
            last;
        }

        if ($p >= $M) {
            $start = $p*2;
        } else {
            $start = $M + (($p - $M % $p) % $p);
        }

        for ($j = $start; $j <= $N; $j += $p) {
            $isprime[$j-$M] = 0;
        }
    }

    for ($i = $M; $i <= $N; $i++) {
        if ($isprime[$i-$M] == 1) {
            print "$i\n";
        }
    }
}
```

<strong>C#</strong>

```csharp
// PRIME1 - C# (gmcs)
// AC Time: 1.50s

using System;
class PRIME1 {
    static void Main() {

        int[] primes = new int[4000];
        int numprimes = 0;

        primes[numprimes++] = 2;
        for (int i = 3; i <= 32000; i+=2) {
            bool isprime = true;
            double cap = Math.Sqrt(i) + 1.0;
            for (int j = 0; j < numprimes; j++) {
                if (j >= cap) break;
                if (i % primes[j] == 0) {
                    isprime = false;
                    break;
                }
            }
            if (isprime) primes[numprimes++] = i;
        }

        int T,N,M;
        T = int.Parse(Console.ReadLine());

        for (int t = 0; t < T; t++) {
            if (t > 0) Console.WriteLine("");

            string[] parts = Console.ReadLine().Split(' ');
            M = int.Parse(parts[0]);
            N = int.Parse(parts[1]);


            if (M < 2) M = 2;
            double cap = Math.Sqrt(N)+1;

            bool[] isprime = new bool[100001];
            for (int i = 0; i < 100001; i++) isprime[i] = true;

            for (int i = 0; i < numprimes; i++) {
                int p = primes[i];
                if (p >= cap) break;
                int start;

                if (p >= M) start = p*2;
                else start = M + ((p - M % p)%p);

                for (int j = start; j <= N; j+= p) {
                    isprime[j - M] = false;
                }
            }

            for (int i = M; i <= N; i++) {
                if (isprime[i-M] == true) Console.WriteLine(i);
            }
        }
    }
}
```

<strong>GNU Pascal</strong>

```pascal
{
    PRIME1 - GNU Pascal
    AC Time: 0.54
}

program PRIME1;
var
    primes: array[1..4000] of integer;
    numprimes: integer;
    i,j: integer;
    cap: double;
    isprime: boolean;
    T,N,M: integer;
    isp: array[0..100001] of boolean;
    p,start: integer;


begin
    primes[1] := 2;
    numprimes := 1;
    for i := 3 to 32000 do
    begin
        isprime := true;
        cap := sqrt(i) + 1;
        for j := 1 to numprimes do
        begin
            if primes[j] >= cap then
                    break;

            if (i MOD primes[j] = 0) then
            begin
                isprime := false;
                break;
            end
        end;

        if isprime = true then
        begin
            numprimes := numprimes + 1;
            primes[numprimes] := i
        end
    end;

    read(T);

    for t := 1 to T do
    begin
        if t > 0 then
            writeln('');

        read(M);
        read(N);

        if M < 2 then
            M := 2;

        cap := sqrt(N) + 1;

        for i := 0 to 100001 do
            isp[i] := true;


        for i := 1 to numprimes do
        begin
            p := primes[i];
            if p >= cap then
                break;

            if p >= M then
                start := (p * 2);

            if p < M then
                start := M + ((p - (M MOD p)) MOD p);

            j := start;

            while j <= N do
            begin
                isp[j - M] := false;
                j := j + p;
            end;
        end;

        for i := M to N do
        begin
            if isp[i-M] = true then
                writeln(i);
        end;
    end;
end.
```
