---
title: LEG v8 Instructions
draft: false
tags:
    - arm/legv8
    - reference
    - pl/asm
source: LEGv8 Reference Data Card ("Green Card")
authors: 
    - Patterson, D.
    - Hennessy, J.
---

## Types
| Type          |     Description      | Format                 | Example             |
| ------------- | :------------------: | ---------------------- | ------------------- |
| 🧮 **R-type** |    Register Based    | `OP RD, RN, RM`        | `ADD x0, x1, x2`    |
| 🔢 **I-Type** |   Immediate-based    | `OP RD, RN, IMM`       | `ADDI x0, x0, #15`  |
| 🧠 **D-Type** |      Load/Store      | `OP RT, [RN, #OFFSET]` | `STUR x0, [x1, #8]` |
| 🎯 **B-Type** |       Branches       | `OP LABEL`             | `BL End`            |
| ❓ **CB-Type** | Conditional Branches | `OP RT, LABEL`         | `CBZ w9, End`       |
## Integer Arithmetic 
> [!NOTE] 
> Consider $x_0$ as output and $x_1$, $x_2$ as entries. 

```asm
add   x0, x1, x2 // 🧮 addition
adds  x0, x1, x2 // 🧮 addition w/ flags
addi  x0, x0, #5 // 🔢 addition
addis x0, x0, #5 // 🔢 addition w/ flags

sub   x0, x1, x2 // 🧮 substraction
subs  x0, x1, x2 // 🧮 substraction w/ flags
subi  x0, x0, #5 // 🔢 substraction
subis x0, x0, #5 // 🔢 substraction w/ flags

mul   x0, x1, x2 // 🧮 multiplies (lower portion)
smulh x0, x1, x2 // 🧮 signed mul. (higher portion)
umulh x0, x1, x2 // 🧮 unsigned mul. (higher portion)

udiv x0, x1, x2  // 🧮 unsigned division
sdiv x0, x1, x2  // 🧮 signed division
```

> [!NOTE]
> Notice that Multiplication always results on 128-bits integer.
> So you need to access the lower and higher portions manually.
## I/O Operations
> [!NOTE] 
> Consider $x_0$ as output and $x_1$, $x_2$ as entries. 

```asm
ldur   x0, [x1, #0] // 🧠 load x1 into x0
ldurb  x0, [x1, #0] // 🧠 load 8-bits
ldurh  x0, [x1, #0] // 🧠 load 16-bits
ldursw x0, [x1, #0] // 🧠 load signed 32-bits
ldxr   x0, [x1, #0] // 🧠 load exclusive

stur   x0, [x1, #0] // 🧠 store x1 into x0
sturb  x0, [x1, #0] // 🧠 store 8-bits
sturh  x0, [x1, #0] // 🧠 store 16-bits
sturw  x0, [x1, #0] // 🧠 store 32-bits
stxr   x0, [x1, #0] // 🧠 store exclusive

movk   x0, #5       // 🔢 move 32-bits keep
movz   x0, #5       // 🔢 move 32-bits zeroed
```
## Branch & Conditionals
### Branching
```asm
b      Label  // 🎯 go to Label
b.cond Label  // 🎯 go to Label if true
bl     Label  // 🎯 go to Label w/ link
br     lr     // 🧮 go to register
```
#### Branch Conditions
+ Signed
	+ `.eq` — `.ne`:  Equal & Not Equal 
	+  `.lt` — `.ge`:  Less than & Greater or Equal
	+ `.le` — `.gt`: Less or Equal & Greater Than 
+ Unsiged
	+ `.lo` — `.hs`: Lower & Higher or Same
### Comparison
```asm
cbz  x1, Label // ❓ go to Label if x1 = 0
cbnz x1, Label // ❓ go to Label if x1 <> 0
```
## Bit-wise Operations
> [!NOTE] 
> Consider $x_0$ as output and $x_1$, $x_2$ as entries. 

```asm
and   x0, x1, x2 // 🧮 AND
ands  x0, x1, x2 // 🧮 AND w/ flags
andi  x0, x0, #2 // 🔢 AND
andis x0, x0, #2 // 🔢 AND w/ flags

orr   x0, x1, x2 // 🧮 OR
orri  x0, x0, #2 // 🔢 OR

eor   x0, x1, x2 // 🧮 XOR
eori  x0, x0, #2 // 🔢 XOR

lsl   x0, x1, x2 // 🧮 shift left
lsr   x0, x1, x2 // 🧮 shift right
```

## Pseudo-Instructions
> [!NOTE] 
> Consider $x_0$ as output and $x_1$, $x_2$ as entries. 

```asm
cmp  x1, x2  // compare registers
cmp  x1, #5  // compare register + immediate
cmpi x1, #5  // compare register + immediate

lda  x0, var // load address

mov  x0, x1  // move register
mov  x0, #5  // move immediate
```
### Alternatives
```asm
sub  xzr, x1, x2 // cmp  x1, x2
subi xzr, x1, #5 // cmpi x1, #5

mov  x0, x1      // orr x0, xzr, x1
mov  x0, x1      // add x0, xzr, x1

mov  x0, #4      // orri x0, xzr, #4
mov  x0, #4      // addi x0, xzr, #4
mov  x0, #4      // movz x0, #4
```
## Floating Point Operations
> [!NOTE] 
> Consider $x_0$ as output and $x_1$, $x_2$ as entries. 
### Arithmetic 
```asm
fadds x0, x1, x2 // 🧮 add single
faddd x0, x1, x2 // 🧮 add double

fsubs x0, x1, x2 // 🧮 substract single
fsubd x0, x1, x2 // 🧮 substract double

fmuls x0, x1, x2 // 🧮 multiply single
fmuld x0, x1, x2 // 🧮 multiply double

fdivs x0, x1, x2 // 🧮 divide single
fdivd x0, x1, x2 // 🧮 divide double
```
### I/O
```asm
ldurs x1, [x0, #0] // 🧮 load single
ldurd x1, [x0, #0] // 🧮 load double

sturs x1, [x1, #0] // 🧮 store single
sturd x1, [x1, #0] // 🧮 store double
```
### Comparison
```asm
fcmps x1, x2 // 🧮 compare single
fcmpd x1, x2 // 🧮 compare double
```