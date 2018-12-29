---
date: 2011-01-12T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1294822476"
  _wp_old_slug: ""
published: true
unlisted: true
status: publish
tags: []
title: Hacker Cup Qualification Round - Solutions
type: post
url: /2011/01/12/hacker-cup-qualification-round-solutions/
---

Overall, I was fairly disappointed with the organization and general structure of hacker cup qualification round. Hopefully the next round will be better, but for now - here are the solutions to the three questions. Unfortunately it seems the questions themselves have been taken down. I did manage to score 3/3, so I can be fairly sure that these are correct to the spec of the questions Facebook wrote.

Squares
=====

**Problem**: For each given z,  Find the # of pairs (x,y) such that x <= y and x^2 + y^2 = z.

**Solution**: Pre-generate the set of perfect squares. Iterate through this set. Check to see if z - x^2 is also in this set, where x is the current perfect square.

**Implementation**:

```cpp
#include <iostream>
#include <set>
#include <cmath>
using namespace std;

typedef long long unsigned int llu;
set<llu> squares;

int main() {
  for (int i = 0; i < 50000; i++) {
    llu i2 = i*i;
    if (i2 > 2147483647L * 2L) {
      break;
    } else {
      squares.insert(i2);
    }
  }

  int N;
  cin >> N;

  for (int i = 0; i < N; i++) {
    int num;
    cin >> num;
    int ans = 0;
    for(set<llu>::iterator it = squares.begin(); it != squares.end(); ++it) {
      llu first = *it;
      if (2 * first > num) break;
      if (squares.count(num - first)) {
        ans++;
      }
    }
    cout << ans << endl;
  }
}

```
Students
======

**Problem**: For a given set of words (sequences of lowercase letters), find the the lexicographically lowest string result from the concatenation of these words in any order.

**Solution**: I'm fairly sure simply sorting the words then concatenating them would suffice, but I was having issues string comparison yielding the same results at Facebook's output. Since the constraints were so low - maximum of 9 words per set, I simply did it exhaustively. This finishes easily within the 6 minute time limit since 9 factorial is relatively small.

**Implementation**:

```python
from itertools import permutations

def doit():
    words = raw_input().split()[1:]

    best = ""

    for x in permutations(words):
        concatted = "".join(x)
        if best == "" or concatted < best:
            best = concatted

    print best

n = input()

for z in range(n):
    doit()
```

Pegs
===

**Problem**: Given a peg board in the format like this:

    x.x.x
     x.x
    x.x.x

defined by the number of rows and cols of pegs, except with a few pegs missing, like this:

    x.x.x.x.x
     x...x.x
    x...x.x.x
     x.x...x
    x.x.x.x.x

Determine the optimal location to drop a ball in order to maximize the probability of the ball landing in a specific slot in the bottom row. The probability of the ball going to either side of a peg is 0.5.

**Solution**: There's no real trick to this one - you just create an array of the probabilities that the ball reaches each available cell given which pegs are missing. This one is more of a coding problem than anything else.

I'm sure I could have written a more elegant solution to this problem, but I just wanted to get it done.

**Implementation**:

```cpp
#include <iostream>
#include <set>
#include <cmath>
#include <utility>
using namespace std;

void doit() {
    int R, C, K, M;
    cin >> R >> C >> K >> M;
    set<pair<int,int> > missing;
    for (int i = 0; i < M; i++) {
        int ri, ci;
        cin >> ri >> ci;
        missing.insert(make_pair(ri,ci));
    }

    int best_pos = 0;
    long double best_prob = 0;
    for (int cur_pos = 0; cur_pos < C-1; cur_pos++) {
        long double probs[R][C];
        for (int r = 0; r < R; r++) {
            for (int c = 0; c < C; c++) {
                probs[r][c] = 0.00;
            }
        }
        probs[0][cur_pos] = 1.00;

        for (int r = 1; r < R; r++) {
            if (r % 2 == 1) { // odd row
                for(int peg = 0; peg < C-1; peg++) {
                    if (missing.count(make_pair(r,peg))) {
                        if (r+1 < R) probs[r+1][peg] += probs[r-1][peg];
                    } else {
                        if (peg == 0) {
                            probs[r][peg] += probs[r-1][peg];
                        } else if (peg == C-2) {
                            probs[r][peg-1] += probs[r-1][peg];
                        } else {
                            probs[r][peg]   += 0.5 * probs[r-1][peg];
                            probs[r][peg-1] += 0.5 * probs[r-1][peg];
                        }
                    }
                }
            } else { // even row
                for(int peg = 1; peg < C-1; peg++) {
                    if (missing.count(make_pair(r,peg))) {
                        if (r+1 < R) probs[r+1][peg-1] += probs[r-1][peg-1];
                    } else {
                        probs[r][peg]   += 0.5 * probs[r-1][peg-1];
                        probs[r][peg-1] += 0.5 * probs[r-1][peg-1];
                    }
                }
            }
        }

        long double cur_prob = probs[R-1][K];
        if (cur_prob > best_prob) {
            best_prob = cur_prob;
            best_pos = cur_pos;
        }
    }

    printf("%d %.6Lf\n", best_pos, best_prob);
}

int main() {
    int z;
    cin >> z;
    while(z--)doit();
}
```
