# Input/Output

## 1. Physical Characteristics

+ Width:
	+ Serial: 1-bit wide (USB)
	+ Parallel: n-bits wide
+ Timing:
	+ Synchronous: with clock
	+ Asynchronous: without clock
+ Direction:
	+ Simplex: 1 way
	+ Half-duplex: 1 way at a time
	+ {Full} duplex: 2 ways all the time
+ Transmission:
	+ Single-ended: 1 wire/trace per bit
		+ Receiver measures V0 - Vground
		+ Cheaper, susceptible to noise, slow (128 Mbps)
	+ Differential: 2 wires/traces per bit
		+ Receiver measures V0a - V0b
		+ higher noise immunity, fast (2 Gbps)

## 2. System Bus

+ Classic Bus:
![Classic Bus](http://sahiljain.ca/media/classicbus.png)
+ Advanced Peripheral Bus:
![APB](http://sahiljain.ca/media/apb.jpg)
+ Bus master: controls bus and initiates transactions
+ Bus slave: responds to transactions
+ Bus write: master writes data to slave
+ Bus read: master reads data from slave

### Bus Transfers

+ Synchronous transfers are faster (2*tprop) but limited by clock to shorter distances
+ Asynchronous transfers use a handshake (Master-ready, Slave-ready), slower (4*tprop)

### Arbitration

+ The deciding of which devices become master
+ Centralized: one device decides
	+ eg. Centralized with priorities (using bus request and bus grant signals)
		+ First request gets a grant, once request1 goes low, grant1 goes low, then next request is granted
+ Decentralized: devices cooperate to decide

## 3. Memory-Mapped IO

+ Bus addresses used for memory and I/O devices
+ Blocks of memory are dedicated to I/O devices

### I/O Device Bus Interface

![Device Bus](http://sahiljain.ca/media/devicebus.jpg)

+ Device registers are mapped to specific addresses
+ The decoder recognizes the device's addresses
+ The control circuit responds to bus transactions

## 4. Polling I/O

+ Program receives/sends data through I/O device by reading/writing to device data registers
+ Must repeatedly check (poll) device status register until it is ready (with received data or to send next data)
+ Pseudocode
	1. Read status register
	2. Check receive/transmit ready bit (if 1, move to step 3, otherwise go back to step 1)
	3. Read received data from data reg (buffer) or write data to data register (which changes device's status)
	4. Process received data, or ready transmit data
	5. Goto step 1

## 5. Exceptions

+ Condition requiring service (not necessarily an error)
	+ current execution is suspended
	+ condition is serviced
	+ original execution is resumed

### Cortex-M3 Exception Types

+ Reset: Power on
+ SVCall: Supervisor Call invokes OS
+ Fault: Execution error such as invalid instruction, unaligned memory access
+ Interrupt request: request from peripheral device for service

### Exception Attributes

+ Number: 1-255
+ Priority: -3 is the highest (worst), going up to 31 (least bad)
+ Vector: Exception handler address (entry point)
+ Vector Table: Entry points of exception handlers
+ Exception list:
	+ Reset: #1, Priority -3
	+ Non-Maskable-Interrupt: #2, Priority -2, connected to reset/watchdog timer
	+ HardFault: #3, Priority -1
	+ MemManage: #4, Priority is configurable from here on out
	+ BusFault: #5
	+ SVCall: #11
	+ IRQ 0: #16
	+ IRQ 239: #255

### Operating Modes

1. User Mode is the mode in which applications run
2. Fast Interrupt Mode (FIQ) entered when external device raises fast interrupt exception
3. Ordinary Interrupt Mode (IRQ) entered when external device raises normal interrupt request
4. Supervisor Mode (SVC) entered on powerup/reset, or when user program executes Software Interrupt Instruction (SWI)
5. Memory Access Violation Mode (Abort) entered when instruction causes memory access violation
6. Unimplemented Instruction Mode (Undefined)entered when the current program attempts to execute an unimplemented function
7. System Mode

+ Modes 2 - 7 (Exception Modes + System Mode) are allowed to change Status Register (CPSR)

### Reset

+ Occurs on powerup or warm reset
+ Special type of exception
+ Actions:
	+ Loads SP_main into r13 from vector 0
	+ Sets operating priority to max priority (weakest) + 1
	+ Executes the code in the Reset Handler (vector 1) using thread mode and priveledged execution

### Exception Handling

+ Exceptions are handled if they have higher priority (stronger) than the current operating priority
+ Actions:
	+ Save current context on stack (push r0-r3, r12, PC, CPSR, LR)
	+ Stores EXC_RETURN code in LR indicating operating mode and stack in use
	+ Loads entry point to handler (from vector table) into PC
+ Exception return is initiated by loading the EXC_RETURN code into the PC by `BX lr`
+ Actions: 
	+ Code sets operating mode and Stack Pointer
	+ Restores context from stack (pops r0-r3, r12, PC, CPSR, LR)
+ Exception handlers can be suspended by exceptions with high priority (nesting exceptions)

### Interrupt-Controlled I/O

+ Setup:
	+ Assign exception number and priority to I/O device
	+ Write handler (ISR) and save entry point in vector table
	+ Enable interrupts on I/O device
	+ Execute application code

### Open Drain IRQ Lines

+ If I/O devices outnumber IRQ lines, then IRQ lines must be shared
+ Problem: can't connect CMOS outputs driving at 1 and 0, creates a direct connection between ground and power
+ Solutions: open-drain technology