---
date: 2010-05-11T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1277339178"
published: true
unlisted: true
status: publish
tags:
- c++
- Code Jam
- codejam
- google
- solutions
title: Google Codejam 2010 Solutions - Qualification
type: post
url: /2010/05/11/google-codejam-2010-solutions-qualification-round/
---

I'm happy to have qualified for Codejam for the second year running - I managed to make it to Online Round 2 last year, so let's see if I can top that this year.

The opening round was not very difficult, and were it not for a very stupid mistake on my part on the first question, I easily could have gotten perfect in under 2.5 hours. Instead I got 76 in 3.5 hours. Bah.

Read the questions here: <a href="http://code.google.com/codejam/contest/dashboard?c=433101#">Qualification Round 2010 @ code.google.com</a>

<h1>Problem 1: Snapper Chain</h1>
This is the one I screwed up, because I somehow overlooked the fact that each snap was simply equivalent to an binary increment of the snappers states. Instead, I did a foolish simulation, which did actually pass me the small test case. But anyway, the real solution is O(1).

For interest, here's what the snapper chain looks like in the first 30 iterations. The left side, labeled "P:" shows which of the snappers are powered, and the right side, labeled "S:" shows the current state of the snappers, with "#" being ON and "." being OFF in both cases. I did this so it would be easier to see the pattern. The first row is the initial set up, before any snaps.

    P: #.........	S: ..........
    P: ##........	S: #.........
    P: #.........	S: .#........
    P: ###.......	S: ##........
    P: #.........	S: ..#.......
    P: ##........	S: #.#.......
    P: #.........	S: .##.......
    P: ####......	S: ###.......
    P: #.........	S: ...#......
    P: ##........	S: #..#......
    P: #.........	S: .#.#......
    P: ###.......	S: ##.#......
    P: #.........	S: ..##......
    P: ##........	S: #.##......
    P: #.........	S: .###......
    P: #####.....	S: ####......
    P: #.........	S: ....#.....
    P: ##........	S: #...#.....
    P: #.........	S: .#..#.....
    P: ###.......	S: ##..#.....
    P: #.........	S: ..#.#.....
    P: ##........	S: #.#.#.....
    P: #.........	S: .##.#.....
    P: ####......	S: ###.#.....
    P: #.........	S: ...##.....
    P: ##........	S: #..##.....
    P: #.........	S: .#.##.....
    P: ###.......	S: ##.##.....
    P: #.........	S: ..###.....
    P: ##........	S: #.###.....

What we're interested in is how to tell where a specific snapper is on. It turns
out that this is quite simple - so long as all snappers before it are on the ON
settings, it will be powered. This means that ultimately, the power column above
is irrelevant. All we need to know is that the N-1 least significant bits are
set. This is fairly trivial to check.

```cpp
#include <iostream>
using namespace std;
int main() {
    int T,N,K;
    cin >> T;
    for(int t = 0; t < T; t++) {
    cin >> N >> K;
    int mask = (1 << N) - 1;
    printf("Case #%d: ",t+1);
    if ( (mask & K) == mask) puts("ON");
    else puts("OFF");
    }
    return 0;
}
```

Time for the large case:

```bash
$ time ./a.out < snapper.in > snapper.out

real    0m0.071s
user    0m0.026s
sys     0m0.040s
```


<h1>Problem 2: Fair Warning</h1>
This problem took me about 40 minutes to think of the solution for and about 5
minutes to code. Honour's Algebra (MATH 115) came in handy here. You can first
calculate T (the optimal anniversary) by looking at the gcd (greatest common
divisor) of the differences between the time elapsed since the events. This
works because in order for T to divide the time elapsed since the events, it
must also divide the time elapsed between the events. After that, you just need
to find the first y that makes any of the numbers divisible by T. I just wrote
out the congruences on my whiteboard (which I probably shouldn't have needed to
do) then typed in my answer.

The only remaining problem here is dealing with the large numbers, which is
hardly a problem in python. Here's my solution:

```python
def gcd(x,y):
    while x:
        x, y = y % x, x
    return y

testcases = open("warning.in").readlines()
t = 1
for tc in testcases[1:]:
    tc = map(lambda x: int(x), tc.split(" ")[1:])
    if len(tc) == 0:
        break
    tc.sort()

    diff = []
    for i in range(len(tc)-1):
        diff += [tc[i+1] - tc[i]]

    T = diff[0]
    for d in diff[1:]:
        T = gcd(T,d)

    print "Case #%d: %d" % (t, (-tc[0] % T))

    t += 1
```

Time for large case:

```bash
$ time python warning.py > warning.out

real    0m0.250s
user    0m0.037s
sys     0m0.021s
```

<h1>Problem 3: Theme Park</h1>
This is an optimization problem. There are a lot of different optimizations you
can apply, but I figured "to hell with it" and decided the solution getting it
down to O(R) was probably sufficient. If you want to look at a better list of
optimizations, go look here: <a
href="http://code.google.com/codejam/contest/dashboard?c=433101#s=a&a=2">Google
Codejam 2010 Qualification Round Contest Analysis</a>.

All I figured I needed to do was make it so I didn't have to find the groups
every single time the ride was loaded. To do this, I simulated using a queue
until I arrived at a position where the group at the front of the line had been
at the front before. While doing this, I record how many euros were made for a
given start of the line, and who ends up at the front of the line while they're
on the roller coaster. After this, the O(R) loop is very simple. Just add the
number of euros for the run, then move on to the next front of the line.

```cpp
#include <cmath>
#include <algorithm>
#include <iostream>
#include <fstream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <queue>
#include <set>
#include <map>
#include <sstream>
#include <vector>
using namespace std;

#define FR(i,a,b) for(int i=(a);i<(b);++i)
#define FOR(i,n) FR(i,0,n)
#define SETMIN(a,b) a = min(a,b)
#define SETMAX(a,b) a = max(a,b)
#define PB push_back
#define FORALL(i,v) for(typeof((v).end())i=(v).begin();i!=(v).end();++i)
#define CLR(x,a) memset(x,a,sizeof(x))
#define BEND(v) v.begin(),v.end()
#define MP make_pair
#define A first
#define B second

typedef unsigned long long int ull;
typedef long double ld;

int main() {
    freopen("themepark.in","r",stdin);
    freopen("themepark.out","w",stdout);

    int T;
    cin >> T;
    FOR(t,T) {
        int R,k,N;
        cin >> R >> k >> N;
        int grps[1001], done[1001], pts[1001], next[1001];
        CLR(done,0);
        queue<pair<int,int> > q;
        FOR(i,N) {
            cin >> grps[i];
            q.push(MP(i,grps[i]));
        }

        while(!done[q.front().A]) {
            int cur = 0;
            int start = q.front().A;
            done[start] = 1;

            FOR(i,N) {
                if (cur + q.front().B > k) break;

                cur += q.front().B;
                q.push(q.front());
                q.pop();
            }
            next[start] = q.front().A;
            pts[start] = cur;
        }

        while (!q.empty()) q.pop();

        int curFront = 0;
        ull ans = 0;
        FOR(i,R) {
            ans += pts[curFront];
            curFront = next[curFront];
        }

        printf("Case #%d: %lld\n", t+1, ans);
    }
    return 0;
}
```

Time for large test case:

```bash
$ time ./a.out

real    0m11.606s
user    0m11.542s
sys     0m0.022s
```

I might code up an O(N) solution later, which really isn't that difficult, but
at this point in the contest, it simply didn't matter enough.
