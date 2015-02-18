CS 240R (regular)

Daniela Maftuleac

DC 2305C

dmaftule@uwaterloo.ca

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
-- constant + constant * (2n-1) - we can simplify this
-- constant + constant * n 
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

##TUTORIAL - Monday Jan 12
https://www.student.cs.uwaterloo.ca/~cs240/w15/tutorials/Tutorial1.pdf

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


----------

Jan 13

e.g. show 5n^2 log(n) ∈ Ω(n*log^2(n))
prove there exists c, n_0 in positive reals s.t. for all n > n_0, 0 <= cnlog^2n <= 5n^2 logn
log(n) <= n  --> 0 <= clogn <= 5n (for c = 5) --> cnlog^2n <= 5n^2logn - which is what we want
so c = 5
so we can make n_0 = 1 (it needs to be > 0 and an integer, and the above statement works for all +ve values)

***functions***
1) constant function: f(n) = c
	useful to count primitive operations
2) logarithmic function: f(n) = log(n)
	useful when dividing the size of the input into 2
	example: j = [some value]
			 while j>= 0 do
			 	j <- j/2
3) linear function: f(n) = n
4) pseudo linear: f(n)= nlogn
	e.g. merge sort (more examples later)
5) quadratic: f(n) = n^2 
	e.g. for i<-0 to n
	     	j++;
6) cubic (polynomial): f(n) = n^3 (n^k)
7) exponential: f(n) = 2^n

How Growth Rates Affect Running Time:
It is interesting to see how the running time is affected when the size of the problem instance doubles (i.e., n → 2n)
1) constant complexity: T(n) = c, T(2n) = c
2) logarithmic complexity: T(n)=clogn,T(2n)=T(n)+c 
3) linear complexity: T (n) = cn, T(2n) = 2T(n)
4) pseudo linear: T(n)=cnlogn,T(2n)=2T(n)+2cn
5) quadratic complexity: T(n) = cn^2, T(2n) = 4T(n)
6) cubic complexity: T(n) = cn^3, T (2n) = 8T(n)
7) exponential complexity: T(n) = c2^n, T(2n) = (T(n))^2/c

Complexity vs. Running Time:
Note that if algorithm A1 has lower complexity than algorithm A2, that means it runs faster for large problem instances - but it could still be slower for small problem instances! 
If A1 and A2 have the same complexity - we can't determine from this information which will be faster for some problem instance.
Note that if A1 is O(n^3) and A2 is O(n^2) we cannot say A2 is more efficient than A1! This is because big O >= not =, so A1 could actually run in constant time and we can still say it's O(n^3)

remember we can use l'hopitals rule, lim to inf of f(n)/g(n)
if limit is 0, g is bigger
if limit is inf, f is bigger
if limit is constant, they're equal

log is alwayyys less than polynomial
[log(n)]^k ∈ o(n^i) for any real numbers k, i
(logn)^100000 will eventually be smaller than n^0.00001

example: n(2+sin(npi/2)) is θ(n) -- in slides
we need to prove 0 <= c_1 n <= n (2+sin(npi/2)) <= c_2 n
0 <= c_1 <= (2+sin(npi/2)) <= c_2
0 <= c_1 - 2 <= sin(npi/2) <= c_2 - 2
sin is bounded, so c_1 -2 = -1 --> c_1 = 1
similarily c_2 = 3
n_0 = 1 since this works for all n


Relationships between Order Notations:
e.g. f(n) ∈ Θ(g(n)) iff g(n) ∈ Θ(f (n))
this makes sense because f = g iff g = f
we can say a lot of other similar things using the logic of >, =,  < 


Algebra of Order Notations
“Maximum” rules: 
Suppose that f(n) > 0 and g(n) > 0 for all n ≥ n_0. Then:
O(f(n) + g(n)) = O(max{f(n), g(n)}) 
Θ(f(n) + g(n)) = Θ(max{f(n), g(n)}) 
Ω(f(n) + g(n)) = Ω(max{f(n), g(n)})
Transitivity: If f(n) ∈ O(g(n)) and g(n) ∈ O(h(n)) then f(n) ∈ O(h(n)).

SUGGESTION: print the formulae and misc. math facts to refer to often
(arithmetic sequence, geometric sequence, harmonic sequence, etc.)

Tip: to find θ bounds, you can find both at the same time, or sometimes it's easier to find the top bound and lower bound separately

Loop analysis: 
1) Identify elementary operations that require constant time (denoted Θ(1) time).
2) Loops: The complexity of a loop is expressed as the sum of the complexities of each iteration of the loop.
3) Analyze independent loops separately, and then add the results (use “maximum rules” and simplify whenever possible).
4) If loops are nested, start with the innermost loop and proceed outwards. In general, this kind of analysis requires evaluation of nested summations.

