CS 241

Foundations of Sequential Programs

Ashif Harji (i prounced as EE)

Tutorials with solutions: https://www.student.cs.uwaterloo.ca/~cs241/#tutorial

-----
##Jan 6

Foundations of Sequential Programs
* Foundations: we want to understand how sequential programs "work". We want to know how we get from the program we write to the program that the compiler runs.
* Sequential programs: not multi-threaded or concurrent


###Administrative stuff

Assignments
- 11 assignments each with multiple parts ~8
- long, lots of work -> start early
- due Wednesdays at 5pm on Marmoset
- we can use C, C++, or Racket - you can read up on the website for things to know to use these languages in the course

Marking scheme:
- Assignments 25%
- Midterm 25%
- Final: 50%
- you need at least 50 for (midterm + 2*final)/3

Midterm date: Tuesday March 3 2015 4:30-6:20pm

We'll be using piazza

no required textbook

###Introduction

What happens when you compile and run a program?

What is a compiler?
- high-level language ->[compiler]-> lower-level language
 - this is common, but not always the case
- more generally: 
 - source language ->[compiler]-> target language
 - source program ->[compiler]-> target program (equivalent, for some definition of equivalent)

Why do we need a compiler? - helping humans write code
- easier for people to understand and program
- lets you choose the right language for the job you want to do
- safety - compiler helps you, lets you know if you're doing bad things
- abstraction - can write programs without understanding how everrrything works

Why do we need a compiler? - why can't computer just run the source language directly?
- slower
- low-level language is hardware specific (machine dependent) - optimized to run well on specific hardware - however the source code is machine independent

###A closer look at the compiler 

(the CS241 compiler - not neccesarily true for every compiler)

	source program -> [scanning/lexical analysis] -> tokens 
        	       -> [parsing] -> parse tree 
        	       -> [semantic analysis] -> parse tree and symbol table 
        	       -> [semantic analysis] -> parse tree and symbol table 
        	       -> [code generation] -> assembly code assembly code 
        	       -> [assembler] -> machine code (1s and 0s) 

- scanning normalizes input e.g. normalizes whitespace - example could be an if statement gets the token "IF"
- the first two steps are the syntactic analysis
- example assembly code: add $3 $5 $7     jr $31
- note the assembler is a specifc type of compiler that translates between assembly code and machine code

We're going to be coding this entire process!


###Bits
- Binary number
- Bit: 0 or 1. Abstraction of high/low voltages or magnets
- Byte: 8-bits e.g. 11001001
 - There are 256 (2^8) possible bytes
- Word: machine specific grouping of bytes
 - 4 or 8 bytes (32-bit or 64-bit)
 - we are going to use a 32-bit word - this matters when we generate and assemble assembly code
- Nibble: 4 bits. Half a byte


#### Bytes

Given a byte in the computer's memory, what does it mean? For example: 11001001

It could be a number. Conventionally in binary it is 2^0 + 2^3 + 2^6 + 2^7 = 1 + 8 + 64 + 128 = 201

It is an unsigned value. Wait, how do we represent negative numbers?

#####Negative numbers 

Simple approach: sign-magnitude representation. 
- Reserve first bit to represnt the sign. 0 means positive, 1 means negative - the rest of the bits are the magnitude
- e.g. 11001001 is a negative number with magnitude 1001001 = 73 -- so our value is -73
- For 8-bit, it can represent numbers form -127 to 127
- 0 is both 00000000 and 10000000 - two representations for zero! need to comparisons for NULL - not good
- another downside - arithmetic is tricky. 
 - If we have to positive or two negative values, do the normal addition for magntiudes and use the common sign.
 - But what if signs are different? Subtract the smaller value from the larger value and use the sign of the larger - overly-complicated. Not good.

Negative numbers - better approach - 2s compliment

- interpret the number as unsigned
- if the first bit is zero, then done
- else, subtract 2^n
- e.g. 
 - n = 3: 011 = 3
 - 111 = 7 -> first bit is not 0 -> subtract 2^3 -> = -1
 - 101 = 5 -> first bit is not 0 -> subtract 2^3 -> -3
 - 1100100 from before is now -55
- To get the twos compliment negation of an n-bit number, subtract the number from 2^n. 
 - Alterntaively, flip the bits (0->1, 1->0) and add one.

e.g. 110

	1000    110->001
	-110         + 1 
	 010         010

Binary <-> Decimal

	Binary 	000 	001	010	011	110 	101 	110 	111
	Decimal	0 	1 	2 	3 	-4	-3 	-2 	-1

- For 8 bits, this gives -128 to 127
 - note: -128 has no negation as 128 has no representation
 - e.g. with 3 bits: 100 -> 011 + 1 = 100 (we get the same thing - no representation for its negation)
- Only one zero
- Arithmetic is clear. Arithmetic is mod 2^n (you can just add them like you usually would)

