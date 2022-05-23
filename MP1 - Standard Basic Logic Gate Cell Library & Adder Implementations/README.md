# MP1 - Standard Basic Logic Gate Cell Library & Adder Implementations

This machine problem introduces the approach to cell-based circuit design. 

## Tasks & Goals 
* Create a miniature cell library implementing several basic CMOS logic gates. 
* Implement a full adder and 8-bit adder by plugging these cells together, hierarchically. 
* Begin to understand and appreciate the impact of individual cell design and multiple cell integration on the quality of the overall layout. 

**Note**: These cells will form a library, which will be reused for subsequent MPs. 

## Files
* *ece425mp1.zip* contains the associated Cadence Virtuoso design files for MP1.
* *mp1_jrarndt2.pdf* is the MP1 Report submission.
* *MP1-SP22* is the provided MP1 documentation. 

## Design Flow 
**Schematic Design** -> **NC-Verilog Simulation & Verification** -> **Layout Design** -> **DRC** -> **Extract Layout** -> **LVS**

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