example of loop analysis: Test1(n) - on slides
sum <-0 					θ(1)
for i<-1 to n do 			sum_[i=1]^n 1
	for j<-i to n do 		sum_[j=1]^n 1
		sum<-sum+(i-j)^2 	θ(1)
		sum<-sum^2 			θ(1)
T(n) = θ(1) + sum_[i=1]^n sum_[j=i]^n (θ(1) + θ(1))
	 = θ(n) + sum_[i=1]^n (θ(1) + (n-i+1))
	 = θ(n) + (θ(1) (n + n-1 + ... + 1)
	 = θ(1) + θ(1)*n*(n-1)/2
	 = θ(1) + θ(1)*n^2+n / 2
	 = θ(n^2+n / 2)
	 = θ(n^2 + n)
	 = θ(n^2)

can also do:
sum <-0 					O(1)													
for i<-1 to n do 			O(n)													
	for j<-i to n do 		O(n) - then multiply by O(n) above to get O(n^2)
		sum<-sum+(i-j)^2 	O(1)
		sum<-sum^2 			O(1)

Test2 - second example
  θ(1) + sum_[i=1]^n sum_[j=i]^n (θ(1)  + sum_[k=i]^j(θ(1) + θ(1)))
= θ(1) + sum_[i=1]^n sum_[j=i]^n (θ(1)  + θ(1)*(j-i+1))
...
= θ(n^3)

-- to over estimate: 1...n for all loops - nested loops so multiply by each other to get O(n^3)
-- to underestimate: first loop let's say it just goes from 1 to n/4, second from n/4 to n/2, third from n/4 to n/2 -- we get underestimate omega(n^3)


Test3 - third example
sum ← 0 				θ(1)
for i ← 1 to n do 		sum i=1..n 1
	j←i 				θ(1)
	while j ≥1 do 		 
		sum ← sum + i/j 
		j ← ⌊j/2⌋  		
return sum

for just the while loop:
when j = i, the time it takes is θ(1) + θ(1) + θ(floor(i/2))  if i >= 1
                    			  otherwise constant time (to check and break)
solving the recurrance, the while part takes θ(logi) time
so we have running complexity:
	θ(1) + sum i..n (θ(1) + θ(logi))
  = θ(n) + sum i..n θ(logi)
  = θ(n) + θ(log(n!))
  = θ(n) + θ(nlog(n)) ******************* where's this from
  = θ(n logn)

---
Jan 15  (was sick, didn't pay attention, notes made after)

***Mergesort and logn running time***
	input: Array A of n integers
	Step 1: 
		split A into two subarrays: AL consists of the first ceil(n/2) elements in A and AR consists of the rest of the elements - floor(n/2)of them
	Step 2: 
		Recursively run MergeSort on AL and AR .
	Step 3: 
		After AL and AR have been sorted, use a function Merge to merge them into a single sorted array. This can be done in time Θ(n).
pseudo-code: MergeSort(A, n)
	1. 	if n = 1 then
	2. 		S←A
	3. 	else
	4. 		nL←⌈n/2⌉
	5. 		nR←⌊n/2⌋
	6. 		AL ← [A[1],...,A[nL]]
	7. 		AR ←[A[nL +1],...,A[n]]
	8. 		SL ← MergeSort (AL , nL )
	9. 		SR ← MergeSort (AR , nR )
	10.		S ← Merge(SL,nL,SR,nR)
	11.	return S
analyzing:
	Let T(n) denote the time to run MergeSort on an array of length n.
	Step 1 takes time Θ(n) (transfer elements into new arrays)
	Step 2 takes time T 􏰁⌈n/2⌉ 􏰂+ T 􏰁⌊n/2⌋􏰂 
	Step 3 takes time Θ(n) 
recurrence relation for T(n):
	T(n) = T(⌈n/2⌉) 􏰂+ T(􏰁⌊n/2⌋􏰂) + Θ(n) if n>1
		 = Θ(1) if n=1
exact recurrence with constants:
	T(n) = T(⌈n/2⌉) 􏰂+ T(􏰁⌊n/2⌋􏰂) + cn if n>1
		 = d if n=1
"sloppy recurrence" (floors and ceilings removed):
	T(n) = 2T(n/2) 􏰂+ cn if n>1
		 = d if n=1
The exact and sloppy recurrences are identical when n is a multiple of 2
If we solve it we get T(n) ∈ Θ(n logn)
This is also true for all n, not just even n


Another example of logn time: binary search
L(n) = L(n/2) + O(1) if n>=2
     = O(1) if n <= 1

***Conditionals - varying runtime***
Insertion sort (A, n)
	for i=1 ... n-1
		key = A[i]
		j=i-1
		while(j>=0 and A[j] > key)
			A[j+1] = A[j]
			j--
		A[j+1] = key
the number	of executions depends on the input. It's between 1 and i.

We assume worst case analysis when runtimes vary so wildly.
	That is, compute an upper bound on the runtime
	for insertion sort: 
		runtime <= sum i=1..n-1 (i-1)O(1) = O(1)sum i=1..n-1 i which is O(n^2)
	If you use upper bounds, alos show they are tight
	To show that they are tight, give an instance (of size n) on which the runtime asymptotically actually occurs
	e.g. consider an array in reverse-sorted order for insertion sort

"worst case":
	T(n) = runtime of max instance I s.t. I has size n 
	     = max {T(I)}
"average case":
	T(n) = sum of instances of size n / # instances of size n
"best case":
	T(n) = min {T(I)}


*** Abstract Data Types (ADT) ****
A description of information and a collection of operations on that information.
The information is accessed only through the operations.
We can have various realizations of an ADT, which specify: 
	How the information is stored (data structure)
	How the operations are performed (algorithms)

e.g. Dynamic arrays
Linked lists support O(1) insertion/deletion, but element access costs O(n).
Arrays support O(1) element access, but insertion/deletion cost O(n).
Dynamic arrays offer a compromise: O(1) element access, and O(1) insertion/deletion at the end.
Two realizations of dynamic arrays:
	Allocate one HUGE array, and only use the first part of it.
	Allocate a small array initially, and double its size as needed. (Amortized analysis is required to justify the O(1) cost for insertion/deletion at the end — take CS 341/466!)

e.g. Stack ADT
Stack: collection of items with operations: 
	push: inserting an item
	pop: removing the most recently inserted item
Items are removed in LIFO (last-in first-out) order. 
We can have extra operations: size, isEmpty, and top
Applications: 
	Addresses of recently visited sites in a Web browser
	procedure calls
Realizations of Stack ADT 
	using arrays
	using linked lists

Queue ADT
Queue: a collection of items with operations: 
	enqueue: inserting an item
	dequeue: removing the least recently inserted item
Items are removed in FIFO (first-in first-out) order. Items enter the queue at the rear and are removed from the front. 
We can have extra operations: size, isEmpty, and front
Realizations of Queue ADT:
	using (circular) arrays 
	using linked lists

Priority Queue ADT
Priority Queue: a collection of items (each having a priority) with operations:
	insert: inserting an item tagged with a priority
	deleteMax: removing the item of highest priority deleteMax is also called extractMax.
Applications: 
	typical “todo” list
	simulation systems
The above definition is for a maximum-oriented priority queue. 
A minimum-oriented priority queue is defined in the natural way, by replacing the operation deleteMax by deleteMin.

Using a Priority Queue to Sort: PQ − Sort(A)
initialize PQ to an empty priority queue 
for i ← 0 to n − 1 do
	PQ .insert (A[i ], A[i ]) 
for i ← 0 to n − 1 do
	A[n − 1 − i] ← PQ.deleteMax()

Realizations of Priority Queues
	Attempt 1: Use unsorted arrays 
		insert: O(1)
		deleteMax: O(n)
		Using unsorted linked lists is identical.
		This realization used for sorting yields selection sort.
	Attempt 2: Use sorted arrays 
		insert: O(n)
		deleteMax: O(1)
		Using sorted linked-lists is identical.
		This realization used for sorting yields insertion sort.
	Third Realization: Heaps
		A heap is a certain type of binary tree. 
		Our goal with heaps is to get insertion and deletion to be O(logn)

Recall binary trees:
	A binary tree is either 
		empty, or
		consists of three parts: a node and two binary trees (left subtree and right subtree).
	Terminology: root, leaf, parent, child, level, sibling, ancestor, descendant, etc. 

Heaps
	A max-heap is a binary tree with the following two properties:
		Structural Property: 
			All the levels of a heap are completely filled, except (possibly) for the last level. The filled items in the last level are left-justified.
		Heap-order Property: 
			For any node i, key (priority) of parent of i is larger than or equal to key of i.
	A min-heap is the same, but with opposite order property. 
	Lemma: Height of a heap with n nodes is Θ (log n).

----

Jan 20

Note that level k (starting counting at 1 at the top)  has 2^(k-1) nodes 
And if we have k levels, the number of nodes we have total is *at least* sum i=0..k-2 2^i + 1 and *at most* sum i=0..k-1 2^i
let the height be the number of edges in the shortest path from the top node to lowest node
so h = #levels - 1
we can use these over and underestimates of the number of nodes to show that the height is theta(logn) where n is #nodes
	sum i=0..k-2 2^i + 1 = 2^(k-1) <= n <= sum i=0..k-1 2^i = 2^k - 1

	2^(k-1) <= n
	k-1 <= logn
	h <= logn

	n <= sum i=0..k-1 2^i = 2^k - 1
	n+1 >= 2^k
	k >= log(n+1)
	h >= log(n+1) - 1

Storing heaps in arrays:
	Let H be a heap (binary tree) of n items and let A be an array of size n. Store root in A[0] and continue with elements level-by-level from top to bottom, in each level left-to-right.

	It is easy to find parents and children using this array representation: the left child of A[i ] (if it exists) is A[2i + 1],
	the right child of A[i] (if it exists) is A[2i + 2],
	the parent of A[i] (i != 0) is A[floor(i−1 / 2)] (A[0] is the root node).

Insertion in Heaps
	Place the new key at the first free leaf
	The heap-order property might be violated: perform a bubble-up:
	bubble-up(v) v: a node of the heap
		while parent(v) exists and key(parent(v)) < key(v) do 
			swap v and parent(v)
			v ← parent(v)
	The new item bubbles up until it reaches its correct place in the heap.

	heapInsert (A, x) A: an array-based heap, x: a new item
	size(A) ← size(A) + 1  --note size(A) is a variable, not a function
	A[size(A) − 1] ← x 
	bubble−up(A, size(A) − 1)
Worst case: v is bigger than the root - h # of swaps and h is O(logn) so bubble-up is O(logn)


Deletemax in heaps:
	The maximum item of a heap is just the root node.
	We replace root by the last leaf (last leaf is taken out).
	The heap-order property might be violated: perform a bubble-down:
￼￼￼
	bubble-down(v) v: a node of the heap
	while v is not a leaf do
		u ← child of v with largest key 
		if key(u) > key(v) then
			swap v and u
			v←u 
		else
			break

	heapDeleteMax (A)  A: an array-based heap
		max ← A[0]
		swap(A[0], A[size(A) − 1])
		size (A) ← size (A) − 1
		bubble−down(A, 0)
		return max
Time: O(height of heap) = O (log n)
worst case: the last inserted node has the min key - so it's tight - so theta(logn)


Problem statement: Given n items (in A[0 · · · n − 1]) build a heap containing all of them.

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

		total run time is sum i=0..k-2 2^i(TI + iTS) 
		 			    = sum 2^i theta(1) + sum 2^i*i*theta(1)
		 			    = 2^(k-1) - 1      + 2^k*k - 2^(k+1) + 2 *****************from sum sheet
		 		  approx= n/2 + nlogn - 2n + 1   (k approx= logn)
	Worst-case running time: Θ(n log n).


Solution 2: Using bubble-downs instead:
	heapify (A) A: an array
	n ← size(A) − 1
	for i ← ⌊n/2⌋ downto 0 do
		bubble−down(A,i)
A careful analysis yields a worst-case complexity of Θ (n). A heap can be built in linear time
- we start at the next to bottom row and bubble to the row below it

----
Jan 22


worst case run time?
look at the heap like a pyramid - the number of nodes in the bottom is 2^k, or n/2 - the number of nodes in the second last row is n/4, etc..
each level has to bubble down to all the levels below
so we have n/2^2 * 1 level    +    n/2^3 * 2    +   n/2^4 * 3 + ..... + n/2^(k+1) * k = sum from i=1..k   n/(2^(i+1)) * i
= 0.5n * sum i=1..k  0.5^i * i = ... use sum sheet on slides ... = (n/2)(2 - k+2/2^k) approx(using k=logn)=   n - logn/2 - 1 which is linear time!


**Sorts Using Heaps***
HeapSort (A)
	initialize H to an empty heap 
	for i ← 0 to n − 1 do
		heapInsert (H , A[i ]) 
	for i ← 0 to n − 1 do
	A[n − 1 − i] ← heapDeleteMax(H)
Takes nlogn + n*logn = nlogn time

HeapSort (A)
	heapify (A)
	for i ← 0 to n − 1 do
		A[n − 1 − i] ← heapDeleteMax(A)
takes n + n*logn  = nlogn time


***Selection***
Problem Statement: The kth-max problem asks to find the kth largest item in an array A of n numbers.
Solution 1: 	
	Make k passes through the array, deleting the maximum number each time.
	Complexity: Θ(kn).
Solution 2: 
	First sort the numbers. Then return the kth largest number. 
	Complexity: Θ(nlogn).
Solution 3: 
	Scan the array and maintain the k largest numbers seen so far in a min-heap
	e.g. if we want 3 elements, we keep a min-heap with 3 nodes (Start with the first 3 elementes in array), always balance like a min-heap with smallest element in root
	compare root to each array element - if the root is smaller, then we replace the root with the element and heapify - in worst case we insert (logk) all n elements
	Complexity: Θ(nlogk).
Solution 4: 
	Make a max-heap by calling heapify(A). Call deleteMax(A) k times.
	Complexity: Θ(n + k log n).

For median selection, k = 􏰑 floor(n/2) 􏰒, giving cost Θ(n log n).
This is the same cost as our best sorting algorithms. 
Question: Can we do selection in linear time?

The quick-select algorithm answers this question in the affirmative. 
Observation: Finding the element at a given position is tough, but finding the position of a given element is simple.

quick-select and the related algorithm quick-sort rely on two subroutines: 
	choose-pivot(A): Choose an index i such that A[i] will make a good pivot (hopefully near the middle of the order).
	partition(A, p): Using pivot A[p], rearrange A so that all items ≤ the pivot come first, followed by the pivot, followed by all items greater than the pivot.

picking the pivot:
	Ideally, we would select a median as the pivot. But this is the problem we’re trying to solve!
	First idea: Always select first element in array. We will consider more sophisticated ideas later on.

artition(A, p)
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


----
Jan 27

We won't be talking about quick select any more in this course, only quick sort
Quicksort(A, r, l)
pre: l-r element array
post: sorted A
if(l>r)
	p <- choose-pivot(r, l)
	i = partition(A, r, l)
	QuickSort(A, r, i-1)
	QuickSort(A, i+1, l)

Worst case:
	claim: Tworst(n) <= c*n*(n+1)/2
	proof: by induction
	base case:
		Tworst(1) = c = c*1*2/2
	IH: assume Tworst(m) <= c*m*(m+1)/2 for all m <= n-1
	IC:
		Tworst(n) = {max value 0 <= i m= n-1}{Tworst(i-1) + Tworst(n-i))} + cn 
		          <=  {max value 0 <= i m= n-1}{c*(i-1)*i/2 + (n-i)(n-i+1)/2} + cn 
		          (max is when i=0 or i=n-1)
		          <= c*(n-1)*n/2 + cn = cn(n+1)/2
    so Tworst(n) is in O(n^2)

A is sorted: at least n^2 running time. T(n) = T(n-1) + cn    omega(n^2)
											 = cn + (c(n-1) + T(n-2))
											 = cn + c(n-1) + c(n-2) + ... + c + d
											 = cn(n+1) + d <= cn(n+1)/2

Best case for QuickSort: each pivot divides the array in half each time
Tbest = {min 0 <= i <= n-1}(Tbest(i-1) + Tbest(n-i)) + cn
     (let i = n/2)
     >= T(n/2) + T(n/2) + cn
     we recognize this recurrence as nlogn

 QuickSort - worst case is worse than worst case of HeapSort or MergeSort, best case is worse than best case for Insertion Sort, average case is nlogn which is good!

Average case for QuickSort:
	Tavg(n) = sum_{all possible instance of array with size n} T(n)  /  n! (# of permutations)
	        = sum i=0..n-1 T(i) + T(n-i-1) + cn) / n
	        	// we can say these are equal because we can divide the permutations of the
	        	// array into n cases - where the first element will be moved to one of n 
	        	// places in the array - the rest of the array is irrelevant to the runtime
	        	// at this point (we account for it in the next steps)
	        = sum i=0..n-1 T(i) + sum i=0..n-1 T(n-i-1) + sum cn
	        = 2/n sum T(i) + cn
	Claim: Tavg(n) <=   D if n<=1    or   D*n*logn if n>=2
	Proof: by induction
		BC: n=0, 1
			T(0) <= T(1) <= D
			(should also show T(2) pretty sure!!)
		IH: assume Tavg(m) <= D, if m<=1  and Dmlnm if m>=2 for all m <= n-1
		IC:
			Tavg(n) = 2/n sum T(i) + cn
			        = 2/n (T(0) + T(1) + sum i=2..n-1 T(i) + cn)
			        <= 2/n sum D*i*lni   + 2/n(D+ D) + cn
			        <= 2D/n sum ilni + 4D/2 + cn
			        <= 2D/n integral 2 to n xlnx dx
			        = .... some calculus
			        <= Dnlogn
	There's something different on the slides, but this is probably too complex for our class
	we see that H(n) ∈ O(log n).
	Average cost is O(nH(n)) ∈ O(n log n).
	Since best-case is Θ(n log n), average must be Θ(n log n).

More notes on QuickSort
	We can randomize by using choose-pivot2, giving Θ(n log n) expected time for quick-sort2.
	We can use choose-pivot3 (along with quick-select3) to get quick-sort3 with Θ(n log n) worst-case time.
	We can use tail recursion to save space on one of the recursive calls.
		By making sure the other one is always smaller, the auxiliary space is Θ(log n) in the worst case, even for quick-sort1.
	QuickSort is often the most efficient algorithm in practice.

--
Jan 29
O(nlogn) - lower bound for sorting
Can we do bettter? Can we do sorting in O(n)?
Answer: Yes and no! It depends on what we allow.
No: Comparison-based sorting lower bound is Ω(n log n). Yes: Non-comparison-based sorting can achieve O(n).

Comparison Model: algorithms make decisions based on comparison of two key values
In the comparison model data can only be accessed in two ways: 
	- comparing two elements
	- moving elements around (e.g. copying, swapping)

Decision tree: is a binary tree thta can model the processing of any algorithm in the comparison model
(took picture of example of binary search decision tree with my phone)
(and another pic of tree for sorting)
Lower bounds for sorting 
- thm: the lower bounds for sorting in the comparison model is omega(nlogn)
- look at the sorting tree - if we have >=n leafs - the neight must be >= logn

Talked about Counting Sort (pictures on phone):
	First have Array A
	Then find out how many of each id we have
	Then find the indices of where those values start in the array
	Then we can copy over whole objects from A into the array, based on those indices
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
	B <- copy A
	for i=0 to n-1 do
		A[I[B[i]]] <- B[i]
		increment I[B[i]]
This takes O(n+R) time

***Radix sort***
sort n integers in base R (R-radix)
	# digits of n? in base R? = floor(log_R(n)) + 1
Let's consider n numbers in base R with m digits
MSD - most sigificant digit
527, 368, 633, 401, 631, 301 -> MST
[301, 368], [401], [527], [633, 631] -> next MSD
[[301], [368]], [401], [[527], [[633, 631]] ->
[[301], [368]], [401], [527], [[[633] [631]]]
not a good algorithm! many recursions
O(m(n+k))

try LSD-radix:
A: array of size n, contains m-digit radix-R numbers
for d ← m down to 1 do 
	Sort A by dth digit

"stable":
	Sort-routine must be stable: equal items stay in original order.
􏰶 	CountSort, InsertionSort, MergeSort are (usually) stable.
􏰶 	HeapSort, QuickSort not stable as implemented.

--
Feb 3

***Dictionary ADT***
- contains n distinct KVP (key value pairs)
- search efficiently
- Insertion/deletion efficiently
A dictionary is a collection of items, each of which contains a key and some data
and is called a key-value pair (KVP). Keys can be compared and are (typically) unique.
Operations: 
	- search(k )
	- insert(k , v )
	- delete(k )
	- optional: join, isEmpty, size, etc.
Examples: symbol table, license plate database

Implementations of Dictionaries
unsorted array/linked list (size n)
- search Θ(n) 
- insert Θ(1)
- delete Θ(n) (need to search)

Ordered array
- search Θ(logn) 
- insert Θ(n)   (logn search and then move n)
- delete Θ(n)	(similar to insert)

Sorted linked list
- Search O(n)
- Insert O(n)
- Delete O(n)

BST (binary search tree)
- either empty or contains KVP at root, left subtree, right subtree
- keys in left subtree are smaller than the key at root
- keys in right subtree are bigger than key at root
- search: if found return value, else go left/right - O(h)
- insert: O(h)
- delete x in BST:
	search for x
	if x is a leaf, delete it
	if x is a node with 1 child then delete x and bring subtree one level up
	if x has 2 children, we swap it with the successor (go left and then all the way down right) or predecessor (go right and all the way left) and delete x
  O(h)
- height will depend - worst case n, best case logn, average logn (like quicksort)

Exercise:
	BST (property) + Heaps (stored in array)
	sort the array (could take O(n) time) =, then we get perfectly balanced

AVL Trees
- Introduced by Adel’son-Vel’ski ̆ı and Landis in 1962,
- BST with an additional structural property: The heights of the left and right subtree differ by at most 1. (The height of an empty tree is defined to be −1.)
- At each non-empty node, we store height(R) minus height(L) ∈ {−1, 0, 1}:
 	−1 means the tree is left-heavy
	0 means the tree is balanced
	1 means the tree is right-heavy
- We could store the actual height, but storing balances is simpler and more convenient.

Each node stores:
- KVP
- left subtree
- right subtree
- height of subtree at the node / (h(R)-h(L))

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

Rotating (See pictures in slides)
rotate-right(T )
	T: AVL tree
	returns rotated AVL tree
	1. newroot ← T .left
	2. T .left ← newroot.right
	3. newroot.right ← T
	4. return newroot
rotate-left(T )
	T: AVL tree
	returns rotated AVL tree
	1. newroot ← T .right
	2. T .right ← newroot.left
	3. newroot.left ← T
	4. return newroot

fix(T )
T : AVL tree with T .balance = ±2 returns a balanced AVL tree
	if T.balance = −2 then
		if T.left.balance = 1 then
			T.left ← rotate-left(T.left) 
			return rotate-right(T)
	else if T.balance = 2 then
		if T.right.balance = −1 then
			T.right ← rotate-right(T.right) 
		return rotate-left(T)

---
Feb 5

Search:
	search for a key in AVL tree - just like BST - O(height)
Insert:
	insert a new key in AVL tree - fix will be called at most once - O(height)
	If after inserting a new key a node z is unbalanced, then rebalance the tree with root z using one of the 4 types of rotation (right left, rightleft, leftright)
	I took some pics of the rotations

Lemma:
let z be an unbalanced node and all its descendents are balanced.
- applying one of the rotations on z balances z and its descendents
- if imbalance comes from insertion, then reblaancing z balances the entire tree

Delete:
	1) delete as in BST
	2) Parse upward from where you removed the node and recompute the balances of nodes
	3) if a node is unbalanced then rebalance using rotations
	4) keep updating balance up to the root
	fix might be called O(height) times

