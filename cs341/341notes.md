#CS 341

##Sept 14

Learn

- most of the content will be there
- find course slides there
- syllabus

Piazza will be used for discussion

Mark breakdown

- (25%) Midterm is Friday, Oct. 23, 2015, 6:30–8:00 PM
- (25%) 5 assignments due 4pm Thursdays, late assignments (within 24h) has 25% penalty

There's a required textbook that's a good reference book, so probably buy it (used)

- you probably don't actually need it though, you're only responsible for content talked about in class (textbook is more of a suppliment)

#Introduction

ANALYSIS

Analysis of an algorithm: correctness and efficiency

Correctness: Sometimes we want a formal proof that an algorithm works

Efficiency can be measured in a few ways:

- asymptotic complexity - order notation
- exact number of computations, we can see for two algorithms with the same asymptotic complexity which is actually better
- average case vs worst case
- is the algorithm the most efficient? is there a lower bound (proof) on the complexity?
- are there problems that cannot be solved efficiently? (need a lower bound proof) - this is the unsolvable NP-completeness problem
- are there problems that cannot be solved by any algorithm? (like the halting problem) - these problems are termed "undecidable"

DESIGN

Design: strategies to create new algoirthms to be correct and efficient and easy to understand

We'll be talking about:

- divide-and conquer
- greedy
- dynamic programming
- depth-first and breadth-first search

### Intro Examples

FIRST EXAMPLE: Find the max in a list

- we can prove things *correct* mathematically, often with induction
- loop invariant: at the end of each loop [some claim] - then we prove that with induction
- we analyze and find that the *complexity* is theta(n) since there's a clear loop
- more *exactly* there are n-1 comparisons
- how can we prove that n-1 comparisons is the lower bound?

proof of optimal solution (not in slides)

- model an algorithm using a graph G that has vertices 1...n
- compare A[i] and A[j] creates an edge of G
- now suppose there are at most n-2 comparisons
- therefore G has at most n-2 edges
- therefore G is not connected - let C1 and C2 be the two connected components in G
- therefore there is no way to decide if the max element is in C1 or in C2


SECOND EXAMPLE: Max-Min (find max and min in a list)

- you can prove this by induction if you wanted
- complexity is linear
- 2n-2 comparisons
- since linear was optimal for max, it can't be any lower for max+min, so linear is optimal for max-min
- the number of comparisons (2n-2) is not optimal though
- we can be more efficient by processing elements two at a time, comparing them, and then updating max and min with the smaller and larger value respectively
- this takes 3 comparisons per 2 elements, instead of 4, and now we have 3n/2-2 comparisons
- three at a time? four at a time? no improvement
- proof that 3n/2-2 is lowest bound for number of comparisons uses directed graphs, adversary argument (4-5 page proof and beyond the scope of this course)

THIRD EXAMPLE: 3SUM (insert inappropriate giggles)

- the trivial brute force way is O(n^3), WLOG we can assume i<j<k to make it a little more efficient
- we can show the lower bound is also OMEGA(n^3) so the algorithm is theta(n^3)
 - we can find the lower bound efficiency by thinking about how many times is the 'if' statement executed? 
 - we remember that i<j<k (all unique groups of 3 indices between 1 and n) 
 - so the answer is n choose 3
- on the slides we show two improvements we can make to optimize the algorithm 
- first improvement is to sort first, find i and then j, and then use binary search to find k  - this is O(logn\*n^2)
 - note we can search just from j+1 to n since i<j<k 

##Sept 16

3SUM CONTINUED