#### Hexadecimal Notation
- Base 16: 0..9, and A-F (case doesn't matter)
- Each hex digit is 4 bits, so 11001001(subscript 2) = C9(subscript 16)
 - subscipt 2 means it's in base 2
- convert binary to hex by taking 4 digit chunks 
 - e.g 11001001 - break into 1100 1011 -> 1100=4+8=12=C  1001=1+8=9 -> C9
- use 0x as the hex prefix e.g. 0xC9

Given a byte how can we tell which interpretation is correct? (unisnged, sign-magnitude, twos complement)
- We can't really know. We need to remember our intent when we stored the byte

But wait! We don't even know if it is a number

####Back to Bytes
- It could be a character - depends on the character coding you're using.
- We will assume a convention ASCII (American standard code for Information Interchange)


----

##Jan 8

what does 11001001 represent? 
- number: unisnged, sign-magnitude, two's compliment
- it could be a character
 - ASCII
 - bit representation - so we don't know what character that is - we'll be using words still but with first bit 0
 - what chracter is 01001001?   I
- could be address
- random flags
- instruction, or part of one (our instructions are 32-bit)
- we can't really know. we need to remember our intent when we stored the byte

###Machine Language - MIPS
- What does an instruction look like?
- What instructions are there?
- We will use MIPS (simplified) : 18 different 32-bit instruction types

###The hardware and datapath

	               CPU                                    Main Memory (RAM)
	 ---------------------------------------                 __________
	|                      registers        |               |          | 0x000
	|     --------         _____            |               |          | 0x004
	|    |  ALU   |       |  $0 |   [HI]    |               |          | 0x008
	|    |        |        -----    [LO]    |               |          | 0x00C
	|     ________        |  $1 |           |               |          | 0x010
	|                      -----      [MAR] | <=== BUS ===> |          |   .
	|    --------         | ... |     [MDR] |               |          |   .
	|   | control|         -----            |               |          |   .
	|   | unit   |        | $31 |           |               |          |
	|   |        |         -----            |               |          |
	|    --------                           |               |          |                    
	|               [PC]                    |               |          |
	|               [IR]                    |               |          |
	|                                       |               |          |
	 ---------------------------------------                 ----------


CPU
- The brains of the computer
- Control unit: 
 - Fetches and decodes instructions. 
 - Coordinates iput and output. 
 - Dispatches to other parts of the computer to carry them output
- ALU: Arithmetic and Logic Unit
 - Responsible for math, logical operations and comparisons

Memory:

	FAST ------------------------------------------------------------------------------- SLOW
   	   Registers    Cache            Main Memory (RAM)       Secondary storage (hard disk)
   	     *                                  *                  (tape, network, ...)	

MIPS 32 General purpose registers
- $0 is always 0
- $30 and $31 are special by convention
- An example register operation
 - "Add the contents of registers s and t, and store the result in d"
 - represented by $d <- $s + $t
- 32 registers, 5 bits per register (2^5)
- 3 registers for an instruction => 15 bits set aside for registers => 17 bits leftover to encode the operation

multiplication gives 64 bit result - first 32 bits are in HI and second 32 bits are in LO
- HI and LO store results but you can't write to them directly - there are ops to move values out of high and low
- HI can also store remainder from division and LO quotient 

RAM - Random Access Memory
- This is the main memory of the computer
- This is a large amount of memory stored away from the CPU
- The data travels between the CPU and RAM and the bus 
 - We think of the bus as 64 wires connecting the two components.
- The RAM is just a big array of n bytes n ~ 10^9 (a Gigabyte). 
- Each byte has an address, running from to n-1, but we group everything on the order of a word, so we will use addresses divisible by 4, and each 4-byte block is a word. 
 - Words have address 0x0, 0x4, 0x8, 0xC, 0x10, 0x14, 0x18, 0x1C, 0x20 etc.

Moving data between RAM and CPU
- Load: 
 - Transfer a word from a specified address to a specified register.
 - The desired address goes into the MAR (memory address register), then goes out onto the bus. 
 - When it arrives at the RAM, the associated data is sent back on the bus and goes into the MDR (memory data register). 
 - The contents of MDR are moved to the destination register.
- Store: exactly like load, but in reverse


###Programs
- How does the computer know which words contain instructions and which contain data? Surprise! It doesn't. 
- There is a special register called PC (program counter) which holds the address of the NEXT instruction to run. 
- By convention, we guarantee that some fixed address (say 0) contains code and then initialize PC to 0.
- Then the control unit runs the fetch-execute cycle.

fetch-execute cycle:

	PC <- 0
	loop
		IR <- MEM[PC] // instruction counter (register)
		PC <- PC+4
		decode and execute instruction in IR
	end loop
	(this program never ends, but gives you the idea)

How does a program get executed? 
- There is a program called the loader, which puts the program in memory and sets PC to the address of the first instruction in the program. 

What happens when the program ends?
- We need to return control to the loader; PC is set to the address of the next instruction in the loader.
- Which instruction is that? 
 - $31 will always store the correct address to return to, so we just need to set PC to $31
 - Note the use of $31 is convention.
- We will use the 'jump register' command (jr) to update the value of the PC.

----
##Jan 13

Last time:
- hardware architecture
- machine language
- main memory vs registers
- 32 general purpose registers
- $0 always 0
- $31 return address - on program start set to return address of loader, our programs should always end by jumping to that return address ($31) using the jump register command (jr)
- $30 is ?? (we do not know yet)
- PC - program counter - holds the address of the next isntruction to execute

Example: add 2 values in registers 5 and 7, storing the result in register 3, then return

	assembly: 
		add $3 $5 $7 0x0000
		jr $31 0x0004
	assembling add instruction:
		0000 00ss ssst tttt dddd d000 0010 0000 
		0000 0000 1010 0111 0001 1000 0010 0000
		0x00a71820
	assembling jr instruction: 
		0000 00ss sss0 0000 0000 0000 0000 1000
		0000 0011 1110 0000 0000 0000 0000 1000
		0x03e00008

Example: add 42 and 52, store sum in $3 and return the result in register 3, then return.

	lis $5         0x000       0000 0000 0000 0000 0010 1000 0001 0100       0x00002814
	.word 42       0x000       0000 0000 0000 0000 0000 0000 0010 1010       0x0000002a
	lis $7         0x008       0000 0000 0000 0000 0011 1000 0001 0100       0x00003814
	.word 52       0x00C       0000 0000 0000 0000 0000 0000 0011 0100       0x00000034
	add $3 $5 $7   0x010       0000 0000 1010 0111 0001 1000 0010 0000       0x00a71820
	jr $31         0x014       0000 0011 1110 0000 0000 0000 0000 1000       0x03e00008

xxd -cols 4 add1.mips
- this command will show hex code

xxd -cols 4 -bits add1.mips
- adding the bits option shows binary

the .mips file size is # instructions * 4 bytes

###Assembly Language
- We will begin writing our programs not in binary and hex, but with simple mnemonics.
- There is a translation back to the required binary (assembler). Each assembly instruction corresponds to one machine instruction (almost).

We will revisit the previous example

	lis $5              ($5<-42)
	.word 42            (not an instruction, a directive that says the next word 
	                     in the binary should be literally 42)
	lis $7              ($7<-52)
	.word 52
	add $3 $5 $7        ($3 <- $5+$7)
	jr $31              (pc <-$31)

####Branching
- beq: go somewhere else if two registers are equal
- bne: go somewhere else if two registers are not equal
- both instruction increment the PC by a given number of words (forward or backward). 
- What is the value of PC before the branch is executed? 
 - Based on fetch-execute cycle, PC has already been incremented to point at the next instruction before the instruction has been decoded and executed.
 - Hence, offset is relative to the next instruction.

####Another command:
slt - set less than
- slt $a $b $c 
- $a<-1 if $b < $c, $a set to 0 otherwise

**Example**: compute the absolute value of $1, store it in $1 and return. 
- To do this we need to use branches and jumps to modify the PC
- how about we start with the c++ version: if (x < 0) x=-x;

assembly:

	slt $2, $1, $0  ; (compare $1 < 0)
	beq $2 $0 1000  ; (if false, skip over)
	sub $1 $0 $1 	; (negate $1)
	jr $31 			; (return)

**Example**: sum the integers 1..13, store in $3 and return
c++:

	int sum = 0;
	for (int i=13; i!=0; i-=1) { sum+=1; }

What if we cannot use loop constructs? - goto!

	int sum=0;
	int i=13;
	top:
		sum+=i;
		i-=1;
		if(i!=0) goto top;

Assembly:

	add $3 $0 $0   ($3 <- 0)
	lis $2 	       ($2 <- 13)
	.word 13
	add $3 $3 $2   ($3 += $2)
	lis $1 	       ($1 <- 1)
	.word 1 		
	sub $2, $2, $1  ($2 -= $1)
 	bne $2 $0 -5    (loops! why 5?)
 	jr $31

 Branch offset -5 because PC is pointing at next instruction.

 inefficiency in code: setting $1 to 1 on each iteration of the loop


----
##Jan 15

Last Time: Assembly Programming

sum integers 1..13, store in $3 and return

	add $3, $0, $0
	lis $2
	.word 13

	add $3, $3, $2
	lis $1
	.word 1
	sub $2, $2, $1
	bne $2, $0, -5 (loop!)
	jr $31

###Programs with RAM
- lw is the load word instruction
 - loads a word from RAM into a register.
 - syntax: lw $a, i($b)
 - loads the word at MEM[$b+i] into $a
 - $b is base register, i is offset
- sw is the store word instruction
 - stores a word from register into RAM.
 - syntax: sw $a, i($b)
 - stores the word at $a into the memory location MEM[$b+i]

Example: $1 holds the address of an array, and $2 holds the length of the array. Retrieve the element with index 5, and store it in register $3.

	lw $3, 20($1)  ; 5*4 = 20
	jr $31

Now we do it again, but more abstractly. In this case, the index is not known. We need:
- mult: the multiply instruction. 
 - Since multiplying two 32-bit numbers might result in a 64-bit number, the results are stored in two special registers hi and lo.
 - syntax: mult $a, $b
- div: the divide instruction
 - The quotient is stroed in lo, and the remainder is stored in hi.
 - syntax: div $a, $b
- mfhi and mflo: move from HI and move from LO instructions
 - move the values from hi or lo respectively into a given register.
 - syntax: mfhi $d, mflo $d

assembly code:

	lis $5  ; load the arbitrary index into $5
	.word ___
	lis $4  ; load the value 4, the size of a word, into $4
	.word 4
	mult $5, $4  ; this is the offset to the array address
	mflo $5  ; move this offset value into register 5
	add $5, $1, $5  ; add the offset to the address of the array, which was stored at $1
	lw $3, 0($5)  ; load from memory the address we need, into $3
	jr $31

###Labels: revisiting the loop
- recall the loop we had (Sum the integers 1..13, store in $3, then return) 
- the lis command was within our loop, which could be moved outside.
- This is fine, but now the bne at the end has an improper immediate (should be -3)
- In nester loops, this is a nightmare. This is because we used explicit branching, adding/removing instructions means we must change branch offsets.
- Instead, the assembler allows labeled instructions:
 - syntax is   label: instruction
 - The assembler associates that name (label) with the instruction
- when the assembler sees a label, it computes the difference between PC and top, in terms of words: 
 - e.g. above: label-PC / 4  = 0x14 -0x20 / 4 = -0xC/4 = -12/4 = -3

Now we can rewrite the loop example with a label:

	0x00 add $3, $0, $0
	0x04 lis $2
	0x08 .word 13
	0x0c lis $1
	0x10 .word 1
	     top:  ; the value of `top` is 0x14
	0x14 add $3, $3, $2
	0x18 sub $2, $2, $1
	0x1c bne $2, $0, top  ; `top` is provided, rather than an explicit offset
	0x20 jr $31

###Procedures
Procedures (functions) in assembly allows us to reuse code.

We have two problems to solve:

1. Call and return: How do we transfer control into and out of the procedure, parameter passing, etc.
2. Registers: How do we make sure that procedures do not overwrite important data?

We will start with the second problem.
- We could reserve registers for the prodcedure f, and some for mainline with no overlap, but if procedures call other procedures or themselves, we run out of registers.
- Instead, we will allow procedures to do whatever they want with registers, as long as it puts them back to their original values on exit.
- Thus we need to use RAM. How do we keep procedures from using the same RAM?
- To prevent procedures from using the same RAM addresses, we use a stack pointer. The stack pointer is located at register $30, which is the other restricted register that we previously discussed.
- Essentially, we allocate memory from the top or bottom of free RAM, and somehow keep track of what RAM is not used.
- The machine helps us out: $30 is initialized (by the lowader) to just past the last word of memory.
- We use $30 as a "bookmark" to separate used and unused RAM, if we allocate from the bottom.
- We use RAM like a stack, moving up on function calls and down on returns.

Our strategy: each procedure pushes the registers it will use onto the stack and pops the origin values from the stack when finished. $30, the stack pointer, contains the address of the top of the stack.

Template for Procedures:

	Push registers function will modify onto the stack.
	Decrement stack pointer ($30) accordingly.
	Execute function, which should only change the saved registers.
	Increment stack pointer $30.
	Pop registers off the stack.
	Return.

Example: Suppose a function f only uses $2 and $3.

	f: 
	    ; store registers we will be using into memory
	    sw $2, -4($30)
	    sw $3, -8($30)
	
	    ; decrement stack pointer
	    lis $3
	    .word 8
	    sub $30, $30, $3
	
	    ; FUNCTION BODY GOES HERE
	    ; can use $2, $3
	    ; ...
	
	    ; pop registers off stack
	    ; move stack pointer back, load stored registers, return
	    add $30, $30, $3
	    lw $3, -8($30)
	    lw $2, -4 ($30)
	    jr $31

Why do we save the registers and ***then*** adjust the stack pointer?

Because once the registers are saved, we can use one to hold the stack adjustment vlaue.

-----
##Jan 20

Last time
- Assembly programming: procedures
- the machine only has 32 registers. How do we make sure important data is not overwritten?

###Code to call and return

	main:
		...
		lis $5 		;#5 contains the address where
		.word f  	;the procedure f begins
		jr $5 		;how do we get back HERE?
		(HERE)
		...
	f:
		...
		jr $31

when we return from a procedure, we need to set PC to the line after the jr (HERE). How do we know which address that is?
- jalr command is jump and link register 
- the instruction is exactly like jr,but it also sets $31 to the address of the next instruction (i.e. $PC)

assembly code now looks like

	main:
		...
		jalr $5 ; $31 = (HERE)
		(HERE)
		jr $31 ;uhoh, overwrote $31

Thus in order to return to the loader we must save $31 on the stack first and then pop it before returning

###mainline template:

	main:
		lis $5
		.word f
		sw $31 -4($30) ;push $31 onto the stack
		lis $31
		.word 4
		sub $30, $30, $31 ;update stack pointer
		jalr $5 ;call f
		lis $31
		.word 4
		add $30, $30, $31 ;update stack pointer
		lw $31, -4($30) ;pop $31
		jr $31
 
	f:
	 	push registers
	 	decrement $30
	 	body
	 	increment stack pointer
	 	pop registers
	 	return

###Parameter/Result Passing

- The easiest option is to pass/return parameters via registers. 
- However, this makes it complicated with respect to figuring out where registers/paramters are.
- If this is done, the procedure writer MUST document their code so that the client knows which registers will be passed backwards and forwards.
- The largest problem is that there are only 32 registers. A better option push parameters onto the stack. This also requires documentation.

Full Code Example - We will write a function which will sum the first n numbers.

	; sum 1 to N - adds the numbers 1..N
	; Registers
	; 	$1 - working (i)
	;   $2 - input, the value of N
	;   $3 - output
	sum1toN:
	sw $1, -4($30)
	sw $2, -8($30)
	lis $1
	.word 8
	sub $30, $30, $1 ;decrement stack pointer
	add $3, $0, $0 ;initialize $3
	lis $1
	.word 1 ;initialize $1
	top:
	add $3, $3, $2
	sub $2, $2, $1
	bne $2, $0, top
	
	lis $1
	.word 8 			;update 1
	add $30, $30, $1 	;increment $30
	lw $2, -8($30)
	lw $4, -4($30) 		;pop registers
	jr $31

Recursion
- No extra machinery is needed
- If registers, parameters, and the stack are managed correctly, recursion will work.

Input and Output
- Input: not supported. Deal with it
- Output: MIPS provides a location (0xffff000c) called video memory, to store words where the least significant byte will be printed to the screen.

example: print CS, followed by newline

	lis $1
	.word 0xffff000c
	lis $2
	.word 67 ;ascii C
	sw $2, 0($1)
	lis $2
	.word 83 ;ascii S
	sw $2, 0($1)
	lis $2
	.word 10 ;ascii newline
	sw $2, 0($1)
	jr $31
	; output: CS\n

###The Assembler
An assembler is a program that translates assembly code into  equivalent machine code
- Assembly code (add $1, $0, $0 ...) -> assembler -> machine code (0100010...)

Any translation proccess involves two steps 
- analysis: understand what is meant by the source string
- synthesis: output equivalent target string

An assembly file (.asm) is just a stream of characters; a text file.

Step 1: Group characters into meaningful **tokens**. For example, labels, hex numbers, regular numbers, .word, registers, etc.
- This part has been done for us; we will talk about it in far more detail.
- (e.g. for the C++ starter code, each token is an instance of the token class)

Step 2: Group tokens into instructions, if possible (analysis)

Step 3: Output equivalent machine code (synthesis)
- If tokens are not arranged into sensible instructions, output ERROR to standard error
- Advice: there are many more wrong configurations than right ones for an assembly file. 

For example, the instructor beq $1, $0, abc  would generate a sequence of token "kinds": ID REGISTER COMMA REGISTER COMMA ID

####Biggest problem with writing assembler
How do we assemble:

	beq $1, $0, abc
	...
	abc: add $3, $3, $3

we can't assemble this because we don't (yet) know what abc is. The solution: we will scan through the program twice.

Pass 1: group the tokens into instructions and record the addresses of all labelled insturctions - a "symbol table" which is conceptually a list of (name, address) pairs.
- A line of assembly can have more than one label

Pass 2: Translate each insctruction into machine code. If an instruction refers to a lable, look up the associated address in the symbol table.
- Our assembler should ouptut the assembled MIPS to stdout, and we should ouput the symbol table to stderr

Assembly Trace:

	main: 	lis $2
		.word 13
		add $3, $0, $6
    	top:  	add $3, $3, $2
     	 	lis $1
	     	.word 1
	     	sub $2, $2, $1
	     	bne $2, $0, top
	     	jr $31
    	beyond:

-----
##Jan 22

Last time
- jalr for call
- jr for return
- Assembler 2 passes  assembly code -> [lexer(given to us)] -> tokens -> [pass 1] -> symbol table + intermediate representation -> [pass 2] -> machine code

####Two passes
Pass 1:
- group tokens into instructions and build symbol table
look at the assembly trace above (from end of last class)

		label 	|	address
		--------------------
		main    |    0x0
		top     |    0xC

- to find the addresses, it's easiest if you write them in beside each line of assembly

Pass 2:
- translate each instruction 
- e.g. lis $2 -> 0x00000104    .word 13 -> 0x0000000d
- ...

e.g. bne $2, $0, top 
- look up top in symbol table 
- calculate (top-PC)/4 = (0xC-0x20)/4 = 12-32 / 4 = -20/4 = -5 
- we get 0x1440fffb

Side note:
- to negate a two's compliment value, flip the bits and add one

example: 

	5 = 0000 0000 0000 0101
	-5 = 1111 1111 1111 1010 + 1
	1111 1111 1111 1011
	f    f    f    b

###Bit-shifts:
- To assemble bne $2, $0, top (top <- -5), we look at: 0001 01ss ssst tttt iiii iiii iiii iiii
 - opcode: 000101 = 5
 - first reg = 2 = 00010
 - second reg = 0 = 0000
 - offset = -5

Problem: we need to assemble the 6 bit opcode, the two 5-bits of regiters and the 16 bits of immediate value into a 32-bit instruction. How?
- unsigned int instr;
- c/c++: 5 <<26
- racket: (arithmetic-shift 5 -26)

e.g.

	5<<26 = 00010100 0000 0000 0000 0000 0000 0000
	2<<21 = 0000 0000 0100 0000 0000 0000 0000 0000
	0<<16 - 000....
	
problem: -5 = 0xfffffffb, we only have room for 16-bits

bit-wise AND: two 1s give a 1, anything else gives 0

	    10111001
	AND 01100011
	    --------
	    00100001

- when we bitwise-and with a 1, the input is unchanged. when we bitwise-and with a 0 the result is 0. Thus we an use it to turn bits off.	  

bit-wise OR:

	    10111001
	OR  01100011
	    --------
	    11111011

- when we bitwise-or with a 1, the result is 1. when we bitwise-or with a 0 the input is unchanged. Thus we an use it to turn bits on

To fix -5, we can do a bitwise-and with 0xffff
- -5 & 0xffff = 0xfffb
- This turns off the bits more significant than we need (only keep the last 16 bits)

our code:

	unsigned int instruc;
	instr = (5<<26)|(2<<21)|(0<<16)|(-5 & 0xffff)
	cout << instr;

Unfortunately we output 339804155, which is the decimal representation and is 72-bits. This is bad. When we print an int, it figures out the digits and then prints them. If we print a char, we print out the ASCII code exactly. Unfortunately, chars are only 8 bits, (we print the fb of 1440ffffb which was desired) so we convert instructions to 4 chars.

	char c = instr>>24;
	cout << c;
	c = instr>>16;
	cout << c;
	c = instr>>8;
	cout << c;
	c = instr
	cout << c; ;no newlines ever
	// remember to pipe output | xxd to see hex

in racket: use write-byte and, for example,(bitwise-and -5 #xfff))

It is possible to create a solution using unions and bitfields. It is not as portable as the previous solution, but very elegant.

Error checking - include the string ERROR somewhere in the error statements. In Racket - (error "ERROR")

##The Loader
Let's start by writing an operating system.

repeat:

	p <- next program to run
	*copy p into memory, starting at 0x00
	jalr $0
	beq $0, $0, repeat

the star is essentially a loader. mips.twoints and mips.array do this

The 0S is also a program. Where does it sit in memory? Other progams may be running as well. Where are they in memory? And more generally, there may be other code in memory e.g. libraries

We can choose a different starting address for programs at assembly
- BUT then how does the loader know where to load them? What if two addresses are the same? 
- We cannot load programs anywhere. Labels may resolve to the wrong address (.word or bne/beq)
 - The load will need to somehow fix .word id  --- we add α
 - the program is going to be loaded at α
 - id represents an absolute address

.word constant ; this is fine, we do not need to fix it

What about branches with an id - no, because the assembler calcualtes a relative offset based on PC, so relocation is unnecessary.

----
##Jan 27

###Tips for Marmoset
- Refer to the MIPS assembly reference shet.
- User specification to create your own tests.
- Can use cs241.binasm to compare against - your assembler shoud give the same output as cs241.binasm

###Last Time
- Loader - we cannot assume our code will always be loaded at a fixed address (e.g. 0x00). Why?
 - There might be other programs or code in memory
- We have been assuming our code is always loaded at 0x00. If this assumption no longer holds, what breaks? Labels.

e.g.

	;address 	;address we had expected    	;assembly  			;machine code
	0x100 		0x00 				lis $1 				;0x00000814
	0x104 		0x04 				.word f 			;0x0000010C 
				***machine code needs to be offset by loader - actual address, not expected
	0x108 		0x08 				jalr $1 			;0x00200009
 	0x10C 		0x0C 				f: add $3, $1, $2 	;0x00221820
 	0x110 		0x10 				jr $31 				;0x03e00008

which instructions need to be fixed? The loader will somehow need to fix
- .word id ;need to add α (a constant) - id references an absolute address
- .word consatnt ; do NOT relocate

what about branches?
- no, because the assembler calculatees a relative offset based on PC, so relocation is unnecessary.
- everything else (including bne, beq) do not relocate

now we have OS 3.0

	repeat:
		p <- next program to run
		$3 <- load_and_relocate (P)
		jalr $3
		beq $0, $0, repeat

Problem (again) - this will not work
- Assembled file is a stream of bits - how do we know which came from a .word with an id and which are instructons? - We cannot. We need help. We need more info from the assembler.
- We note that the output of most assemblers is not pure machine code; it is known as 'object code'. An object file contains binary code, but in addition contains any auxiliary information about the file that will be needed. 
- We use object file formal MERL (MIPS Executable Relocatable Linkable)
 - Note that this is made up for CS241, so do not try to research it. 
- Inside an object file:
 - the code (in binary)
 - which lines (addreses) need to be relocated because they are .word id instructions
 - other stuff to be added later

###MERL

Example:

	*header - 3 words*
		0x10000002 - cookie for MERL - it's actually assembly for beq $0, $0, 2
		length of the merl file ;where the merl file stops
		code length = header+mips ;where table starts
	*code*
		MIPS binary (note the code is written to start at address 0x0C)
	*symbol table (relocation table)*
		format code ;always 1 - this is a relocaton value - we'll add more codes later
		address
		format code 
		address

- The headers has a cookie to let us know it is a MIPS file, and then the lengths of the whole fille and the code. 
- The format code is always 1 for the relocation entry, and the associated address is the address in the MIPS code of the relocatable word.
- Header is always of size 12. In our example on the slide, code is size 32 and symbol table is size 16.
- We note that 0x10000002 is MIPS for beq $0, $0, $2, ie a command to skip header, so that MERL files can be executed as ordinary MIPS programs (if loaded at 0x00)

We also want the assembler to generate relocatable object code (like we did by adding relocs in the example) which is cs241.relasm, which we will get eventually

Relocation Tool: cs241.merl
- input: merl file and a relocation address α
- output: non-relocatable mips file with merl header and footer removed, ready to load at address α

Loader Tools: mips.two-ints, mips.array
- both of these take an optional 2nd argument, which is the address to load the mips file

Example: Load myobj.merl at 0x10000

	java cs241.merl 0x10000 < myobj.merl > myobj.mips
	java mips.twoint myobj.mips 0x10000

we note relocation is typically done by the loader - we do it this way as a visualization of the process 

Loader Relocation Algorithm:

	read() //skip cookie, MERL check
	endMod <- read() // end of MERL file
	codeLen <- read() - 12 // lengthof code
	a <- findFreeRam (codeLen+stack)
	for (i=0; i < codeLen; i += 4) MEM[a+i] <-read() //load code into memory
	i <- codeLen+12 //position of footer
	// Perform Relocation
	while (i < endMod) 
		format <- read
		if (format == 1) //always 1 for now
			rel <- read()
			MEM[a+rel-12] += a-12 
			//adjust by α, back by 12 (no head - still offset from 0, but we do not include the header anymore
		else ERROR
		i += 8
	end


---
##Jan 29

What we came up with last class works in general but can still be broken.

	lis $2
	.word 12
	jr $2
	jr $31

If we jump to a consatnt address or do math on a label, we are in trouble. However, this is bad practice. 

This is a shift in the role of labels; when introduced they were a convenience, but now they are a necessity. Essentially, if you want relocatable code, use labels.

###Linkers

We often find it convenient to split MIPS programs into smaller units, for the same reason as with higher-level languages. There is a major issue. How can an assembler resolve a reference to a label in a different file?

We have a.asm, b.asm, and c.asm with shared labels.

Solution: to assemble, concatenate the files and then assemble:
- cat a.asm b.asm c.asm | java cs241.binasm > abc.mips
- This works, but it doesn't save us any work if I make a small change in a.asm but b.asm is huge.
- Also, I may only want to give someone the binary (not the source). We want to compile first then link after.

Solution 2: Can we assemble first and then cat 
- header + binary + reloc table
- No, they are all assembled to start at address 0, but the second two would need to relocatable. 
- Thus we need to assemble to MERL, not just MIPS. However, if we cat two MERL files, we don't get a MERL file. 

Solution: we need a tool smarter than cat. 
- It must be able to understand MERL files and put them together intelligently. 
- Such a tool is called a *linker*. 
- And yet what should an assembler do with references to lables it cannot find? 
- We will need to change the assembler.

Aside: what happens (conceptually) when I run g++ asm.cc kind.cc lexer.cc
- asm.o kind.o lexer.o --> a.out (assembler out!)
- Compiler generates asm.o, kind.o, lexer.o 
- .o extention used to represent object files
- linker combines the .o files into an executable

####Import and Export

We lose a valuable error-check
- If a label is not defined, then our old assembler assumes an error
- but now we just expect to find it within a linked-in file
- How can an assembler tell between errors and intentional behaviour?

The new assembler directive:
- .import id: tells the assembler to ask for id to be linked in. This does not assemble to a word MIPS
- if we have a label abc, and that label does not occur, and no .import abc, then we have an error

Format code 0x11 means External Symbol Reference (ESR)
- we need the name of the symbol, the address of use (where the blank needs to be filled in)

ESR entry contains
- word 1: 0x11
- word 2: location of where symbol is used
- word 3: length of symbol in chars (n)
- word 4->3+n: ASCII chars in the symbol name (one word per char)

example use:

	.import abc
	lis $1
	.word abc

example ESR entry:

	0x11
	0x10 ;location of abc = 0x4 + 0xc
	0x03 ;length of name
	0x61 ; 'a' in hex
	0x62 ; 'b'
	0x63 ; 'c'

Another problem:

	one.asm      		two.asm         		three.asm
	.import abc 		abc:            		...
	lis $1 	    		sw $3, -4($30) 			abc:	
	.word abc   		...             		add $3, $3, $2
	            		jr $31          		...
	            		;abc is a  procedure	beq $2, $0, abc ; this abc is a loop	

How do we know which vsion of abc to use? We can't assume labels won't be duplicated

Another directive:
- .export id    
- makes id available for linking - this does not assemble to a word of MIPS
- tells the assembler to make an entry in the MERL table

MERL entry tpe: External Symbol Defintion (ESD)

ESD entry contains:
- word 1: 0x05
- word 2: address the symbol represents
- word 3: length of symbol name (n)
- word 4-3+n: ASCII name

example ESD Entry:

	0x05 ;format code
	0xc ;address of abc
	0x3 ;length of abc
	0x61
	0x62
	0x63

MERL now contains code, relocator addresses, addresses and names of ESR and EDR entries

-----
##Feb 3

####Last time
- Note that .word instructions referring to external symbols must also be relocated. 
- Augment MERL files to help linker. 
- MERL: Header + Code + Relocation and External Symbol Table
- Symbol table contains relocatable addresses, adresses and names of ESR (imports) and ESD (exports)

####The linker
Given two MERl files m1.merl, m2.merl - how do we link them? What are the steps?

1) relocate m2.code

	a <- m1.codelen-12   
	// we want to subtract the header from m2 and then push forward by length of m1 code (a)
	relocate m2.code by a
	add a to every address in m2.symboltable

2) resolve symbols (and check for duplicates in exports - ERROR)

	if (m1.exports.labels AND m2.exports.labels) != 0 then ERRROR
	for all <addr1, label> in m1.imports do
		if exists <addr2, label> in m2.exports then
			m1.code[addr1] <- addr2
			remove <addr1, label> from m1.imports
			add addr1 to m1.relocates
	for all <addr2, label> ... same

