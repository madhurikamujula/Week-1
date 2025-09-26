# 🚀 Day 2: Timing Libraries, Synthesis Approaches, and Efficient Flip-Flop Coding

Welcome to **Day 2** of the RTL Design & Synthesis Workshop!  
Today, you’ll dive into three critical aspects of digital hardware design:

- 🧠 Understanding **timing libraries** in the SKY130 PDK  
- 🏗️ Comparing **Hierarchical vs. Flattened Synthesis** approaches  
- 🔁 Learning **efficient and synthesizable flip-flop coding styles**

This session combines **real-world PDK insights**, **RTL best practices**, and **tool-driven experiments**.

---

## 📚 Contents

- [⏱️ Timing Libraries](#⏱️-timing-libraries)
  - [📦 SKY130 PDK Overview](#📦-sky130-pdk-overview)
  - [🔍 Decoding tt_025C_1v80](#🔍-decoding-tt_025c_1v80)
  - [🧾 Opening the .lib File](#🧾-opening-and-exploring-the-lib-file)

- [🏗️ Hierarchical vs. Flattened Synthesis](#🏗️-hierarchical-vs-flattened-synthesis)
  - [📁 Hierarchical Synthesis](#📁-hierarchical-synthesis)
  - [📉 Flattened Synthesis](#📉-flattened-synthesis)
  - [📊 Key Differences](#📊-key-differences)

- [🔁 Flip-Flop Coding Styles](#🔁-flip-flop-coding-styles)
  - [🔄 Async Reset D Flip-Flop](#🔄-asynchronous-reset-d-flip-flop)
  - [🆙 Async Set D Flip-Flop](#🆙-asynchronous-set-d-flip-flop)
  - [⏱️ Sync Reset D Flip-Flop](#⏱️-synchronous-reset-d-flip-flop)

- [🧪 Simulation & Synthesis Workflow](#🧪-simulation-and-synthesis-workflow)
  - [🧬 Simulation using Icarus Verilog](#🧬-icarus-verilog-simulation)
  - [🛠️ Synthesis using Yosys](#🛠️-synthesis-with-yosys)

---

## ⏱️ Timing Libraries

### 📦 SKY130 PDK Overview

The **Sky130 Process Design Kit (PDK)** from SkyWater is an open-source CMOS PDK for IC fabrication at 130nm node.

It includes:

- Cell libraries
- Timing models
- Power analysis data
- Process-voltage-temperature (PVT) corners

---

### 🔍 Decoding `tt_025C_1v80` in SKY130

| Segment         | Meaning                          |
|-----------------|----------------------------------|
| `tt`            | **Typical** process corner        |
| `025C`          | Temperature at **25°C**           |
| `1v80`          | Operating voltage at **1.8V**     |

This naming convention tells synthesis tools the **conditions** under which the timing and power data apply.

---

### 🧾 Opening and Exploring the .lib File

#### 🛠️ Step 1: Install GUI Editor

```bash
sudo apt install gedit
```
📂 Step 2: Open the Liberty File
```bash
gedit sky130_fd_sc_hd__tt_025C_1v80.lib
```
<div align="center"> <img src="https://github.com/user-attachments/assets/0c31ddf8-8a95-44a4-acaa-e1c5f0518425" width="70%"> </div>

🏗️ Hierarchical vs. Flattened Synthesis
📁 Hierarchical Synthesis

Definition: Keeps your module hierarchy intact.

How it Works: Each RTL module is synthesized independently using commands like hierarchy.

✅ Pros

Modular design & faster synthesis

Easier to debug and trace back to RTL

Scales better for large systems

❌ Cons

Limits cross-module optimization

Tool reporting might require tweaking
<div align="center"> <img src="https://github.com/user-attachments/assets/91f0244a-2c41-42ea-be6f-468880c3af33" width="70%"> </div>

📉 Flattened Synthesis

Definition: Combines all RTL modules into one flat structure.

How it Works: The flatten command collapses hierarchy in Yosys.

✅ Pros

Full-chip optimization across modules

Great for final synthesis before layout

❌ Cons

Slower synthesis time

Harder to debug and trace netlist

Complex, memory-heavy outputs
<div align="center"> <img src="https://github.com/user-attachments/assets/e1d94a5d-d3f7-41ee-8e69-ca0c05be81a3" width="70%"> </div>

🔎 Note: Flattening is ideal for final optimization, while hierarchical is great for development and debugging.

🔁 Flip-Flop Coding Styles

🔄 Asynchronous Reset D Flip-Flop
```bash
module dff_asyncres (input clk, input async_reset, input d, output reg q);
  always @ (posedge clk, posedge async_reset)
    if (async_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
Reset acts independent of clock

Good for immediate resets in FSMs

🆙 Asynchronous Set D Flip-Flop
```bash
module dff_async_set (input clk, input async_set, input d, output reg q);
  always @ (posedge clk, posedge async_set)
    if (async_set)
      q <= 1'b1;
    else
      q <= d;
endmodule
```
Sets output to 1 regardless of clock edge

⏱️ Synchronous Reset D Flip-Flop
```bash
module dff_syncres (input clk, input async_reset, input sync_reset, input d, output reg q);
  always @ (posedge clk)
    if (sync_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
Reset happens only on clock edge

Common in pipelined or synchronous systems

🧪 Simulation and Synthesis Workflow
🧬 Icarus Verilog Simulation
Step-by-Step:

Compile:
```bash
iverilog dff_asyncres.v tb_dff_asyncres.v
```
Run:
```bash
./a.out
```
View waveform:
```bash
gtkwave tb_dff_asyncres.vcd
```
<div align="center"> <img src="https://github.com/user-attachments/assets/1176581e-fd6c-4b71-8af5-5d7d5f6dbcda" width="70%"> </div>
🛠️ Synthesis with Yosys
# Step 1: Start Yosys
```bash
yosys
```
# Step 2: Read Liberty timing library
```bash
read_liberty -lib /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
```

# Step 3: Load your Verilog design
```bash
read_verilog /path/to/dff_asyncres.v
```

# Step 4: Run synthesis
```bash
synth -top dff_asyncres
```

# Step 5: Map flip-flops
```bash
dfflibmap -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
```

# Step 6: Perform tech mapping
```bash
abc -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
```

# Step 7: View netlist
```bash
show
```
<div align="center"> <img src="https://github.com/user-attachments/assets/fa8337df-e0ec-4b01-9b18-5910768e4421" width="70%"> </div>
✅ Summary

🎯 In Day 2, you explored the foundation of real-world synthesis:

📘 Understood .lib files and PVT corners

🏗️ Compared hierarchical vs. flattened synthesis strategies

🔁 Wrote synthesizable, real-world flip-flops

🧪 Simulated with iverilog

🛠️ Synthesized flip-flops

