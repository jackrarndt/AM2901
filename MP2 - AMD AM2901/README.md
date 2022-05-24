# MP2 - AMD AM2901

In this machine problem, we create a clone of the AMD AM2901, a datapath chip first introduced in 1976.

## Tasks & Goals 
* Use a complementary static CMOS design style as much as possible to simplify the design.
* Build necessary components at the transistor level as well as reuse the cell library built in MP1. 
* Beginning with block diagrams publicly released by AMD, we will finish with layout, which could be manufactured on silicon by the MOSIS academic prototype service. 

The AM2901 was the heart of many minicomputers in the late 1970s. Containing a register file and a simple arithmetic unit, it often replaced a collection of smaller chips and ran (in 1979) up to 14.5MHz. One AM2901 implemented per four bits of processor word-length (so a 16-bit processor required at least four chips), and the processor would usually fill a large circuit board. The AM2901 powered the 16-bit Xerox Star, the first graphical workstation.

## MP2 Files
* *ece425mp2.zip* contains the associated Cadence Virtuoso design files for MP2.
* *mp2_checkpoint_jrarndt2.pdf* and *mp2_finalreport_jrarndt2.pdf* constitute the MP2 Report submission.
* *ECE425-MP2Presentation_jrarndt2.pptx* is a Powerpoint Presentation outlining the overall system design considerations and tradeoffs. 
* *MP2-SP22* is the provided MP2 documentation. 

## Logic Design
The AM2901 chip design aims to teach students to first analyze chips from top-down point of view and then construct circuit design from bottom-up point of view. We will learn to decompose the chip layer by layer, understand the logic connections in each layer, and narrow down our attention on one signal cell (component) design. After analyzing the chip and specify on each single component, we will start drawing the schematics of each component to make it a cohesive, functional unit. 

