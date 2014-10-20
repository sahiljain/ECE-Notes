# ARM

## 1. Background

+ Acorn/Advanced RISC Machines
+ Licenses designs to other companies, do not manufacture themselves
+ Targets low power and cost applications

## 2. Design Principles

+ RISC with some CISC characteristics
+ All instructions conditionally executed (ignore this)
+ No shift or divide instructions

#### RISC-style Aspects

+ Instructions are all 32 bits
+ Only Load/Store can access memory
+ All other instructions use processor registers

#### CISC-style Aspects

+ Autoincrement/decrement, and PC-relative addressing
+ N, Z, V, C condition codes
+ Can load consecutive words of memory into multiple registers

## 3. Memory

+ Word/Address length: 32 bits
+ Byte addressable (unique addresses for each byte)
+ Word-aligned (multiple of 4 for words, 2 for half-words)

## 4. Registers

+ 16 32-bit processor registers (R0-R15)
+ R0 - R12: General purpose registers
+ R13: Stack Pointer
+ R14: Link Register
+ PC (R15): Program Counter, holds the memory address of the next instruction
+ R15: program counter
+ Status register: N,Z,C,V, interrupt disable bits, processor mode bits

## 5. Instruction Set

### 3 Variations

+ ARM: 32 bit
+ Thumb (haha get it): 16-bit
+ Thumb2: mix of 16-bit and 32-bit

### Data Processing Instructions

`<op>{flags}{cond} Rd, Rn, Op2`

+ op: Opcode, ex. `ADD`
+ flags: `S` if you want to set N,Z,C,V
+ cond: Instruction only executes if true
+ Rd: Destination register
+ Rn: Source register
+ Op2: Operand 2 (register or constant) in two forms:
	+ Immediate Data (encoded in instruction)
		+ eg. `ADD R2, R0, #1` (R2 <- [R0] + 1)
	+ Register Data, shifted from 0-31 bits
		+ LSL: Logical Shift Left
		+ LSR: Logical Shift Right
		+ ASR: Arithmetic Shift Right
		+ ROR: Rotate Right
		+ RRX: Rotate Right Extended
		+ eg. `ADD R2, R0, R1, LSL #2` (R2 <- [R0] + [R1]<<2)

+ Arithmetic
	+ ADD
	+ ADC (Add and Carry)
	+ SUB
	+ SBC (Subtract and Carry)
	+ RSB (reverse subtract)

+ Logical
	+ AND
	+ ORR (Logical OR)
	+ EOR (Exclusive OR)
	+ BIC (Bitwise clear, AND NOT)
	+ ORN (OR NOT)
	+ There's not NOT, you must EOR 0xFFFFFFFF

+ Data Movement
	+ MOV {S}{cond} R2, Op2
	+ MOV {cond} Rd, #imm16 (upper half is zeroed, lower half moved)
	+ MVN {S}{cond} Rd, Op2 (move NOT)
	+ MOVT {cond} Rd, #imm16 (move to the upper half of the word, lower half unaffected)

+ Shift: `<op>{s}{cond} Rd, Rm,<Reg | #n>`
	+ op: ASR, LSL, LSR, ROR, RRX
	+ eg. `LSL R1, R0, R2`: R1 <- [R0]<<[R2]

### Memory Access Instructions

`<op>{size}{cond} Rd, <address>`

+ op: LDR (load) or STR (store)
+ size: B(byte), H(halfword), SB(signed byte), HB(signed halfword)
+ cond: executes only if this is true
+ Rd: destination register
+ `<address>`: register indirect addressing
	+ Immediate offset: `<address> = Rn{, #offset}`
	+ Register offset: `<address> = Rn, Rm{, Shift #n}`
+ Eg. `LDR R1, [R0, #4]`: R1 <- [[R0] + 4]
+ Eg. `LDR R1, [R0, -R2]`: R1 <- [[R0] - [R2]]

+ PC-Relative Addressing
	+ `LDR{size}{cond} Rt, label`
	+ label is converted to an #offset from current instruction (PC)
	+ data is loaded from PC - 4 + offset into Rt
	+ offset range: [-4095, 4095]
	+ Eg. `LDR R1, DATA1` where DATA1 is 32 bytes later: R1 <- [[PC] - 4 + 32]

+ Load Address into register: `ADR{cond} Rd, label`
	+ Eg. `ADR R1, DATA1`: R1 <- [PC] - 4 + 32

### Example: Sum Of Array

![Sum of Array ARM Instructions](http://sahiljain.ca/media/sumarray.jpg)

### Pseudo-Instructions

+ Not real machine instructions
+ Translated by the assembler into the appropriate instructions

`LDR{cond} Rt,=<expr>`

+ converted to MOV/MVN or
+ value added to literal pool and LDR generated with pc-relative address

### Branch Instructions

+ changes control flow by adding offset to PC

`B{cond} label`

+ label: label is replaced by pc-relative offset
+ cond suffixes:
	+ EQ (equal to zero, Z == 1)
	+ NE (not equal to zero, Z == 0)
	+ GT (greater than)
	+ GE (greater than or equal to)
	+ LT (less than)
	+ LE (less than or equal to)
	+ PL (non-negative result)
	+ MI (negative result)
	+ VS (overflow set)
	+ VC (overflow clear)
	+ AL (always, default)

### Pre and Post-indexed Addressing

+ Applies to LDR and STR instructions
+ Pre-indexed: `<op>{size}{cond} Rt, [Rn, #offset]!`
	+ The offset is added to address in Rn, then memory is accessed, then Rn is updated
	+ Like ++x, where x is incremented, and then the value is used
+ Post-indexed: `<op>{size}{cond} Rt, [Rn], #offset`
	+ The memory access is performed with address of Rn, then Rn is updated by offset
	+ Like x++, where value of x is used, and then incremented

### Compare Instructions

+ Compare two operands and set N,Z,C,V flags
+ `CMP{cond} Rn, Op2`: Set flags according to result of Rn - Op2
+ `CMN{cond} Rn, Op2`: Compares Rn with -Op21
+ `TST{cond} Rn, Op2`: Bitwise AND of Rn and Op2, updates flags
+ `TEQ{cond} Rn, Op2`: Bitwise EOR of Rn and Op2, updates flags
+ `CBZ Rn, label`: Branch to label if Rn == 0
+ `CBNZ Rn, label`: Branch to label if Rn != 0

### If-Else

+ With branching:

```
		CBNZ 	R0, ELSE1
		ADD 	R1, R1, #1 ; statement 1
		B 		END1
ELSE1	SUB 	R1, R1, #1 ; statement 2
END1	...
```

+ With conditional execution (only works for short blocks):

```
CMP R0, #0
ADDEQ R1, R1, #1 ; statement 1
SUBNE R1, R1, #1 ; statement 2
```

### The Assembler

+ Converts assembly into machine code, two passes, symbol table, etc
+ Directives which are not translated to machine code:
	+ ALIGN
	+ AREA
	+ DCB, DCW, DCD
	+ END
	+ ENDP
	+ ENTRY
	+ EQU
	+ EXPORT
	+ SPACE/FILL