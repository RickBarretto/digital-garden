---
title: Computer Architecture - Chapter 2 - Exercises
source: "Computer Organization and Design: The Hardware/Software Interface (ARM Ed.)"
tags:
  - arm/legv8
  - pl/asm
  - resolution
date: 2025-04-13
aliases:
  - Computer Architecture - Chapter 2 - Exercises
---

> [!WARNING]
>The above exercises I've done might be wrong, so please verify before. 
>Also, they are incomplete, as I did them based on my exam. Please feel free to open a PR to add or correct them. 

## 2.1

Given the following C code:

```c
f = g + (h - 5);
```

Considering $x_0 = f$, $x_1 = g$ and $x_2 = h$:

```asm
sub x9, x2, #5
add x0, x1, x9
```

> [!NOTE]
> The LEGv8 assembler compiles `sub` to `subi` when using for immediate mode.

## 2.2

Given the following ASM code:

```asm
add f, g, h
add f, i, f
```

We have the correspondent C code:

```c
f = g + h;
f = i + f;

// or
f = (g + h) + i;
```

## 2.3

Given the following C code:

```c
B[8] = A[i-j]
```

Considering $x_3 = i$, $x_4 = j$, $x_6$ and $x_7$ are the base address of $A$ and $B$, respectively.

```asm
loadItem:
	sub  x9 , x3, x4    // index
	lsl  x9,  x9, #3    // byte index
	add  x9 , x7, x9    // address
	ldur x19, [x9, #0]  // loads from A

storeItem:
	stur x19, [x7, #64] // stores at B's 8th
```

## 2.4

Given the following ASM code:

```asm
lsl  x9 , x0 , #3   // X9 = f*8
add  x9 , x6 , x9   // X9 = &A[f]
lsl  x10, x1 , #3   // X10 = g*8
add  x10, x7 , x10  // X10 = &B[g]
ldur x0 , [x9, #0]  // f = A[f]

addi x11, x9 , #8
ldur x9 , [x11, #0]
add  x9 , x9 , x0
stur x9 , [x10, #0]
```

We have the C corresponding:

```c
f = A[f];         // Since f is altered on assembly, I won't inline this
B[g] = A[1] + f;
```

## 2.5

Reducing the IC:

```asm
loadF:
	lsl  x9 , x0 , #3   // byte index
	add  x9 , x6 , x9   // address
	ldur x0 , [x9, #0]  // loads A's Fth into F

loadFirst:
	ldur x9 , [x6, #8]  // loads A's 1st

result:
	add  x19, x0, x9    // result

storeB:
    lsl  x10, x1 , #3   // byte index
    add  x10, x7 , x10  // address 
	stur x19, [x10, #0] // store at B[g]
```


## 2.6

`0xABCDEF12` is the same as:

```
HEX  DEC  BIN
 A   10   1010
 B   11   1110
 C   12   1100
 D   13   1101
 E   14   1110   
 F   15   1111
 1    1   0001
 2    2   0010
```

Big Endian:

```
0xABCDEF12

   AB        CD        EF        12
--------- --------- --------- ---------
1010 1110 1100 1101 1110 1111 0001 0010
```

Little Endian:

```
0x12EFCDAB

   12        EF        CD        AB
--------- --------- --------- ---------
0001 0010 1110 1111 1100 1101 1010 1110
```

## 2.7