Exercises:
1) given an AVL tree of height h, what is the possible level of the leaves?
2) Given an AVL tree of height h, what is the maximum number of nodes?

Delete is done in O(height)
claim: any AVL tree has its height O(logn)
h <= clogn, c>0
2^h <= 2^(clogn)
2^h <= (2^logn)^c = n^c
so thihs also means n >= 2^(h/c)

Equivalent form: For a given AVL tree of fixed height h, find the minimum number of nodes. Let N(h) = the samllest number ofnodes for an AVL tree of height h
One subtree must have height at least h−1, the other at least h−2:
N(h) = 1+N(h−1)+N(h−2), h≥1
N(h) = 1, h=0
N(h) = 0, h = −1
This is the fibinacci sequence!
N(h) = (h+3)th fibinacci number -1
N(h) > N(h−1)+N(h−2) > 2N(h−2) >= 2(N(h-3) + N(h-4) + 1)) > 4N(h−4) > 8N(h−6) > ··· 
     > 2iN(h−2i) = 2^⌊h/2⌋ N(h-2⌊h/2⌋) ≥ 2^(h/2)

from this we can get h <= 2logn
thus an AVL tree with n nodes has height O(logn). 
also, n ≤ 2h+1 − 1, so the height is Θ(log n).


***2-3 Trees***
idea: rebalancing by relaxing the degree=2 condition
Def 2-3 tree
- empty (NIL)
- contains one KVP and 2 children which are 2-3 trees
- two KVP and 3 children which are 2-3 trees
see slides for picture of what that would look like

