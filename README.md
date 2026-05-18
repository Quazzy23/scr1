# Lab 2: SCR1 Core Configuration and Exception Handling

## Objective
The goal of this lab is to configure the hardware parameters of the SCR1 RISC-V core and modify the exception handling mechanism. The task involves changing the Reset and Trap vectors and implementing a custom output for an Illegal Instruction exception.

## Task Description
- **Core:** SCR1 (Syntacore)
- **Reset Vector:** `0x00002000` (Address where the core starts after reset).
- **Trap Vector:** `0x00001880` (Address where the core jumps upon an exception).
- **Custom Handler Output:** Output the string `"illexc"` when an **Illegal Instruction** is detected.

## Configuration Details

### 1. Hardware Configuration
The addresses were modified in the core description file:
- **File:** `src/includes/scr1_arch_description.svh`
- **Parameter `SCR1_ARCH_RST_VECTOR`**: Set to `32'h2000`
- **Parameter `SCR1_ARCH_MTVEC_BASE`**: Set to `32'h1880`

### 2. Linker and Macros Adjustment
To align the software with the hardware changes, the following files were updated:
- **`sim/tests/common/link.ld`**: Set `ORIGIN` to `0x0` to allow absolute positioning using `.org` directives.
- **`sim/tests/common/riscv_macros.h`**: 
    - Updated `.org` offsets to account for a `0x100` byte linker shift discovered during debugging.
    - Implemented a custom loop in `trap_vector` to write ASCII codes for `"illexc"` to the simulation console address `0x006C0000`.

## Results
The configuration was verified using the `isa/rv32mi/illegal.S` test.

### Simulation Output
The simulation successfully reached the **PASS** state. The trace log confirms that the processor jumped to the correct trap address and executed the custom print logic.

**Trace Log Evidence:**
- Time 6: `Curr_PC = 2000` (Successful Reset)
- Time 41: `mcause = 00000002` (Illegal Instruction detected)
- Time 44: `Curr_PC = 1880` (Jumped to custom Trap Vector)
- Time 50-72: Characters `69, 6C, 6C, 65, 78, 63` (`illexc`) written to memory.

### Waveform Analysis
The GTKWave shows the `core2imem_addr_o` signal jumping to `0x1880` and the subsequent data writes on the `core2dmem_wdata_o` bus.

## Conclusion
The SCR1 core was successfully reconfigured. The hardware vectors were moved to non-standard addresses, and the software environment was adjusted to match. The custom exception handler correctly identified the Illegal Instruction and performed the requested output.