3) merge symbol tables

	imports = m1.imports and m2.imports
	exports = m1.exports and m2.exports
	relocates = m1.relocates and m2.relocates

4) output linked program (with new header)

	output MERL cookie
	output totalCodeLen + total(imports, exports, relocates) + 12
	output totalCodeLen + 12
	output m1.code
	output m2.code
	output imports, exports, relocates

###Compiler

Assembly language has a simple structure, is easy to parse, straightforward and unambiguous.

Now we turn our attention to the compiler. Source program -> [compiler] -> assembly code (equivalent meaning)

First step of our compiler: source program->[scanner/lexical analysis]->tokens

A high-level language is a more complex structure. It is harder to recognize, and we have no single translation to machine code. To convert to machine language we use a compiler.

How does a compiler recognize if a given program is valid? Note we're not asking if it's logically correct (a much harder problem), but just whether the sequence of characters forms and allowed program based on the specifications of the language.

Eventually, once the compiler can recognize whether a program is valid, how do we translate the program into an equivalent program in the target language? (usually lower-level).

Is this a valid c++ program?

	int main() {
		int a = 1;
		int b = 2;
		int c = 0;
		c = a+++b;
		cout << c << " " << a << " " << b << endl;
	}
	output: 3 2 2

yes - a++ + b

what about the same program with a+++++b (5 +s)
- no! it will be parsed as a++ ++ b which is invalid

what about a++ +++b 
- nope, same interpretation as above

what's the fix?
- a++ + ++b

How can we handle this complexity?
- We want a formal theory of string recognition - general principles that work in the context of any programming language.

###Definitions:
- alphabet: finite set of symbols (e.g. {a,b,c}) typically denoted Σ = {a,b,c,}
- String(word): finite sequence of symbols from Σ. a, abc, cbca, etc.
- Length: |w| = # of symbols in w
- Empty string: ε is the empty string, not a symbol. |ε| = 0
- Language: a set of strings (e.g. {a^(2n)b | n >= 0}, b, aab, aaaab ....)

Notes: 
- ε is the empty word, while {} = Ø is the empty language.
- {ε} is a singleton language containing only the empty word

###Language Complexities
- {a^(2n)b | n >= 0} - this is easy
- {valid MIPS assembly programs} - harder
- {valid Java programs} - way harder
- some languages (all programs without bugs) - impossible

How can we automatically recognize whether a given string belongs to a given language? This is mindblowing, and depends on how complex the language is. Since the answer is dependent on language complexity, we will charactertize languages according to how hard recognition process is; we will create classes of languages based on the difficulty of recognition. This is based on Chomsky Hierarchy

