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


### READ to 1K DRAM cell

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-26%20194203.png" alt="READ Waveform" width="350"/>
- Put R/W high (Read) and Chip Select (CE) in low state
- **tRC(Read Cycle Time)** : tRC tells how fast memory can READ 
  - **Eg** : if tRC is 500ns, then DRAM can supply 1 bit word at the rate of 2 Mhz.
- **tAC(Access Time)** : Maximum length of time after the input address is changed before the output data(Dout) is valid.

### WRITE to 1K DRAM cell

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/write.png" alt="Write Waveform" width="350"/>
- R/W to low (Write operation).
- **tWC(Write Cycle Time)** : Tells how fast memory can write. 
- **tWP(Write Pulse Width)** : specifies how long the input data must be present before the next read or write operation. 
- **tAW(Address to Write Delay time)** : time between the address changing and the R/W input going low.
- While writing we think R/W as input as a **clock** signal.

### REFRESH the 1K DRAM cell

<img src="https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/ref.png" alt="Write Waveform" width="350"/>
- CE is pulled **HIGH**, the address is changed.
- R/W is used as **strobe or clock**.
- Internally, the data is read out and then written back into the same location at full voltage, by this way the logic levels are restored(or refreshed).

---

## Second Generation DRAM Cell

