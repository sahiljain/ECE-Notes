# Subroutines

### 1. The Stack

+ LIFO
+ Each process has a call stack that starts at high memory addresses and grows lower
+ Used for local variables and parameter passing
+ R13 is the Stack Pointer (SP) which points to the address of the top of the stack
+ Eg. push [R0] onto stack: `STR R0, [sp, #-4]!`
+ Eg. pop from stack into R0: `LDR R0, [sp], #4`

### 2. Calling a Subroutine

+ Branch to subroutine instructions and store the return address in the link register
+ PC-relative: `BL{cond} label`
	+ **Branch and Link**
	+ Offset +-16mb
	+ R14 <- [PC]
	+ PC <- [PC] + offset
+ Register indirect: `BLX{cond} Rn`
	+ **Branch Link Exchange**
	+ Offset +-2GB
	+ R14 <- [PC]
	+ PC <- [Rn]
+ Return using **Branch Exchange**
	+ `BX{cond} Rn`
	+ Eg. `BX lr`: PC <- [R14]

### 3. Load/Store Multiple Instruction

+ Allows you to push/pop multiple values onto/from the stack
+ `<op>{mode}{cond} Rn{!}, reglist`
+ op: LDM (load multiple) or STM (store multiple0)
+ mode: IA (increment after) or DB (decrement before)
+ Rn: Stack Pointer
+ !: writeback on or off
+ reglist: comma separated list or start-end range
+ STMFD = STMDB
+ LDMFD = LDMIA
+ FD: Full Descending, iterates in reverse order, so it can be read in the original order

### 4. AAPCS: ARM Architecture Procedure Call Standard

+ R0-R3: (Non-preserved) Argument/Result/Scratch registers
+ R4-R11: (Preserved) Local Variable
+ R12: (Non-preserved) Intra-procedure Scratch register
+ R13: (Preserved) Stack Pointer
+ R14: (Non-preserved) Link Register
+ R15: (Non-preserved) Program Counter

#### Guidelines

+ preserve and restore r4-r11 if you change them
+ pop everything you've pushed onto the stack
+ put the return values in R0-R3
+ pass arguments through registers
+ pass additional arguments in stack