### Architecture
![AM2901-BlockDiagram](https://github.com/jackrarndt/AM2901/blob/main/MP2%20-%20AMD%20AM2901/Additional%20Figures/AM2901-BlockDiagram.png)

The block diagram shown above is reproduced from *The AM2900 Family Data Book*, the orginial reference on the AM2901 from AMD.

The chip is composed of:
* **Main Components**
    * Register File, or "16x4 bit 2-port RAM"
    * Q-Register
    * ALU
* **Additional Components**
    * Multiplexers

A number of multiplexers allow data to be routed from one component to another. The "triangles" represent tri-state output drivers, electrical components, which we will ignore for now. To help navigate through the diagram's spaghetti, the tables shown below captures the multiplexer connections, with each input listed from top to bottom (the order is insignificant) and bit 3 down to bit 0 from left to right (high-order to low-order). Note that each column of these tables represents a single mux. 

![AM2901-MUXTables](https://github.com/jackrarndt/AM2901/blob/main/MP2%20-%20AMD%20AM2901/Additional%20Figures/AM2901-MUXTables.png)




This section mainly indicates Muxes functions. From this section, we can see that three 3-in Muxes, one 4-in Mux, and one 2-in Mux are needed 4 times in the whole datapath. This means that the datapath consists of four bitslices, each bitslice unit has 5 Muxes: three 3-in Mux, one 4-in Mux, one 2-in Mux. (If we can recall 8-bit adder in MP1, the datapath here is similar to 8-bit adder while the bitslice here is similar to a full adder. 8-bit adder consists of eight full adder unit. Similarly, the datapath here will consist of 4 bitslice unit.)





You might also need to use additional muxes, for example, inside the ALU. Finally, Page 4 (also from AMD) lists the datapath functionality controlled by the muxes in terms of the inputs you are given. You will need to generate mux control inputs from i<8:0>. For more information, see The Am2900 Family Data Book, in particular pages 2-002 to 2-007. It can be found on the course website’s Machine Problem page.







## Layout
In MP1, we implemented an 8-bit adder. Similarly, our *bitslice* cell will be implemented as "rows of cells" with data "flowing" from left to right. The height of this structure is the *bit pitch*, and it is a major factor in determining the overall area of the design. Another factor to consider is the length of each row. 

Area is a good indicator of power and general efficiency. A tight final layout will require prior planning and proper prioritization. It is beneficial to estimate which component will take the most area in the complete design, find the most efficient implementation of that specific piece and, if possible, reduce the bit pitch. Try to fit the other cells to that bit pitch, even if some additional area is wasted (as long as it’s less than the savings from making the larger structure smaller). 

**Note:** Before integrating the cells into a final layout, it is advised to sketch how they should fit together using their known sizes. The bit pitch may be brought as low as 10 um before the memory is most compact.

### General Guidelines
1. The memory array will take up a lot of space.
2. The smallest layout is usually one, which packs the control wires tightest.
3. Control wires should go on metal 3, data operands on metal 2, and metal 1 should be for transistor terminals, with few long-distance signal routes. Keep **metal 3 routes vertical and metal 2 horizontal.**
4. Good layouts devote less space to wires and more to transistors.
5. Get a rough draft that works before trying to optimize.
6. Reduce, reuse, and recycle the cells that have already been implemented.
7. To minimize area, if you could add an input pin instead of adding a gate, do it. 

### Additional Class Layout Rules
In addition to the guidelines and DRC rules, there are some class layout rules that you will be penalized for violating:
1. ***No connect by name.*** You must connect power and ground wires together in the layout. Likewise for all the control signals. (This only applies to the top-level *datapath* cell – you will lose too much space if you tie VDD and GND in every register file cell!)
2. ***Poly routes.*** Do not use polysilicon routes longer than 10 um, measured from contact to terminus. Every polysilicon polygon should contain a transistor gate. Metal contacting poly should only connect to transistor source/drains. Transistor gates should be vertical.
3. ***Verified layout for every cell.*** Layout the smallest units first, check it, and then move on. The smoothest design flow finishes first. Try not to find all the little bugs in the big circuit.
4. ***Body contacts in every cell.*** Find room and add a ptap and an ntap in every cell.

**Note:** For simplification purposes, **we will uniformly size every PMOS transistor to 720 nm and every NMOS transistor to 360 nm**. With regards to the objectives of this MP, we are more concerned about overall functionality rather than timing and delay implications. When running LVS, uncheck *"Compare FET parameters"*. 

## MP2 Checkpoint
The MP2 Checkpoint is organized in the following order:
1. Schematic Printouts
    * *MUX*
    * *latch*
    * *ALU*
    * *logic* part of ALU
2. List the *eight* ALU functions and explain how you implmented each of them. 
3. Top-Level Schematic Printouts
    * *bitslice*
    * *datapath*
4. Simulation waveform from SimVision showing **only the pins** of the top2901 instance executing the given test program
5. Simulation comparison result of the *top2901* instance

## MP2 Final Report
The MP2 Final Report is organized in the following order:
1. Screenshot of *datapath* layout with width and height measurements using ruler. Calculate the area of your final layout and **write down on the first page of your submission!**
    * **Note:** Final *datapath* layout area larger than 20,000 um<sup>2</sup> will be penalized!  
2. Cell Layout Printouts, with only one level of hierarchy per layout. 
    * *ADD*
    * *ALU*
    * *MUX2-1*
    * *MUX3-1*
    * *MUX4-1*
    * *Q-Register*
    * *regbit*
    * *datapath*
3. LVS result for the top-level *datapath* (with “Join Nets with Same Name” turned off and strict NCSU comparison rules, but unchecked “Compare FET parameters”).
4. So far, you have finished the hand-made part of you AM2901 microprocessor. Are there any difficulties you have encountered in your design and layout? List three most important ones and simply describe what they are and how you solved them.
5. Briefly answer the following questions:
    * What is the distance between the VDD and GND in your layout? Why do you set this value? 
    * Some gates are sized to be larger than the minimum size (e.g., I55 and I56 in the regfile or I0 and I1 in the logic). Take one example to explain why it has to be set larger than the minimum size? (Hint: different examples may have different reasons.)





