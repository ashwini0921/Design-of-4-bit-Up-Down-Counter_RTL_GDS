# Design-of-4-bit-Up-Down-Counter_RTL_GDS
This project focuses on the design and implementation of a 4-bit Up-Down Counter, starting from RTL description and proceeding all the way to the final GDSII layout, using a complete open-source VLSI toolchain. The objective was to gain hands-on experience with the digital design flow—covering simulation, synthesis, verification, timing analysis, physical design, and layout generation—without relying on proprietary EDA tools.

The counter was first described at the RTL (Register Transfer Level) using Verilog and functionally verified using Icarus Verilog along with waveform inspection in GTKWave. Logic synthesis was carried out using Yosys, which translated the RTL design into a gate-level netlist mapped to standard cells. For Static Timing Analysis (STA), the design was evaluated with OpenSTA to ensure that the counter met the required timing constraints. The physical design flow—including floorplanning, placement, clock tree synthesis, routing, and layout generation—was performed using OpenROAD. Finally, the project concluded with the generation of a valid GDSII file, which represents the final chip layout.

By completing this project, I gained practical exposure to the RTL-to-GDS flow, learned to effectively use open-source EDA tools, and understood the challenges of ensuring functional correctness, timing closure, and design-rule compliance in VLSI design
## Module: Mycounter

**Verilog Code:**

```verilog
module Mycounter(CLK,RST,OUT);    
    input CLK, RST;
    output [3:0]OUT;
    reg [3:0]OUT;
    
    always @(posedge CLK)
    begin
        if (RST==1'b1)                                                               
            OUT<=4'b0000;
        else
            OUT<=OUT+1;
    end
endmodule

```

**Testbench Code**

```timescale 1ns/1ps
module Testbench();
    reg Clock, Reset;
    wire [3:0] Count;

    // Instantiate the DUT
    Mycounter I1(.CLK(Clock), .RST(Reset), .OUT(Count));

    // Clock generation (100 ns period)
    initial begin
        Clock = 1'b0;
        forever #50 Clock = ~Clock;
    end

    // Reset sequence and simulation control
    initial begin
        $display("Starting simulation...");
        Reset = 1'b1;        // assert reset at t=0
        #100 Reset = 0;      // deassert reset at t=100
        #2000 Reset = 1;     // assert reset again at t=2100
        #400 $finish;        // end simulation at t=2500
    end

    // Dump signals to VCD file for waveform viewing
    initial begin
        $dumpfile("count.vcd");
        $dumpvars(0, I1);   // dump DUT hierarchy only
    end

    // Console monitoring
    initial begin
        $monitor("%t | CLK=%b RST=%b COUNT=%d", $time, Clock, Reset, Count);
    end
endmodule
```
```Simulate the counter using Icarus Verilog and view waveforms in GTKWave
iverilog -o sim.out RTL/Mycounter.v Testbench/Mycounter_tb.v
vvp sim.out
gtkwave count.vcd
```
<img width="1291" height="418" alt="Screenshot from 2025-08-30 11-35-47" src="https://github.com/user-attachments/assets/b8c37cb3-0779-48a7-9447-8c969b35b4b3" />


To ensure the counter works correctly under all scenarios, code coverage analysis was performed using simulation. The testbench stimulates the Mycounter module across different clock cycles and reset conditions, verifying that:

All branches of the always block (reset and counting) are exercised.

All outputs are observed and recorded in the VCD file.

The clock and reset sequences trigger every relevant state transition.
<img width="593" height="315" alt="Screenshot<img width="1113" height="579" alt="Screenshot from 2025-08-30 12-19-10" src="https://github.com/user-attachments/assets/4e36394a-faca-4d6c-bde9-310aff141852" /><img width="1096" height="426" alt="Screenshot from 2025-08-30 12-19-22" src="https://github.com/user-attachments/assets/d82b5e2c-1901-4776-809f-e2794044b7fb" />
<img width="1111" height="456" alt="Screenshot from 2025-08-30 12-19-33" src="https://github.com/user-attachments/assets/48c92ac4-9450-4a83-aee9-de54d9938feb" />

