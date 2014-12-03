# Memory

+ Can be volatile/non-volatile, and writable/non-writeable

## 1. Non-Volatile

+ **Retains data** on power-off

### 1.1 Read-Only Memory (ROM)

+ Insert romcell.png
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

+ Bit cell (see ramcell.png)
+ Read by asserting **word** line, sense **b** line
+ Write by driving **b** and **b'**
+ Fast (invertors actively drive bit lines)
+ Large area (6 transistors) => expensive
+ Used for cache memory

### 2.2 Dynamic RAM (DRAM) (8.2.3)