Restrictions:
- all empty subtrees are at the same level (aka all of the non-empty leaves are at the same level)

Search(x):
- almost same as in BST
- compare x with the key (root)
- if found, break
- else find the unique subtree where we should ook for x
- if subtree empty, return (not found)

--
Feb 10

Searching in a 2-3 tree
- compare x with keys at current node
- if key found, break
- else find unique subtree where x might be
- if subtree is empty, break
- else recurse on the root of the subtree
Running time O(height)

Insertion
- First, we search to find the lowest internal node where the new key belongs.
- If the node has only 1 KVP, just add the new one to make a 2-node.
- Otherwise, order the three keys as a < b < c.
- Split the node into two 1-nodes, containing a and c,
- (recursively) insert b into the parent along with the new link

Deletion
- As with BSTs and AVL trees, we first swap the KVP with its successor, so that we always delete from a leaf.
Say we’re deleting KVP x from a node V : 
- If V is a 2-node, just delete x.
- ElseIf V has a 2-node immediate sibling U, perform a transfer:
	Put the “intermediate” KVP (key that differentates between two children) in the parent between V and U into V , and replace it with the adjacent KVP from U.
	This is kinda like a rotation
- Otherwise, merge V, immediate sibling U, and the corresponding KVP from parent into a node (at the same level), then recurisvely "fix" parent - if parent is an empty root, just delete

