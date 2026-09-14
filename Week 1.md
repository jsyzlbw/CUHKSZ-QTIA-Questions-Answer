![QTIA Week 1 — original questions](images/week-1-questions.png)

# Trailing Zeros in a Factorial

## 1. How many trailing zeros are there in the decimal representation of $100!$?

Each trailing zero corresponds to a factor of $10=2\times5$. In a factorial, there are at least as many factors of $2$ as factors of $5$, so we only need to count the factors of $5$.

Among the integers from $1$ to $100$, each multiple of $5$ contributes at least one factor of $5$, and each multiple of $25$ contributes an additional factor. Since $125>100$, there are no further contributions. Thus the number of trailing zeros is

$$
\left\lfloor\frac{100}{5}\right\rfloor
+\left\lfloor\frac{100}{25}\right\rfloor
=20+4=\boxed{24}.
$$

## 2. For any positive integer $n$, find the number of trailing zeros in $n!$ and give an $O(\log n)$ algorithm.

The same counting argument gives

$$
Z(n)=\sum_{k=1}^{\infty}\left\lfloor\frac{n}{5^k}\right\rfloor.
$$

The term for $5^k$ counts the integers that contribute a $k$th factor of $5$. All terms with $5^k>n$ are zero, so only finitely many terms need to be added.

We can compute this sum by repeatedly dividing by $5$:

```python
def num_of_zero(n):
    """Return the number of trailing zeros in n! for an integer n >= 0."""
    count = 0
    while n >= 5:
        n //= 5
        count += n
    return count
```

After each division, `n` becomes the next term in the sum: $\lfloor n_0/5\rfloor$, $\lfloor n_0/25\rfloor$, and so on, where $n_0$ is the original input. For $n_0\ge1$, the loop runs $\lfloor\log_5 n_0\rfloor$ times. Under the usual unit-cost model for integer arithmetic, the algorithm takes $O(\log n)$ time and uses $O(1)$ auxiliary space. It also returns $0$ for $n=0$, consistent with $0!=1$.

# Ants on a Circle

Ants are equally spaced around a circle. Each ant independently chooses to move clockwise or counterclockwise with probability $1/2$, then moves at a constant speed of one revolution per minute. Whenever two ants meet, both immediately reverse direction without changing speed. Treat the ants as point particles.

Find the probability of each event below after exactly one minute, and justify your answer.

## 1. There are 9 ants, and the set of occupied positions is the same as initially.

If we ignore the ants' identities, two ants reversing direction at a collision is indistinguishable from two ants passing through each other: either way, one ant leaves in each direction.

We may therefore imagine that all ants keep moving in their initial directions without turning. After one minute, each imagined ant completes a full revolution and returns to its starting position. Hence the set of occupied positions is always restored, regardless of the initial directions:

$$
\boxed{P=1}.
$$

## 2. There are 9 ants, and every ant returns to its own starting position.

Here, identities matter. The pass-through argument still tells us that the occupied positions are restored, but it does not tell us which ant occupies each position.

First consider $n$ ants, of which $k$ initially move clockwise. Let the circumference be $1$ and take clockwise as positive. At every collision, one clockwise-moving ant and one counterclockwise-moving ant exchange directions, so the number moving in each direction remains unchanged. The sum of their signed velocities is therefore always

$$
k-(n-k)=2k-n.
$$

Over one minute, their **total signed displacement**, counting complete revolutions, is also $2k-n$.

The actual ants never pass one another, so their order is preserved. Since the final positions are again equally spaced, the ants go back to their initial position iff the **total signed displacement** module $n$ is $0$.  

Therefore,

$$
n\mid(2k-n)
\quad\Longleftrightarrow\quad
n\mid2k.
$$

For $n=9$, this requires $9\mid k$. Since $0\le k\le9$, only $k=0$ and $k=9$ work: all ants must initially move in the same direction. The $2^9$ direction assignments are equally likely, so

$$
\boxed{P=\frac{2}{2^9}=\frac{1}{256}}.
$$

## 3. There are 10 ants, and every ant returns to its own starting position.

Using the condition from Question 2, we need $10\mid2k$, or equivalently $5\mid k$. Hence $k=0,5,$ or $10$.

