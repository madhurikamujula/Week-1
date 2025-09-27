🎯 Day 5: Optimization in Synthesis

Welcome to Day 5 of the RTL Workshop!
Today, we're diving into one of the most impactful areas of RTL design: synthesis optimization.

You’ll explore how coding styles — like if-else structures, case statements, for loops, and generate blocks — can dramatically affect your synthesized hardware. We’ll also identify coding mistakes that lead to unwanted latches and how to avoid them.

This session comes with practical labs to see the effects of your code in action!

📑 Contents

1. If-Else Statements in Verilog
If-else constructs are core to behavioral modeling in Verilog. When used wisely, they synthesize into clean, predictable hardware.

✅ Basic Syntax

```bash
if (condition) begin
    // Executes when true
end else begin
    // Executes when false
end
```
Use begin...end when writing multiple lines.

Nest if-else to handle complex logic cleanly.

🧠 Nested If-Else Example

```bash
if (condition1)
    // Do something
else if (condition2)
    // Do something else
else
    // Default behavior
```
Use else clauses every time to prevent synthesis from inferring unintended hardware!

2. ⚠️ Inferred Latches in Verilog

One of the most common bugs in RTL design is accidentally inferring a latch.

This happens when:

Not all conditions assign a value to a signal in a combinational block.

❌ Problem Example

```bash
always @(a, b, sel) begin
    if (sel)
        y = a; // What if sel is 0?
end
```
➡️ If sel is 0, y holds its previous value → LATCH!

✅ Fixed with Default or Else

```bash
case(sel)
    1'b1 : y = a;
    default : y = 1'b0;
endcase
```


🔒 This guarantees y is assigned under all conditions, preventing latches.
3. 🧪 Labs for If-Else and Case Statements

Let’s dig into practical labs that demonstrate what happens when conditional statements are incomplete or incorrect.
Lab 1: Incomplete If Statement