####Chomsky Hierarchy
- Finite: easy
- Regular: not so easy
- Context-free: pretty decent
- Context-sensitive: challenging
- Recursive: difficult
- anything-else: impossible

We want to study high-level languages at as easy a class level as possible, and move down when we have to.

###Finite Languages
- These languages have finitely many words.
- Write code to answer w ∈ L such that w is scanned exactly once, without storing previously seen characters.
- We can recognize a word by comparing each word in the finite set of words - but we won't use that approach.

Exercise: L = {cat, cat, cow}

	if firstchar != c, reject
	if secondchar = a
		if thirdchar=r
			if input not empty, reject
			else accept
		else if thirdcharacter = t
			if input not empty, reject
			else accept
	if secondchar = o
		if thirdchar = w 
			if input not empty, reject
			else accept
	else reject

----
##Feb 5

####Last time
- started compiler
- formal languages
- chomsky hierarchy
 - finite  
 - regular  
 - context-free 
 - context sensitive  
 - recursive

very losely, recursive means that there is an algorithm or program to determine whether any given input is a member of the language

e.g. from last time L = {car, cat, cow} 

An abstraction of this program:

![cat](/cs241/Feb5-cat.jpg)

Circles are states - configurations of the program based on input seen. 
- The double circles are accepting if the program halts there.

Example - mips operators:

![mips](/cs241/Feb5-mips.jpg)

Since programming langagues don't usually admit only a finite many programs, finite languages are not much use

###Regular languages

These languages are built from finite languages, but also support the following operations:

union
- L1 U L2 = {x | x in L1 or x in L2}
- e.g. L1 = {dog}, L2={cat} L1 U L2 = {dog, cat}

concatenation
- L1·L2 = {xy | x in L1, y in L2}
- e.g. L1 = {dog, cat} L2={fish, emptystring} ---> then L1·L2 = {dogtfish, catfish, dog, cat}
- Repetition 
- L^\* = union i=0...infinity L^i = L^0 U L^1 U L^2 ... = {emptystring} U L U LL U LLL ...
- In other words, it is 0 or more occurrences of L
- e.g. L={a,b} ---> L^\*  = {emptystring, a, b, aa, ab, ba, bb, aaa, aab, aba, abb, etc.}

Example: show that {a^2n b | n >= 0} is regular
* We see that ({aa})^\* · {b} defines this set.

This syntax is very tedious. We have **Regular Expressions**

####Regular Expressions

        Expression 			Language
        --------------------------------------------
        null set			{} - empty language
        ɛ				{ɛ} - language consisting of the empty string
        aaa 				{aaa} - singleton language
        E1|E2				L1 U L2 - alternation (union)
        E1 E2				L1·L2 - concatenation
        E^*      			L^* - repetition

Question - is the language C regular?
- We have IDs [a-zA-Z]([a-zA-Z0-9_])^\*
- A C program is a sequence of tokens, each of which come from a regular language
- How can we define which sequences of tokens are valid? We don't know yet. 
- Thus, our answer is maybe

Can we recognize arbitrary languges automatically?

Can we harness what we learned about recognizing finite languages? LOOPS!

Consider our example {a^(2n)b | n >=0}

![ ](/cs241/Feb5-ab.jpg)

e.g. MIPS labels
![ ](/cs241/Feb5-mipslabels.jpg)

These 'machines' are called Deterministic Finite Automata (DFAs)
- Always a start state
- For each character in the input follow the corresponding arc to the next state
- If in an accepting state when input is exhausted, accept - else reject

What is missing?
- What if there is no transition? Consider the ab example - what if our input is ab? If we fall off the machine, reject. More formally, an implicit error state exists; all unlabelled transitions go there. An error state has an all input loop back to itself and is non-accepting

Example: String over {a,b} with an even number of a's and an odd number of b's
![ ](/cs241/Feb5-example1.jpg)

Formal definition of  DFA: a DFA is a 5-tuple (Σ, Q, q0, A, 𝛿), where
- Σ is a finite non empty set (alphabet)
- Q is a finite, non-empty set (states)
- q0 is an element of Q (start state)
- A is a subset of Q (accepting states / end states)

𝛿(QxΣ) -> Q (Transition function state and input symbol and gives next state)
- 𝛿 consumes a single character of input 
- we can extend 𝛿 to a function that consumes an entire word

define:
- 𝛿\* (q, empty string) = q
- 𝛿\* (q, cw) = 𝛿\* (𝛿(q,c), w)

Thus, a DFA, M=(Σ, Q, q0, A, 𝛿) accepts a word w if 𝛿\* (q0, w) is in A

example from before 
- Σ = {a,b}
- Q = {ee, oe, eo, oo}
- q0 = ee
- A = {eo}

        𝛿      ee  oe  eo oo
        -------------------------
        a          oe  ee  oo  eo 
        b          eo  oo  ee  oe

----
##Feb 10

Last time: Regular Expressions

Handy to know precedence (highest to lowest)
- (x)
- x\*
- xy
- x|y

Example: a|bc\*  is (a)|(b(c\*))

A DFA, M=(Σ, Q, q0, A, 𝛿) accepts a word w if 𝛿\* (q0, w) is in A

If M is a DFA, we denote L(M) ("the language of M"), the set of all words accepted by M: L(M) = {w | M accepts w}

Theorem (Kleene)
- L is regular iff L=L(M) for some DFAM.In other words, the regular languages are accepted by DFAs.
- Proof: we will prove this later.

####Implementing a DFA

	int state=qo
	char ch
	while not EOF do
		read ch
		switch state
			qo: case ch of
				a: state<-
				b: sate<-
			q1: switch ch
				a: state<-
				....
			q2 ...
			end case
	end while
	return (state is in A)

Unfortunatley this is very tedious. Instead, we could use a lookup table (see provided assembler starter code) of states in columns and characters in rows - where they intersect is next state

Currently, our DFA takes an input string and returns yes/no whether the given string is in the language. However, it is possible to  add an output facility to a DFA, resulting in a transducer.

DFAs with actions can attach computation to the arcs of a DFA. For example, consider L = {binary numbers with no leading zeros}, where we compute the value of the number.

![ ](/cs241/Feb10-binary.jpg)

(sorry for horrible quality)

another possible action would be "emit a token" - here we emit the current value of our number

####Non-Deterministic Finite Automata
What do we gain by making our DFAs more complex?
e.g. L={w in {a,b}\*|w ends in abb}

![ ](/cs241/Feb10-complexDFA.jpg)

What if we allowed more than one arc (edge) with the same label from the same state?
- The machine choses one (this is non-deterministic)
- We can think of it as magic, that it knows where it goes - or we can think of it as trying every path and see if there is any path that ends in an accepting path, then it accepts
- We will accept if some set of choices leads to an accepting state. Returning to our example:

->[start]-a->[]-b->[]-b->([])   start loops back to itself with a and b

The machine guesses to stay in the first state until it reaches the final abb, then transitions to accepting 

NFAs are often simpler than DFAs.

Formally NFA is a 5-tuple (Σ, Q, q0, A, 𝛿)
- Σ, Q are finite non-empty sets (alphabet, states)
- q0 (start) and A subset of Q (accepting)
- 𝛿: relation - (QxΣ) --> subsets of the powerset of Q (2^Q)
 - This is the powerset of Q, which makes it non-deterministic. The powerset is the set of all subsets of Q (includes the empty set and Q)

