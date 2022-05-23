# MP1 - Standard Basic Logic Gate Cell Library & Adder Implementations

This machine problem introduces the approach to cell-based circuit design. 

## Tasks & Goals 
* Create a miniature cell library implementing several basic CMOS logic gates. 
* Implement a full adder and 8-bit adder by plugging these cells together, hierarchically. 
* Begin to understand and appreciate the impact of individual cell design and multiple cell integration on the quality of the overall layout. 

**Note**: These cells will form a library, which will be reused for subsequent MPs. 

## MP1 Files
* *ece425mp1.zip* contains the associated Cadence Virtuoso design files for MP1.
* *mp1_jrarndt2.pdf* is the MP1 Report submission.
* *MP1-SP22* is the provided MP1 documentation. 

## Design Flow 
**Schematic Design** -> **NC-Verilog Simulation & Verification** -> **Layout Design** -> **DRC** -> **Extract Layout** -> **LVS**

## Schematic Design
The circuit schematics are constructed using an academically available (through MOSIS at the University of Southern California) manufacturing process from TSMC (Taiwan Semiconductor Manufacturing Corporation) using a design kit from NCSU (North Carolina State University). This will allow for drawing transistors with a **240 nm nominal gate length** and **360 nm minimum width**, corresponding to the state of the art around 1997.

**Design Kit Library:** *NCSU_TechLib_tsmc03d*

### Logic Gate Cells Implemented:
* **inv**
* **nand2**
* **nor2**
* **aoi22 (AND-OR-invert)**
* **xor2**

### Transistor Sizing Discussion





### xor2 Design Considerations
* *xor2* created using **EXACTLY** 10 transistors.
* As shown below, logical equivalence of a 2-input XOR gate is achieved through the use of a *NOR2* gate and *AOI21* gate.
    * The output of the *NOR2* gate is a "third input" to the *AOI21* complex gate. 
* In accordance with the previous discussion, all transistors are sized to achieve the effective drive strength equal to a minimum-sized NMOS transistor (i.e. effective witdth of 360 nm).
* Derivations for the *AOI21* Common Euler Path & Stick Diagram are shown to assist in Layout Design.
![MP1-xor2](https://github.com/jackrarndt/AM2901/blob/main/MP1%20-%20Standard%20Basic%20Logic%20Gate%20Cell%20Library%20%26%20Adder%20Implementations/Additional%20Figures/MP1-xor2.jpg)

## Verification

## Layout Design

### Cell Characteristics







## MP1 Report
The MP1 Report is organized in the following order:
1. **Basic Cells**
    * **xor2:** Schematic and Layout
    * **aoi22:** Schematic and Layout
2. **Full Adder**
    * Schematic
    * Simulation Waveform
    * Top-level of the Layout Hierarchy
3. **8-Bit Adder**
    * Schematic
    * Top-Level Layout
    * LVS Output Report (si.out)
    * Simulation Comparison Output Report (compare.out)