## Logic Synthesis (Yosys)

**Logic synthesis** is the process of converting your RTL design (Verilog code) into a **gate-level netlist** that can be implemented on silicon or an FPGA. In this project, **Yosys**, an open-source synthesis tool, was used to perform this step.

### Steps performed with Yosys:

1. **Read RTL and Constraints**  
   - Yosys parses the `Mycounter.v` Verilog module.  
   - Reads any timing or technology constraints if provided.

2. **Optimization**  
   - Simplifies combinational logic.  
   - Removes redundant logic.  
   - Optimizes registers and flip-flops.

3. **Mapping to Standard Cells**  
   - Maps the design to a standard cell library (e.g., **Sky130** or **Nangate45**).  
   - Produces a **gate-level netlist** suitable for timing analysis and physical design.

4. **Verification**  
   - Ensures **functional equivalence** between RTL and synthesized netlist.  
   - Prepares the design for **Static Timing Analysis (STA)** using tools like OpenSTA.

### Stats of the circuit
 
<img width="631" height="380" alt="Screenshot from 2025-08-30 21-06-52" src="https://github.com/user-attachments/assets/39c8756f-92b3-4351-92bc-e8ecf6691c27" />

### View of optimized Netlist synthesized

<img width="791" height="251" alt="image" src="https://github.com/user-attachments/assets/e8cc5911-6515-4f37-a58d-e50c96826dba" />


## Gate-Level Netlist (Generated by Yosys)

This is the **technology-mapped gate-level Verilog** for the `Mycounter` module generated by Yosys.

```verilog
/* Generated by Yosys 0.46+0 (git sha1 eef1319e7, g++ 11.4.0-1ubuntu1~22.04 -fPIC -O3) */

module Mycounter(CLK, RST, OUT);
  wire [3:0] _00_;
  wire _01_;
  wire _02_;
  wire _03_;
  wire _04_;
  wire _05_;
  wire _06_;
  wire _07_;
  wire _08_;
  wire _09_;
  input CLK;
  wire CLK;
  output [3:0] OUT;
  wire [3:0] OUT;
  input RST;
  wire RST;
  
  INV_X1 _10_ (
    .A(RST),
    .ZN(_01_)
  );
  AND3_X1 _11_ (
    .A1(OUT[2]),
    .A2(OUT[1]),
    .A3(OUT[0]),
    .ZN(_02_)
  );
  AOI21_X1 _12_ (
    .A(OUT[2]),
    .B1(OUT[1]),
    .B2(OUT[0]),
    .ZN(_03_)
  );
  NOR3_X1 _13_ (
    .A1(RST),
    .A2(_02_),
    .A3(_03_),
    .ZN(_00_[2])
  );
  OAI21_X1 _14_ (
    .A(_01_),
    .B1(OUT[0]),
    .B2(OUT[1]),
    .ZN(_04_)
  );
  AOI21_X1 _15_ (
    .A(_04_),
    .B1(OUT[0]),
    .B2(OUT[1]),
    .ZN(_00_[1])
  );
  NOR2_X1 _16_ (
    .A1(OUT[0]),
    .A2(RST),
    .ZN(_00_[0])
  );
  OAI21_X1 _17_ (
    .A(_01_),
    .B1(_02_),
    .B2(OUT[3]),
    .ZN(_05_)
  );
  AOI21_X1 _18_ (
    .A(_05_),
    .B1(_02_),
    .B2(OUT[3]),
    .ZN(_00_[3])
  );
  DFF_X1 _19_ (
    .CK(CLK),
    .D(_00_[0]),
    .Q(OUT[0]),
    .QN(_09_)
  );
  DFF_X1 _20_ (
    .CK(CLK),
    .D(_00_[1]),
    .Q(OUT[1]),
    .QN(_08_)
  );
  DFF_X1 _21_ (
    .CK(CLK),
    .D(_00_[2]),
    .Q(OUT[2]),
    .QN(_07_)
  );
  DFF_X1 _22_ (
    .CK(CLK),
    .D(_00_[3]),
    .Q(OUT[3]),
    .QN(_06_)
  );
endmodule
```
## Logic Optimization and Synthesis TCL Script (Yosys)

