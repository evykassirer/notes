CS486 

Notes taken by Evy Kassirer

Spring 2016

Prof: Peter Van Beek

CONDENSED VERSION (about half the length)

- no examples
- no history/context
- nothing that is untestable


## Problem-solving using search

Problem-solving agents: Goal-based agents that decide what to do by finding sequences of actions that lead to desirable states.


### contrasts in problems

- Find a goal state: could be phrased as _any goal_ state or _optimal goal_ (optimizing cost or something)
- Find a sequence of actions that lead to goal state (any or optimal aka smallest # steps)


### Graph Search

Given a problem:

1. nodes: Specify representation of states, specify initial and goal states
2. arcs: Specify actions (successor function, neighborhood function) that take us from one state to another. Assign a cost to each action
3. search: Find a path from an initial state to a goal state

General search algorithm (doesn't account for cycles)

	L <- [start nodes]
	while L not empty do
		select and remove a node from L, call it p
		if p is a goal node, return(success)
		generate all successor states of p, and add them to L
	return(fail)


Kinds of graph searches (review)

- breadth-first search removes from front, adds successors to end of L (FIFO)
- depth-first search removes from front, adds successors to front of L (LIFO)
- Priority queue gives informed search (greedy, A*)
- depth-limited search: depth first search, but with a cutoff on how far you're willing to go (max depth)

New concept: iterative deepening search (IDS)

- for cutoff = 0, 1, .... do a depth limited search with cutoff as bound
- why is it better than breadth first? 
 - BFS has to store a whole layer before it can get to the next layer
 - so it uses less space than BFS, but takes more time than BFS - though not that much more time because a lot of time is from the last layer if the layers grow exponentially in size

terms referred to when talking about graph searches

- b: branching factor (the number of children at each node, the outdegree)
- d: depth of solution
- m: depth of tree

for a graph search, we want to show/know:

- complete: if goal is reachable, will be found
- optimal: will find lowest cost path to goal


	              | BFS       |   DFS  |  IDS
	    ----------|----------------------------
	    time      |  O(b^d)   |  O(b^m)|  O(b^d)
	    space     |  O(b^d)   |  O(bm) |  O(bd)
	    complete  |  yes      |   no   |   yes
	    optimal   |  yes      |   no   |   yes

DFS:

- might need to go all the way to the bottom of one part of the tree before getting to the solution at depth d later on - so O(b^m)
- space it takes up is the path plus a few siblings for each node on the path - O(bm)
- unless m is finite, we might never reach the solution, so not complete
- we might go on a big loop to get the solution first

IDS:

- same order of time as BFS even if we repeat the layers a few times
- space is similar to DFS, but we only go d deep, with maybe some siblings - so space is only O(bd)
- it has all the perks of BFS and all the perks of DFS - combined! cool

what to do about repeated states?

- could do nothing
- could not return to a state that you just came from
- we can record the ancestor path and not go back to any of the ancestors (to avoid cycle)
- ideally we don't generate any state that has been generated before - don't visit any node that's already been visited - this is what we usually do

We can also build up a solution

- initial state: nothing decided
- successor: all ways of assigning a value to one of the variables


### Informed (heuristic) search 

Assign a cost to each action/rule/arc

- Heuristic function:
 - h(n) = estimated cost of the cheapest path from the state at node n to a goal state.
 - we don't want the cost to be too expensive to compute

Then we greedy search:

- we explore in the direction with lowest h(n)
- it isn't guarenteed to find optimal path, since h is just a lower bound and a state with lower h isn't necessarily going to give a shorter path
- it might never terminate (for similar reasons as previous point)

		L <-- [start nodes] 
		while L not empty do
			remove node with lowest h(n) value from L, call it p
			if p is a goal node, return(success)
			generate all successor states of p, and add them to L
		endwhile
		return(fail)


### A* search: Minimizing total path cost

- let f\*(n) = g\*(n) + h\*(n) be the cost of an optimal path going through node n, where
 - g\*(n) is cost of an optimal path from initial node to n
 - h\*(n) is cost of an optimal path from n to a goal node
- but f\*(n) is perfect information
 - you'd have to compute it for all the nodes (doesn't make sense)
 - so it's not available to us
 - so we estimate


We drop the star to show we're not optimal anymore

- f(n) = g(n) + h(n)
 - g(n) is cost of path we found from initial state to n, where g(n) >= g\*(n)
  - h(n) is a heuristic estimate of cost from n to a goal, which we hope is a good estimate of h*(n)
- Algorithm A\* for searching a tree is like greedy, but we remove the node with the lowest f(n) value

A* algorithm

		L <-- [start nodes] 
		while L not empty do
			remove node with lowest f(n) value from L, call it p
			if p is a goal node, return(success)
			generate all successor states of p, and add them to L
		endwhile
		return(fail)


#### Heuristics

Admissible heuristics

- A heuristic function h(n) is admissible if h(n) <= h\*(n), for all n
 - we know g(n) >= g\*(n)
 - 0 <= h(n) <= h\*(n)
 - it has to hold for every single node, if even one h(n) >= h\*(n) it wouldn't work
 - if we do h(n) = 0, it'd almost be greedy, g(n) makes it still optimal path but it's not very good (expands many more nodes than necessary)
- Theorem: If h(n) is admissible, the A* algorithm is guaranteed to find an optimal path to a goal node


Dominating heuristics

- Def: Given admissible heuristics h1(n) and h2(n), h2(n) dominates if h1(n) if
 - for all nodes n, h2(n) >= h1(n),
 - and there exists a node n' such that h2(n') > h1(n')
- Thm: A* with h1(n) expands at least as many nodes as A* with h2(n), iff h2(n) dominates h1(n)
 - the idea is that if you underestimate, you'll expand nodes that are actually not as good as you think they are

#### So how does A* perform?

Complexity results for A*

- Good news:
 - A* is complete, optimal, and optimally efficient
 - no algorithm with the same information can do better
- Bad news:
 - assuming you have a single goal, are searching a tree, and each action is reversible, worst case time complexity is bad
- time complexity is exponential: O((b^ɛ)^d) = O(b^ɛd)
 - b is the branching factor
 - ɛ is the maximum relative error  (h\*(n) - h(n)) / h\*(n)
 - d is the depth of the goal node
 - worst case is when h(n) is always 0, (h\*(n) - h(n)) / h\*(n), would be 1 - so we'd have O(b^d)

#### Iterative-deepening A*

- Amount of memory needed is a problem for A* (just like BFS)
- IDA*
 - each iteration is a complete DFS with a cutoff (so, no priority queue)
 - branch is cutoff (node is not added to L) if f(n) exceeds some threshold
 - next iteration sets threshold to be minimum of values that exceeded old threshold
 - time complexity depends strongly on number of different values the heuristic can take on
- a lot of duplicate work, but the last run dominates the rest so it's not a big deal


## constraint satisfaction problems (CSP)

- Describe states implicitly in terms of variables and constraints
- More search algorithms:
 - backtracking search
 - local search

Defining a CSP

- set of variables (x1 ... xn)
- set of values for each variable dom(x1), ... dom(xn)
- set of constraints C1, ... Cm 
- A solution to a CSP is a complete assignment to all the variables that satisfies the constraints
- the tricky part is finding a solution where all the constraints are satisfied, each constraint isn't usually that hard to satisfy individually

given a CSP, we can be asked to:

- determine if it has a solution or not
- find one solution
- find all solutions
- find optimal solution, given some cost funciton

example domains and constraints:

- reals, linear constaints
 - linear constraints: e.g. 3x + 4y <= 7, 5x - 3y + z = 2
 - easy, polynomial problem
 - guassian elimination, linear programming
- integers, linear constraints
 - harder - integer linear programming, branch-and-bound
- boolean, propositional sentences
 - this is the satsifiability problem
- Here we are gonna deal with
 - finite domains
 - expressive constraint languages

constraint languages

- we can use arithmetic operators (math stuff), logical operators (and/or/not/implies)
- can also have global constraints which are specified over an artibtrary number of variables 
 - e.g. alldifferent(x1, ..., xn) meaning they all have to have different values
- extensional/table constraints (must be an entry in a table, values in some list of tuples of values that work)
- intensional constraints is the rule explaining what must be true about the variables (without listing out all valid possibilities)

### a closer look at constraints

- an _assignment_ (or instantiation) is when you say x=a where a is in dom(x)
- a _tuple_ t over a set of variables {x1,...,xk} is an ordered list of values (a,...,ak) such that each ai is in the dom(xi) - can be viewed as a set of assignments
- given a tuple t, notation t[xi] selects out the value for variable xi, ie t[xi] = ai

vars(C)

- each constraint is a relation
- a set of tuples over some ordered subset of the variables, denoted by vars(C)
- specifies the allowed combinations of values for the variables in vars(C)
- the set vars(C) is called the scope (or scheme) of the constraint

the size of vars(C) is known as the arity of the constraint 

- unary constraint has arity of 1
- binary has arity of 2
- non binary constraint has arity greater than 2
- a _binary_ CSP is a CSP where all constraints are unary or binary


### local consistency: arc consistency

Idea: given a constraint, remove a value from the domain of a variable if it cannot be part of a solution according to that constraint

- given a constraint C, a value a in dom(x) for a variable x in vars(C) has:
 - a _domain support_ for `a` in C if there exists a `t` in C such that t[x] = a and t[y] is in dom(y) for every y in vars(C)
 - ie there exists values for each of the other variables (from respective domains) such that the constraint is satisfied
- a constraint C is arc consistent iff for each x in vars(C) each value a in dom(x) has a domain support in C
- a CSP is arc consistent if every constraint is arc consistent
- A CSP can be made arc consistent by repeatedly removing unsupported values from the domains of its variables

Algorithm:

![](L4-arcconsalgo.png)


## Local search

- applies to both satisfaction and optimization
- incomplete (non systematic) algorithms
 - complete means will find solution if it exists
 - so it can't be used to find a solution if it exists, and definitely no guarentee that it can find the optimal one
- finds locally optimal solutions that are not necessarily globally optimal

notation and definitions

- S set of states
- c: S->Reals cost function
- N: S -> 2^S  neighbourhood function (like successor function, finds neighbouring states)
- a solution s\* in S is globally optimal iff c(s\*) <= c(s) for all s in S 
 - what we want
- a solution s+ in S is locally optimal iff c(s+) <= c(s) for all s in N(s+) 
 - what we settle for

Graph interpretation

- local search is a walk in a directed, node-labeled graph
 - nodes are the elements of set of states S
 - nodes are labeled with cost values
 - arcs are given by the neighborhood function

local search for CSPs

- some constraints hard (must be satisfied)
 - set of solutions of hard constraints gives set of states S; ie nodes in the search graph
- remaining constraints are soft (moved into cost function) 
 - so make it so the cost is +1 for each constraint not satisfied

#### the local search algorithm 

		s <- some initial complete assignment 
		k <- 0
		repeat
			r <- select a neighbor of s
			if c(r) - c(s) < tk then
				s <- r 
			k <- k + 1
		until stopping criteria satisfied 
		return best s


Choices

- stopping criteria
 - can set a max on # iterations (it's fine to run it for a few minutes)
 - can stop when solution of low enough cost is found (once we get low enough it's fine - e.g. hitting 0 for 4queen here)
 - or when number of iterations since last (big enough) improvement is too large
- how to get an initial feasible solution?
 - random or 'good'
 - maybe do some greedy algorithm to find a 'good' starting point
- what neighbourhood function?
 - small is easily explored, but low quality solution might be found
 - large is expensive to explore
- how to select 'r', the neighbour to move to?
 - first improvement (first improving solution - quick/fast decisions, can make a lot more of them)
 - best improvement (solution with lowest cost, fewer overall moves, decisions take more time)

Thresholds

- iterative improvement: only move to neighbour if strictly lower cost (tk=0 for all k)
- threshold accepting
 - worst cost neighbours are accepted, but diminishes: ie tk >=0, tk >= t_{k+1} -- so at first we can go to worse, but eventually we wouldn't
 - variation: stimulated annealing. worst cost neighbours accepted with a probability that is gradually decreased over time
 - variation: tabu search - worst cost neighbours accepted but only if it is a legal neighbour. set of legal neighbours restricted by a tabu list to prevent going back to a recently visited node
  - how many recent do you keep? turns out 20 is a good number

More improvements

- multistarts - restart a bunch of times and randomize where you start, keep best solution from all runs
- multi-level: start the search in a neighbourhood with a solution selected from a different neighbourhood (use different neighbour functions for finding starting point vs running rest of algorithm)
- can find a starting point using greedy then search from there

Neighborhoods possibilities for lists

- transpose: swap two adjacent  -- O(n) to generate all neighbours
- insert: move one (insert earlier in the list) -- O(n^2) since insert is O(n)
- swap two  (not necessarily adjacent) -- O(n^2)
- block insert (move a subsequence of items) -- (On^3)

exact neighbourhood

- definition: every local optimum is also a global optimum
- if a neighborhood is not exact, the cost of a local optimum can be arbitrarily far from a global optimum
- if a neighborhood is not exact, local search can take an exponential number of steps to read a local optimum


Solving CSPs: find a solution by making it into a search problem

- initial state/root of tree is empty assignment {} (no variables have values assigned)
- arc assigns a value to the next variable (e.g. staring from x1 and assigning values until xn) 
- goal is to find complete assignment that satisfies all constraints
- backtrack when we go ways that don't work

![](L6-searchtreeeg.png)

Finding an algorithm

- depth first with iterative deepening doesn't make sense 	because the goal is at the bottom
- BFS isn't great because we have to go all the way down, so we'd need to get to the last layer 
- the problem with DFS before was that the goal could be infinitely deep, here it's not - so DFS is the way to go
- one adjustment to the DFS
 - at each node we try to estimate if there's a solution below that node 
 - once we know there's no solution below a node we don't go further 
 - have to be sure about this - so we can have false positives but no false negatives

## Backtracking search

A backtracking search is a depth-first traversal of a search tree

- search tree is generated as the search progresses
- search tree represents alternative choices that may have to be examined in order to find a solution
- method of extending a node in the search tree is often called a branching strategy

### Popular branching strategies

- Let x be the variable branched on, let dom(x) = {1, ..., 6}
- Enumeration (or d-way branching) 
 - variable x is instantiated in turn to each value in its domain
 - e.g., x = 1 is posted along the first branch, x = 2 along second branch, ...
- Binary choice points (or 2-way branching)
 - variable x is instantiated to some value in its domain
 - e.g., x = 1 is posted along the first branch, x != 1 along second branch, respectively
 - used more because it often is more efficient
- Domain splitting 
 - constraint posted splits the domain of the variable
 - e.g., x <= 3 is posted along the first branch, x > 3 along second branch, respectively

Backtracking search algorithm template

		backtrack( assignment, csp )
		if assignment is complete then solution found
		var <- select next variable
		for each value in dom( var ) do
			save( csp )
		 	assignment union {var = value}
		 if propagate( assignment, csp ) then
		 	backtrack( assignment, csp ) 
		 endif
		 restore( csp ) 
		endfor

- save csp so we can restore after we plunge down, if we fail, back up
- propagate() determines if there's a solution below (never false negative)
 - uses arc consistency

### Constraint propagation

 - Effective backtracking algorithms for CSPs maintain a level of local consistency during the search; i.e., perform constraint propagation
- Perform constraint propagation at each node in the search tree
 - if any domain of a variable becomes empty, inconsistent so backtrack (and undo any of the effects of the most recent constraint propagation)

Backtracking search integrated with constraint propagation has two important benefits

 1. removing inconsistencies during search can dramatically prune the search tree by removing deadends and by simplifying the remaining sub-problem
 2. some of the most important variable ordering heuristics make use of the information gathered by constraint propagation

Some backtracking algorithms

- Naive backtracking (BT): performs no constraint propagation, only checks a constraint if all of its variables have been instantiated -- so no arc consistency
- Forward checking (FC): maintains arc consistency on all constraints with exactly one uninstantiated variable - so wait until only one left
- Maintaining arc consistency (MAC): maintains arc consistency on *all* constraints with at least one uninstantiated variable - this is more work per node but saves us going some paths


Heuristics for variable ordering

- Basic idea: assign a heuristic value to a variable that estimates how difficult it is to find a satisfying value for that variable
- Principle: most likely to fail first 
- work on the hard part first - because the algorithm spends most of its time recovering from mistakes, so don't leave the part that'll be hard to figure out until the end of picking everything else cause we'll fail late and it'll be more expensive
- Examples:
 - dom: choose the variable x with the smallest domain size
 - dom / deg: divide domain size of a variable by degree of the variable


# Knowledge-based Agents


- "I'm allowed to give only one lecture that doesn't make sense. I'm using it on this lecture" 
 - ***so this stuff is unlikely to be tested***
- knowledge-based Agents use logic to represnt domain knowledge and inference to reason about that knowledge
- one way to solve a problem is to find + code up an algorithm in programming language + execute
- this way is different - we identify the knowledge needed to solve the problem, write down knowledge in representation language, use logical deduction to solve the problem (like CS245, "your favourite course")

Types of knowledge

- Procedural knowledge: "how to" knowlede  e.g. programs or programming languages
- Declarative knowledge: descriptive knowledge e.g. databases, logic

### Formal logic

A logic consists of

- syntax (acceptable sentences)
- semantics (what symbols and sentences mean)
- proof procedure (how we construct valid proofs)

syntax of propositional logic  

- alphabet
 - propositional symbols (Boolean variables): p, q, r, ..., 
 - two constant symbols: true, false
 - brackets: (, )
 - propositional connectives: not, and, or, implies, iff
- Syntax
 - a propositional symbol is a sentence (formula)
 - if alpha and beta are sentences (formulas) so are: NOTalpha, (alpha), alpha AND beta, alpha OR beta, alpha => beta, alpha iff beta

syntax of predicate logic 

- added to alphabet: constants, predicates, funtions, quantifiers (for all/there exists)
- added to syntax: 
 - terms are constants, variables, or functions on terms
 - P(t1,...,tn) is a sentence where ti are terms and P is a predicate
 - if alpha is a sentence and x is a variable, then "for all x, alpha" and "there exists x such that alpha" are sentences

Logical implication (deduction)

- A set of sentences Γ logically implies a sentence Φ iff every interpretation that satisfies Γ  also satisfies Φ 
- We write this as Γ ⊨ Φ

proofs in logic

- Many proof procedures
 - transformational proofs
 - natural deduction
 - resolution refutation
- x ⊢ y means there is a proof of y from x
- soundness and completeness shows that if you have one of proof or deduction, you have the other

### Planning with Certainty

Goal-based agents that decide what to do by finding sequences of actions that lead to desirable states.

Planning

- Given: an agent's ability (actions it can perform), its goal, and the current state of the world
- Find: a sequence of actions to achieve a goal

Initial assumptions:

- Agent's actions are deterministic
- No external events beyond the control of the agent
- World is fully observable
- Time progresses discretely

Actions

- a determnistic action is a partial function from states to states
- STRIPS/PDDL representation
 - preconditions of an action specify set of conditions that must be true for action to have desired effect
 - effects of an action specifies what changes as a result of performing the action:
 - some effects: set of conditions become true (add list)
 - some effects: set of conditions become false (delete list)

Setting up a problem

- use predicates to represent states 
- define actions and their preconditions and effects
- Updating state: 
 - S\_{i+1} <-- (S\_i - delete(action)) UNION add(action)
 - where preconditions are in Si
- note that deleting effects are subset of precondition list

#### Solving planning problems using forward search

Heuristic evaluation function:

- Want: search guided by a heuristic function that is automatically extracted
- Idea: relax a problem P to a simpler problem P', which can be solved efficiently. Use the solution to the simpler problem P' as an estimate for original problem P (like starting local search with greedy solution)
- Here: Relax the planning problem by simply ignoring delete effects
 - actions only add new atoms to the state, never remove any (so it isn't accurate anymore, we're just estimating)
 - problem is "solved" as soon as each subgoal has been added by some action
 - length of an optimal relaxed solution is an admissible heuristic for how long the actual solution will be

Search strategy:

- Use A* and admissible heuristic to find optimal plan
- Use local search and heuristic to find good plans
 - give up on optimality
 - use heuristic to choose best neighbour to move to


# Uncertainty

-  agents may need to handle uncertainty
 - partial observability - we dont know everything about the real world
 - nondeterminism - actions don't always have their intended consequences

random variables

- variables capture the state of the world - but sometimes we can't be sure of their values
- random variables help capture the state of the world
- A random variable X has a domain of possible values {a1, ..., an} and an associated probability distribution
- we can ask about the probability of any statement

## Probability

shorthand

- If X is a Boolean random variable (i.e., the domain is {true, false}) 
- P(X) is shorthand for P(X=true), P(notX) is shorthand for P(X=false)

probabilities

- P(X) - prior/unconditional probability - what is probability X is true with no other given information
- P(X|Y) - posterior/conditional probability - probability X is true given that Y is true
 - equal to P(X and Y) / P(Y), if P(Y) > 0

axioms of probability

1. 0 <= P(A) <= 1
2. in 4 parts

 - P(True) = 1
 - P(False) = 0
 - P(A=True or A=false) = 1
 - P(A=True and A=false) = 0

3. P(A and B) = P(A) + P(B) - P(A or B)

Joint probabiltiy distribution

- probabilistic model of a domain: a set of random variables (describe state of the world)
- atomic event: assignment of value to each random variable in the model (a picture of the world)
- joint probability distribution: assignment of probability to each atomic event
 - all probabilities add to 1
- the catch
 - joint probability distribution can be huge with lots of random variables
 - it's hard to come up with the probability distribution among all these events
 - in practice this strategy is too hard

some important rules

- product rule: P(X,Y) = P(X|Y)P(Y) = P(Y|X)P(X)
- sum rule: P(X=a) = sum P(x=a|Y=b)P(Y=b) for all b in domain of Y
- Bayes' rule: P(Y|X) = P(X|Y)P(Y)/P(X)
- chain rule: P(X1,...,Xn) = P(Xn|Xn-1,...,X1) \* P(Xn-1|Xn-2,...X1) \* ... \* P(X2|X1) \* P(X1) = product i=1..n P(Xi | Xi-1, ..., X1)


### independence

- X is independent of Y if P(X|Y) = P(X)
- if X is independent of Y iff Y is independent of X
- sometimes if they're very close to independent we just say they are, for practical purposes
- in this course we decided if things were independent through intution/thinking about it (instead of looking at actual known probabilties)

conditional independence

- X is conditionally independent of Y given Z if P(X|Y,Z) = P(X|Z)
- if X is cond indep of Y given Z, Y is cond. indep. of X given Z

Importance of independence:

- conditional independence assertions allow chain rule to be simplified
- P(X1 and ... and Xn) = product of P(Xi | Xi-1, ..., X1) for all i from 1 to n 
- reduce number of probabilities that need to be specified
- we can then simplify some of the parts of this product

# Belief networks

- A belief network is a directed acyclic graph (DAG) where
 - nodes are random variables
 - directed arcs connect pairs of nodes (X->Y could mean X has direct influence on Y)
 - direct influence/cause - we don't mean it always causes, it's more of a loose sense of the word
 - each node has conditional probability table specifying effects parents have on the node

Variables

- query variables: the ones you want to know
- evidence variables: variables you can know
- hidden/latent: leftover variables

### semantics of bayesian networks- two ways to understand them

- as representation of joint probablity distribution
- as encoding of conditional independence assumptions

representation of joint probability

- you can find probablity of query given evidence by finding the arc to that point and using joint probability
- Every entry in the joint probability distribution can be calculated from the network:
 - P(X1,...,Xn) = product of P(Xi | parents of Xi)
 - each Xi can be negated or not negated
- From this, any query can be answered: P( Query | Evidence )

encoding of conditional independence assumptions

- since every entry in the joint probability distribution can be calculated 
 - (1) from the network P(X1,...,Xn) = product of P(Xi | parents of Xi)
 - (2) but also from the chain rule = product i=1..n P(Xi | Xi-1, ..., X1) - this is *always* true
- compare these two values
- Want resulting joint probability distribution to be a good representation of the domain
- Equation 1 is a correct representation of a domain only if each node is conditionally independent of its predecessors (in the node ordering), given its parents
- so we're assuming conditonal independence here when we decided what 'caused' what

### coming up with the network

- good networks are simple (few arcs/probabilities) and intuitive (causal)
- and conditional indepdence assumptions must work
- for graphs, incorrect ones we just discard, correct ones we want to be inutuitive and have few probabilities (so a better graph is casual and simple - we will *lose marks* if we don't do this, even if the network is technically correct)


probabilities

- non-boolean domains have a lot more probabilities needed
- you can count probabilities in a couple different ways (write them all out, write out only what you need at minimum) 
 - we can do it any way as long as it's consistent (though I lost marks on the assignment for not writing them all out, so that might be safer for the exam)

### using the network

- you usually query on evidence variables

### Inference in belief networks

Basic task: Determine posterior probability of a set of query variables given exact values for some evidence variables -- P(Query|Evidence)

Types of inferences:

- Diagnostic inferences: inference from effects to causes: P(cause | effect), P(disease|symptom)
- Causal inferences (same network, different kind of query):  inference from causes to effects: P(effect | cause), P(symptom | disease)
- Intercausal inferences (between causes of common effect, hard to reason about): e.g. P(cause1 | cause2, effect), P(disease1 | disease2, symptom)
- Mixed inferences: combining two or all of the previous kinds 


### finding probabilities from the network 

- recall the sum rule - we can use this
- P(X=a) = sum P(X=a | Y=b) \* P(y=b)   for all b in dom(Y)
- = sum P(x=a,y=b)/P(y=b) \* P(y=b) for all b in dom(Y)
- = sum of P(X=a, y=b) for all b in dom(Y)
- do some examples of this! it's hard to remember on the spot sometimes without looking at an example (e.g. in the exam)

#### variable elimination algorithms

- reorder computation
- factor
- cache intermediate results
- worst case: inference in #p-complete (harder than np-complete, counting number of solutions)
- the software we're using for the assignment will optimize a lot of this for us, the algorithms have been worked on a lot
- we can pull out constants
- push sums forward to only the part that uses that value
- some sums will add to 1 and you can take that out

### layers in networks

- varies a lot depending on network
- a common one is root causes -> events -> sensor outputs (things we can measure, like with sensors)
- root causes are sometimes not evidence (latent/hidden) and we have to guess a probability model

# Decision networks

Planning under uncertainty

- to decide among alternative goals and plans of action, an agent's preferences between world states are captured by a real-valued utility function which expresses the desirability of a state
 - e.g. assign 0 to worst posssible outcome, 10 to best
 - note that utility function has no units, it's just a scale of goodness

decision theory is probiability + utility

- decision theory: describes what agent should do
- probability theory: describes what an agent should believe on the basis of evidence
- utililty theory: describes what an agent wants

decision network

- bayesian network + actions + utilities
- we're adding decision variables (and we also have the random variables)

Notation

- Wi for 0 <= i < n are states of the world
- A, B, ... (capital letters) are actions
- U(Wi) is the utilitiy of state Wi (from the point of view of the agent making the decision)

Maximum expected utility (MEU) principle

- a rational agent should choose an action that maximizes the expected utility
- EU(A|E) = sum i={0...n-1} P(Wi|E,A)*U(Wi)
- the expected utility of an action given evidence is the sum of the probability of all the states of the world given the evidence and action times the utility of that state

### What does a decision network look like?

Decision network: Nodes (3 types)

- chance nodes (ovals) represent random vars (as in Bayesian nets)
- decision nodes (rectangles) represent decision variables (choice of action)
- utility node (diamonds) represent agent's utility function on states

Arcs

- it must be a directed acyclic graph
- chance nodes' parents are chance and decision variables that directly influence it
- decision nodes' parents are chance and decision variables whose values will be known when decision is made
- utility node parents are chance and decision variables decribing the outcome state that directly affect utility


### Evaluating a decision network 

Choose an action by:

1. Set evidence variables for current state
2. For each possible value of decision node
  - a. set decision node to that value
  - b. calculate posterior probability for parent nodes of the utility node
  - c. calculate expected utility for the action
3. Return action with highest expected utility

special case: 

- when there's no evidence, we can simplify EU based on just an action
- EU(A) = sum i={0..n-1} P(Wi|A)*U(Wi)
 - some of these might be 0 if they're impossible and P(Wi|A)=0
- (theorem P(A,B|B,C) = P(A|B,C)) is helpful
- you pick the policy with highest EU
 - if you have a variable, the best policy might be different depending on its value 
- theorem: P(X=a|Z=c) = [sum over all b in dom Y] P(X=a | y = b, Z=C)\*P(Y=b | Z=C) is helpful too

### Value of information

the basic idea

- How to decide? Evaluate by the effect on subsequent actions.
- value of information = [expected utility of optimal policy chosen using the new information] minus [the expected utility of optimal policy chosen without new information]
- Notes:
 1. Information has value to the extent that it is likely to cause a change of
plan, and to the extent that the new plan is better than the old plan.
 2. Usually assume that exact information is obtained about the value of
some random variable. May need to average over all possible values of the random variable.

Recall: Evaluating a decision network - we choose an action by:

1. Set evidence variables for current state
2. For each possible value of decision node
  - a. set decision node to that value
  - b. calculate posterior probability for parent nodes of the utility node
  - c. calculate expected utility for the action
3. Return action with highest expected utility

May need to average over all possible values of the random variable.

- so there's different cases, for values of the added variable
- find the best option for each value and it's EU
- then the total overall EU is the weighted sum of each EU (weighted by liklihood it takes on that value)
- value of information is then the (weighted EU with new variable) - (old EU)

### Basis of utility theory (don't need to know in great detail)

- can come up with a utility function for any state of the world

Notation

- A, B, C (states of the world)
- [p, A; 1-p, B] a lottery with two possible outcomes: state A with probability p, state B with probability 1-p
- A > B means outcome A is preferred to B (even though a million gives similar happiness to half a million, most people would still say A > B - so it's preference, not reality)
- A ~ B means agent is indifferent between A and B

Axioms of utility theory

- orderability (A>B) or (B>A) or (A~B)
- transitivity (A>B) and (B>C) => (A>C)
 - if you disagreed with this, and preferred A to B, B>C, C>A
 - suppose you have C, someone can sell you B, then they sell you A, then they sell you C, etc.
- there more axioms on the slides we didn't talk about 

Consequences of axioms

- if an agent's preferences obey the axioms of utility, there exists a real-valued function U such that 
 - U(A) > U(B) iff A > B
 - U(A) = U(B) iff A ~ B
- note that the scale is arbitrary for utility functions, we scale it up or down evenly and it'd be the same (can have really big utilities or negative ones or whatever)


## Sequential Decisions

A sequential decision problem is a sequence of decisions, where for each decision we consider:

- what actions are available to the agent
- what information is, or will be, available to the agent when it will perform the action
- effects of the actions
- desirability of the actions


dynamic programming algorithm:

	repeat:
		consider last decision node
			find optimal decision for every value of the parent node
		replace decision node with a chance node
			- assign probability 1 to optimal choice
			- assign probability 0 to non-optimal choice
	until no more decision nodes
	read policy in forward direction

### Decision processes (feels like we only kinda glossed over this)

- indefinite and infinite horizon problems
 - ongoing processes or it is unknown how many actions are required
- Wide range of applications
 - robotics (e.g., control)
 - investments (e.g., portfolio management)
 - computational linguistics (e.g., dialogue management) - operations research (e.g., inventory management)

Markov Decision Process (MDP)

- set of states S
- set of actions (ie decisions): A
- transition model:  P(S\_t | A\_t-1, S\_t-1) -- given an action and current state, what's the new state we're in
- reward model (i.e., utility): R(S\_t, A\_t-1, S\_t-1)
- discount factor: between 0 and 1 inclusive (10 dollars now and 10 years from now have different values, we value things that are more immediate and over time it loses value)
- horizon (ie # of time steps): h
- goal: find optimal policy


Transition model

-  Markov assumption: P(S\_t+1 | S\_t, ..., S\_0) = P(S\_t+1| S\_t) -- aka the whole history is apparently stored in the current state (though this is not always true in real life)
- Stationary: the transition probabilities are the same for
each time point
 - P(S\_0) specifies initial conditions
 - P(S\_t+1 | A\_t, S\_t) specifies the dynamics, which is the same for each t >= 0

Why so many utility nodes in decision network?

- U(S0, S1, S2, ...)
 - states keep going infinitely into future, infinite process -> infinite utiltity function
- so we assume that they're additive preferences
 - R(S\_t, A\_t-1, S_t-1) is immediate reward from doing action A\_t-1 and transitioning from state S\_t-1 to state S\_t
 - so we say the utility is the sum of all the individual rewards leading up to it

Discounted Rewards

- if process is infinite, isn't the sum of rewards infinite? so how do we pick the right policy
- so we use discounted rewards
 - finite utility is a geometric sum
 - if the discount is less than 1, it converges
 - the discount is like inflation rate
 - intuition is that we prefer utility sooner than later

Policy

- choice of action at each time step
- formally:
 - mapping from states to actions
 - ie delta(state) = action
 - assumption that we have fully observable states (which allows next action to be chosen only based on current state) even though we don't usually know the full state we're in
- optimal policy
 - policy delta* with highest expected utility
 - EU(delta) <= EU(delta*) for all delta

### other representations for decision processes

- dynamic decsion networks
 - MDP is a state based representation
 - so we describe states in terms of random variables
 - replace each state node with baysean network to represent that state
- partially observable Markov decision process (POMDP)
 - states not fully observable
 - partial/noisy observations of the state


# Multiagent systems

- To cooperate or compete with other agents, an agent must reason about other agents' preferences and knowledge of the world, as well as its own preferences and knowledge.

Multiagent framework

- multiple agents, each with 
 - its own info about the world and other agents
 - its own utility function 
- each agent decides what to do, depending on their value and also other agent's values
 - outcome depends on the actions of all agents
- two ends of the spectrum 
 - fully cooperative: agents have same utility function
 - fully competitive: win-lose (zero-sum games) 
- most frameworks are between these two extremes - agent's utilities are synergistic in some aspects, competing in some, independent in others

Game Theory: Framework

- studies what agents should do in mutli-agent setting
- strategic (normal) form of a game
 - finite set of agents I={1,...,n} -- we'll usually use 2
 - set of actions A\_i for each agent i in I
 - a utility function u\_i for each agent i in I
- each agent chooses an actoin without knowing what the other agents choose
- joint action of all agents produces the outcome

Strategies

- a strategy for an agent is either pure or stochastic
 - pure: they pick one action
 - stochastic (mixed): probabiltiy distribution over the actions for this agent (more than one action has a non-zero probability)
- strategy profile is an assignment of a strategy to each agent
 - if sigma is a strategy profile, let sigma\_i be the strategy of agent i in sigma and let sigma\_-i be the strategies of the other agents, ie sigma is (sigma\_i)(sigma\_-i)


Nash equilibrium

- strategy profile sigma has a utility for each agent
 - let utility(sigma, i) be the (expected) utilty of a strategy profile sigma for agent i
- the best response for an agent i to the strategies of other agents is a strategy that has maximal utility for agent i
- a strategy profile is a nash equilibrium if for each agent i, strategy sigma\_i is a best response to sigma\_-i
 - every finite game has at least one nash equilibrium
 - there is not necessarily pure strategy that is nash, though

Pareto optimal 

- An outcome is Pareto optimal if there is no other outcome that makes every player at least as well off and at least one player strictly better off
- every finite game has at least one pareto optimal outcome
- not necessarily a nash equilibirum though

Dominated strategies

- strategy sigma\_i strictly dominates strategy sigma'\_i for agent i if for all strategy profiles sigma\_-i of the other agents, sigma\_i has higher utility
- ie sigma\_i is better than sigma'\_i no matter what other player agents do

Computing Nash Equilibriums

- Iterated elimination of dominated strategies
- eliminate any pure strategy dominated by another strategy
- the dominating strategy can be a randomized strategy
- this is done repeatedly

### Stochastic (mixed) strategies

computing nash equilibrium

- If a stochastic (mixed) strategy is a best response, then each of the pure strategies involved in the mix must itself be a best response; i.e., each must yield the same expected utility
- Forms a set of constraints that can (sometimes) be solved to give a Nash equilibrium
- go over the example of this (it's in the slides I think and also my non condensed notes) cause it's kinda complex

I got a question around this wrong on the assignment - TODO add explanation here

# learning (and supervised learning)

Agents that learn rather than programming them by hand, and agents that learn to improve their performance over time.

taxonomy of learning systems

- rote learning (learning by being told) - memorize facts e.g. database
- speedup learning (learning by practice) - become more efficient over time e.g. caching
- inductive learning (learning by generalizing) - evaluate by correctness of new knowledge

Evaluating learning systems

- Rote learning: evaluate by system's ability to exploit the new knowledge
- Speed up learning: evaluate by measuring increase in efficiency
- Inductive learning: evaluate by correctness of the new knowledge

inductive learning: supervised learning of an unknown function f

- input: collection of training examples 
 - each training example is tuple (xi, yi)
 - where xi is an array of features (also called attributes) 
 - yi is the outcome f(xi) 
- other input is hypothesis space H (could be all neural networks, or in linear regression H could be all possible lines) 
 - can be infinitely big
- output: hypothesis h in H that approximates f (a method of classifying subsequent, unseen examples)

features can be

- real-valued
- discrete, finite set
 - also called categorical or nominal
 - can be binary or (ordered, unordered) multivariate 

outcome -> machine learning problem type

- outcome real valued -> regression problem
- outcome discrete -> classification problem

### issues in learning these functions:

choice of training data

- choice of features/attributes
 - we tend to gather as many features that seem relevant
 - then we reduce it down to the ones we use (using careful methodology)
- constructing new features
 - like ratio of 2 features
 - max, log, and other transformations
- is it representative?
 - e.g. with tennis data if those 2 weeks of data were the only 2 weeks on vacation, we can't really generalize to the rest of the year
- balance of positive and negative examples
 - e.g. collecting data about very rare disease, most of the data is for not having it - we could just say 'no you dont have it' which is really accurate but not what we want
 - one way of fixing this is just duplicate the data for having it
 - keep in mind the cost of the mistake of false positive vs false negative

choice of hypothesis space H

- cost of learning hypothesis h 
 - could take months, the problem is already np complete with just one hidden layer, deep neural networks have multiple hidden layers and need lots of computing power
- amount of training data need to learn h 
 - need lots of data usually to be good
- does representation make sense given domain knowledge and data (is it capable of fitting/representing the data?) 
 - e.g. a line can't model a plot that looks like a curve

choice of hypothesis h in H

- simplicity of learned function h 
 - we always prefer simpler
 - in regression example before, the line was pretty good so we prefer that, even if degree 2 is slightly better
- performance on training set - data it's seen before
- performance on test set - ie ability to generalize (data it hasn't seen before)
- judging performance - measuring errors

Judging performance (measuring errors)

- we are trying to minimize total error, which is the sum of error in each example
- measuring error for discrete classification: e.g. error could equal 0 if we classify wrong, 1 if right 
 - false positive is if you say yes but it's actually no
 - false negative is if you say no but it's actually yes
 - eventually we'll factor in different cost for false positive and false negative, but not talking about it now
- for continuous error: 
 - could take absolute difference between prediction and actual value
 - or could take the squared error - minimizing variance
 - if sum of error (total error) is 0, h is said to be _consistent_ with the data

choice of hypothesis (ct'd)

- definition: given the hypothesis space H, and hypothesis h is said to *overfit* the training data if there exists some hypothesis h' in H such that
 - h has smaller error than h' over the training set
 - BUT  h' has smaller error than h over the entire distributions of instances
- as the complexity of hypothesis h increases
 - error on training set keeps going down
 - error on test set starts high (underfitting), goes down, but then starts to go up (overfitting)
- solution is to keep a test set that is completely independent - you learn h and then check it against test set to see how good it was


## Learning Bayesian networks for classification

- Use a Bayesian network to infer the probability distribution of some class variable
 - specifies the probability the class variable will take on each of its possible values given available evidence

strategies (from simplest to hardest)

- (1) given data and naive bayes (fixed structure) of network, learn probabilities 
 - naive because it assumes a lot of conditional independences that don't hold - but it still works often
 - it's not that accurate, but often works well enough - in this example it actually gets all but 2 correct in the test set
 - and it's easy to calculate!
- (2) given data and structure of network (we come up with it), learn probabilities 
 - polynomial
 - can get a lot of arcs and probabilities
- (3) given data and node for each attribute and class variable, learn arcs and probabilties 
 - NP complete (e.g. local search in A1Q3)
- (4) given data and node for each attribute and class variable, learn hidden nodes, arcs, probabilities
- now whenever someone publishes a fancy new thing, they compare it naive bayes to show that it's better than that simple baseline (turns out some things ppl used to come up with weren't really much better than naive bayes)

#### Naive Bayesian classifier

- create by having a network with output pointing to each cause
- then we can fill out the probability tables using the traininng data
- to use the classifer:
 - let the domain of class variable be {c1,...,cl}
 - given a new instance with feature values a1,...,ak, determine value of class variable with highest probability
 - argument ci that maximizes P(class=ci|a1,...,ak)
 - = argmax\_ci P(class=ci, a1, ..., ak) / P(a1,...,ak)
 - = argmax\_ci P(class=ci, a1, ..., ak) (since the denomator stays constant)
 - = argmax\_ci P(class=ci) * product j=1..k P(aj | class=ci)

Handling missing values

- just omit that part in the calculation

Learning probabilities

- we estimated for example that P( feature=f | outcome=o ) = n\_a/n 
- where n  is number of instances for which outcome=o
- n\_a  is number of instances for which feature=f and outcome=o
- This will be a poor estimate when na is small or zero
 - we'd say the event is impossible but that's not true, it's just unlikely

solution (or rather, hack) to the n\_a = 0 problem

- m-estimate of probability
- probability = (n_a + m\*p) / (n+m)
 - E.g., for P( Outlook = Overcast | Tennis = No )
 - Assume p = 1/3 (equally likely to be Overcast, Sunny, Rain)
- where p is prior estimate of probability
- m is 'equivalent sample size weighting' (the fudge factor, usually 1 or 2) -- this is kind of random and the prof didn't have a good explanation for why certain values work - there's just been done a lot of research on it and this apparently works

#### learning arcs and probabiltiies 

- steps
 1. for each feature/variable and class variable, determine parent sets and score (like A1Q3) (example BIC - Bayesian information criterion - can do this) - we want it to fit the data well but not be too complex (don't want to overfit), so BIC penalizes complexity
 2. pick a parent set that (1) has no cycles and (2) minimizes total score and solve with local search (more efficient, allows more complex network) or A* (guarentees optimal)


# Decision trees

- start at the root
- at each node in the tree, a feature is tested
 - arcs labeled with the values of the feature
- leaves contain the classification
- complexity of tree depends a lot on what you choose to branch on
- each node is a sentence in prop logic : some features with some values => certain output
- nodes (combinations of features) with no data? what would we put there?
 - maybe the answer that shows up more

what makes a good tree?

- simpler trees are easier to parse
- generally, as the tree becomes more complex, the error on the test set will start going up agin (overfitting)
- our more complex tree gets about half of the test set wrong

#### ID3 algorithm

- input: set of S positive and negative examples, set F of features
- recursive algorithm

		ID3(F,S):
			if S contains only positive examples, return yes
		 	if S contains only negative examples, return no
		 	else:
		 		choose best feature f in F
		 		for each value v of f:
		 			add arc to tree with label v 
		 			add subtree ID3(F - {f}, {s in S | f(s) = v})

Which is the *best* feature?

- assumption: simplest decison tree generalizes best
 - NP complete in general to find the smallest decision tree
- approximate using greedy, heuristic approach
- heuristics attempt to distinguish necessary features from extraneous features
 - simplest decision tree is least likely to include unnecessary feature tests

choosing a feature:

- the k children of a feature with p positive and n negative examples each have some number of positive and negative examples p1 n1, p2 n2, ..., pk nk
- worst possible feature is if pi = ni for all i
- best possible feature is if pi=0 or ni=0 for all i (so we get a perfect leaf)

### information theory

- suppose there are l outcomes c_1, ..., c_l each with probability P(c_1), ..., P(c_l) respectively 
- The information content of knowing the actual
outcome is given by:
- I(P(c1),...,P(cl)) = sum i=1..l ( - P(ci) \* log\_2(P(ci)))
- consider the case with two possible outcomes heads/pos or tails/neg
- by defintion, I(1,0) = 0, I(0,1) = 0 
 - because there's no information gained from flipping the coin, we always know it'sll be heads or tails
- more generally, consider I(p, 1-p) for p in [0,1]
 - maximum I at p = 1/2


using this

- Before testing a feature, two possible outcomes 
 - example is positive with probability p / (p + n)
 - example is negative with probability n / (p + n)
- the information content (how much more we'd know) of knowing the actual outcome is I(p/(p+n), n/(p+n))
 - the two paramaters we pass are probability it's a yes and probability it's a no
 - if n or p is 0, knowing the actual outcome gives us no more information - since we already know for sure what the answer is
- now we can test a feature f for how much information we've gained
 - gain(f) = I(p/(p+n), n/(p+n)) - sum i=1..k (pi+ni)/(p+n)\*I(pi/(pi+ni), ni/(pi+ni))
 - we weight the information of each child with (pi+ni)/(p+n)
 - Choose feature with largest information gain


### extending decision trees - tricky things

- numeric (real-valued) attributes (so can't split into buckets of each value) -- we'll look at this one today, the rest next lecture
- missing attribute values
- discrete attributes with many values (will skew gain towards these attributes)
- attributes with cost - e.g. if you're testing for something, you start with things like temperature that are less invasive, and then would do something like surgery later on only if needed, surgery has higher cost
- multiclass (non-binary) class variable
- noise and overfitting

#### numeric (real-valued) attributes

- Many real-world problems contain numeric attributes

solution 1: discretize ranges -> label

solution 2: 

- branch on real valued attributes in decision tree
- dynamically pick a split point c and branch on temp < c and temp >= c)
- how to pick the threshold c though?
 1. sort instances according to the real valued attribute
 2. possible c's are those that are midway between two values that differ in their classification
 3. determine the information gain for each of the possible c's and choose the c with the largest gain

additional complication

- on any path from root to leaf, discrete attribute is tested at most once but real valued attribues can be tested *many* times
- result: large trees, and trees that are difficult to understand (if your goal is just to do well at prediction, this is fine - if you want to understand and explain the decision then it becomes an issue)

**NOTE**: we want the smaller tree *not* because of efficiency (computers are fast at evaluating no matter the size of the tree, we're just going down the tree linear to the height) - it's because simpler trees tend to perform better on data it hasn't seen before

#### Missing attribute values

- real world data often has missing attribute values 
 - maybe values not recorded or maybe too expensive to obtain
- two cases where this comes up
 - data when constructing tree
 - data when using tree

solution when constructing decision tree

- we're trying to build the tree but are missing training data
- one solution is using other instances to estimate missing attribute (use mode)
- or divide example into fractional examples weighted according to frequency of value of attributes

solution when using decision tree

- we want a prediction from the tree but don't have all data
- pretend example has all possible values for the missing variable
- follow all possible branches for those values
- weight answer from each branch by the probailty of that value
- return most probable answer

#### discrete attributes with many values

- Recall: choose the attribute to split on that gives maximum information gain
- Problem: If an attribute has many values, gain will select it
- e.g. imagine day in the training data is an attribute (
 - has 14 values in domain as opposed to others with 2 or 3 
 - so the gain formula will definitely pick this one (because the sum we subtract off in the gain formula will be 0, so gain would be highest)
 - but it wouldn't work at all in the test set

solution: 

- pick an attribute that maximizes gainratio
- Suppose that attribute A splits a set of examples S into k different subsets: S1, ..., Sk
- gainratio(A) = gain(A) / I(|S1|/|S|, ..., |Sk|/|S|)

#### attributes with costs

- In some learning tasks, attributes may have costs 
- so we want high accuracy *and* low cost
- one solution: GainCost(A) = Gain(A)^2 / Cost(A)

#### multiclass (non-binary) class variable

- Suppose class has L possible values {c1, ..., cL}
- update ID3 to check for leaf with all values of class value (and not just positive/negative values as before)
- when we choose the best feature to branch on, instead of I(p/p+n, n/p+n) we'd do I(|C1|/|S|, |C2|/|S|, ..., |Ck|/|S|)
- we have a lot of optimizing for binary variables though, so that's preferred

#### noise and avoiding overfitting

- Attributes may be based on measurements or subjective judgements
- training examples may be misclassified
- problem: ID3 algoithm grows each branch just deeply enough to perfectly classify the training examples
- can't use test set to tell us when to stop (it would corrupt our training)
- solutions:
 - stop growing the tree early (using chi-square statistical test- something something stat 231)
 - post-prune the tree (using a validation set)
- validation set: split up training data into 2/3 training set and 1/3 validation set and prune after 
 - the 2/3, 1/3 ratio is standard but it doesn't have to be exactly that

when we decide to make something a leaf that isn't 100% pure, we can:

- put majority answer
- put probability the answer is yes

# Neural Nets

Modeling a neuron

- output of a neuron 
 - either on ("fires", modelled as 1) or off (0)
 - depends only on inputs
- inputs
 - come in different strengths, depending on the efficiency of the synapses
 - synapse controls how much of the signal gets through
 - learning/memory/etc is adjusting synapses
 - synapses are modelled by weights in neural nets
- learning changes the efficiency of the synaptic junctions

Thresholding functions

- we want to adjust the sum of weighted inputs to be an output between 0 and 1 by applying some function f(x)
- could be step function: 0 if x <=0, or 1 if x > 0
- or sigmoid function: a curve between 0 and 1

single layered network

- we get some inputs, and draw a line/hyperplane using those weights to carve the space

multi-layered, feed-forward networks

- essentially we have similar thing but multiple layers
- multi-layered:
 - a layer of input units 
 - one or more layers of hidden units
 - a layer of output units
- Feed-forward:
 - information flows from input layer, to hidden layer, to output layer
 - the activity of a unit cannot influence its own inputs (no loops)
- Each unit uses the sigmoid threshold function
- note that this network is fully connected between any two adjacent layers

output in classification problems

- we could have one output and divide up the interval for different classification values
- alternatively (and this is what we'll do) we can have an output vector v with v[i] a number between 0 and 1 that is like "probability" the number is i (but the "probabilities" won't sum to 1)

### training a neural network

- the networks must learn to solve a problem
 - learn the weights which allow it to solve the problem
 - programming the weights by hand is impossible
- Supervised learning:
 - network is provided with a set of training examples
 - each example is an input/output pair
 - modifies its weights to give the right output for an input

supervised learning

- initiaize with random weights
- repeat until the error is acceptably small:
 1. present an example
 2. calculte the actual output
 3. calculate error (difference between desired and actual output)
 4. if there is error, determine which weights were at fault and adjust those weights
- pass through training data tons of times until all examples seem to be fine

adjusting weights to account for error: backpropagation learning rule

- the problem is to determine which weights in the entire network were at fault for the error
- backpropagation
 - most popular learning rule for updating the weights in a network
 - updates a weight in proportion to the degree to which changes in the weight will lead to changes in the error

some terms and stuff we'll be using

- sigmoid function, f(x) = 1 / (1 + e^(-kx)) -- gives a value between 0 and 1
- x is input layer with elements x1 to xA
- h is hidden layer with elements h1 to hB
- w1 is the vector of weights on layer one
- w2 is vector of weights on hidden layer
- hj = f((sum i=1..A (w1\_ij * xi)) - beta\_j
 - beta\_j is the threshold
 - we need to learn threshold and weights

We can just treat threshold as another weight

- rewrite as h\_j = f(sum i=0..A  w1\_ij * xi)
 - sum starts at 0 now
 - where x0 = -1 (always)
 - w1_0j is the threshold bj
- same for output units, o\_j = f(sumi=0..B) w2\_ij * h\_i)  where j = 1...c
 - where h1 = -1 (always)
 - and w2\_0j is the threshold for unit o\_j

error:

- 1/2 sum j=1..c (yj-oj)^2
- where yj is desired output, oj is actual output

#### Backpropagation

you probably want to read through the latex's details in slides20.pdf

1. initialize all weights and thresholds to small random values on either side of 0 (between -0.5 and 0.5), so no strong bias, fairly linear, and assign x0=-1 and h0=-1
2. choose an input/output pair (xbar, ybar) where xbar = (x1,...xA), ybar = (y1,...,yB)
3. determine activation levels of hidden layer: hj = f(sum i=0..A W1\_ij * xi) for j=1...B
4. and activation levels of outupt units: oj = f(sumi=0..B W2\_ij * hi) for j=1...C
5. determine how to adjust weights between input and hidden layer to reduce error for this training example: E2\_j = k\*oj(1-oj)(yj-oj) for j=1...C ---- note that we get this expression of how to minimize error from the derivative of the sigmoid function --- k is the multiplicative constant from f(x)
6. then minimize error between input and hidden layer: E1\_j = k\*hj\*(1-hj) sum i=1..C E2_i\*W2\_ji for j=1..B
7+8. then adjust weights, using learning some learning rate (can adjust faster or slower): W2\_ij = W2\_ij + LearningRate\*E2\_j\*hi and W2\_ij = W2\_ij + LearningRate\*E1\_j\*xi
9. If stopping criteria (which you have to decide on) is not met, goto step 2 and repeat

this is all on a handout thing called "backpropagation learning algorithm", posted with slides - seriously just read it, it'll be a lot clearer than this markdown stuff

parameters (we have to decide what values to assign them)

- learning rate 
 - how much we update the weights to adjust for error
 - if it's too high, we might step over global min and oscilate back and forth
 - if it's too low, it'll take forever
 - learning rate is usually good between 0.05-0.35
 - constant vs dynamic - you can use the same k throughout or can change it throughout starting big and getting smaller (e.g. 1/#epochs)
- multiplicative constant k for f(x)
 - if k is higher, more steep
 - if lower, more level
 - what you want depends on the problem
- hidden units size of hidden layer) 
 - if you only have one, error won't converge over many epochs (iterations of the algorithm), will just wander around doesn't really get lower
 - as you have more hidden units - over more epochs, training error goes down but somewhere along the way the test error goes up (overfitting)
 - same kind of graph for #epochs (eventually you're overfitting

stopping criteria

- can have a max number of epochs
 - epoch is one time through the training set
 - this is more of a safety - it might not converge, so we'd have to stop at some point
- can stop when error is 'acceptably small' (better than using max #epochs)
 - acceptably small error on training set, or on separate validation set (traditonally 1/3 validation set, 2/3 training set)
 - classification error (did you get it right or did you get it wrong), or continuous error (how close did you get - even if it's classification we'll use this kind of error because of how neural nets give us a continuous value always)
- can distinguish between training and deployment level
 - e.g. train strictly so that [0, 0.2]->0  and [0.8,1]->1 (needs to be more accurate to be correct when training) and then when we deploy and test [0,0.5]->0 and [0.5->1]->1

preventing overfitting

- early stopping
 - split training set into training and validation set
 - stop learning when error on validation set increases for a specified number of iterations
- regularization
 - add a penalty term to the error function that penalizes large weights (we want to keep weights small so that it still learns)
 - error = 1/2 sum (yj-oj)^2 + penalty
 - e.g. penalty = sum(weight_i)^2

## architectures

- scale inputs/outputs
 - map inputs/outputs to [0, 1], *or* can use mean zero, standard deviation of one
- unary, binary, and discretized real-valued encodings

#### unary encoding e.g.

- ![](L20-unaryencoding.png)


#### binary encoding

- we start at 1 instead of 0 because 0 0 isn't great input to neural networks apparently
- ![](L20-binaryencoding.png)

#### real valued encoding

- now each input/output only represented with one variable
- for 2 values he chooses 0.833 and 0.167 because 0 and 1 are hard to learn (stretched out on the ends of the sigma function) and these values are easier


### Practical Considerations

Training Data

- Size of the data must be large
 - if the problem lacks adequate data, the use of neural networks is not recommended
- Training data must be representative
- Divide data into training set, validation and test set

Network Sizing

- input layer
 - how many units?
 - decide on features
 - decide on unary, binary, or discretized real-valued inputs
- How many hidden units are needed?
 - use as few as possible, it'll be a small fraction of input units
 - if learning doesn't converge, try with more units
- output layer
 - how many units?
 - decide on unary, binary, or discretized real-valued outputs

Training the Network

- The weights are initialized to small random values in the range -0.5 to 0.5
- Many iterations through training set (millions) may be needed before stable solution found
 - each iteration through the training set is called an epoch

Local minima

- The learning rule is not guaranteed to converge
- The network may settle into a local minima, where learning ceases
- If learning ceases and error is still too high:
 - start again with new random weights
 - add more hidden nodes to the network
- experiment with various learning parameters
 - learning rate
 - "squashing" the sigmoid function 
 - momentum ??? TODO ask about this
- In general, much experimentation is needed before the network converges to an acceptable solution

# Support Vector Machines (quick overview)

- there are different lines that could separate  given data 
- which separate the data best? and predict future data
- answer: the decision boundary with largest posible distance to example points (so the green line)
- the data points closest to the line/hyperplane are the support points

What if the data are not linearly seperable? (xor is an example)

- answer: Kernal functions
- Data that are not linearly separable in the original space are separable (almost always) in a higher dimensional (non-linear) space
- the kernel function is a function that maps the original space to the higher demension vector that is seperable

# Ensembles of classifiers

The Basic Idea

- The accuracy of supervised learning can be improved by learning an ensemble of classifiers: a set of classifiers whose individual decisions are combined in some way (typically by voting) to classify new examples.

Improved accuracy

- can be more accurate than its component classifiers if:
 - individual classifiers disagree with each other (errors made by classifiers are uncorrelated)
 - error rate less than 1/2 each (if it's not, you can just inverse it)

Technique 1: bagging

- learning algorithm is run multiple times, each with a different subset of training examples
 - each run uses a training set of m examples drawn randomly with replacement from original training set (TODO ask about m used twice on this slide - slide 11)

Technique 2: cross-validated committees

- learning algorithm is run multiple times, each time with a different subset of training examples
 - divide original training set of m examples into k DIFFERENT sets (difference from bagging, which is with replacement)
 - construct k overlapping training sets by dropping out a different one of the k subsets

Technqiue 3: boosting

- learning algorithm is run multiple times, each time with a different probability distribution p\_l(x) over training examples (this is probability those elements are chosen for the set)
 - in iteration l, training set of m examples drawn randomly with replacement from original set of m examples according to probability distribution p\_l(x)
 - learning algorithm applied to construct classifier h\_l
 - error rate on training set used to determine p\_{l+1}(x) 
- the idea is to place more wieght on misclassified examples, so in subsequent iterations we progressively have more difficult learning problems and are solving tricky things once we're better learners

Technique 4: Injecting randomness

- neural networks: multiple runs based on different initial random weights
- decision trees: when choosing a feature, randomly choose from among the top 10% or 15% of the top information gain

combining classifiers

- unweighted voting
- weighted voting 
 - e.g. weight proportional to how well a classifier does in validation set
- stacking 
 - given classifiers h1,...,hl 
 - learn a classifier h which takes as input the outputs h1,...,hl and figures out how to best choose the 'right' output
 - h could be a neural network, or a decision tree - we feed in inputs (what each classifier predicts) and it tells us its overall guess of a more accurate prediction

# steps in supervised machine learning 

(some of these steps used on assignment)

#### (1) choose features

- to use suprevised learning techniques, problem first has to be phrased as a classification problem
- choice of distinguishing features is critical to successfully learning a classifier
- begin with many features that are felt to be promising
- possibly sythesize new features from exisiting features 
 - e.g. compare two features, max or two features, ratio often works well, average of some set of features

#### (2) collect data

- data must be representative of what will be seen in practice
- let S be the set of correctly labeled data, and let n = |S|

#### (3) Filter features

- goal of filtering is to select most important features- the ones we use for learning
- motivation: 
 - efficiency
 - good performance
 - many learning methods do poorly if there are redundant or irrelevant features
- example: prune feature if... 
 - single feature (so just that features) decision tree classifier using this feature is not much better than random guessing 
 - AND all two feature classifiers using this feature are not much better than random guessing

#### (4) (we start here in assignment) Select classifier - model selection

- given S, the set of correctly labeled data, select (learn) classifier h from hypothesis space H
- if lots of data available:
 - split data into data used for training and data used for testing
 - learn hypothesis h (select classifier) using training data, evaluate h on test data, where there is enough test data for the resuts to be signficant
- if not
 - use cross validation or bootstrapping
 - repeatedly learn hypothesis h (select classifier) using different training data, evaluate h on different test data

Setting parameters

- for each choice of paramater settings, evaluate classifier h learned from trainig data with current choice of paramater settings
- return parameter settings that give best performances on cross validation set
- examples: 
 - setting number of hidden units in neural network
 - setting min examples at leaf in decision tree
- learning may involve splitting data into training and validation sets

#### (5) evaluate classifier (model assessment)

- estimate classifier performance by:
 - applying classifier to test data,
 - use estimate from cross-validation, or
 - use estimate from bootstrapping
- If using cross-validation or bootstrapping, after estimate is obtained, combine all of the data into one large training set to obtain final classifier TODO ? "obtain final classifier" from all the training data combined?

### Evaluating classifiers

Cross validation:

- use this when amount of data is limited

		partition data S into k disjoint subsets S1, ..., Sk 
		for i = 1 to k
			train on S - Si to obtain hi (select hi using S - Si)
			test hi on Si
		average the k performance estimates to give an
		  overall performance estimate

- we use different subsets as test sets and take average performance
- k = 10 is shown to be pretty good (ten-fold cross-validation)

Bootstrapping:

- also used when amount of data is limited

		for i = 1 to m
			sample with replacement n times from S to give Si
			train on Si to obtain hi (select hi using Si)
			test hi on S - Si (unpicked instances)
		average the m performance estimates to give an overall performance estimate

- On average, training set will contain 63% of instances
- Peter prefers cross validation because it's the CS way

Cost of classification errors:

- cost of false negative and false positive may be unequal
- e.g. for spam the cost of false positive is much higher than false negative


# Natural Language For Communication (a brief overview)

Levels of analysis in understanding natural language

- prosody
 - rhythm and intonation (like sarcasm) 
 - also how you convey it in written language 
- phonology
 - basic sounds and how they combine to form words
- morphology
 - rules for formation of words (e.g. plurals, verb tense)
- syntax
 - rules for forming phrases and sentences
 - though grammar is a lot more fluid than the official rules
- semantics 
 - meaning of words and how these combine to form meaning of sentences
- pragmatics
 - study of how language is used, role of context in meaning

We can put these into categories

- prosody, phonology -- speech recognition
 - recognizing the words and word boundaries is hard
 - need context to know what words are being said
- morphology, syntax - surface form
 - the actual text
- semantics and pragmatics - meaning
- surface form + meaning => natural language understanding
- speech understanding is the whole thing

what makes it hard?

- in formal languages (prop logic) 
 - there's no ambiguitiy at sentence level 
 - if we know the meaning of the pieces we know the meaning of the whole
 - ^ compositional semantics
- natural languages:
 - highly ambiguous
 - not necessarily 'grammatical'
 - constantly evolving
 - semantics or meaning is non-compositional and very context-dependent

there's this idea of AI-complete

- if you can solve an AI-complete problem you can solve all of AI
- not as rigourously defined as NP-complete

### types of ambiguity

- lexical (words with different meanings) ambiguity
- syntactical ambiguity (what noun does the verb apply to? is a word a verb or noun? etc)
 - synantic ambiguity leads to semantic ambiguity 
- referential ambituity (e.g. using a pronoun that could refer to either person)
- Intersentencial referential ambiguity
- Figure of speech
 - metonymy: A thing or concept is not called by its own name, but by the name of something closely associated with it
 - metaphor: A phrase with one literal meaning is used to suggest a different meaning by way of an analogy
- pragmatic ambiguity
- conventions, knowledge of culture
- shared background knowledge: 

#### things affecting pragmatics

- conventions on cooperative answers 
- Situation, knowledge of the other agent's knowledge
- goals, beliefs, shared knowledge

syntax grammar, lexicon (vocab), grammar (order these words could be in)

- idea is, we translate the phrase into logical form
- then if we assume semantics are always compositional (it *usually* is) we can get the basic idea of what it means

### Semantics

- Assume compositional semantics is adequate
 - ie semantics of any phrase is a function of the semantics of its sub-phrases
- Basic idea
 - parsing drives translation into meaning representation (logical form)

Noun phrase (NP) semantics

- Basic paradigm: construct a meaning representation for set of all possible referents of NP in domain
- For each noun, adjective, and PP that appears in the NP, and recursively for each embedded NP
 - build / retrieve logical from 
 - add to existing logical form

## Review

coverage: the primary focus for the exam is lectures and assignments, but you can check out the textbook if you want (not all chapters and all parts of chapter has stuff we covered, but there's more examples and sample exercises)

check out slides23 (review pages)

he strongly hinted that stuff not covered on assignments won't show up on the exam, though you can still study it for ~~knowledge~~

random notes:

- casual networks are *better* than non casual (even if they're both "correct")
- "worthwhile" means what's the value of it
- as soon as validation set stops decreasing as much or goes up or even just plateaus STOP there