- second improvement is to find i, then look for j and k linearly, meeting somewhere in the middle to find the pair that works (or doesn't work) - this is O(n^2)

e.g.

	 i   j                   k
	-11 -10 -7 -3  2  4  8  10    S = -11

	 i       j               k
	-11 -10 -7 -3  2  4  8  10    S = -8

	 i          j            k
	-11 -10 -7 -3  2  4  8  10    S = -4

	 i             j         k
	-11 -10 -7 -3  2  4  8  10    S = 1

	 i             j     k    
	-11 -10 -7 -3  2  4  8  10    S = -1

	 i                j  k    
	-11 -10 -7 -3  2  4  8  10    S = 1

	 i               jk       
	-11 -10 -7 -3  2  4  8  10    S = 1

	j = k so while loop breaks and i increments


Analysis:

- formal proof of correctness is kinda annoying and left as an exercise
- complexity: for loop with n-2 iterations, while loop has O(n) iterations with each iteration takin O(1) time
- showing that the while loop has O(n) iterations:
 - consider the value k-j
 - initially, k=n, j= i+1 >=2, k-j <= n-2
 - in each iteration, j<-j+1 or k<-k-1 or both
 - so, k-j decreases by at least 1 each iteration
 - since the loop terminates when k-j = 0 or -1, there are at most n-1 iterations

Beyond this course

- there are subquadratic algorithms for this problem
- there's one that's O(n^2(log(logn))^2/(logn)^2)

### Review from CS 240

TERMINOLOGY

Problems

- Problem: Given a problem instance I for a problem P, carry out a particular computational task
- Problem Instance: Input for the specified problem
- Problem Solution: Output (correct answer) for the specified problem
- Size of a problem instance: Size(I) is a positive integer which is a measure of the size of the instance I

Algorithms and Programs

- Algorithm: a step-by-step process (e.g., described in pseudocode) for carrying out a series of computations, given some appropriate input --- we think about complexity or growth rate
- Algorithm solving a problem: An Algorithm A solves a problem P if, for every instance I of P, A finds a valid solution for the instance I in finite time
- Program: A program is an implementation of an algorithm using a specified computer language --- we think about running time

Running Time

- Running Time of a Program: T\_M(I) denotes the running time (in seconds) of a program M on a problem instance I
- Worst-case Running Time as a Function of Input Size: T\_M(n) =   max run time of program M on instances of size n 
- Average-case Running Time as a Function of Input Size: T\_avg(n) = (sum of all run times of program M over all instances of size n)/(number of instances of size n)

We focus on complexity - here are some really mathy definitions

- Worst-case complexity of an algorithm: Let f : Z+ → R. An algorithm A has worst-case complexity f(n) if there exists a program M implementing the algorithm A such that T\_M(n) ∈ Θ(f(n)).
- Average-case complexity of an algorithm: Let f : Z+ → R. An
algorithm A has average-case complexity f(n) if there exists a program M implementing the algorithm A such that T^avg\_M(n) ∈ Θ(f(n))

Running Time vs Complexity:

- Running time 
- can only be determined by implementing a program and running it on a specific computer
- is influenced by many factors, including the programming language, processor, operating system, etc.
- Complexity (AKA growth rate) 
- can be analyzed by high-level mathematical analysis
- is independent of the above-mentioned factors affecting running time.
- is a less precise measure than running time since it is asymptotic and it incorporates unspecified constant factors and unspecified lower order terms
- If algorithm A has lower complexity than algorithm B, then a program implementing algorithm A will be faster than a program implementing algorithm B for sufficiently large inputs


ORDER NOTATION

O-notation (<=)

- f(n) ∈ O(g(n)) if there exist constants c > 0 and n\_0 > 0 such that 0 ≤ f(n) ≤ c\*g(n) for all n ≥ n\_0
- g(n)is worst case time of f(n)
- to figure out what n\_0 is, find an intersection point --- n\_0 has to be a natural number
- note that this means if something is O(n) it's also O(n^2)

Ω-notation (>=)

- f (n) ∈ Ω(g(n)) if there exist constants c > 0 and n\_0 > 0 such that 0 ≤ c\*g(n) ≤ f(n) for all n ≥ n\_0
- opposite of O-notatoion - g(n) is best case time of f(n)

Θ-notation (=)

- f (n) ∈ Θ(g(n)) if there exist constants c1, c2 > 0 and n\_0 > 0 such that 0 ≤ c1\*g(n) ≤ f (n) ≤ c2\*g(n) for all n ≥ n\_0
- grows at same rate

o-notation (<)

- f (n) ∈ o(g(n)) if for ALL constants c > 0, there exists a constant n\_0 > 0 such that 0 ≤ f(n) < c\*g(n) for all n ≥ n\_0

ω-notation (>)

- f (n) ∈ ω(g(n)) if for ALL constants c > 0, there exists a constant n\_0 > 0 such that 0 ≤ c g(n) < f (n) for all n ≥ n\_0

Preferred notation

- you can say f(n) is O(g(n))
- you can say f(n) ∈ O(g(n)) -- we use ∈ because O(g(n)) is a set of functions with run time <= the run time of g(n)
- the text uses f(n) = O(g(n)) but this doesn't really make sense, so this is not preferred (but you probably won't lose marks)

Example 1:

	f(n) = n^2-7n-30   g(n) = n^2
	Show f(n) ∈ O(g(n))

	we want to show 0 <= n^2 - 7n - 30 <= cn^2 for some c>0, n_0>0

	note that n^2 - 7n - 30 = (n-10)(n+3)
	so 	n^2 - 7n - 30 >= 0 if n >=10

	let c = 1
	then n^2 - 7n - 30 <= 1*n^2 for n >= 0

	so if n_0 = max{0, 10} = 10, then both inequalities hold

	then 0 <= n^2 - 7n - 30 <= cn^2  for c = 1, n > 10


Example 2:

	f(n) ∈ Ω(g(n))

	here we want 0 <= cn^2 <= n^2 - 7n - 30 for some c>0, n_0>0

	1) pick an 'appropriate' value for c
	2) determine n_0

	If we want this inequality to be satisfied, c must be less than 1. So let's say c = 0.5

	we want 0.5n^2 <= n^2 - 7n - 30
	n^2 <= 2n^2 - 14n - 60
	n^2 - 14n - 60 >= 0
	take n_0 to be the larger root and then this will always hold

Technique for order notation:

Tip #1: We can use limits (and sometimes **l'hopitals rule**), find lim to inf of f(n)/g(n)

- if limit is 0, g is bigger
- if limit is inf, f is bigger
- if limit is constant, they're equal

Example 3:

 	f(n) = (ln(n))^2  g(n) = n^(1/2)

 	  lim n->inf (ln n)^2 / n^(1/2) 
 	= lim n->inf 2(ln n) / n*1/2*n^(-1/2)
 	= lim n->inf 4(ln n) / n^1/2
 	= lim n->inf 4 / n*1/2*n^(-1/2)
 	= lim n->inf 8/n^(1/2)
 	= 0

 	therefore (ln n)^2 is o(sqrt(n))

 	more generally, (ln n)^c is o(n^delta) for any c>0, delta>0

Example 4:

	f(n) = n^2 - 7n - 30
	g(n) = n^2

	  lim n->inf n^2-7n-30/n^2
	= lim n->inf (1 - 7/n - 30/n^2)
	= 1

	so f(n) is theta(g(n))

Example 5:

	f(n) = (3+ (-1)^n) * n
	g(n) = n

	f(n) = 4n if n is even
	     = 2n if n is odd

	lim n->inf f(n)/g(n) does not exist

	but f(n) is in theta(g(n))

	we want to show c1 * g(n) <= f(n) <= c2 * g(n) for some c1, c2
	let c1 = 2, c2 = 4, and... we're done

Example 6:

	f(n) = n * abs(sin(n*pi/2)) + 1
	g(n) = sqrt(n)

	abs(sin(n*pi/2)) = 0 if n is even, 1 if n is odd

	f(n) = 1 if n is even, n+1 if n is odd

	g(n) = sqrt(n)

	complexity of f, g are not comparable


Relationships between Order Notations (these make immediate sense if you think about the mathematical inequalities)

- f(n) ∈ Θ(g(n)) ⇔ g(n) ∈ Θ(f(n)) --- ie if f < g, g > f
- f(n) ∈ O(g(n)) ⇔ g(n) ∈ Ω(f(n)) 
- f(n) ∈ o(g(n)) ⇔ g(n) ∈ ω(f(n))
- f(n) ∈ Θ(g(n)) ⇔ f(n) ∈ O(g(n)) and f(n) ∈ Ω(g(n)) 
- f(n) ∈ o(g(n)) ⇒ f(n) ∈ O(g(n))
- f(n) ∈ ω(g(n)) ⇒ f(n) ∈ Ω(g(n))

