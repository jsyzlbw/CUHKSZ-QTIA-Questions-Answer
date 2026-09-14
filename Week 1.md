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

The term for $5^k$ counts the integers containing at least $k$ factors of $5$. All terms with $5^k>n$ are zero, so only finitely many terms need to be added.

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

### Generalization to $n$ bags

Suppose exactly one of $n$ bags contains coins weighing $9$ or $11$ grams each, while all other coins weigh $10$ grams each. Take $i$ coins from bag $i$, for $i=1,\ldots,n$.

If all coins had the normal weight, the total would be

$$
B=10\sum_{i=1}^{n}i=5n(n+1).
$$

Let $W$ be the measured weight and define $d=W-B$. If bag $j$ is unusual, then $d=+j$ when its coins are heavier and $d=-j$ when they are lighter. Thus $|d|$ identifies the bag, and the sign identifies whether it is lighter or heavier. **One weighing is optimal for every $n\ge1$.**

## 2. Coins in one bag are 1 gram lighter or heavier than those in the other two bags. The normal weight is unknown. Identify the unusual bag and determine whether its coins are lighter or heavier.

**Minimum: one weighing.**

The key is to choose sample sizes so that the unknown normal weight disappears when we take a remainder, while each unusual bag still leaves a distinct result.

Take $1$, $2$, and $4$ coins from the first, second, and third bags, respectively. Let the normal weight be the positive integer $m$. Since we take seven coins in total, the measured weight is

$$
W=\begin{cases}
7m\pm1, & \text{if the first bag is unusual},\\
7m\pm2, & \text{if the second bag is unusual},\\
7m\pm4, & \text{if the third bag is unusual}.
\end{cases}
$$

Reducing $W$ modulo $7$ eliminates $7m$. Write the remainder $r=W\bmod7$ in **exactly three binary digits**, including leading zeros:

| Unusual bag | Remainder if lighter | Remainder if heavier |
| --- | --- | --- |
| First: take 1 coin | $6=$ `110` | $1=$ `001` |
| Second: take 2 coins | $5=$ `101` | $2=$ `010` |
| Third: take 4 coins | $3=$ `011` | $4=$ `100` |

Each bag corresponds to one binary position, counted from right to left. If bag $i$ is heavier, the remainder is $2^{i-1}$, so only position $i$ is `1`. If it is lighter, the remainder is $7-2^{i-1}$: subtracting from $7=(111)_2$ changes only position $i$ to `0`.

Thus a single `1` identifies a heavier bag, and a single `0` identifies a lighter bag. All six patterns are distinct. Since zero weighings cannot distinguish the possibilities, one weighing is optimal.

This also explains why the previous sample sizes $1,2,3$ do not work here. Their total is $6$, and $+3\equiv-3\pmod6$, so the two cases for the third bag give the same remainder. Indeed, a heavier third bag with normal weight $m$ and a lighter third bag with normal weight $m+1$ both give $W=6m+3$.

### Generalization to $n\ge3$ bags

Suppose exactly one bag differs from the common, unknown normal weight by $1$ gram per coin. Assign one binary position to each bag: take $2^{i-1}$ coins from bag $i$, for $i=1,\ldots,n$. The total number of coins is

$$
N=\sum_{i=1}^{n}2^{i-1}=2^n-1
=\bigl(\underbrace{11\cdots11}_{n\text{ digits}}\bigr)_2.
$$

If bag $j$ is unusual, the single weighing gives $W=Nm\pm2^{j-1}$. Compute $r=W\bmod N$ and write it in exactly $n$ binary digits:

$$
r=\begin{cases}
2^{j-1}, & \text{if bag }j\text{ is heavier},\\
N-2^{j-1}, & \text{if bag }j\text{ is lighter}.
\end{cases}
$$

- **Exactly one `1`:** its position identifies the heavier bag.
- **Exactly one `0`:** its position identifies the lighter bag.