There are $\binom{10}{k}$ assignments with exactly $k$ clockwise-moving ants, so

$$
\boxed{
P=\frac{\binom{10}{0}+\binom{10}{5}+\binom{10}{10}}{2^{10}}
=\frac{1+252+1}{1024}
=\frac{127}{512}
}.
$$

# Weighing Gold Coins

There are three bags, each containing a sufficient supply of gold coins. All coins within a bag have the same weight, measured in a positive integer number of grams. You have an exact electronic scale with no capacity limit. In each weighing, you may take any finite number of coins, including zero, from each bag and measure their combined weight. You may use earlier results to decide how many coins to take in a later weighing.

The following questions are independent. For each, find the minimum number of weighings and explain both the method and why it is optimal. All weights below are expressed in grams.

## 1. Coins in one bag weigh either 9 or 11 grams each; coins in the other two bags weigh 10 grams each. Identify the unusual bag and determine whether its coins are lighter or heavier.

**Minimum: one weighing.**

Take $1$, $2$, and $3$ coins from the first, second, and third bags, respectively. If all six coins weighed $10$ grams each, the total would be $60$. The unusual bag changes this total by $\pm1$, $\pm2$, or $\pm3$, depending on which bag it is.

| Unusual bag | Total if lighter (9 g per coin) | Total if heavier (11 g per coin) |
| --- | --- | --- |
| First | 59 | 61 |
| Second | 58 | 62 |
| Third | 57 | 63 |

All six totals are distinct, so one weighing identifies both the bag and whether its coins are lighter or heavier. With no weighing, these possibilities cannot be distinguished; therefore, one weighing is optimal.

## 2. Coins in one bag are 1 gram lighter or heavier than those in the other two bags. The normal weight is unknown. Identify the unusual bag and determine whether its coins are lighter or heavier.

**Minimum: one weighing.**

Take $1$, $2$, and $4$ coins from the first, second, and third bags, respectively. Let the normal weight be $m$ grams per coin. The total weight is

$$
W=\begin{cases}
7m\pm1, & \text{if the first bag is unusual},\\
7m\pm2, & \text{if the second bag is unusual},\\
7m\pm4, & \text{if the third bag is unusual}.
\end{cases}
$$

Reducing $W$ modulo $7$ eliminates the unknown normal weight $m$. The possible remainders are:

| Unusual bag | $W\bmod7$ if lighter | $W\bmod7$ if heavier |
| --- | --- | --- |
| First | 6 | 1 |
| Second | 5 | 2 |
| Third | 3 | 4 |

All six remainders are distinct, so they identify both the unusual bag and whether its coins are lighter or heavier. Zero weighings cannot distinguish the possibilities, so one weighing is optimal.

## 3. Determine the weight of a coin from each bag.

**Minimum: two weighings.**

Let $a,b,c$ be the positive integer weights of a coin from the first, second, and third bags, respectively.

**First weighing.** Take one coin from each bag and record

$$
S=a+b+c.
$$

**Second weighing.** After observing $S$, take one coin from the first bag, $S$ coins from the second bag, and none from the third. The measured total is

$$
W=a+Sb.
$$

Since $b,c>0$, we have $0<a<S$. Dividing $W$ by $S$ therefore gives quotient $b$ and remainder $a$. Hence

$$
\boxed{
a=W\bmod S,\qquad
b=\left\lfloor\frac{W}{S}\right\rfloor,\qquad
c=S-a-b
}.
$$

Thus two weighings determine all three weights. The ability to choose the second sample size after seeing the first result is essential to this construction.

**Why one weighing cannot suffice.** Any one-weighing strategy must choose fixed nonnegative integers $p,q,r$ and observe only

$$
W=pa+qb+rc.
$$

If any of $p,q,r$ is zero, the measurement contains no information about the corresponding bag's coin weight. Thus a successful strategy would require $p,q,r>0$.

For any such choice, however, the two distinct positive integer triples

$$
(a,b,c)=(q+1,1,1)
\quad\text{and}\quad
(a,b,c)=(1,p+1,1)
$$

both give

$$
W=pq+p+q+r.
$$

One weighing therefore cannot always distinguish the possible weights. Since two weighings suffice, the minimum is exactly two.
