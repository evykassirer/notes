CS 240R (regular)

Daniela Maftuleac

DC 2305C

dmaftule@uwaterloo.ca

Classes:
* [Jan 6](https://github.com/evykassirer/notes/blob/master/class_notes.md#jan-6)
* [Jan 8](https://github.com/evykassirer/notes/blob/master/class_notes.md#jan-8)
* [Jan 13](https://github.com/evykassirer/notes/blob/master/class_notes.md#jan-13)
* [Jan 15](https://github.com/evykassirer/notes/blob/master/class_notes.md#jan-15)
* [Jan 20](https://github.com/evykassirer/notes/blob/master/class_notes.md#jan-20)
* [Jan 22](https://github.com/evykassirer/notes/blob/master/class_notes.md#jan-22)
* [Jan 27](https://github.com/evykassirer/notes/blob/master/class_notes.md#jan-27)
* [Jan 29](https://github.com/evykassirer/notes/blob/master/class_notes.md#jan-29)
* [Feb 3](https://github.com/evykassirer/notes/blob/master/class_notes.md#feb-3)
* [Feb 5](https://github.com/evykassirer/notes/blob/master/class_notes.md#feb-5)
* [Feb 10](https://github.com/evykassirer/notes/blob/master/class_notes.md#feb-10)
* [Feb 12](https://github.com/evykassirer/notes/blob/master/class_notes.md#feb-12)
* [Feb 24](https://github.com/evykassirer/notes/blob/master/class_notes.md#feb-24)

Tutorials:
* [Jan 12](https://www.student.cs.uwaterloo.ca/~cs240/w15/tutorials/Tutorial1.pdf)
* [Jan 19](https://www.student.cs.uwaterloo.ca/~cs240/w15/tutorials/Tutorial2.pdf)
* [Jan 26](https://www.student.cs.uwaterloo.ca/~cs240/w15/tutorials/Tutorial3.pdf)
* [Feb 2](https://www.student.cs.uwaterloo.ca/~cs240/w15/tutorials/Tutorial4.pdf)
* [Feb 9](https://www.student.cs.uwaterloo.ca/~cs240/w15/tutorials/Tutorial5.pdf)

-----

##Jan 6

Assignments
- LaTeX preferred - pdf submission on Markus
- A0 due next week - intro to LaTeX - gives 5 bonus marks for A1
- 5 assignments (not including A0)

Textbook
- you can find it online and in the library


####Some Terminology
*Problem*: Given a problem instance, carry out a particular computational task.
- *Problem Instance*: Input for the specified problem.
- *Problem Solution*: Output (correct answer) for the specified problem instance.
- *Size of a problem instance*: Size(I) is a positive integer which is a measure of the size of the instance I

*Algorithm*: An algorithm is a step-by-step process (e.g., described in pseudocode) for carrying out a series of computations, given an arbitrary problem instance I.
- *Algorithm solving a problem*: An Algorithm A solves a problem Π if, for every instance I of Π, A finds (computes) a valid solution for the instance I in finite time.
- *Program*: A program is an implementation of an algorithm using a specified computer language.
- note: algorithms are NOT programs - but they can be implemented with a program
- *Algorithm Design*: Design an algorithm A that solves Π (this is focused on in later courses)
- *Algorithm Analysis*: Assess correctness and efficiency of A (this is more the focus of this course)

Determining Efficiency of Algorithms/Programs:
- *Running Time*: the amount of time a program takes to run.
- *Space*: the amount of memory the program requires.
- The amount of time and/or memory required by a program will depend on Size(I), the size of the given problem instance I.
- Can be affected by things like the current state (like how sorted the list already is) and the size of the input (might be more efficient with big or with small compared to others)

Why is it bad to use *experimental studies*? (just running it a few times and time it, compare algorithms)
- We must implement the algorithm.
- Timings are affected by many factors: hardware (processor, memory), software environment (OS, compiler, programming language), and human factors (programmer).
- We cannot test all inputs; what are good sample inputs?
- We cannot easily compare two algorithms/programs.

Measuring running time - Instead of time, count the number of primitive operations - *Random Access Machine (RAM) Model*:
- The random access machine has a set of memory cells, each of which stores one item (word) of data.
- Any access to a memory location takes constant time. (not concurrent)
- Any primitive operation takes constant time.
- The running time of a program can be computed to be the number of memory accesses plus the number of *primitive operations*.
e.g. primitive operation: assigning value to a variable indexing into an array, arithmetic operation like addition, comparing numbers, returning from a function
- primitive operations take constant time - soon we won't care about things that take constant time

####Example 1: counting operations
	algorithm: arrayMax(n)
	Input (precondition) : Given an array a[0 ... n-1] of size n of integers
	Output (postcondition) : the max value among a[0], ... , a[n-1]

	pseudo-code:
		currMax <- a[0]           (we can use arrow notation for assignment or =)
		for i<-1 to n-1 do
			if a[i]  > currMax then currMax<-a[i]
		return currMax

let's count operations:
- first line: indexing to array, get the value, assigning to currMax - constant time
- second line: assign to i then check if it`s in the range n-1, accessing value for n, we do this n times and the last time fails - constant * n
- third line: accessing A[i], comapring, assigning, etc. --- constant * n-1
- now we sum them up 
 - constant + constant * (2n-1) - we can simplify this
 - constant + constant * n 
- we get a function of n so it's O(n) running time, or linear running time

####Simplifying Comparisons
- Idea: Use order notation
- Informally: ignore constants and lower order terms

####Example 2: counting operations
given an array A of size n, compute the array representing the prefix averages A[0],A[1],...,A[n-1]

	S[0] = A[0]    	
	S[1] = A[0]+A[1] / 2 		
	S[i] = sum from j=0 to i of   A[j]/(i+1)  	

aka S[i] is the average of A[0], A[1] ... A[i]

	algorithm: prefixAVG(n)
	pre: n-element array A
	post: n-element arrays where S[i] = 1/(i+1) sum.j=0..i A[j]
	pseudo-code:
		for i<-0 to n-1 do		constant * n+1
			a<-0			constant * n
			for i<-0 to i do 	constant * i      the for loop on the inside takes n^2 time
				a <- a + A[j]	constant * i
			S[i] = a/(i+n)		constant * n
		return array S 			constant

note that inside the loop it takes c(1+2+3+4+...+n-1) time = c(n-1)n/2 <= n^2


-----

##Jan 8

Better algorithm to achieve the results of example 2 from last class? 

	S[i-1] = (A[0] + A[1] + ... + A[i-1]) / i
	Then S[i] = (i*S[i-1] + A[i]) /  (i+1)

algorithm 2: prefixAvg2

	Pre: the n-elem array A
	Post: the n-elem array S, S[i] = sum j=0..i A[j]  /  (i+1)
	pseudo-code:
		s <- 0					constant
		for i<-0 to n-1 do		constant*n
			s <- s + A[i]		constant*n
			S[i] = s/(i+n)		constant*n

Comparing times:
- the first algorithm takes quadratic time - T_A1(n) = c1 n^2 + c2 n + c3
- the second takes linear time - T_A2(n) = c4 n + c5
- you can graph a linear function and a quadratic function to see approximately how much more time one would take than the other as n grows

###Sorting Algorithms
#####Bubble Sort (increasing)
- go through left to write and compare two elements at a time - swap if they're in the wrong order 
- at the end of each iteration one more element at the end is in the right place 

pseudo-code:

	for i<-0 to n-1 do
		swaps<-0
		for j<-0 to n-i-2 do
			if A[j] > A[j+1]
				swap A[j] with A[j+1]
				swaps++;
		if swaps=0
			break;

- if we have one element - 0 swaps
- if we have two elements - 0 or 1 swaps
- if we have three - 0, 1, 2, 3 swaps
- if we have four - 0 to 6 swaps

linear in best case (only go once), quadratic in worst case O(n^2), average case n^2


####ORDER NOTATION
O-notation: 
- f(n) ∈ O(g(n)) if there exist constants c > 0 and n_0 > 0 such that  0 ≤ f(n) ≤ c*g(n) for all n ≥ n_0
- like <= in math
- g(n)is worst case time of f(n)
- to figure out what n_0 is, find an intersection point --- n_0 has to be a natural number
- note that this means if something is O(n) it's also O(n^2)

Ω-notation: 
- f (n) ∈ Ω(g(n)) if there exist constants c > 0 and n_0 > 0 such that 0 ≤ c*g(n) ≤ f(n) for all n ≥ n_0
- like >= in math
- opposite of O-notatoion - g(n) is best case time of f(n)

Θ-notation: 
- f (n) ∈ Θ(g(n)) if there exist constants c1, c2 > 0 and n_0 > 0 such that 0 ≤ c1*g(n) ≤ f (n) ≤ c2*g(n) for all n ≥ n_0
- =
- grows at same rate

o-notation: 
- f (n) ∈ o(g(n)) if for ALL constants c > 0, there exists a constant n_0 > 0 such that 0 ≤ f(n) < c*g(n) for all n ≥ n_0
- <

ω-notation: 
- f (n) ∈ ω(g(n)) if for ALL constants c > 0, there exists a constant n_0 > 0 such that 0 ≤ c g(n) < f (n) for all n ≥ n_0
- >

#####example: show that n ∈ o(n^2)

	for any c, let n_0 = 1/c + 1
	then 0 <= (1/c + 1) 
	       < c(1/c + 1) 
	       = 1/c + 2 + c

#####example: show 2n^2 + 3n + 11 ∈ O(n^2)
we do this by showing 0 ≤ 2n^2 + 3n + 11 ≤ c*n2 for all n ≥ n0

	0 ≤ 2n^2 + 3n + 11 
	  ≤ 2n^2 + 3n^2 + 11n^2  (just showed)
	  ≤ 16n^2
	true for n >= 1, so n_0 = 1

#####example: Prove that 2010n^2 + 1388n ∈ o(n^3) from first principles
we use l'hopital's rule - show lim n->inf f(n)/g(n) = 0

	  lim n->inf    (2010n^2 + 1388n) / n^3 
	= lim n->inf    (4020n + 1388) / 3n^2  
	= lim n->inf     4020/6n  
	= 0
	by definition of the limit, 
	for all epsilon>0, there exists delta>0 such that 
	for all n>=delta, (2010n^2 + 1388n) / n^3 < epsilon
	so 0 < 2010n^2 + 1388n < epsilon*n^3 
	so delta is our n_0 (and epsilon is like c)

-----

##Jan 12 - Tutorial
* [slides](https://www.student.cs.uwaterloo.ca/~cs240/w15/tutorials/Tutorial1.pdf)

####Latex
- compile .tex
- newline: \\
- space: \hspace{x mm}  and  \vspace{x mm}
- to make:

	25x+5 = 10
        x = 1/5

write:

	\begin{align*}
		20x+5 &= 10\\
		x &= \fract{1}{5}
	\end{align*}

math mode: 
- in-line $...$ or \(...\)
- centered: $$...$$ or \[...\]

####Order notation
#####BIG O
show 12n^3+11n^2+10 ∈ O(n^3)

	   12n^3+11n^2+10 
	<= 21n^3 + 11n^3 + 10n^3 (if n>0) 
	= 33n^3
	so c = 33, and n_o = 1

#####BIG omega
show n^2 - 3m ∈ Ω(n^2)

	cn^2 < n^2-3n
	c <= 1 - 3/n

- you can choose multiple combinations of values, e.g. choose c = 1/2, then n_0 must be 6

#####BIG θ
- definitions or from first principals means you can’t just say O and Ω → Θ. Must have values for c1, c2, n0.
- find c1 from big o, c2 from big omega - name them

#####little o
show 1000n ∈ Θ(nlog(n))

	1000n <= cnlog(n)
	1000 <= clog(n)
	1000/c <= log(n)
	e^(1000/c) <= n
	so n_0 = 2^(1000/c)

you only get partial marks if you stop here - you have to show it works

	so for all n >= n_0,
	cnlog(n) >= cnlog(n_0) = cnlog(e^(1000/c)) = cn(1000/c) = 1000n

#####little omega
show 3^n ∈ w(2^n)

	3^n >= c*2^n
	(3/2)^n >= c
	n >= log_(3/2)(c)
	so n_0 = log_(3/2)(c)

so...

	for all n >= n_0
	3^n = (3/2)^n * 2^n
	3^n >= (3/2)^n_0 * 2^n
	3^n >= (3/2)^log_(3/2)(c) * 2^n
	3^n >= c * 2^n 


####loop analysis

	cs = 0
	for i from 1 to 3m
		cs *= 4
		for j from 1388 to 2010
			for k from 4i to 6i
				cs += k

looking at:

	  sum{i=1..3m} sum{j=1388..2010} sum{k=4i..6i} 1 
	= sum{i=1..3m} sum{j=1388..2010} 2i 
	= 2 sum{i=1..3m} sum{j=1..623} i 
	= 2 sum{i=1..3m} 623i
	= 2*623*(3m)(3m+1)/2
	= 5607m^2 + 1869m 
	∈ Θ(m^2)


-----
##Jan 13

##### Example: show 5n^2 log(n) ∈ Ω(n*log^2(n))
prove there exists c, n_0 in positive reals s.t. for all n > n_0, 0 <= cnlog^2n <= 5n^2 logn

	log(n) <= n  
	0 <= clogn <= 5n (for c = 5)
	cnlog^2n <= 5n^2logn - which is what we want
	so c = 5

so we can make n_0 = 1 (it needs to be > 0 and an integer, and the above statement works for all +ve values)

###functions
1. constant function: f(n) = c - useful to count primitive operations
2. logarithmic function: f(n) = log(n) - useful when dividing the size of the input into 2 in a loop
3. linear function: f(n) = n
4. pseudo linear: f(n)= nlogn - e.g. merge sort (more examples later)
5. quadratic: f(n) = n^2  - e.g. for i<-0 to n    j++
6. cubic (polynomial): f(n) = n^3 (n^k)
7. exponential: f(n) = 2^n

####How Growth Rates Affect Running Time:
It is interesting to see how the running time is affected when the size of the problem instance doubles (i.e., n → 2n)

1. constant complexity: T(n) = c, T(2n) = c
2. logarithmic complexity: T(n)=clogn,T(2n)=T(n)+c 
3. linear complexity: T (n) = cn, T(2n) = 2T(n)
4. pseudo linear: T(n)=cnlogn,T(2n)=2T(n)+2cn
5. quadratic complexity: T(n) = cn^2, T(2n) = 4T(n)
6. cubic complexity: T(n) = cn^3, T (2n) = 8T(n)
7. exponential complexity: T(n) = c2^n, T(2n) = (T(n))^2/c

###Complexity vs. Running Time:
- Note that if algorithm A1 has lower complexity than algorithm A2, that means it runs faster for large problem instances - but it could still be slower for small problem instances! 
- If A1 and A2 have the same complexity - we can't determine from this information which will be faster for some problem instance.
- Note that if A1 is O(n^3) and A2 is O(n^2) we cannot say A2 is more efficient than A1! This is because big O is >= not =, so for example A1 could actually run in constant time and we can still say it's O(n^3)

remember we can use **l'hopitals rule**, lim to inf of f(n)/g(n)
- if limit is 0, g is bigger
- if limit is inf, f is bigger
- if limit is constant, they're equal

log is alwayyys less than polynomial
- [log(n)]^k ∈ o(n^i) for any real numbers k, i
- (logn)^100000 will eventually be smaller than n^0.00001

####example: n(2+sin(nπ/2)) is θ(n) 

	we need to prove 0 <= c_1 n <= n (2+sin(npi/2)) <= c_2 n
	0 <= c_1 <= (2+sin(npi/2)) <= c_2
	0 <= c_1 - 2 <= sin(npi/2) <= c_2 - 2
	c_1 -2 = -1 (since sin is bounded) so c_1 = 1
	similarily c_2 = 3
	n_0 = 1 since this works for all n

####Relationships between Order Notations:
e.g. f(n) ∈ Θ(g(n)) iff g(n) ∈ Θ(f (n))
- this makes sense because f = g iff g = f
- we can say a lot of other similar things using the logic of >, =,  < 


####Algebra of Order Notations - “Maximum” rules: 
Suppose that f(n) > 0 and g(n) > 0 for all n ≥ n_0. Then:
- O(f(n) + g(n)) = O(max{f(n), g(n)}) 
- Θ(f(n) + g(n)) = Θ(max{f(n), g(n)}) 
- Ω(f(n) + g(n)) = Ω(max{f(n), g(n)})
- Transitivity: If f(n) ∈ O(g(n)) and g(n) ∈ O(h(n)) then f(n) ∈ O(h(n)).

####SUGGESTION: print the formulae and misc. math facts to refer to often
- arithmetic sequence, geometric sequence, harmonic sequence, etc.

Tip: to find θ bounds, you can find both at the same time, or sometimes it's easier to find the top bound and lower bound separately

Loop analysis: 

1. Identify elementary operations that require constant time (denoted Θ(1) time).
2. Loops: The complexity of a loop is expressed as the sum of the complexities of each iteration of the loop.
3. Analyze independent loops separately, and then add the results (use “maximum rules” and simplify whenever possible).
4. If loops are nested, start with the innermost loop and proceed outwards. In general, this kind of analysis requires evaluation of nested summations.

####Example of loop analysis: 
#####Test1(n)

	sum <-0 					θ(1)
	for i<-1 to n do 				sum_[i=1]^n 1
		for j<-i to n do 			sum_[j=1]^n 1
			sum<-sum+(i-j)^2 		θ(1)
			sum<-sum^2 			θ(1)

finding run time:

	T(n) = θ(1) + sum_[i=1]^n sum_[j=i]^n (θ(1) + θ(1))
	     = θ(n) + sum_[i=1]^n (θ(1) + (n-i+1))
             = θ(n) + (θ(1) (n + n-1 + ... + 1)
             = θ(1) + θ(1)*n*(n-1)/2
             = θ(1) + θ(1)*n^2+n / 2
             = θ(n^2+n / 2)
             = θ(n^2 + n)
             = θ(n^2)

can also look at it like:

	sum <-0 				O(1)							
	for i<-1 to n do 			O(n)								
		for j<-i to n do 		O(n) - then multiply by O(n) above to get O(n^2)
			sum<-sum+(i-j)^2 	O(1)
			sum<-sum^2 		O(1)

#####Test2(A, n)

	max ← 0
	for i ← 1 to n do
		for j ← i to n do
			sum ← 0
			for k ← i to j do
				sum ← A[k]
				if sum > max then
					max ← sum
	return max

run time:

	  θ(1) + sum_[i=1]^n sum_[j=i]^n (θ(1)  + sum_[k=i]^j(θ(1) + θ(1)))
	= θ(1) + sum_[i=1]^n sum_[j=i]^n (θ(1)  + θ(1)*(j-i+1))
	...
	= θ(n^3)

- to **over estimate**: 1...n for all loops - nested loops so multiply by each other to get O(n^3)
- to **underestimate**: first loop let's say it just goes from 1 to n/4, second from n/4 to n/2, third from n/4 to n/2 -- we get underestimate omega(n^3)

#####Test3 - third example

	sum ← 0 				θ(1)
	for i ← 1 to n do 		sum i=1..n 1
		j←i 				θ(1)
		while j ≥1 do 		 
			sum ← sum + i/j 
			j ← floor(j/2)  		
	return sum

- for the while loop: when j = i, the time it takes is θ(1) + θ(1) + θ(floor(i/2))  if i >= 1
- otherwise constant time (to check and break)
- solving the recurrance, the while part takes θ(logi) time

so we have running complexity:
 
	θ(1) + sum i..n (θ(1) + θ(logi))
	  = θ(n) + sum i..n θ(logi)
	  = θ(n) + θ(nlog(n))
	  = θ(n logn)

-----
## Jan 15  
(was sick, didn't pay attention, notes made after)

### Sorts ct'd
#### Mergesort and logn running time

	input: Array A of n integers
	Step 1: 
		split A into two subarrays: 
		AL consists of the first ceil(n/2) elements in A and 
		AR consists of the rest of the elements - floor(n/2)of them
	Step 2: 
		Recursively run MergeSort on AL and AR .
	Step 3: 
		After AL and AR have been sorted, use a function Merge 
		to merge them into a single sorted array. 

This can be done in time Θ(n).

pseudo-code: MergeSort(A, n)

	if n = 1 then
		S←A
	 else
	 	nL←⌈n/2⌉
	 	nR←⌊n/2⌋
	 	AL ← [A[1],...,A[nL]]
	 	AR ←[A[nL +1],...,A[n]]
	 	SL ← MergeSort (AL , nL )
	 	SR ← MergeSort (AR , nR )
		S ← Merge(SL,nL,SR,nR)
	return S

analyzing:
- Let T(n) denote the time to run MergeSort on an array of length n.
- Step 1 takes time Θ(n) (transfer elements into new arrays)
- Step 2 takes time T(ciel(n/2)) + T(floor(n/2))􏰂 
- Step 3 takes time Θ(n) 

recurrence relation for T(n):

	T(n) = T(ciel(n/2)) + T(floor(n/2)) + Θ(n) if n>1
		 = Θ(1) if n=1

exact recurrence with constants:
	T(n) = T(ciel(n/2)) + T(floor(n/2)) + cn if n>1
		 = d if n=1

"sloppy recurrence" (floors and ceilings removed):

	T(n) = 2T(n/2) 􏰂+ cn if n>1
		 = d if n=1

- The exact and sloppy recurrences are identical when n is a multiple of 2
- If we solve it we get T(n) ∈ Θ(n logn)
- This is also true for all n, not just even n

#####Binary search: another example of logn time

	L(n) = L(n/2) + O(1) if n>=2
	     = O(1) if n <= 1

###Conditionals - varying runtime
####Insertion sort (A, n)

	for i=1 ... n-1
		key = A[i]
		j=i-1
		while(j>=0 and A[j] > key)
			A[j+1] = A[j]
			j--
		A[j+1] = key

the number of executions depends on the input. It's between 1 and i.

We assume **worst case analysis** when runtimes vary so wildly.
- That is, compute an upper bound on the runtime
- for insertion sort: runtime <= sum i=1..n-1 (i-1)O(1) = O(1)sum i=1..n-1 i which is O(n^2)
- If you use upper bounds, also show they are **tight**
- To show that they are tight, give an instance (of size n) on which the runtime asymptotically actually occurs
- e.g. consider an array in reverse-sorted order for insertion sort

"worst case":

	T(n) = runtime of max instance I s.t. I has size n 
	     = max {T(I)}

"average case":

	T(n) = sum of instances of size n / # instances of size n

"best case":

	T(n) = min {T(I)}


###Abstract Data Types (ADT)
- A description of information and a collection of operations on that information.
- The information is accessed only through the operations.
- We can have various realizations of an ADT, which specify: 
 - How the information is stored (data structure)
 - How the operations are performed (algorithms)

####e.g. Dynamic arrays
- Linked lists support O(1) insertion/deletion, but element access costs O(n).
- Arrays support O(1) element access, but insertion/deletion cost O(n).
- Dynamic arrays offer a compromise: O(1) element access, and O(1) insertion/deletion at the end.
- Two realizations of dynamic arrays:
 -Allocate one HUGE array, and only use the first part of it.
 -Allocate a small array initially, and double its size as needed. (Amortized analysis is required to justify the O(1) cost for insertion/deletion at the end — take CS 341/466!)

####e.g. Stack ADT
- collection of items with operations: 
 - push: inserting an item
 - pop: removing the most recently inserted item
- Items are removed in **LIFO** (last-in first-out) order. 
- We can have extra operations: size, isEmpty, and top
- Applications: 
 - Addresses of recently visited sites in a Web browser
 - procedure calls
- Realizations of Stack ADT 
 - using arrays
 - using linked lists

####Queue ADT
- a collection of items with operations: 
 - enqueue: inserting an item
 - dequeue: removing the least recently inserted item
- Items are removed in **FIFO** (first-in first-out) order. Items enter the queue at the rear and are removed from the front. 
- We can have extra operations: size, isEmpty, and front
- Realizations of Queue ADT:
 - using (circular) arrays 
 - using linked lists

####Priority Queue ADT
- Priority Queue: a collection of items (each having a priority) with operations:
 -insert: inserting an item tagged with a priority
 - deleteMax: removing the item of highest priority deleteMax is also called extractMax.
- Applications: 
 - typical “todo” list
 - simulation systems
- The above definition is for a **maximum-oriented** priority queue. 
- A **minimum-oriented** priority queue is defined in the natural way, by replacing the operation deleteMax by deleteMin.

#####Using a Priority Queue to Sort: PQ − Sort(A)
initialize PQ to an empty priority queue 

	for i ← 0 to n − 1 do
		PQ.insert (A[i], A[i]) 
	for i ← 0 to n − 1 do
		A[n − 1 − i] ← PQ.deleteMax()

#####Realizations of Priority Queues

Attempt 1: Use unsorted arrays 
- insert: O(1)
- deleteMax: O(n)
- Using unsorted linked lists is identical.
- This realization used for sorting yields selection sort.

Attempt 2: Use sorted arrays 
- insert: O(n)
- deleteMax: O(1)
- Using sorted linked-lists is identical.
- This realization used for sorting yields insertion sort.
	
Third Realization: Heaps
- A heap is a certain type of binary tree. 
- Our goal with heaps is to get insertion and deletion to be O(logn)

####Recall binary trees:
- A binary tree is either 
 - empty, or
 - consists of three parts: a node and two binary trees (left subtree and right subtree).
- Terminology: root, leaf, parent, child, level, sibling, ancestor, descendant, etc. 

###Heaps
A max-heap is a binary tree with the following two properties:
- Structural Property: All the levels of a heap are completely filled, except (possibly) for the last level. The filled items in the last level are left-justified.
- Heap-order Property: For any node i, key (priority) of parent of i is larger than or equal to key of i.

A min-heap is the same, but with opposite order property. 

Lemma: Height of a heap with n nodes is Θ (log n).

----

##Jan 19 - Tutorial
* [Slides](https://www.student.cs.uwaterloo.ca/~cs240/w15/tutorials/Tutorial2.pdf)

----

##Jan 20

####Proving lemma: Height of a heap with n nodes is Θ (log n).
* Note that level k (starting counting at 1 at the top)  has 2^(k-1) nodes 
* If we have k levels, the number of nodes we have total is *at least* sum i=0..k-2 (2^i) + 1 and *at most* sum i=0..k-1 (2^i)
* let the height be the number of edges in the shortest path from the top node to lowest node
* so **h = #levels - 1**

we can use these over and underestimates of the number of nodes to show that the height is theta(logn) where n is #nodes:

	sum i=0..k-2 2^i + 1 = 2^(k-1) <= n <= sum i=0..k-1 2^i = 2^k - 1
	
	lower bound:
	2^(k-1) <= n
	k-1 <= logn
	h <= logn
	
	upper bound:
	n <= sum i=0..k-1 2^i = 2^k - 1
	n+1 >= 2^k
	k >= log(n+1)
	h >= log(n+1) - 1

####Storing heaps in arrays:
-  Let H be a heap (binary tree) of n items and let A be an array of size n. 
-  Store root in A[0] and continue with elements level-by-level from top to bottom, in each level left-to-right.
-  It is easy to find parents and children using this array representation: 
 - the left child of A\[i\] (if it exists) is A\[2i + 1\],
 - the right child of A\[i\] (if it exists) is A\[2i + 2\],
 - the parent of A\[i\] (i != 0) is A\[floor(i−1 / 2)\] (A\[0\] is the root node).

####Insertion in Heaps
1. Place the new key at the first free leaf
2. The heap-order property might be violated: perform a bubble-up:

bubble-up(v) v: a node of the heap
	while parent(v) exists and key(parent(v)) < key(v) do 
		swap v and parent(v)
		v ← parent(v)

The new item bubbles up until it reaches its correct place in the heap.

pseudo-code:

	heapInsert (A, x) A: an array-based heap, x: a new item
		size(A) ← size(A) + 1  --note size(A) is a variable, not a function
		A[size(A) − 1] ← x 
		bubble−up(A, size(A) − 1)

Worst case: v is bigger than the root - h # of swaps and h is O(logn) so bubble-up is O(logn)


####Deletemax in heaps:
- The maximum item of a heap is just the root node.

1. We replace root by the last leaf (last leaf is taken out).
2. The heap-order property might be violated: perform a bubble-down:

bubble-down(v) v: a node of the heap

	while v is not a leaf do
		u ← child of v with largest key 
		if key(u) > key(v) then
			swap v and u
			v←u 
		else
			break

pseudo-code:

	heapDeleteMax (A)  A: an array-based heap
		max ← A[0]
		swap(A[0], A[size(A) − 1])
		size (A) ← size (A) − 1
		bubble−down(A, 0)
		return max

- Time: O(height of heap) = O (log n)
- worst case: the last inserted node has the min key - so it's tight - so theta(logn)

####Problem statement: Given n items (in A[0 · · · n − 1]) build a heap containing all of them.
Solution 1: Start with an empty heap and insert items one at a time:

	heapify1 (A) A: an array
		initialize H as an empty heap 
		for i ← 0 to size(A) − 1 do
			heapInsert (H , A[i ])

This corresponds to going from 0 · · · n − 1 in A and doing bubble-ups 

analyzing time:

		TI - time insert at bottom
		TS - time swap up a single level
		
		level 1: 2^0 * TI
		level 2: 2^1(TI+TS)
		level 3: 2^3(TI+2TS)
		...
		level k-1: 2^(k-2)(TI + (k-2)TS)

                total run time is 
                sum i=0..k-2 2^i(TI + iTS) 
		= sum 2^i theta(1) + sum 2^i*i*theta(1)
		= 2^(k-1) - 1      + 2^k*k - 2^(k+1) + 2 	(from sum sheet)
	  approx= n/2 + nlogn - 2n + 1   			(k approx= logn)

Worst-case running time: Θ(n log n).

Solution 2: Using bubble-downs instead:

	heapify (A) A: an array
	n ← size(A) − 1
	for i ← ⌊n/2⌋ downto 0 do
		bubble−down(A,i)

A careful analysis yields a worst-case complexity of Θ (n). A heap can be built in linear time
- we start at the next to bottom row and bubble to the row below it

----
##Jan 22

Solution 2 from last time - worst case run time?
- look at the heap like a pyramid 
 - the number of nodes in the bottom is 2^k, or n/2 
 - the number of nodes in the second last row is n/4, etc..
- each level has to bubble down to all the levels below
so we have 

	n/2^2 * 1 level    +    n/2^3 * 2    +   n/2^4 * 3 + ..... + n/2^(k+1) * k 
	= sum from i=1..k   n/(2^(i+1)) * i
	= 0.5n * sum i=1..k  0.5^i * i 
	= ... use sum sheet on slides ... 
	= (n/2)(2 - k+2/2^k) 
    = (approx)n - logn/2 - 1 	(using k=logn)
	which is linear time!


####Sorts Using Heaps
HeapSort (A)

	initialize H to an empty heap 
	for i ← 0 to n − 1 do
		heapInsert (H , A[i]) 
	for i ← 0 to n − 1 do
	A[n − 1 − i] ← heapDeleteMax(H)

Takes nlogn + n*logn = nlogn time

HeapSort without making another array

	heapify (A)
	for i ← 0 to n − 1 do
		A[n − 1 − i] ← heapDeleteMax(A) 	
		(we can do this^ because as we delete max, 
		there becomes space at end of array)

takes n + n*logn  = nlogn time


###Selection
Problem Statement: The **kth-max** problem asks to find the kth largest item in an array A of n numbers.

Solution 1: 	
- Make k passes through the array, deleting the maximum number each time.
- Complexity: Θ(kn).

Solution 2: 
- First sort the numbers. Then return the kth largest number. 
- Complexity: Θ(nlogn).

Solution 3: 
- Scan the array and maintain the k largest numbers seen so far in a min-heap
- e.g. if we want 3 elements, we keep a min-heap with 3 nodes (Start with the first 3 elements in array), always balance like a min-heap with smallest element in root
- compare root to each array element - if the root is smaller, then we replace the root with the element and heapify - in worst case we insert (insert takes logk) all n elements
- Complexity: Θ(nlogk).

Solution 4: 
- Make a max-heap by calling heapify(A). Call deleteMax(A) k times.
- Complexity: Θ(n + k log n).

For median selection, k =􏰑 floor(n/2)􏰒, giving cost Θ(n logn). 
This is the same cost as our best sorting algorithms.

###Question: Can we do selection in linear time?
- The quick-select algorithm answers this question in the affirmative. 
- Observation: Finding the element at a given position is tough, but finding the position of a given element is simple.

#####Quick-select
- quick-select and the related algorithm quick-sort rely on two subroutines: 
 - choose-pivot(A): Choose an index i such that A[i] will make a good pivot (hopefully near the middle of the order).
 - partition(A, p): Using pivot A[p], rearrange A so that all items ≤ the pivot come first, followed by the pivot, followed by all items greater than the pivot.

picking the pivot:
- Ideally, we would select a median as the pivot. But this is the problem we’re trying to solve!
- First idea: Always select first element in array. We will consider more sophisticated ideas later on.

partition(A, p)

	A: arrayofsizen, p: integers.t. 0≤p<n
	1. 	swap(A[0], A[p])
	2. 	i←1, j←n−1
	3. 	loop
	4. 		while i < n and A[i] ≤ A[0] do
	5. 			i←i+1
	6.		while j ≥ 1 and A[j] > A[0] do 
	7.			j←j−1 
	8. 		if j < i then break
	9.		else swap(A[i], A[j]) 
	10. end loop
	11. swap(A[0],A[j])
	12. return j

Idea: Keep swapping the outer-most wrongly-positioned pairs.

e.g.

	5 1 9 8 3 2 6 7
	p   i     j
	5 1 2 8 3 9 6 7
	p     i j
	5 1 2 3 8 9 6 7
	p     j i
	3 1 2 5 8 9 6 7
	now 3 is new pivot, everything left of 5 is less, right is greater, continue with pivot 3

---
##Jan 26 - tutorial
* [Slides](https://www.student.cs.uwaterloo.ca/~cs240/w15/tutorials/Tutorial3.pdf)

----

##Jan 27

We won't be talking about quick select any more in this course, only quick sort

###Quicksort(A, r, l)

	pre: l-r element array
	post: sorted A
	
	if(l>r)
		p <- choose-pivot(r, l)
		i = partition(A, r, l)
		QuickSort(A, r, i-1)
		QuickSort(A, i+1, l)

#####Worst case:
- claim: T_worst(n) <= c*n*(n+1)/2

proof: by induction

	base case: Tworst(1) = c = c*1*2/2
	IH: assume Tworst(m) <= c*m*(m+1)/2 for all m <= n-1
	IC:
	Tworst(n) = {max value 0 <= i m= n-1}{Tworst(i-1) + Tworst(n-i))} + cn 
	          <=  {max value 0 <= i m= n-1}{c*(i-1)*i/2 + (n-i)(n-i+1)/2} + cn 
	          (max is when i=0 or i=n-1)
	          <= c*(n-1)*n/2 + cn = cn(n+1)/2
        so Tworst(n) is in O(n^2)

So worst case is when A is sorted: at least n^2 running time. 

	T(n) = T(n-1) + cn    omega(n^2)
	 = cn + (c(n-1) + T(n-2))
	 = cn + c(n-1) + c(n-2) + ... + c + d
	 = cn(n+1)/2 + d 

#####Best case for QuickSort
- each pivot divides the array in half each time
- Tbest = {min 0 <= i <= n-1}(Tbest(i-1) + Tbest(n-i)) + cn
- for best case, we let i = n/2
- Tbest >= T(n/2) + T(n/2) + cn
- we recognize this recurrence as nlogn

#####Average case for QuickSort:

	Tavg(n) = sum_{all possible instance of array with size n} T(n)  /  n! (where n! # of permutations)
	        = sum i=0..n-1 T(i) + T(n-i-1) + cn) / n
	        	// we can say these are equal because we can divide the permutations of the
	        	// array into n cases - where the first element will be moved to one of n 
	        	// places in the array - the rest of the array is irrelevant to the runtime
	        	// at this point (we account for it in the next steps)
	        = sum i=0..n-1 T(i) + sum i=0..n-1 T(n-i-1) + sum cn
	        = 2/n sum T(i) + cn

Claim: Tavg(n) <=   D if n<=1    or   D\*n\*logn if n>=2

	Proof: by induction
		BC: n=0, 1
		  T(0) <= T(1) <= D
		  (should also show T(2) pretty sure!!)
		IH: assume Tavg(m) <= D, if m<=1  and D\*m\*lnm if m>=2 for all m <= n-1
		IC:
		  Tavg(n) = 2/n sum T(i) + cn
		        = 2/n (T(0) + T(1) + sum i=2..n-1 T(i) + cn)
		        <= 2/n sum D\*i\*lni   + 2/n(D+ D) + cn
		        <= 2D/n sum ilni + 4D/2 + cn
		        <= 2D/n integral 2 to n xlnx dx
		        = .... some calculus
		        <= Dnlogn

- There's something different on the slides, but this problem is probably too complex for our class
- we see that H(n) ∈ O(log n).
- Average cost is O(nH(n)) ∈ O(n log n).
- Since best-case is Θ(n log n), and average case has lower bound of n log n, so average must be Θ(n log n).

####Summary
- worst case is worse than worst case of HeapSort or MergeSort
- best case is worse than best case for Insertion Sort
- average case is nlogn which is good!

More notes on QuickSort
- We can randomize by using choose-pivot2, giving Θ(n log n) expected time for quick-sort2.
- We can use choose-pivot3 (along with quick-select3) to get quick-sort3 with Θ(n log n) worst-case time.
- **ASK ON PIAZZA ABOUT THESE OTHER CHOOSE PIVOTS?**
- We can use tail recursion to save space on one of the recursive calls.
- By making sure the other one is always smaller, the auxiliary space is Θ(log n) in the worst case, even for quick-sort1.
- QuickSort is often the most efficient algorithm in practice.

--
##Jan 29

####O(nlogn) - lower bound for sorting
- Can we do bettter? Can we do sorting in O(n)?
- Answer: Yes and no! It depends on what we allow.
- No: Comparison-based sorting lower bound is Ω(n log n). 
- Yes: Non-comparison-based sorting can achieve O(n).

###Comparison Model: 
- algorithms make decisions based on comparison of two key values
- In the comparison model data can only be accessed in two ways: 
 - comparing two elements
 - moving elements around (e.g. copying, swapping)

Decision tree:
- a binary tree that can model the processing of any algorithm in the comparison model

![binary decision tree](/cs240/Jan_29_binary.jpg)
![binary decision tree](/cs240/Jan_29_decision_tree.jpg)

Lower bounds for sorting 
- thm: the lower bounds for sorting in the comparison model is omega(nlogn)
- look at the sorting tree - if we have >=n leafs - the height must be >= logn

Talked about Counting Sort:
- First have Array A
- Then find out how many of each id we have
- Then find the indices of where those values start in the array
- Then we can copy over whole objects from A into the array, based on those indices

Pseudo-code:

	pre: A[0...n-1] contianing integers from 0 to R-1
	Post: Sorted A->B
	//count how many of each type
	c <- array or size R filled with 0
	for i=0 to n-1 do
		increment C[A[i]]
	// find left boundary for each kind
	I <- array with size R, I[0]=0
	for i=1 to R-1 do
		I[i] <- I[i-1] + C[i-1]
	// copy, then move back in B (sorted)
	B <- copy A // we copy so that we can have the whole element 
	            // (which might be more than just the integer we're using to compare)
	for i=0 to n-1 do
		A[I[B[i]]] <- B[i]
		increment I[B[i]]

This takes O(n+R) time

![image of quicksort](/cs240/Jan_29_quick_sort.jpg)

###Radix sort
- sort n integers in base R (R-radix)
- # digits of n, in base R = floor(log_R(n)) + 1
- Let's consider n numbers in base R with m digits

MSD - most sigificant digit

	527, 368, 633, 401, 631, 301 -> sort by MST
	[301, 368], [401], [527], [633, 631] -> sort by next MSD
	[[301], [368]], [401], [[527], [[633, 631]] -> etc.
	[[301], [368]], [401], [527], [[[633] [631]]]
	not a good algorithm! many recursions
	O(m(n+k))

try LSD-radix:

	A: array of size n, contains m-digit radix-R numbers
	for d ← m down to 1 do 
		Sort A by dth digit

![LSD radix](/cs240/Jan_29_LSD.jpg)

**stable**:
- Sort-routine must be stable: equal items stay in original order.
􏰶- CountSort, InsertionSort, MergeSort are (usually) stable.
􏰶- HeapSort, QuickSort not stable as implemented.

-----
##Feb 2 - Tutorial
* [Slides](https://www.student.cs.uwaterloo.ca/~cs240/w15/tutorials/Tutorial4.pdf)

-----

##Feb 3

###Dictionary ADT
- contains n distinct KVP (key value pairs)
- search efficiently
- Insertion/deletion efficiently
- "A dictionary is a collection of items, each of which contains a key and some data
and is called a key-value pair (KVP). Keys can be compared and are (typically) unique."
- Operations: 
- search(k)
 - insert(k , v)
 - delete(k)
 - optional: join, isEmpty, size, etc.
- Examples: symbol table, license plate database

###Implementations of Dictionaries

#####unsorted array/linked list (size n)
- search Θ(n) 
- insert Θ(1)
- delete Θ(n) (need to search)

#####Ordered array
- search Θ(logn) 
- insert Θ(n)   (logn search and then shifting everything takes n)
- delete Θ(n)	(similar to insert)

#####Sorted linked list
- Search O(n)
- Insert O(n)
- Delete O(n)

##### BST (binary search tree)
- either empty or contains KVP at root, left subtree, right subtree
- keys in left subtree are smaller than the key at root
- keys in right subtree are bigger than key at root
- search: if found return value, else go left/right - O(h)
- insert: O(h)
- delete x in BST:
 - search for x
 - if x is a leaf, delete it
 - if x is a node with 1 child then delete x and bring subtree one level up
 - if x has 2 children, we swap it with the successor (go left and then all the way down right) or predecessor (go right and all the way left) and delete x
 - O(h)
- height will depend - worst case n, best case logn, average logn (like quicksort)

Exercise:
- BST (property) + Heaps (stored in array)
- sort the array (could take O(n) time), then we get perfectly balanced

#####AVL Trees
- Introduced by Adel’son-Vel’ski ̆ı and Landis in 1962,
- BST with an additional structural property: The heights of the left and right subtree differ by at most 1. (The height of an empty tree is defined to be −1.)
- At each non-empty node, we store height(R) minus height(L) ∈ {−1, 0, 1}:
 - −1 means the tree is left-heavy
 - 0 means the tree is balanced
 - 1 means the tree is right-heavy
- We could store the actual height, but storing balances is simpler and more convenient.

Each node stores:
- KVP
- left subtree
- right subtree
- height of subtree at the node = h(R)-h(L)

Examples of AVL trees (and not AVL trees):

![AVL and not](/cs240/Feb3_AVL_not.jpg)

Insert x into AVL
- Search for its place
- Insert
- Recompute the heights (balances) going up
- Check the AVL property, if violated fix by rotations

Pseudocode:

	Pre - AVL tree not containing x
	Post - AVL after inserting x
	set height(x) = 0
	while(height(parent(x)) = height(x)
		height(parent(x))++
		if((height(x)-height(sibling(x)) > 1) rebalance(parent(x))
		x = parent(x);

Rotating 

rotate-right(T)

		T: AVL tree
		returns rotated AVL tree
		1. newroot ← T .left
		2. T .left ← newroot.right
		3. newroot.right ← T
		4. return newroot

![Right rotation](/cs240/Feb3_right_rotation.png)


rotate-left(T)

		T: AVL tree
		returns rotated AVL tree
		1. newroot ← T .right
		2. T .right ← newroot.left
		3. newroot.left ← T
		4. return newroot

![Left rotation](/cs240/Feb3_left_rotation.png)


fix(T)

	T : AVL tree with T .balance = ±2 returns a balanced AVL tree

	if T.balance = −2 then
		if T.left.balance = 1 then
			T.left ← rotate-left(T.left) 
			return rotate-right(T)
	else if T.balance = 2 then
		if T.right.balance = −1 then
			T.right ← rotate-right(T.right) 
		return rotate-left(T)

----

##Feb 5

###AVL trees ct'd

#####Search: 
- search for a key in AVL tree - just like BST - O(height)

#####Insert:
 - insert a new key in AVL tree - fix will be called at most once - O(height)
 - If after inserting a new key a node z is unbalanced, then rebalance the tree with root z using one of the 4 types of rotation (right left, rightleft, leftright)


Lemma:
- let z be an unbalanced node and all its descendents are balanced
- applying one of the rotations on z balances z and its descendents
- if imbalance comes from insertion, then rebalancing z balances the entire tree

#####Delete:

1. delete as in BST
2. Parse upward from where you removed the node and recompute the balances of nodes
3. if a node is unbalanced then rebalance using rotations
4. keep updating balance up to the root
 - fix might be called O(height) times

Example of delete:

![Pre-delete](/cs240/Feb5_pre_delete.png)

![Post-delete](/cs240/Feb5_post_delete.png)

Note that we can't just rotate right because then the right half of the tree would be taller than the left half - still a problem. This is why we need to rotate the left half before rotating the whole thing.

Exercises:

1. given an AVL tree of height h, what is the possible level of the leaves?
2. Given an AVL tree of height h, what is the maximum number of nodes?

Delete is done in O(height)

##### claim: any AVL tree has its height O(logn)

	h <= clogn, c>0
	2^h <= 2^(clogn)
	2^h <= (2^logn)^c = n^c
	so this also means n >= 2^(h/c)

Equivalent form: For a given AVL tree of fixed height h, find the minimum number of nodes. Let N(h) = the smallest number of nodes for an AVL tree of height h

One subtree must have height at least h−1, the other at least h−2:

	N(h) = 1 + N(h−1) + N(h−2), h≥1
	N(h) = 1, h = 0
	N(h) = 0, h = −1
	This is the fibinacci sequence! (ish)
	N(h) = (h+3)th fibinacci number - 1 
	     (except not actually - I had copied this from the board, but if we're adding 1
	      *each time* then it would be (h+3)th fibinacci number + h-1   --- I think?
	      anyways this wouldn't change the result)
	N(h) > N(h−1)+N(h−2) 
	     > 2N(h−2) 
	     >= 2(N(h-3) + N(h-4) + h-3)) 
	     > 4N(h−4) > 8N(h−6) > ··· 
	n    > 2iN(h−2i) = 2^⌊h/2⌋ N(h-2⌊h/2⌋) ≥ 2^(h/2)

	from this we can get h <= 2logn

Thus an AVL tree with n nodes has height O(logn). Also, n ≤ 2h+1 − 1, so the height is Θ(log n).

###2-3 Trees

idea: rebalancing by relaxing the degree=2 condition

Def 2-3 tree
- empty (NIL)
- contains one KVP and 2 children which are 2-3 trees
- two KVP and 3 children which are 2-3 trees

![example](/cs240/Feb5_23_example.png)

Restriction: all empty subtrees are at the same level (aka all of the non-empty leaves are at the same level)

---
##Feb 9 - Tutorial
* [Slides](https://www.student.cs.uwaterloo.ca/~cs240/w15/tutorials/Tutorial5.pdf)

----

##Feb 10

###2-3 Trees ct'd

#####Search(x):
- almost same as in BST
- compare x with the key (root)
- if found, break
- else find the unique subtree where we should look for x
- if subtree empty, return (not found)
- else recurse on the root of the subtree

Running time O(height)

#####Insertion
- First, we search to find the lowest internal node where the new key belongs.
- If the node has only 1 KVP, just add the new one to make a 2-node.
- Otherwise, order the three keys as a < b < c.
- Split the node into two 1-nodes, containing a and c,
- (recursively) insert b into the parent along with the new link

(see examples in the lecture and tutorial slides)

#####Deletion
- As with BSTs and AVL trees, we first swap the KVP with its successor, so that we always delete from a leaf.

Say we’re deleting KVP x from a node V : 
- If V is a 2-node, just delete x.
- ElseIf V has a 2-node immediate sibling U, perform a transfer:
 - Put the “intermediate” KVP (key that differentates between two children) in the parent between V and U into V , and replace it with the adjacent KVP from U.
 - This is kinda like a rotation
- Otherwise:
 - merge immediate 1-node sibling U, and the corresponding KVP from parent into a node where V was, then recurisvely "fix" parent by performing delete on it - if parent is an empty root, just remove it

(this is tricky to understand - review lecture and tutorial example slides)

runtime deletion?
- O(height)
- note that n is number of items (KVP) - NOT nodes!

#####Let T be a 2-3 tree of fixed height h - what is min number of KVP's that T can contain?
- It would be a binary tree 
 - we showed in assignment that h <= log(n+1) -1
- lower bound for h? each node has three children - ternary tree
 - 2*3^0 kvp on level 1 ... 2*3^h on level h+1
 - take sum and get n <= 3^(h+1) - 1 --- h >= log3(n+1) -1
- so both upper and lower bound are logn

###(a,b) trees

The 2-3 Tree is a specific type of (a, b)-tree:

An (a, b)-tree of order M is a search tree satisfying:
- Each internal node has at least a children, **unless it is the root**. The root has at least 2 children
- Each internal node has at most b children.
- If a node has k children, then it stores k-1 key-value pairs (KVPs). Leaves store no keys and are at the same level.

a B-tree of order M is a (ciel(M/2),M)-tree. A 2-3 tree has M = 3

height
- height of tree with n nodes is theta((log n)/(log M)) 
- we can find this similarily to how we've found bounds for heights of trees previously.
- a-b trees have h ∈ O(log_a n) and ∈ omega(log_b n)

search in a-b tree 
- b-1 KVPs in one node, log_a n height
- O(b loga n) 
- optimize using arrays for KVPs of a node, then use binary search - now takes O(logb * log_a n)


###Memory

When using big data, it may not fit in the internal memory -> use external memory

Pyramid - fastest + most expensive at top, slowest + least expensive at bottom

	MAIN (primary,internal memory)
	 - CPU register
	 - Cache L1
	 - Cache L2
	 - Cache L3
	 - RAM
	external memory
	 - hard drive
	 - cloud storage

External memory model: transfers of data between internal memory layers are ignored

Our goal: minimize the number of disk transfer from external to internal memory

- Tree-based data structures have poor memory locality: If an operation accesses m nodes, then it must access m spaced-out memory locations.
- Observation: Accessing a single location in external memory (e.g. hard disk) automatically loads a whole block (or “page”).
- In an AVL tree or 2-3 tree, theta(log n) pages are loaded in the worst case. If M is small enough so an M-node fits into a single page, then a B-tree of order M only loads theta((log n)/(log M)) pages. This can result in a huge savings: memory access is often the largest time cost in a computation.

### B-tree variations
- Max size M: Permitting one additional KVP in each node - allows insert and delete to avoid backtracking via pre-emptive splitting and pre-emptive merging.
- Red-black trees: Identical to a B-tree with minsize 1 and maxsize 3,but each 2-node or 3-node is represented by 2 or 3 binary nodes, and each node holds a “color” value of red or black.
- B+-trees: All KVPs are stored at the leaves (interior nodes just have keys), and the leaves are linked sequentially.

--
##Feb 12

B-tree requires O(logn/logB) disk transfers

B-tree of order M is a ciel(M/2) - M tree
- let B be the size of a block
- choose M s.t. a node fits into a single disk block
- search O(logn/log(M/2))

Theorem: In any comparison based dictioanary ADT, search(k) is done in omega(logn)

	Proof: decision tree
	any binary tree (yes, no branches) with N leaves has the height logN
	the decision tree has at least n+1 leaves then the height >= log(n+1)

####consider a dictionary with n KVPs - if the keys are exactly 0, ..., n-1
- then the item (K1, V1) wll be stored in A[K1]
- Search(x) - x is A[x]
- now search/insert/delete is theta(1)

Disadvantages:
- huge array
- initialization (slow)
- only for integer keys

###Hashing
- (Hacher in french means cut into small pieces)
- U - universe of n keys
- find a mapping of U in {0, ..., M-1} (natural numbers)
- then we have an array with indices 0 to M-1
- we're putting our keys into a smaller array of size M

e.g. hash function - h(k) = k mod 9

	18, 11, 5, 16, 31, 9
	Insert(18) - at index h(18) = 0
	Insert(11) - index 2

Problem: 
- multiple items land on the same slot in the hashing table T (collision)

To solve problem:

1. chaining - we allow multiple item on a single spot in T (linked list) (M<=n)
2. open addessing - if the slot is occupied, move to next slot in T (M>=n)

####chaining:
- h(k) is theta(1)
- Worst-case: all KVPs are in same slot in T - fix by choosing a better hash function
- Ideally, h is a uniform function
 - P(k is T[h(k)]) = 1/M
 - Think of h as a random uniform function over {0, ..., M-1}
- (#items)/(#slots in T) = n/M = the symbol alpha = load factor
 - choose M then choose h, trying to keep alpha>=1 small
- Search(x) is O(1+alpha)
- Insert is theta(1)
- Delete(search+delete) is O(1+alpha)

####Open addressing
- each slot of T has at most one KVP

Search(x) in this case will try to find the *probe sequence* <h(k,0), h(k,1), h(k,2), ... , h(k, M-1)>
- if h(k) is not X, then search h(k)+1
- you keep looking down the line until you find the key you're looking for
- if you find empty place, break - you haven't found it

Deletion:
- problem: neeed to distinguish between empty spot and deleted spot, so that search still works
- solution: user a marker for deleted items

improved Seach(x): 
- search through T[(h(k,0)], 1, ... M-1 until x found or empty (unmarked) found

Insert(x): search for empty (marked or unmarked) among T[(h(k,0)], T[(h(k,1)], ...
- for open addressing alpha <= 1 (M>=n)
- Run-time for search O(M)

----
##Feb 24

Recall: 
- Searching in a comparison based model is omega(logn)
- We want to search faster in dictionaries - use hashing
- Collisions - 2 or more items land in the same slot
- Open hashing: the item can "go out" of the hash table (e.g. Chaining)
- Closed hashing: items remain in the hash table (e.g. Open Addressing)

Chaining - worst case O(n), average O(alpha) for search - Insert is theta(1) - Delete is O(d)

####Problem with Open Addressing is **clustering** 

clustering - a bunch of values in the array adjacent to each other
- large clusters grow faster than small clusters (because we're more likely to land there)

idea: change the jump size p>1 
- if h(k,0) is occupied, jump p bins 
- h(k, i) = h(k, i-1) + p
- does not improve at all - will still get clusters

Quadratic hashing?
- h(k, i) = h(k) + c1*i + c2*i^2 for positive cs
- jumps around more, but still not great

####Double Hashing (rehashing)
- h(k,i) = h1(k) + i*h2(k) mod M
- the two hash functions are indepdent
- h1 determines the initial bin, h2 determines the jump size
- search, delete, insert, work same as linear probing (open addressing)

e.g.
- M=10 -- h1(k) = k mod 10 -- h2(k) = 7 - kmod7
- h(k,i) = h1(k) + ih2(k)   mod10
- insert some elements:
 - h(89,0)=h1(89)=9
 - h(18,0)=8
 - h(49,0)=9 - collison
 - h(49,1)=6
 - h(8,0)=8 collision
 - h(8,1)=4
 - h(28,0) collision
 - h(28,1)=5
 - etc
 
Problem - if we end up with a loop where we're skipping over empty spaces 
- we fix this by taking 2M and finding nex prime, then making jump size and M primes between them
- ??????????????

####Cuckoo Hashing

- h1, h2 are independent hash functions
- insert - k is inserted into h1(k) - if it's later kicked out it'll go to h2(k) - if it's kicked out again goes back to h1(k0
- this makes it easier to search!

------ 
##Feb 26

####Cuckoo Hashing continued:
- we use two independent hash functions h1,h2.
- The idea is to always insert a new item into h1(k).
- This might “kick out” another item, which we then attempt to re-insert into its alternate position.
- Insertion might not be possible if there is a loop. In this case, we have to rehash with a larger M.
- The big advantage is that an element with key k can only be in T[h1(k)] or T[h2(k)].

Code: cuckoo-insert(T,x) T: hash table, x: new item to insert
	
	y←x, i←h1(x.key) 
	do at most n times:
		swap(y,T[i])
		if y is “empty” then return “success” 
		if i = h1(y.key) then i ← h2(y.key) 
		else i ← h1(y.key)
	return “failure”

See slides for examples

Complexities of open addressing (we won't do the analysis, just state costs)
- load factor must be  < 1
- cuckoo requires load factor < 1/2
- linear
 - search and delete: 1/(1-alpha)^2  
 - delete: 1/(1-alpha)
- double hashing
 - search and delete: 1/(1-alpha) 
 - delete: 1/alpha * log(1/(1-alpha))
- cuckoo hashing
 - search and delete: 1
 - insert: alpha/(1-2alpha)^2

####Choosing a good hash function

Uniform Hashing Assumption: Each hash function value is equally likely. Proving is usually impossible, as it requires knowledge of the input distribution and the hash function distribution. We can get good performance by following a few rules.

A good hash function should:
- be very efficient to compute
- be unrelated to any possible patterns in the data 
- depend on all parts of the key

If all keys are integers (or can be mapped to integers), the following two approaches tend to work well:
- Division method: h(k) = k mod M.
 - We should choose M to be a prime not close to a power of 2 (***why)
- Multiplication method: h(k) = floor(M(kA − floor(kA))),
 - for some constant floating-point number A with 0 < A < 1.
 - Knuth suggests A = φ = root5−1 / 2 ≈ 0.618.

What if the keys are multi-dimensional, such as strings?
- Standard approach is to **flatten** string w to integer f(w) ∈ N, e.g. adding ascii values
- you can then hash the integer - h(f(k))
- Note: computing each h(f (k)) takes Ω(length of w) time

###Hashing vs. Balanced Search Trees

Advantages of Balanced Search Trees
- O(log n) worst-case operation cost
- Does not require any assumptions, special functions, or known properties of input distribution 
- No wasted space
- Never need to rebuild the entire structure

Advantages of Hash Tables
- O(1) cost, but only on average
- Flexible load factor parameters
- Cuckoo hashing achieves O(1) worst-case for search & delete

External memory:
- Both approaches can be adopted to minimize page loads.


If we have a very large dictionary that must be stored externally, how can we hash and minimize disk transfers? Say external memory is stored in blocks (or “pages”) of size S.
Most hash strategies covered access many pages (data is scattered) - except linear probing where they're usually in the same page, but linear hashing is bad for clustering and stuff anyways. Let's try something new:

###Extendible Hashing

- Similar to B-tree with height 1 and max size S at the leaves
- values have to range from 0 to 2^L - 1
- a **directory** (like root node) is in internal memory
- directory has array of size 2^d where d <= L is the **order**
- each directory points to a block stored in external memory, which contains at most S items (multiple entries can point to same block)
- look up key k by using d leading bits of h(k)
- Every block B stores a **local depth** k_B ≤ d
- Hash values in B agree on leading k_B bits
- All directory entries with the same k_B leading bits point to B
- 2^(d−k_B) directory entries point to block B.

Searching 
- done in the directory, then in a block:
- Given a key k, compute h(k)
- Leading d digits of h(k) give index in directory
- Load block B at this index into main memory
- Perform a search in B for all items with hash value h(k). Search among them for the one with key k.
- Cost:
 - CPU time: Θ(logS) if B stores items in sorted order.
 - Disk transfers: 1 (directory resides in internal memory)

insert(k,v)
- Search for h(k) to find the proper block B for insertion 
- If the B has space, then put (k,v) there
- ElseIf the block is full and kB < d, perform a block split:
􏰀 - Split B into two blocks B0 and B1.
􏰀 - Separate items according to the (kB + 1)-th bit. 􏰀 
 - Set local depth in B0 and B1 to kB + 1
􏰀 - Update references in the directory
- ElseIf the block is full and kB = d, perform a directory grow:
􏰀 - Double the size of the directory (d ← d + 1)
􏰀 - Update references appropriately
􏰀 - Then split block B (which is now possible)
- see slides for examples

delete(k) 
- reverse of insert: search for block B and remove k from it
- If block becomes too empty, then we perform a **block merge**
- If every block B has local depth kB ≤ d − 1, perform a **directory shrink**

Cost of insert and delete:
- CPU time: Θ(S) without a directory grow/shrink
- Directory grow/shrink costs Θ(2d) (but very rare).
- Disk transfers: 1 or 2, depending on whether there is a block split/merge.

Summary of extendible hashing
- Directory is much smaller than total number of stored keys and should fit in main memory.
- Only 1 or 2 external blocks are accessed by any operation.
- To make more space, we only add a block.
 - Rarely do we have to change the size of the directory. 
 - Never do we have to move all items in the dictionary (in contrast to normal hashing).
- Space usage is not too inefficient: can be shown that under uniform hashing, each block is expected to be 69% full.
- Main disadvantage: extra CPU cost of O(logS) or O(S)

------ 
##March 3

###Interpolation Search

- used on a sorted array, and min max of keys are known 
- it is like taking a guess of position of the key
- instead of binary search(A[l,r], k) - where you check the halfway point between l and r
- use the key (if it's a number) to guess its location
- check index l + floor( ( k−A[l]  /  A[r]−A[l] ) (r −l))
- Works well if keys are uniformly distributed: O(log log n) on average (**** calcuate this?)
- Bad worst case performance: O(n)

###Gallop Search

Problem in Binary-Search: Sometimes we cannot see the end of the array (data streams, a huge file, etc.)

Gallop-Search(A, k) 	A: An ordered array, k: a key

	i←0
	while i < size(A) and k > A[i] do 
		i ← 2i + 1
	return Binary-Search(A􏰄⌈i /2⌉, min(i , size(A) − 1)􏰅, k )

- O(log m) comparisons (m: location of k in A)

###Self-Organizing Search
- Unordered linked list is search: Θ(n), insert: Θ(1), delete: Θ(1) (after a search)
- Linear search to find an item in the list - Is there a more useful ordering?
 - No: if items are accessed equally likely
 - Yes: otherwise (we have a probability distribution for items)

Optimal static ordering: sorting items by their probabilities of access in non-increasing order minimizes the expected cost of Search.

Proof Idea: For any other ordering, exchanging two items that are out-of-order according to their access probabilities makes the total cost decrease.

Dynamic Ordering
- What if we do not know the access probabilities ahead of time?
- Move-To-Front(MTF): Upon a successful search, move the accessed item to the front of the list
- Transpose: Upon a successful search, swap the accessed item with the item immediately preceding it

Performance of dynamic ordering:
- Both can be implemented in arrays or linked lists
- Transpose can perform very badly on certain input (e.g. last two keys are most accessed, and keep getting swapped with each other)
- MTF works well in practice. Theoretically MTF is “competitive”: No more than twice as bad as the optimal “offline” ordering - where offline is optimal static ordering where we know the probabilities beforehand - transpose is worse

###Skip Lists
- Randomized data structure for dictionary ADT 
- A hierarchy of ordered linked lists
- good for small number of items, wanting simple implementation
- A skip list for a set S of items is a series of lists S0,S1,···,Sh such that:
􏰀 - Each list Si contains the special keys −∞ and +∞
􏰀 - List S0 contains the keys of S in non-decreasing order
􏰀 - Each list is a subsequence of the previous one, i.e., S0 ⊇ S1 ⊇ ··· ⊇ Sh
􏰀 - List Sh contains only the two special keys
- A skip list for a set S of items is a series of lists S0,S1,···,Sh 
- A two-dimensional collection of positions: levels and towers 
- Traversing the skip list: after(p), below(p)
 - drop down: p ← below(p)
 - scan forward: p ← after(p)

skip-search(L, k) L: A skip list, k: a key

	p ← topmost left position of L
	S ← stack of positions, initially containing p 
	while below(p) ̸= null do
		p ← below(p)
		while key(after(p)) < k do
			p ← after(p) 
		push p onto S
	return S
	
S contains positions of the largest key less than k at each level. after(top(S)) will have key k, iff k is in L. (*****why stack...?)

see slides for examples

-----
##March 5

###Skip lists continued

Skip-Insert(S , k , v)
􏰀- Randomly compute the height of new item: repeatedly toss a coin until
you get tails, let i the number of times the coin came up heads
􏰀- Search for k in the skip list and find the positions p0,p1,··· ,pi of the
items with largest key less than k in each list S0,S1,···,Si (by performing Skip-Search(S, k))
􏰀- Insert item (k,v) into list Sj after position pj for 0 ≤ j ≤ i (a tower of
height i)

Skip-Delete(S , k )
􏰀- Search for k in the skip list and find all the positions p0,p1,...,pi of
the items with the largest key smaller than k, where pj is in list Sj. (this is the same as Skip-Search)
􏰀- For each i, if key(after(pi)) == k, then remove after(pi) from list Si
􏰀- Remove all but one of the lists Si that contain only the two special keys

see slides for examples

Summary of Skip Lists
- Expected space usage: O(n)
- Expected height: O(log n)
- Worst case height is n *********what - shouldn't it be infinite?
- A skip list with n items has height at most 3 log n with probability at least 1 − 1/n^2
- Skip-Search: O(log n) expected time, O(n) worst if we have to go through all the items in some way
- Skip-Insert and delete have same run time as search
- Skip lists are fast and simple to implement in practice

###Multi-dimensional data
- Various applications: 
 - Attributes of a product (laptop: price, screen size, processor speed, RAM, hard drive,· · · )
􏰀 - Attributes of an employee (name, age, salary,· · · )
- Dictionary for multi-dimensional data - a collection of d-dimensional items
- Each item has d **aspects** (coordinates): (x0, x1, · · · , xd−1) 
- Operations: insert, delete, **range-search query**
- (Orthogonal) Range-search query: specify a range (interval) for certain aspects, and find all the items whose aspects fall within given ranges. Example: laptops with screen size between 11 and 13 inches
- aspect values will be numbers, and each item corresponds to a point in d-dimensional space - here we will concentrate on d = 2, i.e., points in Euclidean plane

####One-Dimensional Range Search
- First solution: ordered arrays
􏰀 - Running time: O(log n + k), k: number of reported items
􏰀 - Problem: does not generalize to higher dimensions
- Second solution: balanced BST (e.g., AVL tree)
￼￼
BST-RangeSearch(T , k1 , k2 ) - T: A balanced search tree, k1,k2: search keys Report keys in T that are in range [k1,k2]

	1if T = nil then return
	if key(T) < k1 then
		BST-RangeSearch(T .right , k1 , k2 )
	if key(T) > k2 then
		BST-RangeSearch(T .left , k1 , k2 )
	if k1 ≤ key(T) ≤ k2 then
		BST-RangeSearch(T .left , k1 , k2 )
		report key(T)
		BST-RangeSearch(T .right , k1 , k2 )

see slides for example

Terms from searched tree:
- P1: path from the root to a leaf that goes right if k < k1 and left otherwise
- P2: path from the root to a leaf that goes left if k > k2 and right otherwise
- Partition nodes of T into three groups:
 - boundary nodes: nodes in P1 or P2
 - inside nodes: non-boundary nodes that belong to either (a subtree rooted at a right child of a node of P1) or (a subtree rooted at a left child of a node of P2)
 - outside nodes: non-boundary nodes that belong to either (a subtree rooted at a left child of a node of P1) or (a subtree rooted at a right child of a node of P2)
- k: number of reported items 
- Nodes visited during the search: O(log n) boundary nodes, O(k) inside nodes, no outside nodes => therefore running time O(log n + k)￼￼

####Each item has 2 aspects (coordinates): (xi , yi)

Each item corresponds to a point in Euclidean plane 

Options for implementing d-dimensional dictionaries:
- Reduce to one-dimensional dictionary: combine the d-dimensional key into one key
 - Problem: Range search on one aspect is not straightforward
􏰀- Use several dictionaries: one for each dimension 
 - Problem: inefficient, wastes space
􏰀- Partition trees
 - A tree with n leaves, each leaf corresponds to an item
 - Each internal node corresponds to a region
 - quadtrees, kd-trees
􏰀- multi-dimensional range trees

###Quadtrees
- We have n points P = {(x0, y0), (x1, y1), · · · , (xn−1, yn−1)} in the plane
- How to build a quadtree on P:
􏰀 - Find a square R that contains all the points of P (We can compute minimum and maximum x and y values among n points)
􏰀 - Root of the quadtree corresponds to R
􏰀 - **Split**: Partition R into four equal subsquares (quadrants), each correspond to a child of R
􏰀 - Recursively repeat this process for any node that contains more than one point
􏰀 - Points on split lines belong to left/bottom side
􏰀 - Each leaf stores (at most) one point
􏰀 - We can delete a leaf that does not contain any point

see slides for examples

- Search: Analogous to binary search trees 
- Insert:
􏰀 - Search for the point
􏰀 - Split the leaf if there are two points
- Delete:
􏰀 - Search for the point
􏰀 - Remove the point
􏰀 - Walk back up in the tree to discard unnecessary splits

Range Search:  QTree-RangeSearch(T , R) -- T: A quadtree node, R: Query rectangle

	if (T is a leaf) then
		if (T.point ∈ R) then
			report T.point 
	for each child C of T do
		if C.region ∩ R != ∅ then 
		QTree-RangeSearch(C , R)

Terms
- spread factor: spread factor of points P : β(P) = dmax/dmin
- dmax and dmin: maximum and minimum distance between two points in P

height of quadtree: 

	dmax = d / sqrt2  if d is the width and height of R 
	d / 2^(h-1) > dmin > d/2^h
	sqrt2*dmax / 2^(h-1) > dmin > sqrt2*dmax / 2^h
	2*sqrt2*dmax > 2^h*dmin > sqrt2*dmax
	2*sqrt2*dmax/dmin > 2^h > sqrt2*dmax/dmin
	so h ∈ Θ(log_2 dmax/dmin)

Worst-case complexity to build initial tree = Θ(#nodes · height) = Θ(nh) *****why 

Worst-case complexity of range search = O(#nodes · height) = O(nh) even if the answer is ∅

Quadtree Conclusion
- Very easy to compute and handle
- No complicated arithmetic, only divisions by 2 (usually the boundary box is padded to get a power of two).
- Easily generalizes to higher dimensions (octrees, etc. ).
- Space wasteful
- Major drawback: can have very large height for certain nonuniform distributions of points

-----
##March 10

###kd-trees
- We have n points P = {(x0, y0), (x1, y1), · · · , (xn−1, yn−1)} in the plane
- Quadtrees split square into quadrants regardless of where points actually lie - idea: split the points into two (roughly) equal subsets 
- How to build a kd-tree on P:
􏰀 - Split P into two equal subsets using a vertical line
􏰀 - Split each of the two subsets into two equal pieces using horizontal lines
􏰀 - Continue splitting, alternating vertical and horizontal lines, until every point is in a separate region
- Initially, we sort the n points according to their x-coordinates, so the root of the tree is the point with median x coordinate (index floor(n/2) in the sorted list)
􏰀- All other points with x coordinate less than or equal to this go into the left subtree; points with larger x-coordinate go in the right subtree.
􏰀- At alternating levels, we sort and split according to y-coordinates instead.
- *******how do we deal with multiple points on boundary?

see slides for examples

kd-rangeSearch(T,R,split[← ‘x’]) -- T: A kd-tree node, R: Query rectangle

	if T is empty then return 
	if T.point ∈ R then
		report T.point 
	if split = ‘x’ then
		if T.point.x ≥ R.leftSide then 
			kd-rangeSearch(T.left , R , ‘y’)
		if T.point.x < R.rightSide then 
			kd-rangeSearch(T.right , R , ‘y’)
	if split = ‘y’ then
		if T.point.y ≥ R.bottomSide then
			kd-rangeSearch(T .left , R , ‘x’) 
		if T.point.y < R.topSide then
			kd-rangeSearch(T .right , R , ‘x’)

The complexity 
- O(k + U) where k is the number of keys reported and U is the number of regions we go to but unsuccessfully (regions which intersect one of the sides of R, but are not fully in R)
- Q(n): Maximum number of regions in a kd-tree with n points that intersect a vertical (horizontal) line
 - Q(n) satisfies the following recurrence relation: Q(n) = 2Q(n/4) + O(1)
 - It solves to Q(n) = O(√n) 
- Therefore, the complexity of range search in kd-trees is O(k + sqrt(n))

Variations of k-d trees:
- store points in nodes
- also store x <= xi or y <= yi in nodes
- just store the separating line in nodes

###kd-tree: Higher Dimensions
- kd-trees for d-dimensional space
􏰀- at the root the point set is partitioned based on the first coordinate
􏰀- at the children of the root the partition is based on the second coordinate
􏰀- at depth d − 1 the partition is based on the last coordinate
􏰀- at depth d we start all over again, partitioning on first coordinate
- Storage: O(n)
- Construction time: O(n log n) *******why?
- Range query time: O(n^(1 − 1/d) + k) Note: d is considered to be a constant

----
##March 12

###Range Trees
- We have n points P = {(x0, y0), (x1, y1), · · · , (xn−1, yn−1)} in the plane
- A range tree is a tree of trees (a multi-level data structure) 
- How to build a range tree on P:
􏰀 - Build a balanced binary search tree τ determined by the x-coordinates of the n points
􏰀 - For every node v ∈ τ, build a balanced binary search tree τassoc(v) (associated structure of τ) whose keys are the y-coordinates and whose nodes are those of the subtree of τ with root node v

Operations:
- Search: trivially as in a binary search tree 
- Insert: insert point in τ by x-coordinate
 - From inserted leaf, walk back up to the root and insert the point in all associated trees τassoc(v) of nodes v on path to the root
- Delete: analogous to insertion 
 - Note: re-balancing is a problem!

Range Trees: Range Search
- A two stage process
- To perform a range search query R = [x1, x2] × [y1, y2]:
􏰀 - Perform a range search (on the x-coordinates) for the interval [x1,x2] in τ (BST-RangeSearch(τ, x1, x2))
􏰀 - For every outside node, do nothing.
􏰀 - For every “top” inside node v, perform a range search (on the y-coordinates) for the interval [y1,y2] in τassoc(v). During the range search of τassoc(v), do not check any x-coordinates (they are all within range).
􏰀 - For every boundary node, test to see if the corresponding point is within the region R (the y coordinate is stored in the node)

Complexities
- Running time: O(k + log^2 n)
- Range tree construction time: O(n log n) *****justify
- Range tree space usage: O(n log n)

Range trees for d-dimensional space Trees 􏰀 
- Storage: O(n (log n)^(d−1))
-􏰀 Construction time: O(n (log n)^(d−1)) 
- Range query time: O((logn)^d + k) 


###Tries

Trie (Radix Tree): A dictionary for binary strings
- Comes from retrieval, but pronounced “try”
- A binary tree based on bitwise comparisons
- Similar to radix sort: use individual bits, not the whole key
- Structure of trie:
 - A left child corresponds to a 0 bit
 - A right child corresponds to a 1 bit
- Keys can have different numbers of bits
- Keys are not stored in the trie: a node x is flagged if the path from root to x is a binary string present in the dictionary

see slides for example

Search(x):
- start from the root
- take the left link if the current bit in x is 0 and take the right link if it is 1 (return failure if the link is missing)
- if there are no extra bits in x left and the current node is flagged then success (x is found)
- else, if the current node is a leaf, then - failure (x is missing)
- recurse

Insert(x)
- First search for x
- If we finish at a leaf with key x, then x is already in trie: do nothing
- If we finish at a leaf v and x has extra bits then flag v and expand the trie from the node v by adding necessary nodes that correspond to extra bits.
- If we finish at an internal node and there are no extra bits: the node is then flagged
I If we finish at an internal node and there are extra bits: expand trie by adding necessary nodes that correspond to extra bits

Delete(x)
- Search for x
- if x found at an internal flagged node, then unflag the node
- if x found at a leaf vx , delete the leaf and all ancestors of vx until we reach an ancestor that has two children or we reach a flagged node

Time Complexity of all operations: 
- Θ(|x|)
- |x|: length of binary string x, i.e., the number of bits in x

-----
##March 17

##Compressed Tries (Patricia Tries)
- Reduces storage requirement: eliminate unflagged nodes with only one child
- Every path of one-child unflagged nodes is compressed to a single edge
- Each node stores an index indicating the next bit to be tested during a search (e.g. index= 0 for the first bit, index= 2 for the second bit, etc)
- A compressed trie storing n keys always has at most n − 1 internal (non-leaf) nodes

Search(x):
- Follow the proper path from the root down in the tree to a leaf
- If search ends in an internal flagged node, it is successful *********** you still have to check, right?
- If search ends in an internal unflagged node, it is unsuccessful
- If search ends in a leaf, we need to check again if the key stored at the
leaf is indeed x

Delete(x):
- Perform Search(x)
- if search ends in an internal node, then
 - if the node has two children, then unflag the node and delete the key
 - else delete the node and make its only child the child of its parent
- if search ends in a leaf, then delete the leaf and
- if its parent is unflagged, then delete the parent

Insert(x):
- Perform Search(x)
- If the search ends at a leaf L with key y, compare x against y to determine the first index i where they disagree.
- Create a new node N with index i.
- Insert N along the path from the root to L so that the parent of N has index < i and one child of N is either L or an existing node on the path from the root to L that has index > i.
- The other child of N will be a new leaf node containing x.
- If the search ends at an internal node, we find the key corresponding to that internal node and proceed in a similar way to the previous case

***** there are no examples on the slides - make sure you find some (tutorial? other class notes?)

##Multiway Tries

- To represent Strings over any fixed alphabet Σ
- Any node will have at most |Σ| children
- this looks realllly similar to initial state diagram things in cs241
- Append a special end-of-word character, say $, to all keys - this lets us store substrings of other strings in the tree easily
- we can have Compressed multi-way tries just like patricia trees

###Pattern Matching

Search for a string (pattern) in a large body of text
- T[0..n − 1] – The *text* (or *haystack*) being searched within
- P[0..m − 1] – The pattern (or needle) being searched for
- Strings over alphabet Σ
- Return the first i such that P[j] = T[i + j] for 0 ≤ j ≤ m − 1
- This is the first occurrence of P in T
- If P does not occur in T, return FAIL

Some terms:
- Substring T[i..j] 0 ≤ i ≤ j < n: a string of length j − i + 1 which consists of characters T[i], . . .T[j] in order
- A prefix of T: a substring T[0..i] of T for some 0 ≤ i < n
- A suffix of T: a substring T[i..n − 1] of T for some 0 ≤ i ≤ n − 1

A *guess* is a position i such that P might start at T[i].
- Valid guesses (initially) are 0 ≤ i ≤ n − m.

A *check* of a guess is a single position j with 0 ≤ j < m where we compare T[i + j] to P[j]. 
- We must perform m checks of a single correct guess, 
- but may make (many) fewer checks of an incorrect guess.

####Brute-force Algorithm --  Idea: Check every possible guess.

BruteforcePM(T[0..n − 1], P[0..m − 1]) T: String of length n (text), P: String of length m (pattern)

	for i ← 0 to n − m do
		match ← true
		j ← 0
		while j < m and match do
			if T[i + j] = P[j] then
				j ← j + 1
			else
				match ← false
		if match then
			return i
	return FAIL

Worst case performance Θ((n − m + 1)m)
- m ≤ n/2 ⇒ Θ(mn) ***************? point

Pattern Matching
- More sophisticated algorithms
- Deterministic finite automata (DFA)
- KMP, Boyer-Moore and Rabin-Karp
- Do extra preprocessing on the pattern P
- We eliminate guesses based on completed matches and mismatches.

####String matching with finite automata

- Let T be a text string over a finite alphabet Σ and P a pattern to find in T.
- Many string-matching algorithms build a finite automaton that scans the text string T for all occurrences of the pattern P
- String-matching automata are very efficient: they examine each text character exactly once, taking constant time per text character
- The time to build the automaton, however, can be large if Σ is large

Note - This is a DFA from cs 241
- Let T be the text string of length n,
- P - the pattern to search of length m and
- δ the transition function of a finite automaton for pattern P
- the below algorithm only makes sense to me because I'm in cs241 - I'm assuming m is the final state (which here is the 'last state' or length of pattern) and the transition function works the same as 241 - if you're confused check out my 241 notes which are also posted here

FINITE-AUTOMATON-MATCHER(T, δ, m)

	n ← length[T]
	q ← 0
	for i ← 1 to n do
		q ← δ(q,T[i])
		if q = m
			then print ”Pattern occurs with shift” i − m

Analysis
- matching time on a text string of length n is Θ(n)
- this does not include the preprocessing time required to compute the transition function δ.
- we can find all occurrences of a length-m pattern in a length-n text over a finite alphabet Σ with O(m|Σ|) preprocessing time and Θ(n) matching time.

####KMP Algorithm
- Compares the pattern to the text in left-to-right
- Shifts the pattern more intelligently than the brute-force algorithm
- When a mismatch occurs, what is the most we can shift the pattern (reusing knowledge from previous matches)?
- Answer: the largest prefix of P[0..j] that is a suffix of P[1..j]

You're gonna want to view the examples on the slides for this one, but here's the overview:
- Define F[j] as the value of the first sliding position past the current one that matches the text T, up to position T[i − 1] = P[j − 1]
- This can be computed by trying all sliding positions until finding the first one matching the text
- F[j] is the length of the largest prefix of P[0..j] that is also a suffix of P[1..j]
- so we can preprocess the pattern to find matches of prefixes of the pattern with the pattern itself
- when you shift, you can start at the offset of F[j] to continue to comparing, since you know the first F[j] bits match

failureArray(P) --  P: String of length m (pattern)

	F[0] ← 0
	i ← 1
	j ← 0
	while i < m do
		if P[i] = P[j] then
			F[i] ← j + 1
			i ← i + 1
			j ← j + 1
		else if j > 0 then
			j ← F[j − 1] ---- it took me a bit to figure out why this is, make sure you get it
		else
			F[i] ← 0
			i ← i + 1

KMP(T, P) -- T: String of length n (text), P: String of length m (pattern)

	F ← failureArray (P)
	i ← 0
	j ← 0
	while i < n do
		if T[i] = P[j] then
			if j = m − 1 then
				return i − j //match
			else
				i ← i + 1
				j ← j + 1
			else
				if j > 0 then
					j ← F[j − 1]
				else
					i ← i + 1
	return −1 // no match

Analysis
- failureArray
 - At each iteration of the while loop, either i increases by one, or the guess index i − j increases by at least one (F[j − 1] < j)
 - There are no more than 2m iterations of the while loop
 - Running time: Θ(m)
- KMP
 - failureArray can be computed in Θ(m) time
 - At each iteration of the while loop, either i increases by one, or the guess index i − j increases by at least one (F[j − 1] < j)
 - There are no more than 2n iterations of the while loop
 - Running time: Θ(n)

Another example
- T =abacaabaccabacabaabb
- P =abacab
- TRY THIS! You weren't paying attention / not in lecture so make sure you can do this

###Boyer-Moore Algorithm

Based on three key ideas:
- Reverse-order searching: Compare P with a subsequence of T moving backwards
- Bad character jumps: When a mismatch occurs at T[i] = c
 - If P contains c, we can shift P to align the last occurrence of c in P with T[i]
 - Otherwise, we can shift P to align P[0] with T[i + 1]
- Good suffix jumps: If we have already matched a suffix of P, then get a mismatch, we can shift P forward to align with the previous occurence of that suffix (with a mismatch from the actual suffix).
 - Similar to failure array in KMP.
- Can skip large parts of T

isn't second bad character example 6 checks? - yes

####Last-Occurrence Function
- Preprocess the pattern P and the alphabet Σ
- Build the last-occurrence function L mapping Σ to integers
- L(c) is defined as
 - the largest index i such that P[i] = c or
 - −1 if no such index exists
- The last-occurrence function can be computed in time O(m + |Σ|)
- In practice, L is stored in a size-|Σ| array.

boyer-moore(T,P)

	L ← last occurrance array computed from P
	S ← good suffix array computed from P
	i ← m − 1, j ← m − 1
	while i < n and j ≥ 0 do
		if T[i] = P[j] then
			i ← i − 1
			j ← j − 1
		else
			i ← i + m − 1 − min(L[T[i]], S[j])
			j ← m − 1
	if j = −1 return i +1
	else return FAIL

***I had to stare at this for a while to get it - make sure you get why and how this works*** Check out examples from tutorial

Exercise: Prove that i − j always increases on lines 9–10 - I might have done this - check with Andreas and maybe post something more formal here


Boyer-Moore algorithm conclusion
- Worst-case running time ∈ O(n + |Σ|) -- This complexity is difficult to prove.
- What is the worst case?
 - On typical English text the algorithm probes approximately 25% of the characters in T (****what does probe mean?)
- Faster than KMP in practice on English text.

###Rabin-Karp Fingerprint Algorithm
- Idea: use hashing
- Compute hash function for each text position
- No explicit hash table: just compare with pattern hash
- If a match of the hash value of the pattern and a text position found, then compares the pattern with the substring by naive approach

Example:

	Hash ”table” size = 97
	Search Pattern P: 5 9 2 6 5
	SearchTextT: 314159265358979323846 
	Hash function: h(x) = x mod 97 and h(P) = 95.
	31415 mod 97 = 84
	14159 mod 97 = 94
	41592 mod 97 = 76
	15926 mod 97 = 18
	59265 mod 97 = 95 - found!

Guaranteeing correctness - need full compare on hash match to guard against collisions (since multiple strings could hash to the same goal number)

Running time
- Hash function depends on m characters
- Running time is Θ(mn) for search miss (how can we fix this?) *****what do they mean by search miss?
- The initial hashes are called fingerprints.

Rabin & Karp discovered a way to update these fingerprints in constant time.
- To go from the hash of a substring in the text string to the next hash value only requires constant time.
- Use previous hash to compute next hash O(1) time per hash, except first one
- use mod arithmetic:

example:

	15926 mod97 = (41592 − (4 ∗ 10000 )) ∗ 10 + 6 
				= (76    − (4 ∗ 9 ))     ∗ 10 + 6
                = 406 = 18

Conclusion:
- Choose table size at random to be huge prime 
- Expected running time is O(m + n)
- Θ(mn) worst-case, but this is (unbelievably) unlikely
- Main advantage: extends to 2d patterns and other generalizations

###Suffix Tries and Suffix Trees
- What if we want to search for many patterns P within the same fixed text T?
- Idea: Preprocess the text T rather than the pattern P
- Observation: P is a substring of T if and only if P is a prefix of some
suffix of T.
- We will call a suffix trie, a trie that stores all suffixes of a text T, and a
suffix tree the compressed suffix trie of T.
- Build the suffix trie, i.e. the trie containing all the suffixes of the text
- Build the suffix tree by compressing the trie above (like in Patricia trees)
- Store two indexes l,r on each node v (both internal nodes and leaves) where node v corresponds to substring T[l..r] 

see pictures on slides for trie and tree examples!
**********confused about the numbers on the trie... why is [3..3] in multiple places? shouldn't they each be subtrings?*****

note on the slides how to use indices to show which substring is at the roots of the tree (easier to write)

####Suffix Trees: Pattern Matching

To search for pattern P of length m:
- Similar to Search in compressed trie with the difference that we are looking for a prefix match rather than a complete match
- If we reach a leaf with a corresponding string length less than m, then search is unsuccessful
- Otherwise, we reach a node v (leaf or internal) with a corresponding string length of at least m
- It only suffices, to check the first m characters against the substring of the text between indices of the node, to see if there indeed is a match

*****slide 90 - shouldn't it be checking at index 6?*****

Pattern Matching Conclusion 
(insert pic from slides! and make sure things make sense)