Algebra of Order Notations

- Maximum rules: Suppose that f(n) > 0 and g(n) > 0 for all n ≥ n0
 - O(f (n) + g(n)) = O(max{f (n), g(n)}) 
 - Θ(f (n) + g(n)) = Θ(max{f (n), g(n)}) 
 - Ω(f (n) + g(n)) = Ω(max{f (n), g(n)})
- Summation rules (applies to finite sums)
 - O(sum f(i)) = sum O(f(i))
 - theta(sum (f(i)) = sum theta(f(i))
 - omega(sum (f(i)) = sum omega(f(i))
 - e.g. sum i=1..n O(i) = O(sumi=1..n i) = O(n(n+1)/2) = O(n^2)

## Sept 21

### Formulae

####Sequences

 - we talked about arithemetic, geometric, arithmetic-geometric, harmonic sequences
 - a, d, r > 0
 - geometric depends on value of r (<, >, or = 1)
 - sum i=1..n i^c is theta(n^c(+1))
 - sum i=1..inf 1/i^2 = pi^2/6 which is theta(1)

Example of computing Arithmetic-Geometric sequence:

	  1x2 + 3x2^2 + 5x2^3 + ... + (2n-1)*2^n
	= 1x2 + 1x2^2 + 1x2^3 + ... + 1x2^n
	   +    2x2^2 + 2x2^3 + ... + 2x2^n
	          +     2x2^3 + ... + 2x2^m
	          ............
	              ............. + 2x2^n 

	whoa! now each row is a geometric sequences, compute each sum separately
	  2^(n+1) - 2
	+ 2(2^(n+1) - 4)
	+ 2(2^(n+1) - 8)
	...
	+ 2(2^(n+1) - 2^n)
	= (2n-1)*2^(n+1) - (the first 2 b/c that line isn't x2) - another geometric sequence
	= (2n-1)*2^(n+1) - 2 - 2(2^(n+1) - 4)

####There's a slide with misc. formulae

Proving a^(log\_b(c)) + c^(log\_b(a))

- let x = log\_b(c) so b^x = c
- let y = log\_b(a) so b^y = a
- we want to show a^x is equivalent to c^y
- a^x = (b^y)^x = b^yx = b^xy = (b^x)^y = c^y

**Exercise: Prove log\_b(a) = log\_c(a)/log\_c(b)

why is this formula useful?

- replace a by n and b, c are constant
- then log\_c(n) = lob\_c(b) \* log\_b(n)
- log function = constant \* log function
- so log\_c(n) = theta(log\_b(n))
- this shows that all log functions have the same growth rate
- this formula is also helpful for changing base of logarithms (like with your calculator if there's no button for your base)

Note that n! is theta(n^(n+1/2)\*e^-n)

- this is theta((n/3)^n\*sqrt(n)) which is quite bad
- derviced from sterling's formula
- ln(n^(n+1/2)\*e^-n) = (n+1/2)lnn - n  which is nlogn

###Complexities sorted by increasing growth rate

- polynomial growth (good):
 - theta(1)
 - theta(logn)
 - theta(sqrtn)
 - theta(n)
 - theta(n^c) c>1, constant
- not polynomial or exponential, beyond scope of course
 - theta(n^(sqrtn\*logn)) graph isomorphism
 - theta(e^(c \* (logn)^(1/3) \* (loglogn)^(2/3))) number field seive (factoring)
- exponential growth (bad)
 - theta(1.01^n)
 - theta(2^n)
 - theta(c^n)
 - theta(n!)
 - ...
 - theta(n^n)

###Techniques

For algorithm analysis

- Use theta-bounds throughout the analysis and thereby obtain a theta-bound for the complexity of the algorithm.
- Prove a O-bound and a matching omega-bound separately to get a theta-bound. Sometimes this technique is easier because arguments for O-bounds may use simpler upper bounds (and arguments for omega-bounds may use simpler lower bounds) than arguments for theta-bounds do.

For Loop Analysis

- Identify elementary operations that require constant time (denoted theta(1) time)
- The complexity of a loop is expressed as the sum of the complexities of each iteration of the loop.
- Analyze independent loops separately, and then add the results: use “maximum rules” and simplify whenever possible.
- If loops are nested, start with the innermost loop and proceed outwards. In general, this kind of analysis requires evaluation of nested summations.

Example 1:

![loopanal1](loopanalysis1.png)

- each iteration of the inner for loop takes theta(1)
- i iterations of inner for loop so theta(i) for inner for loop
- outer loop is sum i=1..n theta(i) = theta(sumi=1..n i) = theta(n^2)
- so we have theta(1) + theta(n^2) that we just calculated + theta(1) which is theta(n^2)

Second example with separate computation of upper and lower bounds

![loopanal2](loopanalysis2.png)

- algorithm
 - i = 1..n
 - j = i..n
 - k = i..j
- upper bound (more iterations than algorithm)
 - i = 1..n
 - j = 1..n
 - k = 1..n
 - easy O(n^3)
- lower bound
 - note that 1 <= i <= k <= j <= n always
 - let i = 1..n/3
 - let j go from 2n/3 + 1 .. n
 - let k go from n/3 + 1 .. 2n/3
 - this is a subset of the iterations of the algorithm
 - we get omega(n^3/26) aka omega(n^3)

Second example with just finding the theta running time

- innermost loop 
 - k=i..j
 - j-i+1 iterations (don't forget +1!)
 - time = theta(j-i+1)
- total time is sum i=1..n sumj=i..n theta(j-i+1) = theta(sumi=1..n sum j=1..n (j-i+1)) = ...... = theta(n^3)

##Sep 23

Third loop analysis 

![loopanal3](loopanalysis3.png)

- each iteration of the whole loop takes theta(1) time
- for a given value of i, the while loop requires log\_2(i) iterations
- so inner while loop takes theta(log(i)) time
- so overall complexity is sum i=1..n theta(log(i))
- = theta(sum i=1..n logi)
- = theta(log of product i=1..n i)
- = theta(logn!)
- = theta(nlogn) - given in the formulae slide

###Divide and Concur

####Recurrence relations

Defining it

- recurrence relation: a formula that expresses a general term a\_n in
terms of one or more previous terms a\_1,...,a\_(n-1)
- also specifies initial values starting at a\_1
- solving a recurrence relation means finding a formula for a\_n that does not
involve any previous terms 
- there are many methods of solving recurrence relations
 - guess-and-check 
 - recursion tree method (looked at a lot in this class)

Guess and Check

1. Tabulate first few values using the recurrence relation
2. Guess that the solution an has a specific form, involving undetermined constants.
3. Use first few terms to determine specific values for the unspecified constants.
4. Use induction to prove your guess for an is correct.

Guess and Check example

- T(0) = 4, T(n) = T(n-1) + 6n - 5 if n>=1
- first few: 4, 5, 12, 25
- we graph it and guess prabola
- so try to find constants a, b, c in an^2 + bn + c
- there are three unknowns, so we need three equations (use first three terms)
 - c = 4
 - a + b + c = 5
 - 4a + 2b + c = 12
 - we find a = 3, b = -2, c = 4

then we need to prove:

	initial case n = 0: 
	T(0) = 4 (given)
	3*0^2 - 2*0 + 4 = 4
 
 	induction assumption: formula is correct for n = j-1 (j>=1)
 	T(j) = T(j-1) + 6j - 5 (recurrence relation)
 	     = 3(j-1)^2 - 2(j-1) + 4 + 6j -5 (from assumption)
 	     = ... (math)
 	     = 3j^2 - 2j + 4
 	therefore by the principle of mathematical induction, the formula is true for all n>=0

Recursion Tree Method

- we assume n = 2^j
- consider the sequence T(1), T(2), T(4), T(8) ...
- we have a tree T(n) with root cn and two children which are T(n/2)
- this works based off of something we see in Mergesort
 - T(n) = 2T(n/2) + cn if n>1 is a power of 2, and T(n) = d if n=1
- similarily T(n/2) = 2T(n/4) + c(n/2)
- the 'value' of a tree is the sumof values of each node

Example: n = 2^j

	imagine a tree:
	root:       n = 2^j,   level is j,   #nodes is 1,   node value is c*2^j 
	next level: n = 2^j-1, level is j-1, #nodes is 2,   node value is c*2^(j-1)
	....
	near end :  n= 2^1,    level is 1,   #nodes is 2^(j-1), node value is c*2 
	last row:   n=2^0=1 ,  level is 0,   #nodes is 2^j, node value is d

using this:

- values at each level is c\*2^j for every level except d\*2^j on the last level
- therefore, T(2^j) = j\*c\*2^j + d\*2^j
- ^ this is an exact formula and it is a mathematical proof!
- if n = 2^j, j = log\_2(n)
- T(n) = dn + logn\*c\*n
- T(n) is theta(nlogn)

####Master Theorem

- provides a formula for the solution of many recurrence relations typically encountered in the analysis of algorithms.

The following is a simplified version of the Master Theorem:

![MT](mastertheorem.png)

y is a real number >=0

Example:

- T(n) = 2T(n/2) + theta(n)
- a=3
- b=2
- so x=log\_2(3) =approx 1.59
- y = 1
- so x > y
T(n) = theta(n^log\_2(3)) =approx theta(n^1.59)

Example (mergesort):

- a = 2
- b = 2
- y = 1
- x = log\_2(2) = 1
- x = y, so
- T(n) = theta(nlogn) like we got before

Proof of the theorem:

- we can make a chart like before

![MTtable](MTtable.png)


- T(n) is a lot more complicated now, but we can make a nice sum out of it, which turns out to be a simple geometric sequence
- we can also clean up how many variables there are, getting rid of a and b (see slides)
- three cases:
- r > 1:
 - sum i=0..j-1 r^i is theta(r^j)
 - then T(n) = theta(dn^x + c\*n^y\*r^j) 
 - r^j = b^(x-y)^j = (b^j)^(x-y) = n^(x-y)
 - so we have theta(n^x + n^y\*n^(x-y)) = theta(n^x + n^x) = theta(n^x)
- we won't do the others

Complexity of T(n):

- heavy leaves means that 
 - the value of the recursion tree is dominated by the values of the leaf nodes.
 - y < x so r>1 -- T(n) is theta(n^x)
- balanced means that
 - the values of the levels of the recursion tree are constant (except for the last level)
 - y=x so r=1 -- T(n) is theta(n^x\*logn)
- heavy top means that
 - value of the recursion tree is dominated by the value of the root node
 - y>x so r < 1 -- T(n) is theta (n^y)


##Sep 28

Modified genereal version of Master Theorem

- can use wehen we don't have +n^y 

![genMT](GenMT.png)

Example: T(n) = 3T(n/4) + nlogn

- a = 3, b = 4, sp x = log\_4(3) approx= 0.793
- we will apply case 3 of the modified general version of the MT because:
- fn)/n^(x+epsilon) = nlogn / n^(0.793-epsilon) = n^(0.207-eps) \* logn
- let epsilon = 0.1
- then f(n) / n^(x-eps) = n^1.07logn which is clearly an increasing function of n
- so T(n) is theta(nlogn)

Example: T(n) = 2T(n/2) + nlogn

- we can't use MT to oslve this recurrence
- since a=2, b=2, x = log2(2) = 1, cases 1 and 2 don't apply
- case 3? 
 - f(n)/n^(x-epsiolon) = nlogn/n^(1+eps) = logn/n^eps 
 -  which is not an increasing fucnction for any epsilon > 0
 - logn is o(n^epsilon) for any epsilon > 0 because lim n->inf of logn/n^eps = 0
- so MT cannot be applied!

We can solve this recurrence using the recursion tree method!

- let n = 2^j
- T(n) = 2T(n/2) + nlog\_2(n)
- t(1) = 1 (base case - this comes from initial condition given that T(1) = 1)
- tree:
 - level j has 1 node, value nlogn or 2^j\*j
 - level j-1 has 2 nodes, value 2^(j-1)\*(j-1)
 - level j-2 has 4 nodes, value 2^(j-2)\*(j-2)
 - ...
 - level 1 has 2^(j-1) nodes, value 2^1\*1
 - level 0 has 2^j nodes, value 1
- values on the levels are j\*2^j, (j-1)\*2^j, (j-2)\*2^j, ... 2^j and level 0 is also 2^j
- T(2^j) = 2^j[j + j-1 + j-2 + ... + 1] + 2^j = 2^j \* [j(j+1)/2 + 1]
- since n = 2^j, j = log\_2(n) so T(n) is theta(n\*log^2(n))

Example: T(n) = T(floor(n/2)) + T(floor(n/3)) + n

- base cases: T(1) = 1, T(2) = 2
- this isn't in a form where we can use the master theorem
- we'll guess T(n) <= cn for all n >= 1, and c>0 is a constant
- we will specify an appropriate value for c later

we will prove this by induction on n

	base case: T(1) = 1 <= c\*1 and T(2) = 2 <= c\*2
	
	Induction assumption: T(n) <= cn for all n < m

	We now prove that T(m)  <= cm

	T(n) = T(floor(m/2)) T(floor(m/3)) + m 	(from rec rel) 
	<= c*floor(m/2) + c*floor(m/3) + m  	(from assumption)
	<= cm/2 + cm/3 + m
	= c(5/6)m + m 
	= m(5c/6 + 1)
	<= ? cm

	This will be true if 5c/6 + 1 <=c
	                iff  1 <= c/6
	                iff  c >= 6

	so if c = 6 then the proof is valid and we have T(n) <= 6n for all n>=1 by induction

Recursion tree approach

- first level has one node that is n
- second level has values n/2 and n/3 which total to 5/6 n
- third level has n/4 and n/6 and n/6 and n/9 totals to 25/36n = (5/6)^2 n
- each level has (5/6)^(level-1) * n
- computing the infinite sum of n(1 + 5/6 + (5/6)^2 + ... infinitely)
- this is a geometric sequence with ratio 5/6 < 1
- so the sum is 6n, so T(n) <= 6n
- notice this is not that accurate/rigorous because we took out the floor function, and the tree might not go on forever in certain places, etc.

###The Divide-and-Conquer Design Strategy

Definitions

- divide: Given a problem instance, construct one or more smaller problem instances(subproblems). Usually, we want the size of these subproblems to be small compared to the size of the instance, e.g., half the size.
- conquer: solve each subproblem recursively, obtaining solutions
- combine: Given the solutions, use an appropriate combining function to find the solution to the original problem 
 - the combine part is often the hardest part to think of

Example: merge sort

- divide: splitting A into subarays (first half and second half)
- conquer: run mergesort on left and right half
- combine: after halfs are sorted, use function merge to merge the two sorted arrays into a single sorted array.

![mergesort](mergesort.png)

- divide takes theta(n), conquer takes theta(T(ciel(n/2)) + T(floor(n/2))) and combine takes theta(n)
- this gives us a recurrence relation

Sloppy and Exact Recurrence Relations

- It is simpler to replace the theta(n) term by cn, where c is an unspecified constant. The resulting recurrence relation is called the exact recurrence
 - The Master Theorem provides the exact solution of the recurrence when n = 2^j (it is in fact a proof for these values of n).
 - We can express this solution (for powers of 2) as a function of n, using ⇥-notation.
- If we then remove the floors and ceilings, we obtain the so-called
￼￼sloppy recurrence
- The exact and sloppy recurrences are identical when n is a power of two. Further, the sloppy recurrence makes sense only when n is a power of two.
- It can be shown that the resulting function of n will in fact yield the complexity of the solution of the exact recurrence for all values of n.
- This derivation of the complexity of T(n) is not a proof, however. If a rigourous mathematical proof is required, then it is necessary to use induction along with the exact recurrence.

The Max-Min Problem

- Let’s design a divide-and-conquer algorithm for the Max-Min problem. 
- Divide: Suppose we split A into two equal-sized subarrays, AL and AR.
- Conquer: We find the maximum and minimum elements in each subarray recursively, obtaining maxL, minL, maxR and minR.
- Combine: Then we can easily “combine” the solutions to the two subproblems to solve the original problem instance: max = max{maxL,maxR} and min = min{minL, minR}
- The recurrence relation describing the complexity of the running time is T (n) = 2T (n/2) + theta(1)
- The master theorem shows that T(n) is theta(n)
- we can also count the exact number of comparisons done by the alogirthm, obtaining the (sloppy) recurrence C(n) = 2C(n/2) + 2, C(2) = 1
 - for n a power of 2, the solution is C(n) = 3n/2 - 2, so the divide and conquer alogirhtm is optimal for these values of n (see slide 26)

##Sep 30

###Non-dominated points problem

- Given two points (x1, y1), (x2, y2) in the Euclidean plane, we say that (x1, y1) dominates (x2, y2) if x1 
- Problem Instance: a set of n points in a plane
- Problem Question: find all non dominated points in the se (all the points that are not dominated by any other point)
- this has a trivial theta(n^2) algorithm to solve it, based on comparing all pairs of points in S. Can we do better? (of course we can)
- thing of this visually - the non dominated points will form a staircase -- (think of a staircase from top left to bottom right, each point is the edge of a stair) -- all non dominated points are lower or left of the points of the staircase, so they're under the staircase

Using divide and conquer:

- Suppose we pre-sort the points in S with respect to their x-co-ordinates. This takes time theta(n log n).
- Divide: Let the first n/2 points be denoted S1 and let the last n/2 points be denoted S2.
- Conquer: Recursively solve the subproblems defined by the two instances S1 and S2.
- Combine: Given the non-dominated points in S1 and the non-dominated points in S2, how do we find the non-dominated points in S?
 - Observe that no point in S1 dominates a point in S2.
 - Therefore we only need to eliminate the points in S1 that are dominated by a point in S2. This can be done in time O(n).

![staircase](staircase.jpg)


Complexity:

- presort O(nlogn)
- T(n) = 2T(n/2) + O(n) 
 - the while loop to merge is the O(n), also note this is a sloppy relation
 - we recognize this as O(nlogn) from the Master Theorem
- so overall is O(nlogn) + O(nlogn) = O(nlogn)

###Closest Pair problem

- Instance: a set Q of n distinct points in the Euclidean plane
- Find: Two distinct points that are closests to each other
- brute force is quadratic (compare points to all others), let's do better
- we divide and find the closest pair on the left and closest on the right (deltaL, deltaR)
- when we combine, we determine if there is a pair of points (one on the left, one on the right) that are closer than either of our closest pairs so far). There are potentially n/2 \* n/2 = n^2/4 such pairs to check, which takes quadratic time which we're trying to avoid
- let delta be the min of deltaL and deltaR - we only wish to consider points where x coord is within delta of he vertical splitting line - the 'critical strip' of width 2delta
- if there is a pair of points whose distance is less than delta, then both points will be in this critical strip
- so we discard al points that are not in the critical stripo. the points that remain are 'candidate points'. how many points are there? there could be all n points!
- next step is to sort candidate points on the y axis

Lemma: suppose candidate points are sorted with respect to y coordinates. Suppose that R[j] and R[k] have distance less than delta, where j < k. Then k <= j+7

- rectangle R that is width of strip and height delta, divided into 8 squares of side delta/2

![rectangle](rectangleR.jpg)

- two points in the same square can be at most the distance of the diagonal apart - that value is delta/2 * root2 < delta - but this is impossible because we're on one side of the middle line of the critical section where the smallest distance was delta
- so only one point max per rectangle
- so R[j+8] is above the rectangle (because there are at most one point per square) so its distance from R[j] is more than delta
- so now we can say checking the trip is O(n) because each iteration of the inner loop is constant (only 8 times)

Complexity:

![step1](selectcandidates.png)

![step2](checkstrip.png)

![algo](closestpair.png)

- presorting is O(nlogn)
- T(n) = 2T(n/2) + O(n) to select candidates + O(nlogn) to sort by y + O(n) to check strip
- simplifying, T(n) = 2T(n/2) + O(nlogn)
- from last lecture, we know T(n) is O(n(logn)^2)
- can we improve this? can we get O(nlogn)? 
- we'd need recurrence to be T(n) = 2T(n/2) + O(n), so we'd need to eliminate the sort y in the recursive part of the algorithm
- two approaches: 
 - perform sorting y as a precomputation. so we have to think about sorted in the x way and y way in different place and be careful - it's a bit messier to code actually
 - or replace sorting by a merge of two sorted lists that we get from the recurrence call, and merging is just O(n)
  - think of it as precondition that Q[l] ... Q[r] is sorted by x coordinates, and post condition that Q[l] ... Q[r] is sorted by y coordinate (part of the work the algorithm does)
  - if l = r, it is trivially true. if l < r, the sublists are sorted -> so we can merge in linear time and get the post condition

##Oct 5

###Multiprecision Multiplication

Usually we think of integer multiplication as taking theta(1) time - this assumes that integers are a fixed size, e.g. 32 bits

We are interested in the bit complexity 

- determining the complexity as a function of k, the number of bits in the binary representation
- size(X) = k = # bits (not the value of X)
- X approx= 2^k, k = log\_2(x)

Multiprecision Multiplication

- instance: two k-bit positive numbers x and y
- question: compute the 2k bit positive integer z = xy

"grade school" multiplication

- in base 10 
- we'd have two numbers times each other
- multiply one digit at a time
- shift result over a digit on each line as we move through the digits of the second number
- in binary, it's the same as base 10

Time to add everything together

- we do a k-1 shifts over k rows, so there are numbers with up to 2k-1 digits (if you consider the space on the end to be 0s, since a k bit integer can be shifted k-1 to the left)
- so to add these integers, each addition has <=k integers, each of which has <=2k-1 bits, so time is O(k^2)

Divide and Conquer

Finding the algorithm

- x = x\_l x\_r (divide digits in half) = 2^(k/2)x\_l + x\_r
- y = y\_l y\_R (divide digits in half) = 2^(k/2)y\_l + y\_r
- e.g. in base 10:
 - x = 4655 = 10^2\*46 + 55
 - y = 1232 = 10^2\*12 + 32
- xy = (2^(k/2)\*x\_l + x\_r)(2^(k/2)\*y\_l + y\_r)
- xy = 2^k(x\_l)(y\_l) + 2^(k/2)\*(x\_l\*y\_l + x\_r\*y\_l) + x\_r\*y\_r
- this gives us 4 subproblems (x\_l\*y\_l, x\_l\*y\_r, x\_r\*y\_l, and x\_r\*y\_r), each with size k/2, then shifting and adding

![code](notsofastmultiply.png)

Run time

- assume k is a power of 2, we use a recurrence relation
- T(k) = 4T(k/2) + theta(k)
- the theta(k) is from the additons of k (maybe shifted up to k more) digit numbers
- MT: a=4, b=2, y=1, x = log\_b(a) = log\_2(4) = 2
- x > y so T(k) is theta(k^x) = theta(k^2)
- no improvement over gradeschool algorithm!

Improvement? try reducing number of subproblems from 4 to 3

- T(k) = 3T(k/2) + theta(k)
- so T(k) would be theta(k^log\_2(3)) = theta(k^1.59) -- (this was an example from a previous lecture)

Reducing subproblems

- let's make xlyr + xryl from 2 subproblems to a single subproblem
- how about (xl + xr)(yl + yr) - xlyl - xryr
- = xlyl + xlyr + xryl + xryr - xlyl - xryr (some stuff cancels)
- = xlyr + xryl 
- so now we only have three subproblems: (xl + xr)\*(yl + yr), xl\*yl, and xr\*yr

Doing better? (outside the scope of this course)

- Toom Cook came up with a way to split x and y into 3 parts and do 5 multiplications (this is not that intutiive and we didn't say how to do it) and it's theta(k^log\_3(5)) which is approx theta(k^1.47)
- in 1971, Schontage-Strussen came up with an O(k\*logk\*loglogk) algorithm based on fast fourier transform
- in 2007, Furer came up with O(klogk\*2^O(log\*(k))
 - log\* is the inverse of the ackerman function and grows realllly slowly

###Matrix Multiplication

Matrix multipliation

- A = (a\_ij) B = (b\_ij) C = (c\_ij)
- c\_ij = sum k=1..n (a\_ik \* b\_kj) = (row i of A)\*(col j of B)
- here: multiplication of integers takes theta(1) time
- To compute c\_ij we need n multiplications and n-1 additions - theta(n) time
- n^2 entries in C -> total time is theta(n^3)

Problem Decomposition

- remember math 136? great, neither do I
- there's a property that's hard to type out, but see slide 82, where you can divide the matrices into four sections of n/2 by n/2 matrices, and do some multiplication and addition, and then put them back together to get the same thing as if you did the multiplication all together -- so pretty much a divide and conquer thing
- T(n) = 8T(n/2) + theta(n^2)
 - n^2 comes from four additions of n/2 by n/2 matrices
- MT: a=8, b=2, y=2, x = log\_2(8) = 3
- x > y so T(n) is theta(n^3)
- no improvement over naive algorithm!

Improvement

- reduce the number of subproblems from 8 to 7
- so we don't know ~how~ we got the 7 subproblems, but we can show that they work sooo :p
- verifying:
 - we want r = ae + bg
 - r5 + p4 - p2 + p6 = (a+d)(e+h) + d(g-e) - (a+b)h + (b-d)(g+h) = .... cross things out = ae + bg = r
 - I'm definitely not typing this all out, but yeah it works
- best improvment found: Coppersmith Wingrad (1990) O(n^2.376)

###Selection

Problem 

- instance: an array of n distinct integer values and an integer k between 1 and n inclusive
- find: the kth smallest integer in array A
- the problem Median is the special case of Selection where k = ciel(n/2)

Simple algorithms

- sort A then return A[k] - this is O(nlogn) no matter what k is
- modified selection sort, but only for k iterations of finding smallest element and moving it to the front - this is O(kn) which is great for small k, but quadratic for k=n/2
- modified heap sort
 - build a heap - O(n) if we do heapify all at once
 - do k deletemin operations - O(klogn)
 - total is O(n + klogn)
 - for small k this is linear for k=n/2 it's O(logn) (a good balance between the pros/cons of the last two algorithms and their complexities)

Our goal is a linear algorithm for any k

##Oct 7

Recall quicksort

- we have a pivot, often the last element
- restructure the array so anything bigger than pivot is after the pivot, and smaller is before
- this restructuring per pivot is O(n)
- quicksort does the restructuring, then two recursive calls on the smaller-than and larger-than parts
- the average complexity if theta(nlogn)
- worst case is if the pivot always is on the end, and that's theta(n^2)

QuickSelect

- we only have to recurse on the left side (if that's where the element we want to select is) or the right side
- if the element is in the position we want, we don't even have to recurse

Average case analysis of QuickSelect

- We say that a pivot is good if posn is in the middle half of A. The probability that a pivot is good is 1/2.
- If a pivot is good, then we're recursing on a section less than 3n/4 
- On average, after two iterations, we will encounter a good pivot. 
- so T(n) <= T(3n/4) + theta(n)
 - the theta(n) accounts for 2 restructing operations (each linear time)
- It follows from MT that the average-case complexity of the QuickSelect is linear

What we want: worst case theta(n)

- suppose n = 5 x odd# = 5(2x+1) = 10x + 5
- so we have an odd number of groups of 5 elements
- m\_i is median of each section of 5 elements B\_i
 - compute these non recursively, e.g. find min, next smallest, next smallest until m\_i
- take M to be the array of medians, and then find it's median recursively (quickselect) - THIS is the pivot
- e.g.  
 - 1 10 5 8 21 -- 34 6 7 12 23 -- 2 4 30 11 25
 - medians: 8 12 11
 - pivot is 11
- we recurse in the same way using the pivot

![mom](momsteps.png)
![code](momcode.png)

Analysis

- first we will show y, the pivot we find with this algorithm, is a 'good' pivot
- then we will use a recurrence relation to determine the *worst* complexity

Claim 1: the number of elements of A that are > y is at most 7n/10, and the number of elements of A that are < y are at most 7n/10

- assume n = 5(2r+1)
- diagram -> "conceptual aid" with 2r+1 rows of each group of 5 elements, and y is in the middle as the median of each median
- each row is a Bi sorted in increasing order from left to right
- medians are sorted in increasing order, from top to bottom
- so the top left quadrant has elements all <= y
- therefore the number of elements <=y is at least 3(r+1) 
 - the r+1 is the top half of the rows including y, and the 3 is the left half of the 5 elements, including the column with y
- the proportion of elements <=y is at least 3(r+1)/(10r+5) which is about 3/10
- therefore the proportion of elements >y is at most 7/10

Claim 2: T(n) <= T(n/5) + T(7n/10) + theta(n)

- the n/5 is from step 6 
- the 7n/10 is stop 9 or 10 (the recursion of subset of elements)
- finding the medians and the rest takes theta(n)
- this is a sloppy recurrence
- we can do a recurrence tree
 - n with children T(n/5) and T(7n/10)
 - the n/5 node has (n/5)(1/5) and (n/5)(7/10) children 
 - similariy, we keep multiplying left by 1/5 and right by 7/10
 - so the first level sums to n(1/5 + 7/10) = 9n/10
 - second level sums to (9/10)^2 n
 - so we have a geometric sum of n(1 + 9/10 + (9/10)^2 + ... )
 - r = 9/10 < 1 so we have sum equalling 10n so it's theta(n)
- note that we're not being precise here - to be mathematically precise, T(n) <= T(floor(n/5)) + T(floor(7n+12/10)) + theta(n)
 - the +12 comes from n = 10r + 5 + theta where 0<=theta<=9 (I asked why it's 12, it's a messy thing about where those extra elements could lie)

##Part 4 of the course: Greedy Algorithms

###Optimization Problems

- Problem: Given a problem instance, find a feasible solution that maximizes (or minimizes) a certain objective function.
- Problem Instance: Input for the specified problem.
- Problem Constraints: Requirements that must be satisfied by any feasible solution.
- Feasible Solution: For any problem instance I, feasible(I) is the set of all outputs (i.e., solutions) for the instance I that satisfy the given constraints.
- Objective Function: A function f : feasible(I) -> non-neg real numbers. We often think of f as being a profit or a cost function.
- Optimal Solution: A feasible solution X in the set of feasible(I) such that the profit f(X) is maximized (or the cost f(X) is minimized).


------
missed two days of class for a conference - [see another student's notes](http://anthony-zhang.me/University-Notes/CS341/CS341.html#section-8)

- the knapsack proof doesn't convince me (doesn't seem very rigorous), I'll look at it more later to see

##Oct 21

###Stable Marriage problem ct'd

- the number of iterations is <= n^2, but in actually <= n^2 - n + 1 (the tight upper bound)
- average # iterations is theta(nlogn) 
- these two numbers are for interst, not proved

complexity: what is the complexity of each iteration? each while loop

What data structures should we use?

- we keep track of the matched pairs using two arrays 
 - Match1[mi] = wj, and Match2[wj] = mi IFF {mi, wj} is in Match
- to quickly identify an unengaged man, use a queue or stack (the order doesn't really matter FIFO or LIFO) to store all unegaged men - so O(1) lookup time
- each man's preference list is a linked list or array (static, created at beginning of algorithm)
- women's preferences are stored in an nxn array - each women's preference is R[wj, mi] = l if mi is the lth favourite man of wj (static, constructed at beginning of algorithm)

complexity

- with these data structures, each iteration of the loop takes O(1) time
- initialization takes theta(n^2) to construct R
- there are O(n^2) iterations, O(1) time per iteration --> O(n^2)
- so total time O(n^2)

fibinacci numbers

- f0, f1, f2, f3 ...
- 0, 1, 1, 2, 3, 5, 8, 13, 21 ...
- recurrence relation 
 - f0 = 0
 - f1 = 1
 - f\_n = f\_{n-1} + f\_{n-2} if n >=2
- input n, compte f\_n

badfib 

- recurse to find fib(n-1) and then fib(n-2)
- think about the tree - with n-1 to the left and n-2 to the right - this is getting really big! and there's a lot of repetition
- properties of this recursion tree
 - there are f\_n leaf nodes having the value 1
 - there are f\_{n-1} leaf nodes having the value 0
 - f\_n + f\_{n-1} = f\_{n+1} leaf nodes
 - # interior nodes = #leaf-nodes -1 = f\_{n+1} - 1
 - so the total #nodes = 2f\_{n+1} - 1!
 - this is concerning because the complexity is omega(# nodes) = omega(2f\_{n+1}-1) = omega(f\_{n+1})
 - f\_n = (goldenratio^n - (-goldenratio)^(-n) / root5
  - goldenratio^n grows exponentially, the (-goldenratio)^(-n) is negligable
  - goldenratio = (1+root5)/2 approx= 1.6
- so fn is approx theta(1.6^n) which grows very fast!
- so the complexity of badfib is omega(1.6^n)

why is it so inefficient?

- we have to compute everything multiple times - each time we compute f3 we go through the whole f3 tree (e.g. when computing f4 and also when computing f5)
- better: compute f0, f1, f2, f3, ... , fn in order
- comment: we only need to keep track of the two 'last' fi's at any point in time to compute next one -> reduces storage (does not affect complexity)
- if addition takes theta(1) time, then complexity would be theta(n)

What's the problem?

- the numbers are growing exponentially quickly - need to consider "multiplicaiton arithmetic" (bit complexity) to add the k-bit integers, it takes theta(k)
- how many bits are in f\_i?
- f\_i is theta(1.6^i)
- # bits in f\_i is log\_2(1.6^i) which is theta(i)
- time to compute the addition is theta(i) 
- so overall complexity is sum i=2..n theta(i) = theta(n^2)

Algorithm: BetterFib(n)

	f[0] <- 0
	f[1] <- 1
	for i <-2 to n
		do f[i] <- f[i-1] + f[i-2]
	return f[n]

###Designing Dynamic Programming Algorithms for Optimization Problems

Optimal Structure

- Examine the structure of an optimal solution to a problem instance I, and determine if an optimal solution for I can be expressed in terms of optimal solutions to certain subproblems of I.

Define Subproblems

- Define a set of subproblems S(I) of the instance I, the solution of which enables the optimal solution of I to be computed. I will be the last or largest instance in the set S(I).

Recurrence Relation

- Derive a recurrence relation on the optimal solutions to the instances in S(I). This recurrence relation should be completely specified in terms of optimal solutions to (smaller) instances in S(I) and/or base cases.

Compute Optimal Solutions

- Compute the optimal solutions to all the instances in S(I). Compute these solutions using the recurrence relation in a bottom-up fashion, filling in a table of values containing these optimal solutions. Whenever a particular table entry is filled in using the recurrence relation, the optimal solutions of relevant subproblems can be looked up in the table (they have been computed already). The final table entry is the solution to I.

####0-1 Knapsack problem

- instance: an array of profits, array of weights, and capacity M - all +ve integers
- feasible solution: an ntuple [x1, ... xn] where each xi is 0 or 1 and the sum of wi\*xi <= M
- find: a feasible solution that maximizes the sum of pi\*xi

thinking about the problem:

- consider xn which = 0 or 1
- case xn = 0
 - let X' be the solution to the subproblem consisting of the first n-1 items
 - then X = [X], 0]
- case xn = 1
 - subproblem: first n-1 objects with capacity M' <- M-w\_n
 - optimal solution X = [X', 1]
- the subproblem profit(x) = profit(x') + p\_n