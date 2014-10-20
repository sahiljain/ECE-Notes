# Computers

## 1. Types of Computers (Classes)
+ **Embedded** (99% of market, microwaves, braking, single purpose)
+ **Personal** (1% of market, desktop, laptop, tablet, multipurpose)
+ **Server** (<1% of market, shared by many users over network, mainframe, supercomputer, cloud computer)

## 2. 5 Functional Units

A computer is a programmable device that can store, retrieve, and process data.

+ **Input** (keyboard, mouse, camera, network)
+ **Output** (printer, monitor, network)
+ **Arithmetic/Logic** (ALU, operations on registers)
+ **Memory** (stores data and machine instructions, organized into linear array of bytes, accessed by byte address)
+ **Control** (interprets instructions, fetches operands, controls ALU)

![Processor Basic](http://sahiljain.ca/media/processor1.jpg "Basic Processor")

## 3. Instruction Execution

#### Five steps of Instruction Execution

1. **Instruction Fetch**
	+ Copy PC contents to MAR
	+ Assert Read signal
	+ Wait for memory response, and copy MDR contents to IR
	+ Increment PC
2. **Instruction Decode**
	+ Interpret bits in IR
3. **Operand Fetch**
	+ Read necessary data from registers/extract constants from IR
4. **Execute**
	+ Use ALU OR read memory (load) OR write to memory (store)
5. **Writeback**
	+ Write result to register

#### Registers

+ IR: Instruction Register, holds the current instruction
+ R0 - R12: General purpose registers
+ R13: Stack Pointer
+ R14: Link Register
+ PC (R15): Program Counter, holds the memory address of the next instruction
+ MAR: Memory Access Register, holds the memory address from where to store/load
+ MDR: Memory Data Register, holds the value to be stored/loaded

Example: *Load R2, LOC*

1. MAR <- [PC], R/~W = 1, IR <- [MDR], PC <- [PC] + 4
2. Recognize that it's a *Load* command
3. Extract LOC from IR
4. MAR <- LOC, assert R/~W = 1
5. R2 <- [MDR]

## 4. Instruction Set Architecture

#### RISC (Reduced Instruction Set Computers)

+ Every instruction must use only 1 word
+ Memory operands accessed only using Load or Store
+ All operands in arithmetic/logic instruction must be in registers
+ Easy to pipeline (speed up by overlapping execution)

#### CISC (Complex Instruction Set Computers)

+ Instructions may span multiple words
+ Instruction operands can be memory addresses (no more Load/Story architecture)
+ Complex instructions such as copy an array of bytes
+ Hard to pipeline

## 6. Memory

+ Bit: binary digit
+ Byte : 8 bits
+ Word: usually 4 bytes
+ Big-endian: low addresses represent MSB (like how we write decimal)
+ Little-endian: low addresses represent LSB
+ Word alignment: can only access memory addresses that are a multiple of 4 (beginning of word)
+ If there are n bits in a word, you can only have 2^n bits of memory