---
title: LEG v8's Registers Reference
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

| Registers           | Usage                    | Should be Preserved |
| ------------------- | ------------------------ | ------------------- |
| $x_0 \to x_7$       | **Argument or Result**   | ❌                   |
| $x_8$               | Indirect Result location | ❌                   |
| $x_9 \to x_{15}$    | **Temporary**            | ❌                   |
| $x_{16} \to x_{17}$ | Scratch                  | ❌                   |
| $x_{18}$            | Platform dependent       | ❌                   |
| $x_{19} \to x_{27}$ | **Saved**                | ✅                   |
| $x_{28}: SP$        | **Stack Pointer**        | ✅                   |
| $x_{29}: FP$        | Frame Pointer            | ✅                   |
| $x_{30}: (LR)$      | **Return Address**       | ✅                   |
| $XZR$               | **Constant 0**           | -                   |
