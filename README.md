# AXI-DMA-Based-Data-Transfer-and-Custom-IP-Integration-on-PYNQ-Z2


## Overview

This repository contains three FPGA hardware acceleration experiments implemented on the PYNQ-Z2 board using AXI DMA and AXI4-Stream interfaces. The objective of these experiments was to understand communication between the Processing System (PS) and Programmable Logic (PL) of the Zynq-7000 SoC and to develop custom hardware accelerators that can be controlled from Python through Jupyter Notebook.

The experiments implemented are:

1. FIFO Generator IP Verification
2. 8-bit Inverter with Combinational AXI-Stream Wrapper
3. Radix-4 Booth Multiplier with FSM-Based AXI-Stream Wrapper

---

# Hardware Platform

- PYNQ-Z2 Board
- Zynq-7000 SoC
- Ethernet Cable
- MicroUSB Cable
- MicroSD Card
- Host PC/Laptop

---

# Software Tools

- Xilinx Vivado
- PYNQ Linux Image
- Jupyter Notebook
- Python
- NumPy
- PYNQ Library
- PuTTY Terminal

---

# Complete Development Flow

```text
RTL Design
    ↓
Create Custom IP (if required)
    ↓
Vivado Block Design
    ↓
ZYNQ Processing System Integration
    ↓
AXI DMA Integration
    ↓
AXI4-Stream Connections
    ↓
Create HDL Wrapper
    ↓
Generate Bitstream
    ↓
Generate .bit and .hwh Files
    ↓
Upload Files to PYNQ Board
    ↓
Execute Using Python
```

---

# Initial Board Setup

## Step 1: Flash PYNQ Image

Download the PYNQ image and flash it onto the MicroSD card using Win32 Disk Imager.

Insert the MicroSD card into the PYNQ-Z2 board.

---

## Step 2: Hardware Connections

Connect:

- MicroUSB cable for power
- Ethernet cable between host PC and PYNQ-Z2 board

Power ON the board.

---

## Step 3: Connect Through PuTTY

Open PuTTY and establish a serial connection to the board.

Obtain the board IP address:

```bash
hostname -I
```

or

```bash
ifconfig
```

Example:

```text
192.168.1.100
```

---

## Step 4: Open Jupyter Notebook

Open a browser and enter:

```text
http://192.168.1.100:9090
```

The PYNQ Jupyter Notebook dashboard should appear.

---

# Vivado Hardware Design Flow

For all three tasks, the following embedded design flow was used:

1. Create Vivado Project
2. Open IP Integrator
3. Add ZYNQ7 Processing System
4. Run Block Automation
5. Add AXI DMA
6. Add FIFO IP or Custom IP
7. Connect AXI4-Stream Interfaces
8. Validate Design
9. Create HDL Wrapper
10. Generate Bitstream
11. Obtain `.bit` and `.hwh` files

---

# Task 1: FIFO Generator IP Verification

## Objective

Verify AXI DMA functionality and AXI4-Stream communication using the Xilinx FIFO Generator IP.

## Architecture

```text
DDR Memory
     ↓
AXI DMA
     ↓
FIFO Generator IP
     ↓
AXI DMA
     ↓
DDR Memory
```

## Design Steps

1. Create Vivado Project
2. Open IP Integrator
3. Add ZYNQ Processing System
4. Run Block Automation
5. Add AXI DMA
6. Add FIFO Generator IP
7. Connect AXI Stream Interfaces
8. Validate Design
9. Create HDL Wrapper
10. Generate Bitstream

## Expected Result

The FIFO performs buffering only.

```text
Input Data = Output Data
```

This experiment verifies:

- DMA functionality
- AXI4-Stream communication
- FIFO operation
- End-to-end data transfer

---

# Task 2: 8-Bit Inverter with Combinational AXI-Stream Wrapper

## Objective

Implement a custom combinational hardware accelerator and integrate it into the AXI DMA data path.

## RTL Design

The inverter performs a bitwise NOT operation.

```verilog
assign dout = ~din;
```

