---
title: LEG v8's CookBook
draft: true
tags:
    - arm/legv8
    - cookbook
    - pl/asm
source: "Computer Organization and Design: The Hardware/Software Interface (ARM Ed.)"
authors: 
    - Patterson, D.
    - Hennessy, J.
---

## If/Else Branches
The following code will be used for the *Leg V8 ASM* code:

> [!NOTE]
> Consider $f = x_0$, $i = x_1$, $j = x_2$, $g = x_3$ and $h = x_4$.

```c
if (i == j) {
	f = g + h;
} else {
	f = g - h;
}
```
### Assembly version
```asm
main:
	sub  x9, x1, x2       // compares x1 and x2
	cbnz x9, alternative  // go to alternative if not equal
	
    add x0, x3, x4
	b   exit

alternative:
	sub x0, x3, x4

exit:
	...
```
### Using positive logic
```asm
main:
	sub  x9, x1, x2   // compares x1 and x2
	cbz x9, caseTrue  // go to caseTrue if equals    
	
    sub x0, x3, x4
	b   exit

caseTrue:
	add x0, x3, x4

exit:
	...
```
### Using flags
To save one temporary register, you may use `XZR` special register, only to save the flag and then using the condition `B.NE` to change branch.

```asm
main:
	sub xzr, x1, x2   // compares x1 and x2
	b.ne x9, else     // go to else if not equal

	add x0, x3, x4
	b   exit

else:
	sub x0, x3, x4

exit:
	...
```

## If branches
Similarly, you can create `if` branches:

```asm
// Increment x0 if x0 > x1

main:	
	subs xzr, x0, x1
	b.le exit
	
	addi x0, x0, #1
	// go to exit anyway

exit:
	...
```

## Loops
Loops can be done using the same strategy, just label your procedure, then go back to the top until certain condition…

```asm
main:
	addi x19, xzr, #0  // index
	addi x20, xzr, #29 // max value

count30:
	// same as: while (x19 < 30) x0 += 1
	
	subs x9, x19, x20
	cbz  x9, end

	addi x19, x19, #1
	b count30

end:
	...
```
## Leaf Procedures (Functions)
Leaf procedures, or functions, are procedures which stores the current state onto the stack and then restores it before return back to the callee address.

A function takes parameters and also stores its result onto a register.

Have a look at the above function. This takes the nth number ($x_2$) of an array ($x_1$) and returns it at $x_0$.

This code may be split into:

1. Main Procedure:
	1. Store the arguments into $x_1$ and $x_2$
	2. Go to `nth` branch
		* Note that `BL` is used because this stores `PC` into `LR`, so we can go back in the end of `nth` procedure.
2. Nth function:
	1. Stores the current state
		1. The stack goes top → bottom, so we need to subtract the current `SP`
			- `SP` is subtracted by $16$, since we need to store $2$ elements of 64-bit
			- The memory is always accessed each byte, so 64-bit = 8 units and 32-bit = 4 units.
			- Follow the formula: $offset = 8 * items$
				- So, $8 * 2 = 16$.
		2. Stores each register into the first and the second position, remembering of using the offset’s formula, so:
			- $Store(x_{19}) = 8 * 1 = 8$.  
			- $Store(x_{20}) = 8 * 0 = 0$.  
	2. Executes the internal logic using $x_{19}$ and $x_{20}$
	3. Restores the stored state back
		- Do the reverse of the step 1. So, increment the counter and loads from the stack, on the right order.
	4. Return back to the old flow
		- Since the old `PC` address was saved into `LR`, we can use `BR LR` to return back to our old flow.

```asm
// consider x19 as being an array of 10 elements

main:
	orr  x1, xzr, x19  // nth's array
	orri x2, xzr, #3   // nth's position
	bl   nth           // calls nth function

	orri x19, xzr, x0  // stores the 2nd item of array

nth:
	// nth(array: x1, position: x2) -> x0
	// 
	// Description:
	//     Gets the nth item of an array
	//
	// Internal:
	//     x19: nth position in bits
	//     x20: offset in bits

    // Reserves x19, x20
	subi sp, sp, #16
	stur x19, [sp, #8]
	stur x20, [sp, #0]   

    lsl  x19, x2, #3  // position in bits
    add  x20, x1, x19 // offset
    ldur x0,  [x20]   // get the item

    // Restores x19, x20
    ldur x19, [sp, #8]
    ldur x20, [sp, #0]
    addi sp, sp, #16

	br lr
```

**What is happening to the Stack and SP when we do this?**

Basically, we decrement the `SP` and pushes the values to the stack, then we decrement it again:

```
       Before       On nth func   On return

0x8000 +-----+       +-----+       +-----+
       | ... |       | ... |       | ... |
  SP > +-----+       | x19 |  SP > +-----+
       | ... |       | x20 |       | ... |
       |     |  SP > +-----+       |     |
       |     |       | ... |       |     |
       |     |       |     |       |     |
0x0000 +-----+       +-----+       +-----+
```
## Synchronization
### Atomic Swap
```asm
// x0: Memory Address
// x19: Value
	
swap
	ldxr  x9 , [x0]       // loads into x9
	stxr  x19, x10, [x0]  // Checks if this is unlocked
	cbnz  x10, again
	add   x19, xzr, x9     // moves value to x19
```
### Spinlock
- Creates a Mutex using memory
	- If the memory location is 1, it’s **Locked**
	- Otherwise, it’s **Unlocked**

```asm
// x0:  Result
// x1:  Address
// x9:  Current value
// x10: Locked constant

lock:
	// Tries to load lock
	ldxr  x9, [x1]        // Load Lock
	cbnz  x9, lock        // Try again

	// Tries to Lock
	addi  x10, xzr, #1    // Const 1
	stxr  x9 , x19, [x0]  // Lock Address
	cbnz  x9, lock        // Try again

unlock:
	// Sets the Lock to Unlocked
	stxr  xzr, [x0]
```

## Indexes x Pointers
```c
void indexedClear(u64 array[], u64 size)
{
	for (u64 i = 0; i < size; i++)
		array[i] = 0;
}

void pointedClear(u64 *array, u64 size)
{
	for (
		u64* ptr = &array;  // points to 1st address
		ptr < &array[size]; // while pointer < last address
		ptr++               // goes next
	) *ptr = 0;             // sets its value to 0
}
```

```asm
indexedClear:
	// indexedClear(array: x0, size: x1)
	// 
	// Description:
	//     Clears an array
	//
	// Internal:
	//     x9:  index per element
	//     x10: index per byte
	
	add  x9, xzr, xzr                // set index as 0

indexedClearLoop:
	// Get Accessing index
	lsl  x10, x9, #3                 // byte-index
	add  x10, x0, x10                // offset

	stur xzr, [x11, #0]              // stores 0 at current index

    addi x9 , x9, #1                 // inc. index
    sub  x10, x1, x9                 // Compares index with size
    cbnz x10, indexedClearLoop
```

```asm
pointeredClear:
	// indexedClear(&array: x0, size: x1)
	// 
	// Description:
	//     Clears an array
	//
	// Internal:
	//     x9:  pointer
	//     x10: max address
	
	add   x9 , xzr, x0             // set pointer to &array
    lsl   x10, x1 , #3             // set max index address
    add   x10, x0 , x10            //

pointeredClearLoop:

	stur xzr, [x9, #0]              // stores 0 at *p

	addi  x9 , x9 , #8              // points to next
	subs  xzr, x9 , x10             // Compares pointer 
	b.lt  pointeredClearLoop        //     with max address
```