runtime deletion?
O(height)
n - number of item (KVP) - not nodes!

Let T be a 2-3 tree of fixed height h - what is min number of KVP's that T can contain?
It would be a binary tree 
	showed in assignment that h <= log(n+1) -1
lower bound for h? each node has three children - ternary tree
	2*3^0 kvp on level 1 ... 2*3^h on level h+1
	take sum and get n <= 3^(h+1) - 1 --- h >= log3(n+1) -1
so both upper and lower bound are logn

***(a,b) trees***
The 2-3 Tree is a specific type of (a, b)-tree:
An (a, b)-tree of order M is a search tree satisfying:
Each internal node has at least a children, **unless it is the root**. The root has at least 2 children
Each internal node has at most b children.
If a node has k children, then it stores k-1 key-value pairs (KVPs). Leaves store no keys and are at the same level.

a B-tree of order M is a (dM/2e,M)-tree. A 2-3 tree has M = 3
height of tree with n nodes is theta((log n)/(log M))

a-b trees have h in O(log_a n) and omega(log_b n)
search in a-b tree - O(b loga n) -- b-1 KVPs in one node, log_a n height
using arrays for KVPs of a node, the use binary search - now takes O(logb * log_a n)


***memory***
when using big data, it may not fitin the internal memory -> use external memory

