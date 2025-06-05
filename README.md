# SDRAM Controller

## Table of Contents

- [First Generation DRAM cell](#first-generation-dram-cell)
- [Second Generation DRAM cell](#second-generation-dram-cell)
- [Third Generation DRAM cell](#third-generation-dram-cell)

## Overview

This README describes the basic principles of an SDRAM (Synchronous Dynamic Random-Access Memory) controller design, including operations like writing, reading, and refreshing DRAM cells. It also covers technical insights into DRAM cell behavior and structure.

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-26%20122526.png" alt="DRAM" width="300"/>

---

## Write Operation

To perform a write operation in DRAM:

- **Enable the Wordline** (closes the circuit).
- Set the **Bitline** to `VDD` (for logic 1) or `GND` (for logic 0).
- Current flows **into or from the capacitor**, depending on the bitline voltage.
- The charge/discharge behavior depends on **oxide thickness**, **operating voltage**, and other physical properties.

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-27%20105808.png" alt="Sense Amplifier" width="350"/>

---

## Reading New Data

Reading from DRAM involves:

1. **Precharge the Bitline to VDD/2**.
2. **Enable the Wordline** to close the circuit.
3. Depending on the capacitor charge:
   - If the capacitor is charged (logic 1), current flows **from the capacitor to the Bitline**, raising its voltage → detected as `1`.
   - If discharged (logic 0), current flows **from the Bitline to the capacitor**, dropping voltage → detected as `0`.

The **Sense Amplifier** uses VDD/2 as a reference:
- `VDD/2 - small delta` → `0`
- `VDD/2 + small delta` → `1`

---

## Destructive Read

Reading is a **destructive operation**:
- Reading a `1` discharges the capacitor slightly.
- Reading a `0` can inadvertently inject charge.

**Solution**: After reading, **refresh** the cell by rewriting the detected value back into the capacitor.

---

## Periodic Refreshing of DRAM Cells

Due to **leakage** and destructive reads, **refreshing** is required.

## Types of Refresh:
- **Auto Refresh**: Performed during read/write operations.
- **Self Refresh**: Triggered during power-down or frequency changes.

For a design with **4096 rows** and a **64ms refresh window**, the refresh interval per row is:

```
64 ms / 4096 = 15.62 μs per row
```

---

## First Generation DRAM Cell

- Size: **1024 bits**
- Configuration: **32 rows × 32 columns**
- Address Pins:
  - **5 pins for Row**
  - **5 pins for Column**

- **Column lines**: Bitlines  
- **Row lines**: Wordlines

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-26%20193738.png" alt="Rows and Column" width="350"/>

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-26%20193820.png" alt="Micro Archi" width="350"/>

### 3 Transistor DRAM Cell

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/3T%20DRAM%20Cell.png" alt="3T DRAM Cell" width="350"/>
- It consist of separate column and row lines for read and write.

### READ Operation (1K DRAM Cell)

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-26%20194203.png" alt="READ Waveform" width="350"/>

- Set **R/W = High** for Read, and **CE = Low** to enable the chip.
- **tRC (Read Cycle Time)**:  
  - Defines how fast memory can read.  
  - *Example*: If `tRC = 500ns`, DRAM can supply 1-bit words at a 2 MHz rate.
- **tAC (Access Time)**:  
  - Maximum delay after the address is set before valid data appears on `Dout`.

---

###  WRITE Operation (1K DRAM Cell)

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/write.png" alt="Write Waveform" width="350"/>

- Set **R/W = Low** to perform a write operation.
- **tWC (Write Cycle Time)**:  
  - Determines how quickly a write can be completed.
- **tWP (Write Pulse Width)**:  
  - Duration for which data must be held stable during write.
- **tAW (Address to Write Delay)**:  
  - Minimum delay between setting the address and starting the write (R/W goes low).
- **R/W signal acts like a clock** during write operations.

---

###  REFRESH Operation (1K DRAM Cell)

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/ref.png" alt="Refresh Waveform" width="350"/>

- Set **CE = High**, then change the address to target the row to be refreshed.
- **R/W acts as a strobe or clock** during refresh.
- Internally, data is **read** and **rewritten** to restore charge levels and preserve data integrity.
- For REFRESH operation we actually give **column** address first and then **row** address (which indicate REFRESH operation).
- For normal operation we give **Row** address first and **Column** address next.

---

### Disadvantage 
- No Clock, will restrict the speed of operation.
- Lesser memory access.
- Separate Row and Column pins, which further if we need to improve the design (adding more memory) we have to increase the pins, which increase the space.

---

## Second Generation DRAM Cell

- The major difference from the first generation is the use of a **single transistor per cell** to store data.
- Offers improvements in flexibility and performance with support for:
  - **Multiple address inputs**
  - **Multiple memory arrays**
  - Advanced operation modes:  
    - **Fast Page Mode**,  
    - **Extended Data Out (EDO)**,  
    - **Static Column Mode**
- Typical memory size is **4K** (4096 x 1-bit), providing 4096 addressable locations with 1-bit word size.
  
  <img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Single%20T.png" alt="1T" width="300"/>
  <img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/2G.png" alt="2G" width="350"/>
  <img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/2G%20(2).png" alt="2G" width="250"/>

### Advantages:

- Using a single transistor reduces **dynamic power consumption** compared to 3-transistor cells.
- Storing individual bits in separate transistors allows for **compact storage**.
- However, more transistors per wordline increase **resistance**, which can **decrease frequency**.
- **Solution**: Replicate the memory structure (dual arrays).
  - Enables **double data extraction** from both arrays.
  - Allows **single-bit access** by disabling one of the arrays.
    <div style="display: flex; gap: 10px;">
  <img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-26%20193738.png" alt="Image 1" width="200"/>
  <img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-26%20193738.png" alt="Image 2" width="200"/>
</div>

### Address & Control:

- **Row Count**: 64 → requires **6-bit address bus**
- **Column Count**: 32 → accessed using **6 pins total**
- Additional control signals:
  - **CAS** (Column Address Strobe)
  - **RAS** (Row Address Strobe)
  - Used for **address multiplexing**

### Disadvantages:

- No clock usage.
- Still limited memory access speed compared to later generations.

---

## Third Generation DRAM Cell:

We have **multiple BANKS instead of arrays** to improve pipelining during memory access.

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/3G.png" alt="3G" width="350"/>

**Memory Matrix Structure**:  
- **(4096 x 256 x 16)** → *(rows × columns × data width)*  
  - `4096`: Number of rows  
  - `256`: Number of columns  
  - `16`: Number of arrays or **I/O data bus width**  
    - We can write or read 16 bits at a time.  
    - "16 arrays" means there are 16 bit-lines available for simultaneous access → **16-bit data bus**.

So, a **single memory matrix** contains 256 columns and 4096 rows, and all 16 arrays are combined to form a **BANK**.

> **Advantage of Multiple Banks**:  
> If one bank is busy (e.g., doing preprocessing or refresh), other banks can still handle read/write operations — improving performance via parallelism.

**Total Memory**:  
`4096 rows × 256 columns × 16 bits = 16,777,216 bits = 2 MB`

**Architecture Features**:  
- **Independent Row and Column MUX**  
- **Auto Refresh Logic**:  
  - Unlike second-generation DRAM, we **do not need to supply a column address** before refresh.  
  - SDRAM includes a **built-in refresh counter**, and we simply issue a **refresh command**.
- **Synchronous DRAM (SDRAM)**:  
  - Clock is added for synchronization — hence **"synchronous"**.  
  - We send commands, and the **controller handles signal transmission to SDRAM**.

 <img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/CMD_3G.png" alt="Commands" width="450"/>
  
