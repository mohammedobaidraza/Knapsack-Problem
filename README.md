
git clone https://github.com/mohammedobaidraza/UHaul-problem.git
cd UHaul-problem
# This project implements a solution for the classic 0/1 Knapsack Problem using dynamic programming. It helps to determine the most valuable subset of items that can be carried in a knapsack without exceeding a given weight capacity.

### How It Works
Build the DP table:
We first build a table that stores the best possible value for each item combination and knapsack capacity. This allows us to make decisions efficiently without recalculating values.

Trace back to find the optimal subset:
After building the table, we trace back through it to find the combination of items that maximizes the value while keeping the total weight under the knapsack's capacity.

# The 0/1 <strike>Knapsack</strike> UHaul problem

Consider a collection of n items, enumerated as a set $M=\{1,2,\ldots,n\}$. Each item $i\in M$ has a value $v_i > 0$ and a weight $w_i > 0$. We want to find a subset $S\subseteq M$ such that

\begin{align}
\sum_{i\in S} v_i\ \text{is}\ \max \text{while}\
\sum_{i\in S} w_i \leq C_\max
\end{align}

where $C_\max>0$ is some weight constant, for example the capacity of a truck we use to move things.

In other words, of all $2^n$ subsets of $M$, which one is the most valuable while it doesn't exceed a specific weight?

For small values of $n$, the problem can be solved with brute force. When $n=10$, we can consider each of the $2^{10}$ subsets, rejecting those whose total weight is not below $C_\max$, then finding the one with the most value. For larger values of $n$, things may slow down.

Imagine a scenario where an international ring of art thieves plans to hit the Art Institute of Chicago. They rent a small truck that can carry up to $C_\max=500$ lbs. Of all the items from the AIC they can fit in their truck, which combination is the most valuable? The museum has over 100,000 artifacts. There are $2^{300\,000}$ combinations or about $10^{100\,000}$. If we can examine one billion combinations per second, we can eliminate approximately $10^{17}$ combinations per year. After $10^{99\,983}$ years, the international art thieves would have their answer. That's a *very* long time, considering that the universe is about $1.4\times 10^{10}$ years old.
## Can we help the thieves?
(Yes, you are paying tuition for this!)

What if we told the would-be-thieves that we can find the best combination in only $5\times 10^7$ steps instead of $10^{100\,000}$ steps?  
Instead of $10^{99\,983}$ years they can have the answer in 0.05 seconds (since we are computing 1 billion combinations per second).

Wouldn't that be worth something? Maybe 20% of their profits?

<center>