e.g. from before
Q = {a,b,c}
2^Q = Powerset of {a,b,c} = {{}, {a}, {b}, {c}, {a,b}, {a,c}, {b,c}, {a,b,c}

We want to accept if some path through the NFA lead to an accepting state, and reject if no such path exists.

𝛿\* for NFAs sets of states x Σ -> sets of states
- 𝛿\*(P, empty) = P, where P is a set of states
- 𝛿\*(P, cw) = 𝛿\*(union of 𝛿(q,c) for all q in P , w)

And we accept if 𝛿\*({q0},w) union A != 0

##NFA Simulation Procedure

	states <- {q0}
	while no EOF do
		ch<-read()
		states<-union 𝛿(q,c) for all q in states
	end while
	if (states union A) is not empty, accept
	else reject

work with the following NFA, we will simulate baabb

![ ](/cs241/Feb10-example1.jpg)

    Read Input	    Unread input     	States 		Name
    empty           baabb				{1} 		A
    b               aabb 				{1} 		A
    ba 		        abb 				{1,2} 		B
    baa 			bb 					{1,2} 		B
    baab 			b 					{1,3} 		C
    baabb 			empty 				{1,4}  		D

Thus we accept. 

BUT WAIT. I am adding the right column to the table above as we speak. If we give each set of states a name, and call those states, every NFA becomes a DFA. For example:

    𝛿(C, a) -> {1,2} = B
    𝛿(D,a) -> {1,2} = B
    𝛿(D, b) -> {1} 

note if we draw this it looks just like our DFA from before!

Holy cow, we have a DFA because D contains an accepting state

---
##Feb 12

Last time
- NFAs

Consider L={cab} U {strings over {a,b,c} with an even # of a's}. We can draw an NFA

![ ](/cs241/Feb12-example1NFA.jpg)

The DFA is much harder to produce. Let's do a trace

    Read 		Unread 		States
    --------------------------------
    empty 		caba		{1}
    c 		    aba 		{2,6}
    ca  		ba  		{3,5}
    cab  		a 		    {4,5}
    caba 	 	empty 		{6}

Now we build the DFA via the *subset construction*
![ ](/cs241/Feb12-example1DFA.jpg)

Accepting state are any states that includes an accepting state from the original NFA.
Every NFA hs an equivilent DFA, and NFAs recognize the same class of languages.

###ɛ-NFA

What if we let ourselves change state without consuming a character? We call these ɛ transitions. (we label the arrow as ɛ)
- This is a free pass to a new state without reading a character.
- This makes it easy to glue smaller automa together.

Revisiting the above example, 
![ ](/cs241/Feb12-example2.jpg)


    Read 		Unread 		States
    --------------------------------
    ɛ 	caba 		{1,2,6}
    c 			aba 		{3,6}
    ca  		ba 			{4,7}
    cab 		a 			{5,7}
    caba 		ɛ 	{6}

By the same renaming trick as before (ie the subset construction) every ɛ-NFA has an equivilent DFA. Thus, ɛ-NFAs and DFAs recognize the same class of languages.

Yes, DFAs and NFAs are finite state machines and the class of languages accepted by FSM are regular languages.

###Proof of Kleene's theorem (one way)
- L is regular if L = L(M) for some DFA M. 
- If we can find an ɛ-NFA for every regular expression, then we have proved one direction of Kleene's theorem.

The below pictures show empty languages, ɛ languages, single caracter, alternation, concatenation, repetiton:

![ ](/cs241/Feb12-rules1.jpg)

![ ](/cs241/Feb12-rules2.jpg)

Thus every regular language has an equivalent ɛ NFA, which has an equivalent DFA and the conversion can be automated

###Scanning

Is C a regular language?
- C keywords, identifiers, literals, operators, comments, punctuation

These are all regular languages, and sequences of these are also regular, so we can use finite automata to do tokenization (scanning, lexical analysis)

Ordinary DFA can only tell us if a word is in a language. We need something that takes an input string w, breaking w into w1,w2,...,wn such that each wi is in L and then output each wi

Consider L={valid C tokens} is regular.

Let M\_L be the DFA that recoginizes L. Then the second representation M\_{LL\*} which is a nonempty sequence of tokens is NFA modified from the NFA for M\_L - using the rules from above for * 
![ ](/cs241/Feb12-tokenizing.jpg)

We can add an action to each each ɛ-move such as output a token. Our machine is non-deterministic. ɛ-moves are optional

The question: does the current setup guarantee a unique decomposition w = w1 w2 ... 2n? The answer is no.

Consider just the portion of the machine that does IDs
- ->()--a-zA-Z-->[()]--a-zA-Z0-9-->(back to ending state)
- ɛ /output token from ending state back to start state
- The input abab could be interpreted as 1,2,3 or 4-tokens. 

How can we fix this? We could always return the longest possible next token. However, this can still fail.
- Consider L={aa,aaa}, w=aaaa  
- If we take the longest possible token, we take aaa first and then we are stuck. 
- However, we could have tokenized the string successfully as aa and aa. 
- Oh well. It would be bad to design a programming language like this. 
- But wait... many of the languages we use are designed like this. 

Remember the example when we started.
- c=a+++b -> a++ + b   NOT a + ++b
- Again from out example, c=a+++++b; -> a++ ++ +b which is invalid
- But, a++ + ++b would be valid - but that's not what the compiler does.

Another example from c++:
- vector<vector<int>> v;
- c++ treats the >> like the bitshift operator instead of two closing angle brackets. We have to trick the scanner:
 - vector < vector <int> > v;
- Fixed in  c++11

###Maximal Munch Algorithim

    Run DFA (no ɛ-move) until nonerror move possible.
    if in accepting state:
    	token found
    else 
    	back up to most recent accepting state
    	(track this with a variable)
    	input up to that point is next token
    endif
    output token
    ɛ-move back to q0

Simplified Maximal Munch: as above, but if we are not in an accepting state, when no transition possible, then we output an error (don't backtrack)

Example:
- Our identifier must start and end with a letter and may contain a single -
- We also accept operator -
- For input ab-   
 - we find a valid start state, then b, and then -, but we cannot scan past this point since no further action is possible
 - Thus we would go back ab since it was the last valid point, and then continue from there. 
 - THe SMM algorithm would simply find an error but in practice this is usually sufficient

----
##Feb 24

####Last time:

- Note that if we have L  {a^2n | n>=0} we show it's regular by describing it with a regular expression - using only operations in regular expressions that we talked about - here it's (aa)*
- Scanning algorithm - Maximal Munch vs Simplified Maximal Munch
 - difference? - no backtracking



Now consider Σ  = {( , )} (alphabet of brackets)

- We have L = {w in Σ\* | w is a string of balanced parentheses}
- Can we build a DFA for L?
- Each new state lets us recognize one more level or nesting, but no finite number of states recognizes all levels of nesting, and a DFA must have a finite number of states

tokens => [Parsing/syntactic analysis] => parse tree

what is required?
- Given a formal syntax specication, the parser verfiies whether the incoming tokens have a valid syntax confirming to the specifications and outputs a parse tree to allow semantic analysis in the following step.
- This cannot be done with regular languages, so we move to the next class in the Chomsky Hierarchy

###Context-Free Languages

These are languages which can be described by a context-free grammar; a set of "rewrite rules". 

Following the balanced paranthesis example:
- empty: S->ɛ
- a word in the language surrounded by (): S->(S)
- the concatenation of two words in the language: S->SS

The shorthand for this is S->ɛ|(S)|SS

Example: show how this system generates (())():
- S => SS => (S)S => ((S))S => (())S => (())(S) => (())()

**The notaton we have used:**
- => means "derives"
- a->β means β can be dervied from a by one application of a grammar rule

we formally define a context-free language to consist of:
- an alphabet of terminal symbols
- a finite non-empty set N of of non-terminal symbols, such that N union Σ = null set
 - we often use V (vocabulary) to denote N U Σ
- a finite set of P productions, where productions have the form A->β, A in N, β in V*
- an element S in N as the start symbol

In our parentheses example, we have that terminal symbols are {(,)}, non-terminal symbols are {S}, and S is our start symbol

Conventions
- a, b, c,... are elements of Σ (characters, terminals)
- w, x, y,... are elements of Σ* (words)
- A, B, C, ..., S, ... are elements of N (non-terminals)
- S is the start symbol
- α, β, γ, ... indicate elements of V*

We write αAβ => αγβ if there is a production A->γ in P

α=>β means that α=>delata1=>𝛿1=>...=>𝛿n=>β for n>=0

Definition: L(G) = {w in Σ\* | S =>\*  w}, where L(G) is the langauge specified by G

Note that this is a set of strings of terminals only. A language L is context-free if L=L(G), for some context-free grammar G

Example: Palindromes over {a,b,c}
- S->ɛ|a|b|c|aSa|bSb|cSc
- We can also define this as 
 - S->aSa|bSb|cSc|M
 - M->ɛ|a|b|c
- Show that  S=>abcba
 - S => aSa => abSba => abMba => abcba
 - ^ This proccess is called a derivation

Example 1: sigma = {a,b,c,+,-,x,/} and L1 = {arithmetic expression from sigma}
- S -> S op S|a|b|c
- Op->+|-|/
- But we could have speciiced this with a regular expression

Example 2: sigma2={a,b,c,+,-,/,(,)} where L2 = {arithmetic expressions over sigma2 with balanced parthentheses}
- S->a|b|c|S op S|(S)
- Op->+|-|x|/
- Show that S =>* a+b
 - S => S op S => a Op S => a + S => a + b     
 - alternatively: S => S op S => S op b => S + b => a + b
- Note that we have a choice over which symbmol to expand first

Leftmost Derivation: always expand leftmost symbol first

Rightmost Derivation: always expand rightmost symbol first

We are showing too much information, since this information doesn't matter and this can get very long. We can express them naturally and succinctly as a tree stucture.

Example: consider abcba

![ ](/cs241/Feb24-abcba.jpg)

For the leftmost derivation, there is a unique corresponding parse tree and vice versa.

----
##Feb 26

Midterm - we will provide
- MIPS reference sheet
- MERL file format spec
- ASCII chart

####Last Time

- CFL (context free languages)/CFGs
- In a derivation, one non-terminal in the current string is selected and replaced or rewritten using RHS of a production rule for that non-terminal

e.g. production rules

	S->a|b|c|S op S|(S)
	Op->+|-|x|/

What is the RE for this langugae?
- (a|b|c)((+|-|\*|/)(a|b|c))\*

Leftmost derivation for a+bxc: S => S Op S => a Op S => a + S => a + S Op S => a + b Op S => a + b x S => a + b x c

![ ](/cs241/Feb26-leftmost.jpg)

Or, expand the first S first: S => S Op S => S Op S Op S => a Op S Op S => a + S Op S => a + b Op S => a + b x S => a + b x c

![ ](/cs241/Feb26-rightmost.jpg)

These correspond to DIFFERENT parse trees. A grammar for which the same word has more than one distinct parse tree is called **ambiguous**. The grammar we defined above is ambiguous. If we only care about whether a string is in the language, the ambiguity doesn't matter.

As compiler writers we want to know why a word is in a language, so the derivation matters. Why does it matter? The shape of the parse tree describes the meaning of the word, so a word with an ambiguous parse may have multiple possible meanings.

In the first tree, bxc is grouped more tightly, and in the second a+b is grouped. So it could mean a+(bxc) or (a+b)xc

##END MIDTERM CONTENT

What do we do about this?
- use heuristics ("precedence") to guide the derivation proccess
- Make the grammar unambiguous

unambiguous grammar:
	E -> E Op T | T
	T -> a|b|c|(E)
	Op -> +|-|*|/

Then we have a strict left to right precedence

	E => E Op T => E Op T Op T => T Op T Op T

![ ](/cs241/Feb26-lefttoright.jpg)

new producton rules:

	E -> E PM T | T
	T -> T TD F | F
	F -> a|b|c|(E)
	PM -> +|-
	TD -> x|/

new derivation:
(NOTE: use single arrows for production rules and double arrows for derivation)

	E => E PM T 
	  => T PM T 
	  => F PM T 
	  => a PM T 
	  => a + T 
	  => a + T TD F 
	  => a + F TD F 
	  => a + b TD F 
	  => a + b TD F
	  => a + b x F
	  => a + b x c

![ ](/cs241/Feb26-tree.jpg)

If L is context-free, is there always an unambiguous grammar?
- L={a^i b^j c^k | i=j or j=k}
- No! There are ineheritently ambiguous languages that only have ambiguous grammars.
- Can we construct a tool to tell us if a grammar is ambiguous? No! This is undecidable. There is provably no algorithm to solve this problem. The equivalence of grammars is also undecidable.

We need to build a recognizer - what class of programs recognize CFLs?
- regular languages are represented by DFAs, a program with finite memory
- Context-free languages must recognize infinite memory, they are represented by NFA with a stack
 
But we need more than a yes/no answer. We don't want to have a program and the compiler just say yes or no

We can use grammars to specify the syntax of a language. For example, a while loop
 	
 	statement -> WHILE LPAREN test RPAREN LBRACE statements RBRACE

 Parse trees allow us to understand the program. We need to know the derivation (parse tree) and error diagnosis. The problem of finding the derivation is called parsing. How do we use grammar to go from source program to a parse tree?

###Parsing

We have two choices:
- forwards - "top down" - start at S, work to w

![ ](/cs241/Feb26-forwards.jpg)

- backwards - "bottom up" - start at w, work to S
 
![ ](/cs241/Feb26-backwards.jpg)

####Top-Down Parsing

Example:

Rules

	S->AyB
	A->ab
	A->cd
	B->z
	B->wx

How can we derive S=>abywx?
We want an algorithm to generate the derivation

Consider a leftmost derivation:

	S=>AyB=>abyB=>......

What are we doing here? Match input symbols startng from left until you encounter a non-terminal. Replace non-terminal with RHS of a rule and continue matching more formally.

	S=>α=>α1=>α2=>...=>αn=>w

Use the stack to store α's in reverse and match against characters in input.

**Invariant**: the consumed input plus the reverse of the stack contents is equal to α_i

For simplicity, we will use **augmented grammars** for parsing. We invent two new symbols BOF and EOF and a new start symbol S'

1) S' -> BOF S EOF
2) S -> AyB
3) A -> ab
4) A -> cd
5) B -> z
6) B -> wx

say w = BOF a b y w x EOF

		Stack  			Read Input 			Unread Input 			Action
		-----------------------------------------------------------------------
		S'  			ɛ 				BOF a b y w x EOF   	Pop S', push EOF S BOF
		EOF S BOF 		ɛ 				BOF a b y w x EOF 		match BOF (first in unread input)
		EOF S 			BOF 				a b y w x EOF 			Pop S, push ByA
		EOF B y A 		BOF 				a b y w x EOF 			Pop A, Push ba
		EOF B y b a 	BOF 				a b y w x EOF 			match a
		EOF B y b       BOF a 				b y w x EOF 			match b
		EOF B y 		BOF a b 			y w x EOF 				match y
		EOF B 			BOF a b y 			w x EOF 				pop B, push xw
		EOF x w   		BOF a b y 			w x EOF 				match w
		EOF x 			BOF a b y w 		x EOF 					match x
		EOF 			BOF a b y w x   	EOF 					match EOF
		ɛ 			BOF a b y w x EOF   	epsilon					accept

----
##March 3 


Let's codify this
- When the top of othe stack is a terminal pop and matched against the input
- when TOS (top of stack) is a non-terminal A, pop A and push reverse (alpha), where A->alpha is a grammar rule
- accept when stack and input are empty
- brute force: try all combinations until one works - this is inefficient
- we want a Deterministc Procedure with no backtracking
- our solution is to use the next symbol of input (lookahead) to help decide

Construct a **predictor table** - given a non-terminal on the stack and an input symbol, tell us which production to use. Empty cells are errors.

	     BOF  a  b  c  d  w  x  y  z  EOF
	-----------------------------------------
	S'    1
	S         2     2
	A         3     4
	B                     6         5

The rest of the table can be used for descriptive error mesesages: "Parse error at row, col: expecting one of (symbols where the predictor table does have entries)"

What if a cell contains more than one rule? THe method breaks down

A grammar is called LL(1)
- L: left to right scan of input
- leftmost derivations produced
- 1: 1 symbol of lookahead

(top down parsing - starting with s ending at w - recall that triangle diagram)

We can automatically compute the predictor table
- Predict(A,a) -> rules that apply when A is on the setack and a is the next input character
 - Predict(A,a) = {A->B | a is elem of First(beta)}
- First(beta), beta in V* is the set of characters thata can be the first letter of a derivation starting from beta
 - First(beta) = {a|beta =>* a gamma}
 - For example: First(AyB) = {a,c}
- So Predict(A,a) = {A->beta | beta =>* a gamma}

However, this is not quite right. What if A=>*epsilon?
- Then a might not come from A, but from something AFTER A.
- we missed something - what if there is more than one production with the same LHS? How do we know which one to pick?
- So, Predict(A,a) = {A->beta | a in First(beta)} U {A->beta | Nullable(beta), a in Follow(A)}
- Nullable(beta) = true if B=>*epsilon, false otherwise
- Follow(A) = {b | S' =>* alpha A b beta}: the set of terminal symbmols that can come immediatly after A in a derivation of S'. 
 - In our example: Follow(A) = {y}

Computing Nullable -  Nullable(Beta) = true if beta =>* epsilon

	initialize Nullable[A] <- false for all A // an array
	repeat
		for all rule A->B1...Bk do
			if k = 0 or Nullable[B1] = Nullable[B2] = ... = Nullable[Bk] = true then
				Nullable[A]<-true
	until nothing changes

First(beta) = {a|beta =>* a gamma}

	initialize First[A] <- {} for all A //an array
	for all rule A -> B1...Bk do
		for i in 1...k do 
			if Bi in sum terminal then
				First[A] <- FIrst[A] U Bi
				break
			else (Bi in N (non terminal))
				First[A] <- First[A] U First[Bi]
				if not Nullable[Bi] then
					break

First*(B)

	First*(Y1..Yn)
		result<-{}
		for i in 1...n do 
			if Yi in sigma then
				result <- result U Yi
				break
			else (Yi in N)
				result <- result U first[Yi]
				if not Nullable[Yi] then
					break
		return result

Follow(A)

	initialize Follow[A] <- {} for all A != S'
	repeat
		for all rule A->B1...Bn do
			for i in 1..n do
				if Bi in N then
					Follow[Bi]<-Follow[Bi] U First*(Bi+1 ... Bn)
				if all of Bi+1...Bn are Nullable then
					Follow[Bi]<-



					THESE ARE BEING POSTED :C


Sample Trace:

	1) S' -> BOF S EOF
	2) S -> bSd
	3) S -> pSq
	4) S -> C
	5) C -> lC
	6) C -> epsilon