This project uses a **Yosys TCL script** to optimize and synthesize the `Mycounter` design. The script performs **process translation, optimization, resource sharing, technology mapping, and gate-level netlist generation**.

**TCL Script (`optimize_counter.tcl`)**

```tcl
# Read modules from Verilog file
read_verilog Mycounter.v

# Translate processes to netlists
proc

# Remove unused cells and wires
clean

# Show the generic netlist
show

# Perform optimization
opt

# Resource sharing optimization
share -aggressive

# Show the generic netlist after optimization
show

# Mapping to internal cell library
techmap

# Map flip-flops to standard cell library
dfflibmap -liberty NangateOpenCellLibrary_typical.lib

# Map combinational logic to standard cell library
abc -liberty NangateOpenCellLibrary_typical.lib

# Remove unused cells and wires after mapping
clean

# Report design statistics
stat -liberty NangateOpenCellLibrary_typical.lib

# Write the optimized gate-level netlist to Verilog
write_verilog -noattr -noexpr -nohex -nodec netlist_final_opt_counter.v
```

## Static Timing Analysis (STA) with OpenSTA

**Static Timing Analysis (STA)** is a critical step in digital design that verifies whether a design meets its **timing requirements** without requiring exhaustive simulation. Instead of applying all input vectors, STA computes **maximum and minimum delays** along all paths to ensure timing closure.

In this project, **OpenSTA**, an open-source STA tool, is used to analyze the **optimized gate-level netlist** generated by Yosys and mapped to the Nangate standard cell library.

### Key Steps:

1. **Read Gate-Level Netlist and Constraints**  
   - Load the optimized netlist from Yosys.  
   - Load the SDC (Synopsys Design Constraints) file specifying clock periods, input/output delays, and exceptions.

2. **Timing Analysis**  
   - Compute **arrival times**, **required times**, and **slacks** for each path.  
   - Identify the **critical path** that limits maximum operating frequency.  
   - Check setup and hold violations at flip-flops.

3. **Reporting**  
   - Generate timing reports, including slack distribution, critical paths, and max/min delay statistics.  
   - Verify the design meets the target clock frequency and timing constraints.

## Timing Constraints (SDC File)

The **SDC (Synopsys Design Constraints)** file defines the timing constraints for the `Mycounter` design, including the **clock period** and **input/output delays**. These constraints are used during **STA (Static Timing Analysis)** with OpenSTA or other EDA tools to ensure timing closure.

### Contents of `Mycounter.sdc`:

```sdc
# Define clock on CLK port with 1.0 ns period (adjust if needed)
create_clock -name CLK -period 1000 [get_ports CLK]

# Input delay for reset relative to clock
set_input_delay 5 -clock CLK [get_ports RST]

# Output delay for each bit of OUT relative to clock
set_output_delay 5 -clock CLK [get_ports OUT[0]]
set_output_delay 5 -clock CLK [get_ports OUT[1]]
set_output_delay 5 -clock CLK [get_ports OUT[2]]
set_output_delay 5 -clock CLK [get_ports OUT[3]]
```

The following **TCL commands** are used with OpenSTA to perform **Static Timing Analysis** on the `Mycounter` design using the Nangate standard cell library and the SDC timing constraints.

### OpenSTA TCL Commands:

```tcl
# Read the standard cell library
read_liberty NangateOpenCellLibrary_typical.lib

# Read the gate-level netlist generated by Yosys
read_verilog Mycounter.v

# Link the design to prepare for timing analysis
link_design Mycounter

# Read the SDC file containing timing constraints
read_sdc Mycounter.sdc

# Report maximum path delays with full details
report_checks -path_delay max -format full

# Report minimum path delays with full details
report_checks -path_delay min -format full
```


<img width="734" height="350" alt="Screenshot from 2025-08-30 21-31-12" src="https://github.com/user-attachments/assets/b5318fb7-ebf6-4a24-a778-e44a32073555" />

<img width="714" height="331" alt="Screenshot from 2025-08-30 21-31-25" src="https://github.com/user-attachments/assets/9d035196-faa7-4beb-bb54-d26ca0fcd1dd" />


<img width="719" height="395" alt="Screenshot from 2025-08-30 21-31-43" src="https://github.com/user-attachments/assets/af687f79-e240-4b93-ab9d-2f1327d97e40" />

## Power Estimation and Optimization

Power analysis is an important part of digital design, especially for low-power applications. In this project, **power estimation** is performed using the **synthesized gate-level netlist** and timing/activity information from the SDC file.

### SDC Constraints for Power Estimation

```tcl
# Define clock on CLK port with 1000 time units period
create_clock -name CLK -period 1000 [get_ports CLK]

# Input delay for reset relative to clock
set_input_delay 5 -clock CLK [get_ports RST]

# Output delay for each bit of OUT relative to clock
set_output_delay 5 -clock CLK [get_ports OUT[0]]
set_output_delay 5 -clock CLK [get_ports OUT[1]]
set_output_delay 5 -clock CLK [get_ports OUT[2]]
set_output_delay 5 -clock CLK [get_ports OUT[3]]

# Input transition (slew) for reset
set_input_transition 0.1 [get_ports RST]

# Load capacitance for each output bit
set_load 0.1 [get_ports OUT[0]]
set_load 0.1 [get_ports OUT[1]]
set_load 0.1 [get_ports OUT[2]]
set_load 0.1 [get_ports OUT[3]]
```

```tcl command
# Read standard cell library
read_liberty NangateOpenCellLibrary_typical.lib

# Read synthesized gate-level netlist
read_verilog Mycounter.v

# Link the top-level module
link_design Mycounter

# Load SDC timing/power constraints
read_sdc test_counter.sdc

# Set global switching activity (10%)
set_power_activity -global -activity 0.1

# Report estimated power consumption
report_power
```



<img width="730" height="364" alt="image" src="https://github.com/user-attachments/assets/898d6a45-4d1c-4cc9-ae0d-739e567784cf" />

## Physical Design and Place & Route with OpenROAD

**OpenROAD** is an open-source tool that automates the **RTL-to-GDSII flow**, performing tasks such as **placement, clock tree synthesis, routing, and final layout generation**. In this project, OpenROAD is used to take the synthesized gate-level netlist and generate a **physical layout** of the 4-bit Up-Down Counter.

### Key Steps in OpenROAD Flow:

1. **Read Design and Constraints**  
   - Load the synthesized netlist from Yosys (`netlist_final_opt_counter.v`).  
   - Apply timing and floorplan constraints.

2. **Floorplanning and Placement**  
   - Define the chip area and I/O placement.  
   - Place standard cells optimally to minimize wirelength and delay.

3. **Clock Tree Synthesis (CTS)**  
   - Insert buffers to balance clock skew and ensure reliable timing.  

4. **Routing**  
   - Connect placed cells according to the netlist while respecting DRC rules.  

5. **Design Rule Check (DRC) and LVS**  
   - Ensure the layout meets fabrication rules and matches the logical netlist.  

6. **Generate GDSII File**  
   - Output the final **GDSII layout** ready for fabrication.

### Example OpenROAD Commands:

```tcl
read_verilog netlist_final_opt_counter.v
read_sdc Mycounter.sdc
initialize_floorplan -die_area 0 0 100 100
place_io
place_cells
cts
route
drc
write_gds final_counter.gds
```