![American Gothic](https://drive.google.com/uc?export=view&id=1dUo-zLokDVRIKpSsZ4_3HROPVvDWwDlQ)
![Nighthawks](https://drive.google.com/uc?export=view&id=1dY4ecE6WZWNajY-l_ZDENj_igRGVsSUL)
![Sky Above Clouds IV](https://drive.google.com/uc?export=view&id=1dTqECkj9HV0Vm3SicGNheGptw3fl_4Mf)
![Old Guitarist](https://drive.google.com/uc?export=view&id=1dQU88AaWqTkAFu-_BLGkmDJvYsy5dL6E)

*Four emblematic paintings in the Art Institute of Chicago: American Gothic by Grant Wood; Nighthawks by Edward Hopper; Sky Above Clouds by Georgia O'Keefe, and; The Old Guitarist by Pablo Picasso. The pictures have been cropped to fit in this notebook.*
</center>

## How?

Instead of all the artifacts, let's say that $M$ is the set of all the paintigs in the Art Institute:

$$
M =
\{
  \textsf{American Gothic},\ \
  \textsf{Nighthawks},\ \
  \textsf{Sky Above Clouds IV},\ldots,\  
  \textsf{The Old Guitarist}
\}
$$

There are about 3000 paintings in the museum, so we know there are $2^{3000}$ possible combinations. At least one of them comes to about 500 lbs and has the highest value than any other combination we can carry away.

Let's imagine that somehow we found the perfect solution: a subset $S$ of  $M$ that contains the most valuable paintings whose weight does not exceed 500 lbs.

And we ask a simple question: **is the last painting listed in** $M$, **part of the solution or not?** When the thieves drive away with several paintings, is Picasso's *Old Guitarist* in the truck or not?

### Picasso's *Old Guitarist* **in** the truck

Right before grabbing *The Old Guitarist,* the thieves had found the most valuable subset of
$$M-\{\textsf{The Old Guitarist} \}$$
to fit in their truck, now fully loaded with 500 lbs of paintings. When they come across the last painting, they face a dilemma: drive away with what they already got or unload a few
paintings to make room and take the Picasso with them?

If they unload a few paintings, the capacity of the truck becomes $$500-w_\textsf{The Old Guitarist}$$
i.e. enough to fit the Picasso painting. For this new capacity, there is a different subset of $M-\{\textsf{The Old Guitarist} \}$ that is the most valuable combination of paintings with total weight $\leq 500-w_\textsf{The Old Guitarist}$.

Is the value of this different subset, plus the value of the Picasso painting, higher or lower than the value of previous subset?
### Notation

We need some notation to differentiate between the values of two subsets mentioned above.

One is the most valuable subset with weight $\leq 500$ lbs that does not include the last painting (in this example, *The Old Guitarist).* We denote the value of this subset as

$$
S(
  \underbrace{n-1}_{
    \begin{array}{r}
    \text{exclude}\\
    \text{the last}\\
    \text{painting}
    \end{array}
  },
  \underbrace{C_{\max}}_{{
    \begin{array}{l}
    \text{load the}\\
    \text{truck to}\\
    \text{capacity}
    \end{array}
    }}
  )
$$
### Take it or leave it

The decision to take or leave the last painting comes to a simple comparison between two options.


#### Leave it

There is no room in the truck for the last painting. The truck is full to capacity and the items we take are the most valuable subset of $M-\{n\}$. The total value of this option is $S(n-1, C_\max)$.

#### Take it

We make room in the truck for the last painting. Before we load the last painting, the items we take are the most valuable subset of $M-\{n\}$ that fit in the reduced capacity of the truck. The value of that subset is $S(n-1, C_\max-w_n)$. When we add the last item, the truck is at capacity and the total value of this option is $\color{maroon}{v_n}+S(n-1, C_\max-w_n)$

### Implications of $n\not\in S$

If the last item $n$ **is not** in the optimal solution $S$, then $S$ is an optimal solution to the problem with $\{1,2,\ldots n-1\}$ items as well. We can prove it mathematically (see Miscellaneous section at the end), but first let's agree that it's just common sense.
### Implications of $n\in S$

If the last item $n$ is in the optimal solution $S$, then we know that $S$ **cannot be** an optimal solution for the smaller set $\{1,2,\ldots,n-1\}$. Why? Because the smaller set does not include $n$. But $n$ is in the optimal solution $n$, which means that $S$ must be a subset of $\{1,2,\ldots,n\}$.

If we removed $n$ from $S$, then $S-\{n\}$ is an optimal solution for $\{1,2,\ldots,n-1\}$. The total weight of $S-\{n\}$ is

$$
\sum_i w_i \leq C_\max-w_n,\ \ i\in S-\{n\}
$$

and its total value is $V-v_n$, where

$$
V = \sum_{i\in S} v_i
$$
is the total value of items in $S$.

The reduction in weight above, by $w_n$, allows room to add the last item $n$ to $S-\{n\}$. The question now is: **do we want to do it?**

Since $S-\{n\}$ is an optimal solution for the smaller problem of $\{1,2,\ldots,n\}$, it has the best possible value among all subsets of $\{1,2,\ldots,n\}$ whose weight is at-or-below $C_\max-w_n$. We denote that value as

$$S(n-1, C_\max-w_n)$$

There is room for the last item $n$. If we add that item to $S-\{n\}$, the total value becomes

$$S(n-1, C_\max-w_n)\ + v_n$$

Just because we can carry $w_n$ weight however, doesn't mean that we have to take item $n$. Are we better of by taking other items whose weight comes at-or-below $w_n$ and whose value exceeds the added value of $n$?



## Formal stuff

The formula above gives the value of the optimal solution for the full set $\{1,2,\ldots, n\}$. The items included in this solution have a total weight at-or-below our maximum capacity $C_\max$.

The same principle applies however when we try to find the optimal capacity for smaller problems like  $\{1,2,\ldots, \color{green}{n-1}\}$, $\{1,2,\ldots, \color{green}{n-2}\}$, etc. Essentially, we are solving a problem with fewer items and different capacities. In general, we write

\begin{align}
 S(i, r)  = &
\begin{cases}
\color{maroon}{
S(i-1,r)},\ &\text{when}\ w_i > r\\
\max\left\{\color{maroon}{S(i-1,r),}\ S(i-1,r-w_i)+v_i\right\},\ &\text{when}\ w_i \leq r
\end{cases}\\ \\
& 0 \leq  i \leq n  \\
& 0 \leq r \leq C_\max
\end{align}

$r$ is the *residual capacity*, i.e., how much capacity is available after we take on some load.

It is straight-forward to determine the initial values of $S(i,r)$:

\begin{align}
S(0,r) & = 0\ \ (\text{There are 0 items to chose from}) &\\
S(i,0) & = 0\ \ (\text{There is no room to take aything}) &
\end{align}

With these initial values, we can begin to build the rest of the values, for example:

\begin{align}
 S(1, 1)  = &
\begin{cases}
S(0,1),\ &\text{if}\ w_1 > 1\\
\max\left\{S(0,1),\ S(0,1-w_1)+v_1\right\},\ &\text{if}\ w_1 \leq 1
\end{cases}
\end{align}

This probably evalues to 0 if $w_1 > 1$ or $v_1$ if $w_1\leq 1$. In other words if the weight of the first item is 1 (whatever weight units we use), we can take it and add its value $v_1$ to $S$.

## Implementation

To implement the <strike>Knapsack</strike> UHaul problem we need to decide what data structures you use. The data needed for this problem are the values and the weights of the items we wish to consider. The simplest approach is to use two arrays, one for values and one for weights. The arrays must be synchronized, so that the `i`-th element in both arrays related to the same item.

The length of the arrays must be the same. From that length we can tell how many items there are.

We assume that values and weights are integer numbers. The desired output is the table with all $S(i,r)$ values.

Once we have that table, we can construct the subset $S$, by finding the path that leads to the bottom-right cell $S(n,C_\max)$.

## Miscellaneous

### Proofs

**Theorem:** if S is an optimal solution for $\{1,2,\ldots,\color{maroon}{n-1,} \color{red}n\}$ and  $\color{red}n\not\in S$, then $S$ is an optimal solution for $\{1,2,\ldots,\color{maroon}{n-1}\}$ as well.

If $S$ is an optimal solution, then
$$\sum_{i\in s}w_i \leq C_\max$$
Let $V=\sum_{i\in S}v_i$ be the value of all items in $S$. Because $S$ is an optimal solution for $\{1,2,\ldots,\color{maroon}{n-1}\}$, it means that $V$ is the highest possible value of any subset of $\{1,2,\ldots,\color{maroon}{n-1}\}$ that meets the weight restriction.

**Proof.** Assume that there is another set $S^*$ that is the optimal solution for $\{1,2,\ldots,\color{maroon}{n-1}\}$. Its value $V^* = \sum_{i\in S^*}v_i$. But this contradicts the assumption that $S$'s value is the highest possible value of any subset of $\{1,2,\ldots,\color{maroon}{n-1}\}$

