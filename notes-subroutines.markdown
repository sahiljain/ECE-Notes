# Subroutines

### The Stack

+ LIFO
+ Each process has a call stack that starts at high memory addresses and grows lower
+ Used for local variables and parameter passing
+ R13 is the Stack Pointer (SP) which points to the address of the top of the stack
+ Eg. push [R0] onto stack: `STR R0, [sp, #-4]!`
+ Eg. pop from stack into R0: `LDR R0, [sp], #4`

### Calling a Subroutine

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