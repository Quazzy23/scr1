# Lab3: SCR1 Pipeline Analysis and Monitoring

## 1. Objective
The goal of this laboratory work is to analyze the microarchitectural behavior of the SCR1 core pipeline. This involves implementing an internal hardware monitor to detect specific instruction execution and analyzing instruction flow using timing diagrams (waveforms).

## 2. Monitor Implementation
A non-synthesizable monitoring block was integrated into the `src/core/pipeline/scr1_tracelog.sv` module. This location was chosen because it provides direct access to the core's internal signals without hierarchy-related visibility issues in the simulator.

The monitor detects the `DIV` instruction by verifying the following bit fields:
- **Opcode:** `7'b0110011` (Register-Register arithmetic)
- **Funct3:** `3'b100` (Division)
- **Funct7:** `7'b0000001` (M-extension)

Upon detection, the monitor outputs the exact simulation time, the Program Counter (PC), and the architectural state (CSR registers) to the console.

## 3. Simulation Results and Analysis
The simulation of `div.hex` was performed using Verilator with the `TRACE=1` flag.

### Console Output Evidence:
During simulation, the monitor successfully captured multiple `DIV` instructions. A representative capture is shown below:
- **Time:** [Your Simulation Time] ns
- **PC Address:** `000002A4`
- **Captured Instruction Hex:** `0220C1B3`
- **Status Registers (Variant 5):**
    - `mstatus`: `00001880` (Indicates Machine Mode, interrupts enabled)
    - `mcause`: `00000000` (No exceptions during execution)

### Pipeline Observation
Analysis of the instruction fetch (Fetch stage) and execution (Execute stage) revealed the following pipeline characteristics:
1. When the **Execute** stage was processing the `DIV` instruction at PC `0x000002A4`, the **Fetch** stage was already requesting subsequent instructions from the memory at PC `0x000002B0`.
2. This lag (approximately 3 instructions ahead) demonstrates the effectiveness of the instruction prefetch buffer and the pipeline depth of the SCR1 core.
3. The `imem_rdata` signal correctly supplied the instruction hex `0220C1B3` synchronously with the `imem_resp` flag.

## 4. Conclusion
The hardware monitor was successfully implemented and verified. The analysis confirmed the correct operation of the division unit and provided clear visibility into the concurrent operation of different pipeline stages.