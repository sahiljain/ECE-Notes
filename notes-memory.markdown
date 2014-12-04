# Memory

+ Can be volatile/non-volatile, and writable/non-writeable

## 1. Non-Volatile

+ **Retains data** on power-off

### 1.1 Read-Only Memory (ROM)

![ROM Cell](http://sahiljain.ca/media/romcell.png)
+ If **p** is **closed**, then **bitline** = 0, **p** is **open**, then **bitline** = 1
+ Cost to create mask with pattern of **p**'s is high, must make large number of ROM cells

### 1.2 Programmable ROM (PROM)

+ **p** is a fuse
+ Blow fuse with high voltage to write '1'
+ Can only write once
+ Mobile phones, RFID tags

### 1.3 Erasable PROM (EPROM)

+ **p** is a special transistor that holds charge for decades
+ Erased by ultraviolet light

### 1.4 Electrically Erasable PROM (EEPROM)

+ Different voltages for writing, reading, and erasing
+ Any byte can be written
+ Multiple writes
+ aka NVRAM (Non-Volatile Random Access Memory)

### 1.5 Flash

+ Like EEPROM but **erased/written in blocks**
+ Cheaper than EEPROM
+ Used in LPC1678, USB memory sticks, cameras

## 2. Volatile Memory

+ **Loses data** on power-off

### 2.1 Static Ram (SRAM)	(8.2.2)

![RAM Cell](http://sahiljain.ca/media/ramcell.png)
+ Read by asserting **word** line, sense **b** line
+ Write by driving **b** and **b'**
+ Fast (invertors actively drive bit lines)
+ Large area (6 transistors) => expensive
+ Used for cache memory

### 2.2 Dynamic RAM (DRAM) (8.2.3)

![DRAM Cell](http://sahiljain.ca/media/dramcell.png)
+ Uses capacitor
+ C:charged = 1, discharged = 0
+ Write by driving bit line to Vcc or ground
+ Read by sensing bit-line (already charged), write back to amplify
+ 4x slower than SRAM
+ Periodic refresh (10 ms)
+ Small area => cheaper
+ Used for main memory

## 3. Classification

Type | Volatile | Writable | Erase Size
-----|----------|----------|-----------
ROM|no|no|N/A|
PROM|no|once|N/A|
EPROM|no|yes|whole chip|
EEPROM|no|yes|byte|
Flash|no|yes|block|
SRAM|yes|yes|byte|
DRAM|yes|yes|byte|

## 4. Memory Chips

![Memory Chip](http://sahiljain.ca/media/memchip.png)
+ 16 words = 2^4 words, so 4 address pins
+ 8 bits per word, so 8 data pins
+ 2 control pins for R/~W, and CS
+ 2 power pins (Vcc and ground)
+ Decoder asserts word line to enable row

### 4.1 Large Memory Chips

+ eg. 2M word x 8-bit (2M = 2^21)
+ Requires 21 -> 2^21 decoder (slow!)
+ Instead, arrange bit cells in square array (symmetric array)
+ 2^21 words and 2^3 bits/word => 2^24 bits total (16M)

+ Arrange as 2^12 x 2^12 bits:
+ 4096 x 4096
+ Decoder would be 12 -> 2^12
+ Each row contains 2^12/2^3 => 512 words
+ 512 -> 1 mux required (2^9, so 9 input select pins)
+ Address bits would be A20 - A9 (12 bits), and feed to Row Address Strobe to select row
+ A8 - A0 fed to mux through Column Address Strobe to select word
+ Output is D7 to D0 (bits of data)
+ Timing: async DRAM read (2 words)

### 4.2 Fast Page Mode (FPM)

+ Add latch to sense/write circuit that holds entire row
+ Only have to select row once if multiple addresses read from same row
+ Timing diagram: async DRAM FPM read (2 words)
+ DRAM controlled asynchronously by timing ~RAS and ~CAS
+ Common until mid-90s

### 4.3 Synchronous DRAM (SDRAM)

+ Control circuit integrated into memory
+ Programmed for bursts of 1,2,4,8... words
+ Timing diagram: SDRAM read (2-word burst)
+ Takes starting address and produces words in consecutive bus cycles (from same row)

### 4.4 Double Data Rate (DDR) SDRAM

+ Data transferred on rising AND falling edge of bus cycle
+ Timing diagram: SDRAM DDR read (2-word burst)
+ Memory has two "banks" timed to different clock edges and data is stripped across banks
+ DDR: 2000, DDR2: 2003, DDR3: 2007, DDR4: 2014
+ Same principle, just faster circuitry
+ Naming convention: PC{2,3,4}-b/w (b/w = bandwith, MB/s)
+ eg. PC3-12800 - 800 MHz clock => 1600M transfers/sec * 8 bytes/transfer = 12800 MB/s

## 5. Metrics

+ **Latency**: delay from start until first word produced (eg. 5 cycles)
+ **Bandwidth**: volume of data per unit time
+ eg. word size = 8 bytes, clock speed = 200 MHz, 2 word read
+ Async: 2 words * 8 bytes/word * 200 Cycles/sec / 12 cycles = 267 MB/s
+ FPM: 2 words * 8 bytes/word * 200 Cycles/sec / 9 cycles = 356 MB/s
+ SDRAM: 2 words * 8 bytes/word * 200 Cycles/sec / 7 cycles = 457 MB/s
+ DDR: 2 words * 8 bytes/word * 200 Cycles/sec / 6 cycles = 533 MB/s

## 6. Modules

+ **DIMM**: dual inline memory module
+ Multiple memory chips in a PCB (Printed Circuit Board)
+ eg. 1 GB/DIMM with 64M word * 8-bit chips
+ 2 sides (banks), with 8 chips/side
+ Volume = 64MB/chip * 8 chips/side * 2 sides = 1 GB
+ Dual-channel: 2 DIMMS side by side => 128 bits in parallel