Nullable

	0 - initialize array
	1 - there's a rule for C to null
	2 - there's a rule for S to C (which is null)
	3 - no change, so done

	iterator   0   1   2   3
	--------------------------
	S'         F   F   F   F 
	S 		   F   F   T   T
	C          F   T   T   T

First

	iteration   0     1      2       3-same as 2
	----------------------------------------------
	S'         {}   {BOF}    {BOF}
	S          {}   {b,p}    {b,p,l}
	C          {}   {l}      {l}

Follow
   iter  0    1                2
   S    {}   {BOF, d, q}    {BOF, d, q}
   C    {}   {BOF, d, q}    {BOF, d, q}

------
##March 5

###Last time
- LL(1) Parsing


	1) S' -> BOF S EOF
	2) S -> bSd
	3) S -> pSq
	4) S -> C
	5) C -> lC
	6) C -> epsilon

Nullable:  S' false    S true	L true

First: S' {BOF}    S {b,p,l}    L {l}

Follow: S {BOF, d, q}  L {BOF, d, q}

Now since Predict(A,a) = {A->beta | a in First(beta)} U {A->beta | Nullable(beta), a in Follow(A)}

	    BOF   EOF   b   d   p   q   l
	S'   1                             <-- rule numbers
	S          4    2   4   3   4   4 
	C          6        6       6   5

This makes a very good exam question

A grammar is LL(1) if
- no two distinct productions with the same LHS can generate the first terminal symbol (the first set in the union for predict is at most 1)
- there is only one way to send a nullable symbol to epsilon (the second set in the union for predict must be at most 1)
- no nullable symbol has the same terminal in both its first set and its follow set (only one of the sets has size 1)

Weakness: LL must predict which production to use based on first (k) tokens of RHS

###Bottom-up parsing

We want w to S. THe stack stores partially reduced input: s <= alpha_k <= ... <= S

Our invariant: the stack and unread input are equal to alpha_i

[some slides that will be posted]

We have two choices at each step
- shift a character from input to top of stack
- reduce - top of stack is RHS of a grammar rule, replace with the LHS
- accept if the stack contains S' and input epsilon (equivlalently, if the stack is BOF S EOF on empty input) and equivalently shifting END of is good enough too

How do we know whether to shift or reduce? We know
- the next character of input
- this is is a very hard problem

Theorem (Donald Knuth, 1965): Let w be the stack and a be the next input character. The set {wa | there exists x . S' =>* wax} is a regular set. This can be described by a DFA, and we can use the DFA to make shift/reduce decisions.

The resulting method is LR parsing left-to-right scanning.
- rightmost derivation (remember triangle pic)

LR is more powerful than LL because it chooses the production after tokens for the entire RHS have been seen (plus any lookahead)

Example:

	S' -> BOF E EOF
	E -> E + T
	E -> T
	T -> id

Definition: An **item** is a production with a dot (·) somewhere on the RHS, indicating a partially completed rule.

Label an arc with the symbol that follows the dot. Advance the dot in the next state. If the dot preceeds a non-terminal A, add all the productions with A on the LHS to the state (dot in the leftmost position

[more slides]

####Using the Automaton

Start in the start state with an empty stack
- shifting
 1. shift a character from input to the stack
 2. follow the transition labeled with that chracter in themachine, to the next state (if there is one)
 3. if none - error or reduce

Reducing
1. Reduce states have only one item and the dot is rightmost
2. Reduce by the rule in the stack - pop RHS off the stack, backtrack size(RHS) states in DFA, psush LHS onto stack and then follow the transition for LHS
- accept when you shift EOF
- backtracking in a DFA is accomplished by remmebering the previous DFA states. We can push the DFA states onto the setack as well. We can use 2 stack or comine into one stack.

Example: BOF id + id + id EOF

Stack 					Read 			Unread					Action
1 						epsilon			BOF id + id + id EOF 	Shift BOF, goto 2
1 BOF 2 				BOF 			id + id + id EOF 		shift 6
1 BOF 2 id 6  			BOF id 			+ id + id EOF 			R T->id (Now in state 2) 
1 BOF 2 T S				BOF id 			+ id + id EOF			R E->T
1 BOF 2 E 3 			BOF id 			+ id + id EOF 			S 7
1 BOF 2 E 3 + 7  		BOF id + 		id + id EOF 			S 6
1 BOF 2 E3 + 7 id 6 	BOF id + id 	+ id EOF                R T->id
1 BOF 2 E 3 + 7 T 8     BOF id + id     + id EOF    			R E->E+T
1 BOF 2 E + 7

...etc (I think it's in Brad's notes I have from somewhere)

Note this is a DFA (from NFA)

What can go wrong? 

What if a state looks like:	

	A -> alpha dot c beta
	B -> gamma dot

Do we shift c (as suggested by reule 1) or reduce by second rule?

This is a shift-reduce conflict
Also what about

	A->alpha dot
	B-> beta dot

Which rule do we reduce? This is a reduce-reduce conflict	

If any item

	S' -> BOF E EOF
	E -> T + E 
	E -> T
	T -> id

For each A->alpha dot, attach Follow(A) 
Follow(E) = {BOF}
Follow(T) = {+, EOF}


-----
##March 10


Reduce-shift conflict
- A -> alpha dot c beta
- beta -> alpha dot
- do we shift c or reduce second rule?

Reduce-reduce conflict
- A -> alpha dot
- B -> beta dot

If any item which has a dot at the end occurs in a state which it is not alone, then there is a conflict and the grammar is not LR(0).

	S' -> BOF E EOF
	E -> T + E
	E -> T
	T -> id 

	E -> dot T + E
	E -> dot T

Right associative expressions (slides)

We see a shift reduct conflict in state 5. For example, if the input starts with BOF id, we go from state 1 to 2 to 6 to 5. Then should we reduce E->T? IT DEPENDS. If the input is BOF id EOF then YES. If the input is BOF id + ... EOF then NO. Add lookahead to the automaton to resolve the conflict.

For each A -> alpha, attach Follow(A): 
Follow(E) = {EOF}, Follow(T) = {+, EOF}

THe interpretation: A reduce action applies only if the next character is in the follow set of the non-terminal. Thus, the first rule only applies if the next char is EOF, and the second only applies if the next char is + (for state 5). Conflict resolved.

The result is called an SLR(1) parser (simple left-to-right, rightmost derivation, 1 character lookahead).
- SLR resolves many (but not all) conflicts. LR(1) is more sophisticated than SLR(1), parsing more grammars.

recall that theorem from Knuth from before
- on the translation of languages from left to right: Information and Control, 8 607-639 (1965)

More resources:
- Introduction to the Theory of Computation, Sipser
- Introduction to Automata Theory, Languages, and Computation - Hopcroft and Ullman

###back to LR1

Example: This language is not LL(1)

	E -> E + T | T
	T -> T * F | F
	F -> a | b | c

How do we know this right away? 

This is because we have left recursion (ie recursion on the left). This is because we with E on the stack and a on the input, we can do two different Things. Note that First(E) and First(T) both contain a.

Let's try right recursion:

	E -> T + E | T
	T -> F * T | F
	F -> a | b | c

But this is still not LL(1), since T, T+E generate the same first symbols. So we do this:

	E -> T E'
	E' -> epsilon | + epsilon
	T -> F T'
	T' -> epsilon | * F
	F -> a | b | c

Left factoring - This is LL(1) but it's ugly and we've changed the associativity of expressions. So LL(1) is a odds with left associativity.

Building a parse tree:
- Top Down: 
 - Expand S->Ayb - pop S, push b, y, A
 - keep S, make new nodes its children
- Bottom Up:
 - Reduce A -> ab
 - attach the old nodes as children of the new node

 ### Context sensitive Analysis

 Now that we have a grammar and can parse, if our program is not rejected by this point, do we have a valid C program?

 No, but hopefully we haven't excluded any valid programs.

 What do we know? - The program is syntactically well-formed. What properties of valid C programs cannot be enforced by CFGs?
- Variables and Functions - declaration before use, multiple declarations
- Scope
- Types - operators, parameter lists

Third step of our compiler: parse tree -> [semantic analysis] -> symbol table and parse tree

Note: our compiler is broken into separate programs to isolate the pieces of each assignment . Remember the tree question from A3 for A8.

To solve these, we need more complex programs. We will perform context sensitive analysis by traversing the parse tree. To use the given parse tree we need the following 

Example: expr - three children expr + expr

Class Tree {
	public
		string rule; //expr expr PLUS term
		vector<string> tokens; // "expr, "expr", "PLUS", "term"
		vector<Tree*> children; // 3 children
}

-----
## March 12

Traversal through a tree:

void doSomething(const Tree &t) {
	// do something with t
	for(vector<TREE*>::const_iterator it = t.children.begin(); it != t.children.end(); ++it) {
		dosomething(**it);
	}
}

###Declaration Errors - Multiple/Missing

For simplicity, for now we assume that there is only one procedure. We've seen this before - we created a symbol table to keep track of used labels when we assembled a MIPS file. We'll do it again:

For each node with rule [dcl->type ID]
- extract name (e.g. x) and type ("int" or "int*") and add it to the symbol table
- if name already in symbol table, error
- multiple declarations are now checked

Traverse again - for rules factor -> ID, statement -> lvalue becomes expr SEMI
- if ID's name is not in symbol table, error (lvalue has an id)
- undeclared variables now checked

To actually implement the symbol table, we could use a global variable: 
- map<string, string> symbolTable  (where the first string is the name, second is the type)
- but, this does not take scope into consideration:

	int f() {
		int x = 0;
		return 1;
	}
	int wain(int a, int b) {
		int x = 0; //this is a duplicate declaration of x
		return x;
	}

Likewise, if we declared x in f and used it in wain, it would appear valid. We also haven't checked procedure declarations. How can we forbid duplicate declarations in the same procedure, but permit them in different procedures? The solution is to have a separate symbol table for each procedure. Have one top-level symbol table that collects all procedure names: map<string, map<string, string>> topSymbolTable (where the first string is procedure name, the map is the procedure's symbol table)

When traversing, if the rule is "procedure -> INT ID LPAREN ..." or "main -> INT WAIN LPAREN ..." then we have a new procedure. If the name is not in the symbmol table, create a new entry (and a new symbol table)
. Note - you probably want a variable that determines what procedure is currently being parsed. Update each time we encounter one of the two procedure rules above.

For variables, the declared type is stored in the symbol table. For procedures, we still need the signature (the types of the parameters). Note that all procedures return int for this language So we don't need to worry about the return type. We re-update the table again:

#include<utility>
map<string, pair<vector<string>, map<string,string>>> topSybolTable;    (where the first of our pair is the types of the parameters of that procedure to make sure we call it correctly)

To compute the signature:
- traverse nodes of the form: 
 - paramlist -> dcl   
 - paramlist -> dcl COMMA paramlist
- if params -> epsilon, the signature is empty

All this analysis can be done in a single pass

### Type Checking

Why do programming languages have types? Recall - we can't know just by looking at raw bits what they represent. We need to remember what our intent was when we stored the bits. Types help us do that. Types allow us to assign and remmeber an interpreation for the contents of some memory address. A good type system prevents us from reinterpreting the bits as something else.

	int *a = NULL; // denotes pointer
	a = 7; // attempting to store an int where a pointer is needed, should be prevented

In WLP4, there are only two types: int and int*. To check type correctness, we need to get the type for every variable/expression and ensure that operators/functions are applied to operands of the correct type. Catching type errors can be performed as follows:
	string typeOF(const Tree &t) {
		for each c in t.children
			compute_type_of(c);
		use t.rule to decide what type rule is relevant
		combine typoes of children with the rule, use these to determine t's type
		if not possible, error
	}

For example, how do we type check on ID? We get its type from the symbol table: look if <id.name, T> is in dcls
- <id.name, T> in dcls => id.name : T (notation on the board shows as a/b instead of a=>b - recall notation from CS 245)
- x:y means x has type y
- if id.name was declared to have type T, then id.name has type T

	string typeOf(const Tree &t) {
		....
		if (t.rule == "ID NAME") {
			return symbolTable.lookup(name);
		}
	}

Singleton Productions:
- e.g. 
 - expr -> term
 - term -> factor
 - factor -> ID
 - we return typeOf(only child);
- other forms are also easy (especially leaves)
 - [nothing] => Num:int
 - [nothing] => NULL:int*
 - E:T -> (E):T
- other forms get more complicated
 - E:int => &E:int
 - E:int* => *E:int
 - E:int => new int[E]:int*
- multiplicaiton and division
 - E1:int E:int => E1*E2:int
 - E1:int E2:int => E1/E2:int
 - E1:int E2:int => E1%E2:int
- Addition is more complicated
 - int + int -> int
 - int* + int or int + int* -> int*
 - int* + int* -> NO
- subtraction is similar
 - int - int -> int
 - int* - int -> int*
 - int - int* -> NO
 - int* - int* -> int
- function calls:
 - <f,()> in dcls => f():int
 - <f,(T1..Tn)> in dcls E1:T1 ... En:Tn => f(E1,...,En):int

For while loops and if statements, the condition should be booelean, not int or int*, but there is no boolean type in WLp4. The grammar forces T to be a comparison (expr comparison expr). However, the comparison stil needs to be checked. Any expression witha  type is well-typed.
- E:T => wellTyped(E)

For comparisons
- E1:T E1:T => welltyped(E1 == E2)
- E1:T E2:T => welltyped(E1 != E2)
- E1:T E2:T => welltyped(E1 < E2)

A comparison is welltyped if its arguments have the same type. We allow less than comparisons of pointers if they are the same array; otherwise behavour is undefined. (which is fine - we just don't worry about it)

-------
## March 17

Assignment:
- E1:T E2:T => welltyped(E1=E2)

Print:
- E:int => welltyped(println(E))

Deallocation:
- E:int* => welltyped(delete[]E)

If
- welltyped(T) welltyped(S1) welltyped(S2) => welltyped(if (T) {S1} else {S2})

Loops:
- welltyped(T) welltyped(S) => welltyped(while(T) {S})

Statement sequences (including empty):
-    => welltyped(epsilon)
- welltyped(S1) welltyped(S2) => welltyped(S1 S2)

Procedures:
- welltyped(dcls) welltyped(S) E:int => welltyped(int id (params) {dcls S return E;})

Wain (using mips.twoints or mips.array):
- dcls:int welltyped(dcls) welltyped(S) E:int => welltyped(int wain(dcl, dcl2) {dcls S return E;})

The results so far:

	source -> [lexical analysis] -> tokens -> [parsing] -> parse tree -> [semantic analysis] -> symbol table and parse tree -> [code gen] -> assembly

We're at the code gen stage! 

###Code generation

By now the source program is guarenteed to be without compiler errors. We must now output an equivalent MIPS assembly program. THere are infinitely many equivalent MIPS programs - which do we choose?
- correct: must work in most cases
- easy: for CS241
- efficient: compile time and runtime - for the purpose of A11, fastest will be interpreted as fewest instructions

Example: 
	
	int wain (int a, int b) {
		return a;
	}

Conventions:
- the parameters are held in $1 and $2
- since our loaders are mips.twoints and mips.array, the output of wain goes into $3

some assembly code for this:

	add $3, $1, $0
	jr $31

We will never see assembly code this clean and efficient, unless the compiler is really really really smart.

Example 2:

	int wain (int a, int b) }
		return b;
	}