```bash
if (i0)
    y <= i1;
```
![in_comp_if](https://github.com/user-attachments/assets/91d1cc1d-bb3a-4ea8-a272-363414777868)

⚠️ Missing else clause leads to latch inference.

Lab 2: Synthesis Result of Lab 1

Observe how the latch is inferred in synthesis!
![incomp_synth](https://github.com/user-attachments/assets/691045e7-39e0-4e6c-98bd-511b501fbe35)


Lab 3: Nested If-Else

```bash
if (i0)
    y <= i1;
else if (i2)
    y <= i3;
```
❗ Still incomplete — what happens if both i0 and i2 are false?
![icomp2](https://github.com/user-attachments/assets/2f614956-e4af-4d29-80ae-13a167e7831d)


Lab 4: Synthesis Result of Lab 3
![incomp2synth](https://github.com/user-attachments/assets/880ff7bb-23fb-4362-bf8f-a2494a854b53)


Synthesis shows a latch yet again.

Lab 5: Complete Case Statement

```bash
case(sel)
    2'b00: y = i0;
    2'b01: y = i1;
    default: y = i2;
endcase
```
![compcase](https://github.com/user-attachments/assets/cfe97c45-a487-4f06-b4a2-74b3a61bee14)

✅ Clean, complete logic. No latches.

Lab 6: Synthesis Result of Lab 5

![compcase_synth](https://github.com/user-attachments/assets/8c871511-6e55-4e80-be11-86e9efd87cad)

Clean synthesis — no surprises here!

Lab 7: Incomplete Case Handling

```bash
2'b1?: y = i3;
```


❗ Wildcards can be dangerous. Always handle all cases explicitly!

![badcase](https://github.com/user-attachments/assets/4ccf37aa-5502-4750-bedb-9b2ec0748a53)

badcase
Lab 8: Partial Assignments in Case

```bash
// 'x' is not always assigned in every case!
```
![Screenshot_2025-05-28_12-39-30](https://github.com/user-attachments/assets/3f6068f3-726d-4192-b3cd-f88b3611e752)

> **Note:** Steps to perform the above labs are shown in [Day 1](https://github.com/Ahtesham18112011/RTL_workshop/tree/main/Day_1).


➡️ Leads to unexpected behavior and possible latches.

Screenshot_2025-05-28_12-39-30

    🧭 Need a refresher on running these labs? Follow the steps from Day 1

4. 🔄 For Loops in Verilog

Use for loops in procedural blocks to repeat logic compactly.
Example: 4-to-1 MUX with Loop

```bash
for (i = 0; i < 4; i = i + 1) begin
    if (i == sel)
        y = data[i];
end
```
🧠 Tip: Loop must have a static iteration count for synthesis to succeed.

5. 🏗️ Generate Blocks in Verilog

Need to repeat logic at compile-time? Use generate blocks with genvar.

Example:

```bash
generate
  for (i = 0; i < 4; i = i + 1)
    and_gate and_inst (...);
endgenerate
```
📐 Great for creating scalable modules like adders, multiplexers, or replicated logic.

6. ➕ What is an RCA (Ripple Carry Adder)?

An RCA adds binary numbers using a chain of full adders.

Carry-out from one stage feeds into the next.

Easy to build using generate blocks.

7. 🧩 Labs on Loops and Generate Blocks

Time to practice powerful loop-based and scalable RTL design techniques!

🚀 Lab 9: 4-to-1 MUX Using a For Loop

Harness the power of for loops to build a clean and efficient 4-to-1 multiplexer! This approach simplifies code by iterating over inputs dynamically instead of writing repetitive logic.

```bash
module mux_generate (
    input i0, input i1, input i2, input i3,
    input [1:0] sel,
    output reg y
);
wire [3:0] i_int;
assign i_int = {i3, i2, i1, i0};
integer k;
always @(*) begin
    for (k = 0; k < 4; k = k + 1) begin
        if (k == sel)
            y = i_int[k];
    end
end
endmodule
```
🔄 Lab 10: 8-to-1 Demux Using Case Statement

Master the classic case statement to create an 8-to-1 demultiplexer — the perfect way to understand explicit control flow in hardware logic.

```bash
module demux_case (
    output o0, output o1, output o2, output o3,
    output o4, output o5, output o6, output o7,
    input [2:0] sel,
    input i
);
reg [7:0] y_int;
assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;
always @(*) begin
    y_int = 8'b0;
    case(sel)
        3'b000 : y_int[0] = i;
        3'b001 : y_int[1] = i;
        3'b010 : y_int[2] = i;
        3'b011 : y_int[3] = i;
        3'b100 : y_int[4] = i;
        3'b101 : y_int[5] = i;
        3'b110 : y_int[6] = i;
        3'b111 : y_int[7] = i;
    endcase
end
endmodule
```
🔁 Lab 11: 8-to-1 Demux Using For Loop

Elevate your design skills by using a for loop to generate an 8-to-1 demultiplexer — keeping your code DRY and scalable!

```bash
module demux_generate (
    output o0, output o1, output o2, output o3,
    output o4, output o5, output o6, output o7,
    input [2:0] sel,
    input i
);
reg [7:0] y_int;
assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;
integer k;
always @(*) begin
    y_int = 8'b0;
    for (k = 0; k < 8; k = k + 1) begin
        if (k == sel)
            y_int[k] = i;
    end
end
endmodule
```
🔧 Lab 12: 8-bit Ripple Carry Adder with Generate Block

Implement a modular and scalable 8-bit Ripple Carry Adder using a generate block! This lab showcases hierarchical design by chaining full adders in an elegant way.

```bash
module rca (
    input [7:0] num1,
    input [7:0] num2,
    output [8:0] sum
);
wire [7:0] int_sum;
wire [7:0] int_co;

genvar i;
generate
    for (i = 1; i < 8; i = i + 1) begin
        fa u_fa_1 (.a(num1[i]), .b(num2[i]), .c(int_co[i-1]), .co(int_co[i]), .sum(int_sum[i]));
    end
endgenerate

fa u_fa_0 (.a(num1[0]), .b(num2[0]), .c(1'b0), .co(int_co[0]), .sum(int_sum[0]));

assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule
```
Full Adder Module:

```bash
module fa (input a, input b, input c, output co, output sum);
    assign {co, sum} = a + b + c;
endmodule
```
Pro Tip: If you want a refresher on performing these labs step-by-step, check out the walkthrough in Day 1

🎯 Summary — Key Takeaways

Avoid inferred latches! Always write complete if-else and case statements to ensure every signal gets assigned in every execution path.

Leverage for loops & generate blocks to write clean, scalable, and maintainable RTL code without repetition.

Always verify that all signals have default or explicit assignments inside combinational blocks.

The hands-on labs are designed to solidify your understanding through real-world Verilog coding and synthesis experience — keep practicing to master these concepts!
