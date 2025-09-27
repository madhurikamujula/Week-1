# 🧠 Day 4: Gate-Level Simulation (GLS), Blocking vs. Non-Blocking in Verilog & Synthesis-Simulation Mismatch

Welcome to **Day 4** of the RTL Design Workshop!  
Today, we explore **critical concepts that bridge RTL and real hardware**, ensuring that what you write actually works as intended after synthesis.

We'll deep-dive into:

- 🧪 **Gate-Level Simulation (GLS)** — validating synthesized logic
- 🔄 **Blocking vs. Non-Blocking Assignments** — crucial for writing correct RTL
- ⚠️ **Synthesis-Simulation Mismatch** — why RTL and synthesized netlists might behave differently

This session is packed with both **practical labs** and **real-world insights** to help you write **reliable, synthesizable Verilog** and catch issues early in the flow. Let’s get start

1. 🔬 Gate-Level Simulation (GLS)

Gate-Level Simulation (GLS) is your reality check after synthesis. Once your RTL is transformed into a gate-level netlist, GLS simulates the actual hardware, ensuring that the design behaves exactly as expected. Think of it as a final proofing step before diving into physical design.

Why Should You Care About GLS?

Synthesis Validation: Verify that your RTL translates correctly into gates.

Timing Verification: Check for timing issues (setup/hold violations) by incorporating delay data from SDF files.

Testability: Ensure your design has the necessary features (like scan chains) for post-synthesis testing.

When is GLS Performed?

Post-synthesis: After converting RTL into a gate-level netlist.

Pre-physical design: Catch timing and functional bugs before layout.

Types of GLS

Functional GLS: Focuses on logic functionality (typically with zero or unit delays).

Timing GLS: Annotates the design with real-world timing, checking for violations.

2. ⚡ Synthesis-Simulation Mismatch

A synthesis-simulation mismatch is a silent killer—it occurs when the behavior of your RTL doesn't match the gate-level simulation after synthesis. This mismatch can lead to subtle bugs that only appear in hardware.

Common Causes:

Non-synthesizable Constructs: Avoid using delays, initial blocks, or constructs that aren't supported by synthesis tools.

Incomplete or Ambiguous Code: Missing else clauses or faulty sensitivity lists can confuse both synthesis and simulation tools.

Tool Differences: Synthesis tools and simulators may interpret the RTL differently.

Quick Tip:

Write clean, synthesizable, and unambiguous RTL to reduce the chances of mismatches.

3. 🛠️ Blocking vs. Non-Blocking Assignments in Verilog

One of the core principles in Verilog is knowing when to use blocking and non-blocking assignments. The choice between these two can make or break your design, especially when dealing with sequential logic.

3.1 Blocking Statements (=)

Syntax: =

Execution: Sequential and immediate.

Best for: Combinational logic where order of execution matters.

Example:
```bash
always @(*) y = a & b;

```
3.2 Non-Blocking Statements (<=)

    Syntax: <=

    Execution: Concurrent and scheduled at the end of the current timestep.

    Best for: Sequential logic, like flip-flops and registers.

    Example:

```bash
always @(posedge clk) q <= d;

```
3.3 Comparison Table
```bash
| **Blocking (`=`)**                         | **Non-Blocking (`<=`)**                            |
| ------------------------------------------ | -------------------------------------------------- |
| Executes sequentially, immediately         | Executes concurrently, at the end of the time step |
| Suitable for combinational logic           | Suitable for sequential logic (flip-flops)         |
| Updates happen instantly in the code order | Updates are scheduled for later                    |
| Inferred as combinational logic (gates)    | Inferred as sequential logic (flip-flops)          |
```
4. 🧑‍💻 Labs

Let’s get hands-on and reinforce these concepts with practical labs that simulate real-world scenarios.

Lab 1: Ternary Operator MUX

A simple 2:1 multiplexer using a ternary operator:

```bash
module ternary_operator_mux (input i0, input i1, input sel, output y);
  assign y = sel ? i1 : i0;
endmodule
```
Function:
y = i1 when sel = 1; otherwise, y = i0.
Lab 2: Synthesis Using Yosys

Synthesize the MUX using Yosys:

Follow the standard Yosys synthesis flow to get your MUX into gate-level format.
Lab 3: Gate-Level Simulation (GLS) of MUX

Run Gate-Level Simulation to verify the functionality of the synthesized MUX:

```bash
iverilog /path/to/primitives.v /path/to/sky130_fd_sc_hd.v ternary_operator_mux.v testbench.v
```
Lab 4: Bad MUX Example (Common Pitfalls)

Here’s a buggy version of the MUX design. Let’s spot the issues and learn how to fix them:

```bash
module bad_mux (input i0, input i1, input sel, output reg y);
  always @ (sel) begin
    if (sel)
      y <= i1;
    else 
      y <= i0;
  end
endmodule
```
Common Issues:

Incomplete Sensitivity List: Should include i0, i1, and sel.

Non-blocking Assignment in Combinational Logic: Use blocking assignments (=) here.

Corrected version:

```bash
always @ (*) begin

  if (sel)
    y = i1;
  else
    y = i0;
end
```
Lab 5: GLS of Bad MUX

Now, let’s run GLS on the bad_mux and check for mismatches or warnings:

You’ll likely see mismatches due to the improper assignments or sensitivity list.

Lab 6: Blocking Assignment Caveat

Take a look at this design with a potential problem:

```bash
module blocking_caveat (input a, input b, input c, output reg d);
  reg x;
  always @ (*) begin
    d = x & c;
    x = a | b;
  end
endmodule
```
What’s Wrong?

The order of assignments causes d to use the old value of x, not the new one.

Fix: Always assign intermediate values before using them in further computations.

Corrected version:

```bash
always @ (*) begin
  x = a | b;
  d = x & c;
end
```
Lab 7: Synthesis of the Blocking Caveat Module

Now, let’s synthesize the corrected module and check the output:

Make sure your RTL code is logically sound before you synthesize!

5. 📝 Summary

Gate-Level Simulation (GLS): Crucial for ensuring the functionality and timing of your design after synthesis. Don’t skip it!

Synthesis-Simulation Mismatch: Avoid using non-synthesizable code and always write unambiguous RTL.

Blocking vs. Non-Blocking: Use blocking (=) for combinational logic and non-blocking (<=) for sequential logic (flip-flops).

Labs: Practical, hands-on labs that reinforce the concepts, while showcasing common pitfalls.

Tip
Always simulate both your RTL and gate-level netlist, and review any warnings from your synthesis and simulation tools! It could save you from mysterious bugs down the road. 🔍