assembly:

	add $3, $2, $0
	jr $31

These two programs have the exact same parse tree, so how do we tell one from the other? We are going to use the symbol table. Let's add a field to the symbol table which indicates where each symbmol is stored.

	Name 	Type 		Location
	-----------------------------------
	a 		int 		$1
	b 		int 		$2

(suggestion to combine type and location into a class/struct)

Then when traversing the parse tree for code generation, when ID is encountered	within the return statement, look itup in the symbol table. If ID is a, get back $1, and $2 for b.

####Adding Local Declarations

These can't all be in registers, since there are not enough. For simplicity, allocate space on the stack for all local variables, including parameters of wain.

	int wain(int a, int b) {
		int c = 0;
		return a;
	}

Symbol table without c:

	Name  	type 	offset
	-----------------------
	b 		int 	4
	a  		int 	0

stack:  $30-> b a  (b is on the top of the stack)

	sw $1, -4($30)
	sw $2, -8($30)
	lis $4
	.word 4
	sub $30, $30, $4
	sub $30, $30, $4
	lw $3, 4($30) ; 3 is equal to the value at offset 4
	add $30, $30, $4
	add $30, $30, $4
	jr $31

what happens when we add c?

new symbol table:

	Name  	type 	offset
	-----------------------
	c 		int 	8
	b 		int 	4
	a  		int 	0

stack:  $30-> c b a 

	sw $1, -4($30)
	sw $2, -8($30)
	lis $4
	.word 4
	sub $30, $30, $4
	sub $30, $30, $4
	sw $0, -4($30)
	sub $30, $30, $4
	lw $3, 8($30) ; now 3 is equal to the value at offset 8
	add $30, $30, $4
	add $30, $30, $4
	add $30, $30, $4
	jr $31

Problem: we dont konw the offsetes until all declarations have been processed. Since $30 changes with each new declaration and if we ever use the stack for temporary data, $30 changes and all the offsets in the symbol table are wrong.

Solution: $29 points ot the bottom of the stack frame, aka the "frame pointer". We compute the offsets relative to $29, and they won't move. A stack frame consists of all the state information stored on the stack for an active procedure call. 

A convention taht we will follow is that $4 will always contain the value 4.

New symbol table:

	Name  	type 	offset
	-----------------------
	a 		int 	0
	b 		int 	-4
	c  		int 	-8

stack: c b a
 - $30 points to c
 - $29 points to a

new code:
	
	; prologue
	lis $4
	.word 4
	sub $29, $30, 4
	sw $1, -4($30)
	sub $30, $30, $4
	sw $2, -4($30)
	sub $30, $30, $4
	sw $0, -4($30)
	sub $30, $30, $4   ; int c = 0
	lw $3, 0($29)      ; return a
	; epilogue
	add $30, $29, $4
	jr $31

--------
##March 19

####Last time
- codegen
- recall $3 is used for return value of wain

More complicated example

	int wain (int a, int b) {
		return a+b;
	}

###More code gen - addition example

In general, for each grammar rule A -> gamma; we will build code (A) from code (gamma). We will extend the $3 convention to use $3 for the "output" of all expressions 

	a+b;   code(a) ($3 <- value(a))
		   code(b) ($3 <- value(b))
		   $3 <- $3 + $3

We need to store pending computations in other registers

	a+b; 	code(a) ($3 <- value(a))
			add $5, $3, $0 ($5<-$3)
			code(b) ($3 <- value(b))
			add $3, $5, $3 ($3 < a+b) // 1 extra register

This works for now, but we are about to see some problems. What about a+b+c?

a+(b+c) : 	

	code(a) 		($3 <- value(a))
	add $5, $3, $0 	($5<-$3)
	code(b) 		($3 <- value(b))
	add $6, $3, $0 	($6<-$3) // can't add now due to ()
	code(c) 		($3<-value(c))
	add $3, $6, $3 	($3<-b+c)
	add $3, $5, $3 	($3<- a+(b+c)) // 2 extra registers

Similarily, a+(b+(c+d)) needs 3 extra registers. Eventually, we will run out of registers. Therefore, this doesn't work. The more general solution is touse the stack. Let's compile a+(b+(c+d)) using the stack.

a+(b+(c+d)): 	

	code(a) 	($3<-a)
	push($3) 	(sw $3, -4($30); sub $30, $30, $4)
	code(b) 	($3<-b)
	push($3)	 etc.
	code(c)
	push($3)
	code(d)
	pop($5) 	(add $30, $30, $4; lw $5, -4($30))
	add $3, $5, $3 	($3 <- c+d)
	pop($5)
	add $3, $5, $3 ($3 <- b+(c+d))
	pop($5)
	add $3, $5, $3 ($3<-a+(b+(c+d))) // 1 extra register

In general, expr1 -> expr2 + term

code(expr1):

	code(expr2)
	push($3)
	code(term)
	pop($5)
	add $3, $5, $3

###More rules:

####Singleton Rule: 

For general singleton rule (e.g. expr->term)
- code(expr) : code(term)

####Print

For print expression - println(expr) : print expr followed by newline to the screen. We've done this already (A2Q6, A2Q7a). We use a *runtime environment*: a set of procedures provided by the compiler or OS to assist programs in their execution. (for example libc.so, msvert.dll, etc)

We make the procedure print part of the runtime environment, which will be provided as print.merl.

	./wlp4gen < source.wlp4i > source.asm
	java cs241.linkasm < source.asm > source.merl
	linker source.merl print.merl > exec.mips
	java mips.xxx exec.mips

Thus, we assume that 'print' is available to be used. It takes input from $1

code(println(expr))

	code(expr) 		($3 <- expr)
	add $1, $3, $0 	($1 <- expr)
	call(print)

Note that
- $1 is going to be clobbered - if its value is needed, save and restore
- save and restore $31
- import print function : we will add .import print to prologue

code(println(expr))
	
	code(expr)
	add $1, $3, $0
	push($31)
	lis $5
	.word print
	jalr $5
	pop($31)

###Assignment Statements

	int x;
	x = 7;
	x = x+1;

What does int x mean for the compiler? It sets aside a memory address big enough to hold an integer and calls it x. The meaning of x changes depending on whether it is on the left or right hand sideo f an assignment. 
- On the right side it is the value.
- On the left side it is an address or location
- Originally, an lvalue is something that could appear on the leftside of an assignment
- A simple definition is an lvaulue is an object (or location) that presents beyond an expression

statement -> lvalue BECOMES expr SEMI
- For now we will assume lvalue=ID (no pointers yet)

code(stmt):

	code(expr)
	sw $3, __($29) // lookup offset in symbol table

###Boolean testing

We will suggest two more conventions
- store print in $10 and add to prologue
- store 1 in $11 in prologue

test -> expr1 < expr 2

	code(expr1)
	push($3)
	code(expr2)
	pop($5)
	slt $3, $5, $3

To the contrary, test -> expr1 > expr2
- code(test) treat as expr2 < expr 1, proceed as above

If we have non-equal rule: test -> expr1 != expr2

	code(expr1)
	push($3)
	code(expr2)
	pop($5)
	slt $6, $3, $5
	slt $7, $5, $3
	add $3, $6, $7 // $6 and $7 cannot both be 1, if neither are then we're equal

If we have equal rule: test -> expr1 == expr2
- code(test) same as above !=
- sub $3, $11, $3

<= and >= are left as exercises

###If statements

if(test) {stmts1} else {stmts2}

code(stmt):

	code(test)
	beq $3, $0, else
	code (stmts1)
	beq $0, $0, endif
	else:
	code(stmts2)
	endif:

##March 24

###Last time: codegen

if statements -- if(test) {stmts1} else {stmts2}

code(stmt):

	code(test)
	beq $3, $0, else
	code (stmts1)
	beq $0, $0, endif
	else:
	code(stmts2)
	endif:

But what if the program has more than one if statement? This is bad. We need to generate unique label names. Keep a counter x for if statements. Use elseX, endifX for labels and increment X for each new if statement.

new code:

	code(test)
	bne $3, $0, trueX
	code(stmts2)
	beq $0, $0, endifX
	trueX:
		code(stmts2)
	endifX:

while loops: stmt->WHILE(test) {stmts}

	toploopY:
	code(test)
	beq $3, $0, endloopY
	code(stmts)
	beq $0, $0, toploopY
	endloopY:

####Advice for Writing Compilers
- generate comments along with MIPs instructions to make output easier to decipher

###Pointer Code Generation
- allocation/deallocation
- deferencing and address-of
- pointer arithmetic / comparison
- NULL
- assignment through pointers

What is the value of NULL? 
- at the source level 0 and NULL are indistinguishable
- at the machine level, we will use 0x01 to guarentee deferencing NULL will crash

factor->NULL

	add $3, $11, $0

with respect to deferencing: factor->*expr

	code(expr)
	lw $3, 0($3)

Pointer comparisons are the same as integer comparisons, except that there are no negative pointers, so pointer comparisons sould be unsigned - ie uses sltu instead of slt

For pointer arithmetic, the exact memory depends on the types involved

expr1 -> expr2 + term
- if expr2:int, term:int - as before
- if expr2:int*, term:int - expr2 + 4*term

code: 
	code(expr2)
	push($3)
	code(term)
	mult $3, $4
	mflo $3
	pop($5)
	add $3, $5, $3

if expr:int, term:int* - left as exercise

We can't know which + - < > to do unless we know the tpe of expr2 and term. We should add a "type" field to each tree node. We can record each node's type during type checking. Then, when we need the type during code generation, it will be right there.

expr1 -> expr2 - term

	if expr2: int, term:int - as before
	if expr2:int*, term:int --- we do expr2 - 4*term (same as before, but subtract)
	if expr2:int*, term:int* - same as regular subtraction, divided by 4

For new and delete, we are given code in the runtime environment. There is an allocation module called alloc.merl. This must be linked in *last*. We will add to the prologue:

	.import init
	.import new
	.import delete

init sets up the allocator's data structure. We call init once in the prologue, and it takes a parameter in $2. If we are calling with mips.array (ie the first parameter of wain is int*), $2 needs to be the length of the array. Otherwise, $2 needs to be 0.


------
##March 26

####Last time
- codegen - pointers
- CORRECTION: Null pointer arithemetic is undefined

