# ⚙️ Day 3: Combinational and Sequential Optimization

Welcome to **Day 3** of the RTL Design & Synthesis Workshop!  
Today is all about **making your circuits smarter and faster** by learning how to **optimize** both combinational and sequential logic.

You'll explore techniques used by synthesis tools to:
- ✨ Simplify logic
- ⚡ Boost performance
- 🧠 Reduce area and power

Let’s dive in! 🚀

---

## 📚 Table of Contents

- [1️⃣ Constant Propagation](#1️⃣-constant-propagation)
- [2️⃣ State Optimization](#2️⃣-state-optimization)
- [3️⃣ Cloning](#3️⃣-cloning)
- [4️⃣ Retiming](#4️⃣-retiming)
- [5️⃣ Labs on Optimization](#5️⃣-labs-on-optimization)
  - [🧪 Lab 1](#lab-1)
  - [🧪 Lab 2](#lab-2)
  - [🧪 Lab 3](#lab-3)
  - [🧪 Lab 4](#lab-4)
  - [🧪 Lab 5](#lab-5)
  - [🧪 Lab 6](#lab-6)

---

## 1️⃣ Constant Propagation

**Constant Propagation** is an optimization technique that replaces known constant values at compile-time, allowing the logic to be simplified during synthesis.

🛠️ **How it works:**  
When the synthesis tool sees a variable always holds a constant, it **removes unnecessary logic**.

✅ **Benefits:**
- Simpler logic = smaller circuits  
- Faster performance = shorter critical paths  
- Lower gate count = lower power and area  

<div align="center">
  <img src="https://github.com/user-attachments/assets/d7f06056-66c1-44af-99a8-623fdf5879be" width="70%">
</div>

---

## 2️⃣ State Optimization

**State Optimization** focuses on reducing and encoding the states in an FSM (Finite State Machine) for efficiency.

🧠 **How it's done:**
- 🔄 **State Reduction**: Merge equivalent states  
- 🔢 **State Encoding**: Use binary/one-hot/gray codes smartly  
- ➗ **Logic Minimization**: Apply Boolean simplification  
- 🔌 **Power Optimization**: Clock gating to reduce dynamic power  

📈 Result: Leaner FSM = faster and more power-efficient designs!

---

## 3️⃣ Cloning

**Cloning** duplicates certain logic cells to **balance load**, reduce fanout, and **improve timing**.

🔧 **Steps:**
1. Identify critical path(s) using timing analysis.
2. Duplicate (clone) logic driving high fanout nets.
3. Reconnect load to clones to **shorten wire delays**.
4. Re-run synthesis and verify improved timing.

<div align="center">
  <img src="https://github.com/user-attachments/assets/6bdd2c12-02a2-4ea5-895c-98e349b93bac" width="70%">
</div>

---

## 4️⃣ Retiming

**Retiming** is a powerful technique that repositions flip-flops in a design **without altering its functionality** to achieve better timing performance.

🕸️ **Process:**
- Model the design as a **directed graph**
- Move flip-flops across logic boundaries to:
  - Minimize clock period
  - Balance timing across paths
  - Reduce power

🏁 **Goal**: Smarter register placement for better performance with no logic change.

---

## 5️⃣ Labs on Optimization

Each lab focuses on a different optimization concept with real Verilog code.  
Use Yosys for synthesis and GTKWave for waveform analysis.

---

### 🧪 Lab 1: Constant Propagation

```verilog
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
📌 This is a multiplexer-like structure:
If a = 1, output y = b. Otherwise, y = 0.

🛠️ Add this between abc -liberty and synth -top in your Yosys script:
```bash
opt_clean -purge
```
<div align="center"> <img src="https://github.com/user-attachments/assets/4d224d8d-f6f5-4a37-9732-ab570b64e31e" width="70%"> </div>
🧪 Lab 2: Logic Simplification with Constants

```bash
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```
📌 Mux logic again:
If a = 1, y = 1. Else, y = b.
Expect simplification during optimization!

<div align="center"> <img src="https://github.com/user-attachments/assets/59545745-8a8b-4afd-b4d5-0a3ad1d5b80e" width="70%"> </div>
🧪 Lab 3: Another Mux Form

```bash
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```
📌 Same as Lab 2 – observe synthesis result again and explore optimizations.

<div align="center"> <img src="https://github.com/user-attachments/assets/157b16d3-cecd-441a-aacf-bae296910886" width="70%"> </div>
🧪 Lab 4: Nested Ternary Logic Simplification

```bash
module opt_check4 (input a , input b , input c , output y);
 assign y = a?(b?(a & c ):c):(!c);
 endmodule
```


🔍 Logic Simplifies To:
y = a ? c : !c — use synthesis tools to confirm this!
<div align="center"> <img src="https://github.com/user-attachments/assets/08d1e447-78c6-47c4-8c99-239645b38617" width="70%"> </div>
🧪 Lab 5: D Flip-Flop with Constant Load

```bash
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end
endmodule
```
🧠 Flip-flop always sets output to 1 unless reset.
Observe how synthesis tools handle such constant behavior.

<div align="center"> <img src="https://github.com/user-attachments/assets/a42fac06-a092-4efc-be39-33b263caaaa1" width="70%"> </div>
🧪 Lab 6: Always 1 Flip-Flop

```bash
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b1;
	else
		q <= 1'b1;
end
endmodule
```
🤔 No matter what — output q is always 1.
Tools will likely optimize this away completely!

<div align="center"> <img src="https://github.com/user-attachments/assets/ae45f7db-0a7f-4256-b43b-01cc4a1588f7" width="70%"> </div>
✅ Summary

🎯 Today’s mission: You learned how to make RTL designs leaner, faster, and smarter using key optimization techniques:| Technique                   | Purpose                                   |
| --------------------------- | ----------------------------------------- |
| 🔢 **Constant Propagation** | Replaces known values to simplify logic   |
| 🎛️ **State Optimization**  | Shrinks and simplifies FSMs               |
| 🧬 **Cloning**              | Improves timing by duplicating logic      |
| 🕹️ **Retiming**            | Balances path delays by moving flip-flops |

