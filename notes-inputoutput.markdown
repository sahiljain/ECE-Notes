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
+ Decentralized: devices cooperate to decide