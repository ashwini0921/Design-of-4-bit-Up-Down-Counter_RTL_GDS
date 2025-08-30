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

 from 2025-08-30 12-12-29" src="https://github.com/user-attachments/assets/d03374b5-0937-43c2-82bd-76490813fcbf" />

 

