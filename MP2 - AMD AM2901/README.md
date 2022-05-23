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
    * **Note:** Total *datapath* layout area larger than 20,000 um<sup>2</sup> will be penalized!  
2. 





