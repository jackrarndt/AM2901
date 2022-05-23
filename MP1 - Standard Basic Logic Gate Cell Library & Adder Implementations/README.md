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
**Note:** ALL transistor lengths are kept minimum at 240 nm, as dicitaed by the process. 

**RC Delay Model:** The RC delay model treats a transistor as a switch in series with a resistor. The *effective resistance* is the ratio of *V<sub>ds</sub>* to *I<sub>ds</sub>* averaged across the switching interval of interest.
* A unit NMOS transistor is defined to have effective resistance *R*. The size of the unit transistor is arbitrary, but conventionally refers to a transistor with minimum length and minimum contacted diffusion width. Alternatively, it may refer to the width of the NMOS transistor in a minimum-sized inverter in a standard cell library.
* An NMOS transistor of *k* times unit width has resistance *R/k* because it delivers *k* times as much current.
* A unit PMOS transistor has greater resistance, approximately in the range of *~2R*, because of its lower mobility.

As explained in the section above, the resistance of a transistor is inversely proportional to its width, *k*. Therefore, the series combination of several transistors yields a *total effective width* according to the following relation: 1/W<sub>total</sub?> = 1/W<sub>A</sub> + 1/W<sub>B</sub> + ... 

When expressed relative to the minimum transistor width, the *effective width* is also referred to as the *drive strength*. For example, a 1/2x drive strength NAND gate in our process would have two 360 nm NMOSes in series with an effective width of 360 nm / 2 = 180 nm.

Further, because a PMOS transistor has about twice the resistance of an NMOS of the same size, the drive strength of any PMOS must be divided by two. For example, a 1/2x drive strength NAND gate would have two 360 nm PMOSes in parallel, each with effective width 180 nm or drive strength 1/2x.

Note: Effective width is generally not affected by parallel connections, because transistors generally turn on one at a time. After the first transistor has turned on, it will finish driving the new output value before there is time for the second parallel transistor to switch. If their gates are connected together, however, they will switch at the same instant. In this case, they combine to form a single “super-transistor,” with the widths adding up. Little distinction is made between a single large transistor and an array of smaller transistors in parallel – a schematic diagram will simply specify a total effective width without showing any parallel connections.

**Each cell should have drive strength equal to one minimum-size NMOS transistor, or effective width of 360 nm.**

### xor2 Design Considerations
* *xor2* created using **EXACTLY** 10 transistors.
* As shown below, logical equivalence of a 2-input *XOR* gate is achieved through the use of a *NOR2* gate and *AOI21* gate.
    * The output of the *NOR2* gate is a "third input" to the *AOI21* complex gate. 
* In accordance with the previous discussion, all transistors are sized to achieve the effective drive strength equal to a minimum-sized NMOS transistor (i.e. effective width of 360 nm).
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