Answer at [[#2.6]].

## 2.8

Given the following C code:
```c
B[8] = A[i] + A[j];
```

Considering that $x_3 = i$, $x_4 = j$ and $x_6$ and $x_7$ are respectively A and B's base address, we have:

```asm
Result:
	lsl  x9 , x3 , #3   // byte index
	add  x9 , x6 , x9   // address
	ldur x9 , [x9]      // load from ith

    lsl  x10, x4 , #3   // byte index
	add  x10, x6 , x9   // address
	ldur x10, [x9]      // load from jth

    add  x19, x10, x9   // result

StoreB:
	stur x19, [x7, #64] // store at 8th
```

## 2.9

Given the following ASM code:

```asm
add  x9 , x6 , #8  // address of A[1]
add  x10, x6 , xzr // address of A[0]

stur x10, [x9, #0] // store A[0] into A[1] 
ldur x9 , [x9, #0] // load A[1] into x9

add  x0 , x9 , x10 // f = A[1] + &A[0]
```

We have:

```c
f = A[1] + &A;
```

## 2.11

### 2.11.1

```
   0x8 + 0xD = 8 + 13 = 21
   21 => 0001 0101 => 0x15

   80 00 00 00 00 00 00 00
 + D0 00 00 00 00 00 00 00
   -----------------------
   00 00 00 00 00 00 00 15
```

### 2.11.2
Overflow

### 2.11.3

```
	~D + 1 = ~(1101) + 1 = 0010 + 1 = 0011 = 0x3

	3F FF FF FF FF FF FF FF
  + 00 00 00 00 00 00 00 01
    -----------------------
    40 00 00 00 00 00 00 00
	
	80 00 00 00 00 00 00 00
  + 40 00 00 00 00 00 00 00
    -----------------------
    C0 00 00 00 00 00 00 00
```

### 2.11.4
Undeflow

## 2.12
* For signed:
	* $max = \frac{2^n}{2} - 1$
	* $min = -\frac{2^n}{2}$
* For unsigned:
	* $max = 2^n$
	* $min = 0$
### 2.12.1
Given the $max$ value from [[#2.12]], we have $[max, max - x)$.

## 2.17.1
The OPcode field may grow, but we can't ensure how many bits this will require, since the OPcode field may vary in size depending on the instruction.

Each register field will mandatorily grow to fit the new registers, with 32 registers, each field is 5 bits sized. But for 128 registers, this is required 7-bit for each field, since we have 3 operands, this requires a total of 21-bits for all 3 used registers.

### 2.17.2
The OPcode follows the same rule of [[#2.17.1]]. The only field that is mandatorily affected is the first operand, that is a register, so, this will need 7-bits instead of 5-bits.

### 2.17.3
More registers not necessarily means more lines of code written, this all depends on the programmer, that may use a few amount of them instead.
The code may grow because of the context saving, since we will need to save and restore more registers for leaf-procedures.

By other hand, adding more instructions may have different effects. This is possible to reduce the amount of lines of code, but this would probably violate the RISC philosophy, and be inclined to a CISC-like approach more software-centric.

### 2.18.1

```
   00000000AAAAAAAA
<<                4
   ----------------
   0000000AAAAAAAA0

   0000AAAAAAAA0000
or 1234567812345678
   ----------------
   0000000????????0

   Discovering the ? section:
   
   1010 1010 1010 1010 1010 1010 1010 1010
or 1000 0001 0010 0011 0100 0101 0110 0111
   ---------------------------------------
   1010 1011 1010 1011 1110 1111 1110 1111
   ---------------------------------------
    A    B    A    B    D    F    D    F

R = 0x0000000ABABDFDF0
```

## 2.19

```asm
SetMask:
	mov  x19, #0xF800             // 16:11 insert mask
	mov  x20, #0xFFFFFFFF03FFFFFF // 31:26 clear mask

ExtractField:
	and  x19, x10, x19    // Keep only 16:10

InsertField:
	lsl  x19, x19, #15    // Move from 16:11 to 31:26
	and  x20, x20, x11    // Clear 31:26
	orr  x11, x20, x19    // Replace 31:26  
```
## 2.20

```asm
sub   x9, xzr,  #1 // Binary mask full-filled with 1
xor  x10,  x1, x11 // Selects the difference
```

Examples:
$\neg 0b0101 = 0b1111 \space xor \space 0b0101 = 0b1010$
$\neg 0b1101 = 0b1111 \space xor \space 0b1101 = 0b0010$

## 2.21

```asm
ldur x11, [x13, #0]
lsl  x11, x11, #4
```

## 2.22

This goes to else, since $x_0 > 0$. So, $x_1 = 2$.

## 2.24

### 2.24.1

```asm
loop:
	...
	rpt  x12, loop
```

So, since this is a conditional branch this would be a **CB-Type**, see [[LEGv8's Instruction Set#Types|LEG v8's Instruction Types]].

### 2.24.2

```asm
subi x12, x12, #1
cbnz x12, loop
```

## 2.25

### 2.25.2

Given the following code:

```asm
Loop:
	subs x1, x1, #0
	b.le End

	sub  x1, x1, #1
	add  x0, x0, #2
	b Loop

Done:
	...
```

We have:

```c
u64 acc = 0;
for (u64 i = 10; i > 0; i--) {
	acc += 2;
}
```

### 2.25.3

$n$ full loops + 2 operations (test & branch).
A full loop has 5 operations.

So: $5n + 2$.


### 2.25.4

The final result would be $2(x + 1)$, since we only go to done only when this is *less than* 0 and not *equal or less*.
So, we execute the loop one more time.

Obs.: `B.MI` checks if the number is negative.

### 2.25.5

```c
u64 acc = 0;
for (u64 i = 10; i => 0; i--) {
	acc += 2;
}
```

### 2.25.6
`SUBIS` subtracts two numbers, the `I` means *Immediate-mode* (See [[LEGv8's Instruction Set#Types|LEG v8's Instruction Types]]) since we are subtracting from a constant and the `S` means to enable flags from the *ALU*, so we can do certain comparisons, such as greater than (`B.GT`).


## 2.25.7

```asm
Loop:
	add  x0, x0, #2
	sub  x1, x1, #1
	cbnz x1, Loop

Done:
	...
```

Or even better:

```asm
// Since we know the formula is $2x$
lsl  x0, x0, #1
```

## 2.26

```c
for(i=0; i<a; i++)
	for(j=0; j<b; j++)
		D[4*j] = i + j;
```

```asm
OutterInit:
	orr  x10, xzr, xzr

OutterLoop:
	// Finishes if Outter Index == a
	sub  x9, x0, x10  	
	cbz  x9, End

	InnerLoop:
		orr  x11, xzr, xzr
	
		Operation:
			add   x9, x10, x11  // index's sum
			lsl  x12, x11, #32  // byte index * 4
			stur  x9, [x12, #0] // store at (inner index) *4
		
		InnerNext:
			// Go next or repeat
			addi x11, x11, #1
			sub  x9,  x1, x11
			cbz  x9, InnerLoop

	OutterNext:
		// Repeat
		addi x10, x10, #1
		cbz  x9, OutterLoop

End:
```

## 2.28

Given

```asm
orr X10, XZR, XZR

Loop: 

	LoadIntoResult:
		ldur x11, [x1, #0]
		add   x0,  x0, x11
	
	PointsToNext:
		add   x1,  x1,  #8
	
	LoopNextOrEnd:
		add  x10, x10,  #1
		cmp  x10, #100
		b.lt Loop
```

```c
u64 *p = &MemArray;              // *p is x1
for (u64 i = 0; i < 100; i++) {  // i is x10
	result += p[0];              // load into result         
	p++;                         // p points to next
}
```

## 2.29

```asm
addi x10, x1, #800 // max address = base + 100 8-bit items

Loop: 
	LoadIntoResult:
		ldur x11, [x1, #0]
		add   x0,  x0, x11
	
	Next:
		add  x1, x1, #8
		sub  x9, x1, x10
		cbnz x9, Loop
```


## 2.30

```asm
// x1: unsigned n
// x0: unsigned result
Fib:
	SaveState:
		// Needs to save LR because this function is recursive
		subi SP, SP, #32
		stur lr, [SP, #32]
		stur x1, [SP, #16]
		stur x0, [SP, #8]

	// Apply f(n-1) + f(n-2) for n > 1
	ReturnItSelf:
		subis xzr, x1, #1
		b.hi Recursion

		orr x0, x1, xzr     // fib(n) = n
		b RestoreState

	Recursion:
		Left:
			subi x1, x1, #1 
			bl Fib
			orr  x9, xzr, x1
		Right:
			subi x1, x1, #2 
			bl Fib
			orr  x10, xzr, x1

		add x0, x9, x10

	RestoreState:
		addi SP, SP, #16
		ldur lr, [SP, #32]
		ldur x1, [SP, #8]
		ldur x0, [SP, #0]

	br lr
```

## 2.32

```asm
f:
	// c + d
	add x9, x3, x4 

	// g(a, b) -> x0
	bl g

	// g(...)
	mov x1, x0
	mov x2, x9
	bl g
```


## 2.38

```asm
Lock:
	TryLoad:
		ldxr x0, [x1]
		cbnz x0, Lock

	Operation:
		cmp x1, x2
		b.gt TryLock

		orr x1, xzr, x2

	TryLock:
		addi x9, xzr, #1
		stxr x9, x0, [x1]
		cbnz x0, Lock

Unlock:
	stxr xzr, [x0]  // unlocks x0
```