Positions are counted from the right, starting at $1$. Since $N$ has a `1` in every position, subtracting $2^{j-1}$ changes just the digit in position $j$ from `1` to `0`, with no borrowing. For $n\ge3$, a heavier case has one `1`, whereas a lighter case has $n-1\ge2$ ones. These two types cannot coincide, and the distinguished position uniquely identifies the bag. **One weighing therefore remains optimal for every $n\ge3$.**

**Example: five bags.** Take $1,2,4,8,16$ coins, so $N=31$. If the measured weight is $W=306$, then

$$
r=306\bmod31=27=(11011)_2.
$$

The only `0` is in the third position from the right, so bag $3$ is lighter. Its four sampled coins account for a deficit of four grams, giving the normal weight

$$
m=\frac{306+4}{31}=10.
$$

We decode the binary digits of the **remainder**, not those of the total weight. Also, the quotient $\lfloor W/N\rfloor$ need not equal $m$: in a lighter case it equals $m-1$.

**Why require $n\ge3$?** With two bags, the unknown normal weight makes the problem intrinsically ambiguous. Even if we knew the weights were $10$ and $11$, we could not distinguish “bag 1 is lighter, with normal weight 11” from “bag 2 is heavier, with normal weight 10.” No number of weighings can resolve that ambiguity without extra information. With one bag, there is likewise no normal reference from another bag to determine whether its coins are lighter or heavier.

## 3. Determine the weight of a coin from each bag.

**Minimum: two weighings.**

Let $a,b,c$ be the positive integer weights of a coin from the first, second, and third bags, respectively.

The idea is to encode weights using place values. If we can arrange a measurement of the form $W=a+Sb$ with $0\le a<S$, division by $S$ recovers $b$ as the quotient and $a$ as the remainder. The first weighing provides a suitable base $S$.

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

For example, if $S=30$ and $W=338$, then $338=30\times11+8$. Hence $a=8$, $b=11$, and $c=30-8-11=11$.

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

### Generalization to $n$ bags

For any fixed $n\ge2$, let the positive integer weights be $a_1,\ldots,a_n$. **Two weighings still suffice**, because we can use the first result as a base and encode several weights as its digits.

First, weigh one coin from every bag to obtain

$$
S=\sum_{i=1}^{n}a_i.
$$

Positivity gives $1\le a_i<S$ for every $i$, so each weight is a valid digit in base $S$.

Next, take $S^{i-1}$ coins from bag $i$ for $i=1,\ldots,n-1$, and none from bag $n$. In other words, the sample sizes are $1,S,S^2,\ldots,S^{n-2},0$. This gives

$$
W=\sum_{i=1}^{n-1}a_iS^{i-1}.
$$

The digits of $W$ in base $S$, read from right to left, are exactly $a_1,\ldots,a_{n-1}$. There are no carries because every weight is less than $S$. Recover them by repeated division and remainders, or directly using

$$
\boxed{
a_i=\left\lfloor\frac{W}{S^{i-1}}\right\rfloor\bmod S
\quad(1\le i\lt n),\qquad
a_n=S-\sum_{i=1}^{n-1}a_i
}.
$$

All sample sizes are finite. They may be large, but the problem places no limit on sample size or scale capacity; the quantity being minimized is the number of weighings.

To prove optimality, suppose a single weighing uses fixed counts $p_1,\ldots,p_n$. A zero count leaves the corresponding weight undetermined. If all counts are positive, the distinct weight assignments

$$
(p_2+1,1,1,\ldots,1)
\quad\text{and}\quad
(1,p_1+1,1,\ldots,1)
$$

both produce the total $p_1p_2+\sum_{i=1}^{n}p_i$. Thus one weighing cannot always determine the weights when $n\ge2$.

For $n=2$, the construction simply weighs both coins together first and one coin from bag 1 second. For $n=1$, weighing one coin once is sufficient and necessary. Therefore,

$$
\boxed{
\text{Minimum number of weighings}=\begin{cases}
1, & n=1,\\
2, & n\ge2.
\end{cases}
}
$$

The common principle is to make the measurement uniquely decodable: the first question uses the sign and size of a deviation, the second uses binary patterns after taking a remainder, and the third uses digits in a base chosen from the first measurement.