#####Init
- call once in prologue, parameter in $2
- otherwise $2 should be 0
- paramter used to account for already allocated space

#####New
- this takes the size of the array in $1
- new returns a pointer to the allocated memory, or returns 0 if the allocation is not possible
- if new returns 0, replace it with 0x01, so it matches our representation of NULL

#####Delete
- $1 contains a pointer to memory to be deallocated
- this delete does not check for NULL. Your assembly code should not call delete if pointer is NULL

#####Assignment through pointers
- the lefthand side is the address at which to store the value and the righthand side is the value
- recall that since assignments and comparisons don't nest, we don't need to push and pop
- start- > lvalue = expr
 - calculate address for lvalues
 - store expr there

assignment code (statement -> lvalue BECOMES expr SEMI)

	code(lvalue)
	add $6, $3, $0
	code(expr)
	sw $3, 0($6)

#####Address of 

factor -> AMP lvalue
- code(lvalue)
- it already returns an address, so just pass it along for factor

lvalue -> ID

	get offset
	lis $3
	.word offset
	add $3, $3, $29
	
lvalue -> STAR factor

	code(factor)

(end of A10/pointer stuff)

###Compiling Procedures

How do you run starting at wain, when it is not the first procedure?

(see pic)

The main line will always have a prologue and epilogue. THe procedures must also have a procedure specific prologue and epilogue. In procedures we don't need to setup the constants or imports. We do need to set $29, save registers, and restore registers at the end, as well as jr $31

####Saving and restoring registers: 

how do we know which registers to save and restore? We can save and restore all of them. Our code generation scheme doesn't use any registers past 7. Don't forget to save and restore $29.

There are two general approaches to saving registers. Suppose f calls g:
- caller-save: f saves all registers that contain critical data before calling g
- callee-save: g saves all registers it modifies

Which approach is better?
- one advantage of callee saving is fewer lines of code as the saving and restoring only occurs once in the procedure definition.
- if caller strategy, saving and restoring code must be put in for each call

We have been using a hybrid appraoch - caller-save for $31 and callee for everything else. Other approaches work as well. Who should save $29?
- suppose g saves $29. g has to save registers and point $29 to g's stack frame
- $30 has already been changed if we save registers first, so we need to figure out where $29 should be pointing to based on the number of pushed registers
- if we set $29 first, we know right away where to put $29, this is easier but we changed $29 before saving it. Thus, caller should save $29

f:
	
	push($29)
	push($31)
	lis $5
	.word g ; clean
	jalr $5
	pop($31)
	pop($29)

####Duplicate Names
- what if my WLP4 proram has procedures called init or point etc. 
- procedure names that match procedures in the runtime environment, and we'll have duplicated names in assembly.
- More generally, what if a function has the same name as one of the labels the computer generates?
- for functions, we will use the labels Ff, Fg, Fh, etc.
 - reserve labels starting with F for functions

Parameters:
- we can use registers for parameters
- alternatively, we can (should) push parameters on th stack
- a call to a function looks like 

factor-> ID (Expr1, ..., exprn)

	code(expr1)
	push($3)
	...
	code(exprn)
	push($3)
	push($29)
	push($31)
	lis $31
	.word FID
	jalr $31
	pop($31)
	pop($29)
	pop all arguments

then we have code(procedure) = 

	sub $29, $30, $4
	push registers
	code(dcls)
	code(stmts)
	code(expr)
	pop regs
	jr $31

currently, on the stack we have the caller, then the arguments, then $29, and $31 (f's frame), then anything g pushes, then local variables

(see pic)

the issue here is that there are registers in between the offsets originally recorded in the symbol table are wrong, because there are things stored between them


-------

##March 31

Last time: Codegen Procedures
- stack from top to bottom
 - g locals
 - g saves <- $29
 - ----f----
 - $31
 - $29
 - arguments
 - caller

The issue here is that there are registers in between the offsets originally recorded in the symbol table, so the offsets are wrong.

The parameters should have positive offsets since they are below the frame pointer, and local variables should have negative offsets or 0. (this is in contrast to how we handled wain, where both had positive offsets)

This problem has an easy solution. We add 4 times the number of parameters to all offsets in the symbol table. This means we are looking down for parameters, and up for local variables.

However, we still have registers between the arguments and the local variables. Instead, we should save $29 and $31 before pushing the arguments and save the registers after pushing the local variables

new stack from top to bottom:
- g saves
- g locals <- $29
- ----f----
- arguments
- $31
- $29
- caller

e.g. stack frame:
- -8: local 3
- -4: local 2
- 0: local 1
- 4: arg n
- 8: arg n-1

code(factor):

	push($29)
	push($31)
	code(expr1)
	...
	code(exprn)
	push($3)
	lis $3
	.word FID
	jalr $3
	pop args
	pop($31)
	pop($29)

code(procedure):

	sub $29, $30, $4
	code(dcls)
	push regs
	code(stmts)
	code(expr)
	pop regs
	add $30, $29, $4 ;pops off locals - don't need to save them, can just move pointer

---- should be able to complete A11 (without bonus) ----

###Code Optimization

This is a large and difficult topic. In general, we wish to minimize runtime. For cs241, we aim to produce short MIPs code, which is an approximation of runtime. This problem is computationally unsolvable. The best we can do is to employ heuristics. 

Some of the code written is really long. For example, consider 5+2. We could compute 5+2 at compile time instead, dropping from 9 to 2 words. This is called *constant-folding*.

	lis $3
	.word 7

*Constant-Propogation:*

	int x = 1;
	//x doesn't change
	return x+x;

If we never changes, we could recognize that this will return 2. If that is the only place x is used, it doesn't een need a stack entry.

	lis $3
	.word 2
	jr $31

When trying to determine that x nevre changs, what problem can you run into?
Alias - when two or more variables denote the same memory address they are aliases of each other.

	int *y =  &x;
	*y = 5; // x = 5 now

Be careful, you may overlook that the value of x is changed. 

If x's value is not known, we can still recogize that $3 aleady contains x. This is called *Common Subexpression Elimination*

	lw $3, -12($29)
	add $3, $3, $3
	jr $31

(a+b)*(a+b)
- use a register to hold a+b, then multiply by itself rather than computing twice
- this might not work with function calls (f()+f()) since f ma print something

*Dead Code Elimination*

If we are certain that some part of the program willnever run, then we don't output code for it.

	while(false) {
		// don't compile this!
	}

*Register Allocation*:
- It is cheaper to use registers for variables instead of the stack. This saves the number of lw and sw calls we make
- we could, however, use some of these to hold variables or temporary values.
- which variables should go into registers? the most used ones.
- the issue:
 - & address-of
 - registers don't have addresses. If you take the address of a var, it can't be in registers - it must have a RAM address

*Strength Reduction*

Add is usually faster than mult, so we prefer add for speed. For CS241, multiplying by 2 would require 4 lines, while adding would require 1.

	lis $2
	.word 2
	multi $3, $2
	mflo $3

vs

	add $3, $3, $3

Looping unrolling: outputting a loop multiple times to reduce branching

####Procedure specific optimizations

Using registers for parameters is an immediate optimization for procedures. There are other options:

*Inlining*

	int f(int x) {
		return x+x;
	}
	int wain(int a, int b) {
		return f(a);
	}

This could be accomplished with a smaller program

	int wain(int a, int b) {
		return a+a;
	}

We can replace the function call with its body in the caller. The downside is that if f is called many times, we get many copies of the body.

This could be good or bad - it depends on relative sizes of the body of f, or the code to call f. Some functions are harder to inline than others (e.g. recursive). If al calls to f are successfully inline, then we don't need f anymore

*Tail recursion*

	int fact(int n, int a) {
		if (n==0) return a;
		else return fact(n-1, n*a);	
	}

The last thing that a function does is make a recursive call. There is no pending work to do when the recursive call to fact returns. The contents of the current stack frame (local variables, etc) will not be used again, so we can reuse the current frame.

This is tricky in wlp4, because we can only return at the end of a procedure, meaning that there is no base case. Here is the basic transformation after parsing, expand language language and then mutate the parse tree. Whenever a return immediately follows if-then-else, push inside both branches.

	(see picture of transformed code)

Whenever return x follows assignment to x, we merge.
- merge ret = f(); return ret; -> return f();
- this can be generalized to tail call optimization

-----

##April 2

###New and Delete

####We will talk about scope vs extend
- scope: the part of the pgoram in which a variable's name is visible. It is a static property which can be determined at compile time
- extent: the part of the program's execution in which the content of a variable's memory is live; a dynamic (runtime) property

some code:

	int f() {
		int x;
		...
		return x;
	}

what is the scope of x? what is the extent of x? (here they're the same)

In many situations, these two are equivalent. For example, stack allocated, the data lives uintil the declared variable has gone out of scope or the block in whihc the variable is defined is finished executing. Sometimes, we want the data to outlive the variable that contained it - here we use the heap.

memory model:

	code
	heap (filled downwards)
	...
	stack (filled upwards)

code snippet:
	
	class c {
		...
	}
	c *f() {
		c *d = new c(...);
		return d; // c is in the heap, but the variable d is on the stack
	}

memory model:

	code
	c()
	...
	d (points to instance of c)

- d lives on the stack until it is popped (memory is managed implicitly)
- the instance of c's extent is ended by calling delete (explicit - you have to explicitly allocate and dealloate)
	
- In WLP4, C, C++, the user must do explicit memory management, i.e. new/delete or malloc/free.
- Java and Racket (Scheme) have implicit memory management	

####Using new and delete

- How do new/delete (malloc/free) work?
- There are a variety of implimentations
- Basic Approach: list of free blocks and a list of pointers to areas of free RAM
- initially, the entire heap is free and the list contains one entry

suppose the heap is 100 bytes, suppose 16 bytes bytes are allowed

	free->[100| / ]

Then the user has access to 16 bytes of memory. The free list and the amount of free RAM are updated. Do we need a list of used memory? No! Used memory is pointed at by program variables (directly or indirectly). Otherwise, memory leak!

But then, how do we know what to delete? We actually allocate 20 byetes; 16 bytes plus 1 int. We erturn the pointer to the second word, while the first word stores the size of the memory it came with.

	 4   16
	[16|     ]   pointer points to | line

Now if we allocate 28 bytes, we actually allocate 32. Suppose, we free the 16 allocation, then 28 bytes allocated.

	allocated: [32 bytes]
	free->[20 bytes]->[48 bytes left]   

Now suppose the first block is freed
- we check p[-1]  (memory before pointer) to determine how much memory came back, and then we add it to the free list.
- then if the other block is freed, we will recognize that the blocks were adjacent in RAM and merge them (100 bytes of free memory)
- then repeated allocation and deallocation creates holes in the heap
- Allocate 20, then 40. Free 20, then allocate 5. Then allocating 20 more cannot fill that space and has to go after the 40 
- a subsequent allocation of 24 words would fail even thugh there is enough space on the heap, but not enough continuous space

heap:

	[100 free]
	[20 taken | 80 free]
	[20 taken | 40 taken | 40 free]
	[20 free  | 40 taken | 40 free]
	[5 taken | 15 free | 40 taken | 40 free]
	[5 taken | 15 free | 40 taken | 20 taken | 20 free]

This is called fragmentation - what this means is that even if n bytes are free, we may not be able to allocate a coninuous block of n bytes.
- We cannot eliminate fragmenation, but if we choose the smallest block that can hold the requested data then we can limit it.
- However, searching the RAM takes time, it is not free. 
- First fit (best fit) model: the smallest block that is big enough

###Garbage collectoin in Implicit Memory Management:

This is more in languages with a known type runtime environment like Java or Scheme

	int f() {
		MyClass x = new MyClass();
		...
	} - object on longer accessible, so GC reclaims

####Mark and Sweep

Scan the entire stack looking for pointers. For each pointer found, mark the heap block it points to. Then if the heap object contains pointers, follow those as well and mark the heap block it points to. This is a recursive proccess.

Then scan the heap; reclaim any blocks which are not marked and clear the marks. This does not work well in c++ where the runtime types of stack elements are not known. 

A disadvantage here is that the proram must be stopped temporarily to do this.

####Reference counting

- Reference counting does not require a program stop. 
- For each heap block, keep track of the number of pointers which point to it. 
- This means that we watch every pointer and update the reference count (decrement old, increment new) each time a pointer is reassigned.
- if a block's reference count reaches - then it can be reclaimed
- A problem with this is circular references. If two objects point to each other and nothing else points to them,then they are still gargabe, but have a reference count of 1 (each)

####Coping Garbage Collector
- the heap is in 2 halves: "from" and "to"
- we only allocate memory from "from"
- at some point "from" fills up, all the memory reachable is copied into "to"
- any memory that was not reachable was not copied over
- then we just reverse the roles of "from" and "to"
- once again, this requires a program stop

This scheme is no susceptible to fragmentation: when memory is copied over it can be copied into continuous blocks. This is also a trash compactor. However, only half of the heap can be used at any given time. 