pyramid - fastest most expensive at top, slowest least expensive at bottom
MAIN (primary,internal memory)
- CPU register
- Cache L1
- Cache L2
- Cache L3
- RAM
external memory
- hard drive
- cloud storage

Tree-based data structures have poor memory locality: If an operation accesses m nodes, then it must access m spaced-out memory locations.
Observation: Accessing a single location in external memory (e.g. hard disk) automatically loads a whole block (or “page”).
In an AVL tree or 2-3 tree, theta(log n) pages are loaded in the worst case. If M is small enough so an M-node fits into a single page,
then a B-tree of order M only loads theta((log n)/(log M)) pages. This can result in a huge savings: memory access is often the largest time cost in a computation.

External memory model: transfers of data between internal memory layers are ignored
Our goal: minimize the number of disk transfer from external to internal memory


--
Feb 12

B-tree requires O(logn/logB) disk transfers
B-tree of order M is a ciel(M/2) - M tree
- let B be the size of a block
- choose M s.t. a node fits into a single disk block
- search O(logn/log(M/2))

Theorem: In any comparison based dictioanary ADT, search(k) is done in omega(logn)
Proof: decision tree
any binary tree (yes, no branches) with N leaves has the height logN
the decision tree has at least n+1 leaves then the height >= log(n+1)