## Custom IP Creation

1. Write inverter RTL
2. Design AXI-Stream Wrapper
3. Package as Custom IP
4. Add IP to Vivado IP Catalog

## BLOCK DESIGN 

<img width="1600" height="639" alt="WhatsApp Image 2026-06-10 at 15 39 47" src="https://github.com/user-attachments/assets/6dde6d19-ef2f-455b-a977-6848b76aa7c7" />

## Architecture

```text
DDR Memory
     ↓
AXI DMA
     ↓
AXI-Stream Wrapper
     ↓
8-bit Inverter
     ↓
AXI DMA
     ↓
DDR Memory
```

## Wrapper Function

The AXI wrapper:

- Receives AXI Stream data
- Extracts 8-bit input
- Applies inversion
- Sends inverted data back through AXI Stream

Since the inverter is purely combinational, no FSM is required.

## Example

```text
Input  = 0x55
Output = 0xAA
```

---

# Task 3: Radix-4 Booth Multiplier with FSM-Based AXI-Stream Wrapper

## Objective

Implement a multi-cycle arithmetic accelerator using the Radix-4 Booth multiplication algorithm.

## Custom IP Creation

1. Write Radix-4 Booth Multiplier RTL
2. Design FSM-Based AXI Wrapper
3. Package as Custom IP
4. Add IP to Vivado IP Catalog
5. Integrate into Vivado Block Design

## Architecture

```text
DDR Memory
     ↓
AXI DMA
     ↓
FSM AXI Wrapper
     ↓
Radix-4 Booth Multiplier
     ↓
AXI DMA
     ↓
DDR Memory
```

## FSM Operation

```text
IDLE
  ↓
RECEIVE_DATA
  ↓
START_MULTIPLICATION
  ↓
WAIT_FOR_DONE
  ↓
SEND_RESULT
  ↓
IDLE
```

## Why FSM?

Unlike the inverter, the Booth multiplier requires multiple clock cycles to complete computation.

The FSM manages:

- Operand reception
- Computation start
- Completion detection
- Result transmission

---

# Bitstream Generation

After validating the block design:

## Create HDL Wrapper

```text
Block Design
    ↓
Create HDL Wrapper
```

Vivado generates:

```text
design_wrapper.v
```

This is the top-level HDL module for the design.

---

## Generate Bitstream

```text
Flow Navigator
    ↓
Generate Bitstream
```

Vivado performs:

```text
Synthesis
    ↓
Implementation
    ↓
Bitstream Generation
```

---

# Generated Files

After successful bitstream generation:

```text
design.bit
design.hwh
```

## design.bit

Programs the FPGA fabric.

## design.hwh

Contains hardware metadata including:

- IP hierarchy
- AXI interfaces
- DMA information
- Address mapping
- Register descriptions

PYNQ uses the `.hwh` file to automatically discover hardware IP blocks and expose them through the Overlay API.

---

# Uploading Files to PYNQ

Upload the following files into the Jupyter workspace:

```text
design.bit
design.hwh
input_data.hex
task.ipynb
```

---

# Python Execution Flow

## Import Libraries

```python
import numpy as np
from pynq import Overlay, allocate
```

## Load Overlay

```python
overlay = Overlay("design.bit")
```

## Access DMA

```python
dma = overlay.axi_dma_0
```

## Read Input Data

```python
with open("input_data.hex", "r") as f:
    data = f.readlines()
```

## Allocate DMA Buffers

```python
in_buffer = allocate(shape=(N,), dtype=np.uint32)
out_buffer = allocate(shape=(N,), dtype=np.uint32)
```

## Execute DMA Transfer

```python
dma.recvchannel.transfer(out_buffer)
dma.sendchannel.transfer(in_buffer)

dma.recvchannel.wait()
dma.sendchannel.wait()
```

## Save Output

```python
with open("output.hex", "w") as f:
    for value in out_buffer:
        f.write(f"{value:08X}\n")
```

## Release Buffers

```python
in_buffer.free()
out_buffer.free()
```

---





