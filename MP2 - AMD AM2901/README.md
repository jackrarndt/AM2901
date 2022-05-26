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

A number of multiplexers allow data to be routed from one component to another. The "triangles" represent tri-state output drivers, electrical components, which we will ignore for now. To help navigate through the diagram's spaghetti, the tables shown below captures the multiplexer connections, with each input listed from top to bottom (the order is insignificant) and bit 3 down to bit 0 from left to right (high-order to low-order). Note that each column of these tables represents a single MUX. 

![AM2901-MUXInputTables](https://github.com/jackrarndt/AM2901/blob/main/MP2%20-%20AMD%20AM2901/Additional%20Figures/AM2901-MUXInputTables.png)

The plethora of available data routes allowed a team of AM2901’s to perform a computation with the outside assistance of extra chips, then load the result into the Q-Register and send it serially to slower I/O logic while performing the next computation.

The chain of components, which operate on four bits of data is called the *datapath*. Each of these comprises four single-bit components. There are also several individual "decode" blocks, which do not directly handle data. They serve to reduce the number of pins connecting the AM2901 to the outside world. Note that pin count is a **major factor** in the cost of any microchip. In this MP, we will only implement the *datapath*, and leave everything else for MP3. The part of the *datapath* operating on a single bit is called a *bitslice*. We will design and layout a single bitslice, and then combine four *bitslices* to form a complete *datapath*.

From the tables above, we can see that three 3-in MUXes, one 4-in MUX, and one 2-in MUX are needed 4 times in the whole *datapath*, corresponding to four *bitslices*. Therfore, each *bitslice* unit has 5 MUXes: three 3-in MUXes, one 4-in MUXes, one 2-in MUXes. Recalling the 8-bit adder in MP1, the *datapath* here is similar to the 8-bit adder, while the *bitslice* here is similar to a full adder. An 8-bit adder consists of eight full adder units. Similarly, the *datapath* here will consist of 4 *bitslice* units.

**Note:** Additional MUXes might be necessary, for example, inside the *ALU*. 

Finally, the tables listed below (also from AMD) lists the *datapath* functionality controlled by the MUXes in terms of the inputs given. Therefore, we will need to generate MUX control signal inputs from *i<8:0>*, which we will derive using Boolean algebra in a later section. For more information, see *The AM2900 Family Data Book*, in particular pages 2-002 to 2-007. 

![AM2901-MUXControlSignalTables](https://github.com/jackrarndt/AM2901/blob/main/MP2%20-%20AMD%20AM2901/Additional%20Figures/AM2901-MUXControlSignalTables.png)

### Complementary Logic Schematics
We begin by building the logic schematics for all the necessary components of the overall system, starting with the MUXes. We will use multiple stages of simple NANDs or NORs, and sometimes, larger complex logic gates like AOIs. **The goal here is to use the least number of transistors**.

There are several basic rules that will keep our circuit design “reasonable” in physical considerations, beyond ensuring the basic Boolean logic functionality. Two will affect our logic gates and one is common sense:

* **Rule 1.** *Complementary static CMOS*: Our logic must be composed of logic gates, each consisting of an NMOS network connected to ground and a PMOS network connected to the supply voltage. At any time, either the NMOS or else the PMOS network must be conducting, no matter what inputs are given to the gate. (No transmission gates!)
* **Rule 2.** *Fan-in*: No path from a gate output to power or ground may be more than 3 transistors long. This improves reliability by limiting the body effect near the output node, and also ensures we do not add long chains of bulky transistors.
* **Rule 3.** *Combinational logic only*: Do not introduce feedback loops besides the ones given.

Each logic gate (according to the definition of Rule 1) is to be placed in its own cell. Therefore, gate cells will contain only transistors, and all other cells will contain only sub-cells but no individual transistors, as to maintain the hierarchical structure of the design. Input pins may need to be added to our cells, for example, MUX select inputs. We will write Verilog code to generate these inputs once the schematics are finished. **It is not wise to implement logic to generate a function when one could simply add an input pin!** For example, if we need the complementary signal of pin *p*, instead of adding an inverter in the datapath, we can also choose to add an input pin *p’*. **This way, we can greatly reduce the number of gates in our datapath, which means smaller area for the overall layout!**

### Memory Schematics
Although complementary CMOS flip-flops and memory arrays are possible, they are never used. Simpler alternatives use *ratioed logic*: rather than requiring exactly one path to power or ground from the data-storage node, allow either one or two. Consider this circuit below:
![AM2901-latch](https://github.com/jackrarndt/AM2901/blob/main/MP2%20-%20AMD%20AM2901/Additional%20Figures/AM2901-latch.png)

Inverter *A* is always driven by inverter *B*. However, if the pass-gate *C* is turned on, it will be driven by both *B* and *C*. This may result in a direct path from power to ground as *B* and *C* form a voltage divider. If *C* has much less resistance than B, however, the voltage divider will always drive *A*’s input close to VDD or ground. Inverter *A*, in turn, sends a perfect VDD-or-ground (*rail-to-rail*) output to *B*, which also flips, shutting off the power-to-ground short circuit. The cross-coupled inverter structure, which we will call a *bitcell*, is at the heart of “static” computer memories.

**Note:** There is an extra rule to follow within the *regfile* and *latch* cells:
* **Rule 4.** *Drive ratio*: The bitcell must be driven with at least 720 nm effective width to perform a write. To simplify the MP, the sizing has already been done for us. Therefore, what we need to do is the following: First, do not delete the inverters already existing in the given schematic and also do not alter the sizes of them. Second, when we add an inverter to our design, use the a minimum-sized inverter, with W<sub>n</sub> = 360 nm and W<sub>p</sub> = 720 nm.

Timing is important in any system containing feedback. The *bitcell* cell contains a Verilog delay command that prevents the RAM input from racing through the entire datapath when the transmission gates turn on and off. To take advantage of the delay, we will use one terminal of the bitcell as an input and the other as an output. 

As for the clock inputs of RAM and also Q register, the following is quoted from *The AM2900 Family Data Book*. *"The Q register and register stack outputs change on the clock LOW-to-HIGH transition. The clock LOW time is internally the write enable to the 16x4 RAM which compromises the master latches of the register stack. While the clock is LOW, the slave latches on the RAM outputs are closed, storing the data previously on the RAM outputs. This allows synchronous master-slave operation of the register stack."* To put it simply, the master latches of both RAM and Q register should be open at clock LOW, while the slave latches should be open at clock HIGH.

### Carry Chain
Inside of the ALU, we use a carry chain, which violates a couple rules to improve area and performance. It is similar to the Manchester carry chain, except it replaces the dynamic clock input with a static “kill” input. Each stage of the chain works as follows:
1. Carry-out is always connected to VDD, GND, or carry-in (static pass-transistor logic)
2. Carry-out connects to VDD if a=b=1 (generate = a AND b)
3. Carry-out connects to GND if a=b=0 (kill = a NOR b)
4. Otherwise, carry-out connects to carry-in through a transmission gate (propagate = a XOR b)

Two of our style rules are broken, so we can ignore them as a special case:
1. The chain is four transmission gates long. Including one transistor driving the carry-in pin outside the chip, this is a fan-in of five. Our rule limits fan-in to three.
2. The generate, kill, propagate, and negative propagate signals all go into the carry stage. A glitch or transient short circuit may occur, for example, if the propagate XOR turns off slowly while the generate NAND turns on quickly. Usually we avoid such situations by using fully complementary static CMOS, as explained earlier. The performance gain is worth the small cost in power in this case.

### RTL Code
Besides the datapath structures, the AM2901 block diagram shown above also shows other components such as those labeled “decode.” Although not all are related to each other, these blocks are collectively known as the *control unit*. Since performance is generally measured in terms of the time between operands going into the datapath and results coming out, and since these structures will only appear once in the final layout (contrast with the bitslice being copied four times), **we will not manually optimize the control unit**. We need only to specify its functionality in Verilog. This Verilog will automatically be converted to working layout in MP3.

The style of Verilog we will write is called *RTL code*. This stands for “register transfer language.” Although our code contains no registers, it is a common term referring to anything that describes desired functionality regardless of the exact implementation.

The datapath will be most efficient if kept small, using the minimum number of transistors. Wherever possible, **it is encouraged to keep functionality out of the datapath and inside the control unit**. In particular, it is wasteful to decode every instruction word *i<8:0>* using identical transistors in each bitslice of the datapath.

Our RTL is split into two files:
* *controller.v*: describes the control unit
* *Am2901.v*: integrates the datapath with the control unit

### *controller.v* Code Snippet Defining Control Signals with Boolean Logic Expressions
```
// add your control signals here...

// RAM Function Control
assign reg_wr = i[8] | i[7];
assign ram_data[1] = i[8] & (~i[7]); // shift_right
assign ram_data[0] = i[8] & i[7]; // shift_left  
assign inv_ram_data = ~ram_data;  

// Q-Register Function Control 
assign q_en = ((~i[8]) & (~i[7]) & (~i[6])) | (i[8] & (~i[7]) & (~i[6])) | (i[8] & i[7] & (~i[6]));
assign q_reg_data[1] = i[8] & (~i[7]) & (~i[6]); 
assign q_reg_data[0] = i[8] & i[7] & (~i[6]);
assign inv_q_reg_data = ~q_reg_data;

// ALU Source Operand Control 
assign alu_r[1] = ((i[2]) & (~i[1]) & (~i[0])) | ((~i[2]) & i[1]); 
assign alu_r[0] = (i[2] & (~i[1]) & (~i[0])) | (~i[2]);
assign inv_alu_r = ~alu_r; 
assign alu_s[1] = (i[2] & i[1] & i[0]) | ((~i[2]) & i[0]);
assign alu_s[0] = (i[2] & (~i[1])) | (i[2] & i[1] & i[0]); 
assign inv_alu_s = ~alu_s;

// ALU Function Control
assign inv_r = (~i[4]) & i[3];
assign inv_s = (~i[5]) & i[4] & (~i[3]);
assign alu_op[2] = i[3];
assign alu_op[1] = i[5];
assign alu_op[0] = ((~i[5]) & i[4] & i[3]) | (i[5] & i[4]); 
assign inv_alu_op[1:0] = ~alu_op[1:0]; 

// Y Output Select Control
assign y_output_sel = (~i[8]) & i[7] & (~i[6]);  
assign inv_y_output_sel = ~y_output_sel; 
 
//end
endmodule
```
The control signals are defined using minimum SOP (i.e. Sum-of-Products) form. The Boolean logic expressions for these control signals were dervied using the AM2901 literature and documentation from the tables shown above. 

### Logic Verification
We will use NC-Verilog to run an AM2901 program through our schematics. It is important to check that schematics do the right thing before moving onto layout!

There are three major components to our Verilog code:
1. “Netlist” code: generated from our schematics
2. “RTL” code: written by hand and expresses functionality we “know will happen”
3. “Stimulus” code: drives the input pins according to a test program

**Note:** Debugging latches will be easier if we select *Edit => Preferences* in SimVision, open the preferences for Verilog, and select *Show Strength* and *Show colors by strength*. Signals driven by the bitcell will then appear light blue, so we can see when a latch is being read versus written.

Besides only caring about the falling edges, we also will ignore mismatches on the carry signal when we're not performing addition or subtraction. (The AMD book specifies exactly what the carry signals do under all conditions, but we don't need to be that detailed. We might get different results depending which operands we negate to get each logical function.)

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
2. List the *eight* ALU functions and explain how you implemented each of them. 
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