consider a dictionary with n KVPs - if the keys are exactly 0, ..., n-1
then the item (K1, V1) wll be stored in A[K1]
Search(x) - x is A[x]
now search/insert/delete is theta(1)

Disadvantages:
- huge array
- initialization (slow)
-  only for integer keys

***Hashing***
U - universe of n keys
find a mapping of U in {0, ..., M-1} (natural numbers)
then we have an array with indices 0 to M-1
we're putting our keys into a smaller array of size M

Hacher in french means cut into small pieces

e.g. hash function - h(k) = k mod 9
e.g. 18, 11, 5, 16, 31, 9
Insert(18) - at index h(18) = 0
Insert(11) - index 2

Problem: 
multiple items land on the same slot in the hashing table T (collision)
To solve problem:
1. chaining - we allow multiple item on a single spot in T (linked list) (M<=n)
2. open addessing - if the slot is occupied, move to next slot in T (M>=n)

with chaining:
- h(k) is theta(1)
- Worst-case: all KVPs are in same slot in T - fix by choosing a better hash function
- Ideally, h is a uniform function
	P(k is T[h(k)]) = 1/M
	Think of h as a random uniform function over {0, ..., M-1}
	#items/#slots in T = n/M = alpha - load factor
	choose M then choose h, trying to keep alpha>=1 small
- Search(x)   O(1+alpha)
- Insert is theta(1)
- Delete(search+delete) = O(1+alpha)

***Open addressing***
each slot of T has at most one KVP
Search(x) in this case will try to find the *probe sequence*
	<h(k,0), h(k,1), h(k,2), ... , h(k, M-1)>
	if h(k) is not X, then search h(k)+1
	you keep looking down the line until you find what you're looking for
	if you find empty place, break - you haven't found it
Deletion:
	problem - neeed to distinguish between empty spot and deleted spot, so that search still works
	user a marker for deleted items
	seach(x): search through T[(h(k,0)], 1, ... M-1 until x found or empty (unmarked) slo found
	Insert(x): search for empty (marked or unmarked) among T ...
	for open addressing alpha <= 1 (M>=n)
	Run-time for search O(M)
