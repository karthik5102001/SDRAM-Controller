# SDRAM Controller

## Overview

This README describes the basic principles of an SDRAM (Synchronous Dynamic Random-Access Memory) controller design, including operations like writing, reading, and refreshing DRAM cells. It also covers technical insights into DRAM cell behavior and structure.

![DRAM](https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-26%20122526.png)

---

## Write Operation

To perform a write operation in DRAM:

- **Enable the Wordline** (closes the circuit).
- Set the **Bitline** to `VDD` (for logic 1) or `GND` (for logic 0).
- Current flows **into or from the capacitor**, depending on the bitline voltage.
- The charge/discharge behavior depends on **oxide thickness**, **operating voltage**, and other physical properties.

![Sense Amplifier](https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-27%20105808.png)

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

![Rows and Column](https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-26%20193738.png)

![Micro Archi](https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-26%20193820.png)

### READ Signals

![READ Waveform](https://github.com/karthik5102001/SDRAM-Controller/blob/main/Img/Screenshot%202025-05-26%20194203.png)

---

## Summary

This SDRAM controller outlines the core operations essential for DRAM:
- Writing and reading with Wordline and Bitline interactions
- Managing destructive reads
- Implementing periodic refresh mechanisms
- Understanding physical constraints of first-generation DRAM cells
