# SDRAM Controller

## Overview

This README describes the basic principles of an SDRAM (Synchronous Dynamic Random-Access Memory) controller design, including operations like writing, reading, and refreshing DRAM cells. It also covers technical insights into DRAM cell behavior and structure.

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-26%20122526.png" alt="DRAM" width="350"/>

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

### Types of Refresh:
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

---


## Second Generation DRAM Cell

