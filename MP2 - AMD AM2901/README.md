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









## MP2 Checkpoint
The MP2 Checkpoint is organized in the following order:
1. Schematic Printouts
    * *MUX*
    * *latch*
    * *ALU*
    * *logic* part of ALU
2. *Eight* ALU functions with Implementation
3. Top-Level Schematic Printouts
    * *bitslice*
    * *datapath*
4. Simulation waveform from SimVision showing **only the pins** of the top2901 instance executing
the given test program
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





