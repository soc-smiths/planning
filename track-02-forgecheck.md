# Track 02 — Forge Check

## Status: DRAFT v1

---

## Part 1 — Recall Round

1. Why is register x0 hardwired to zero in RISC-V?
   ✅ Simplifies instruction encoding and hardware (e.g. NOP, MOV via
   ADD with x0, comparisons) without needing special-case instructions.

2. True or False: RV32I and RV32E use the same number of general-
   purpose registers.
   ✅ False — RV32E has 16 registers (x0–x15), RV32I has 32.

3. In the R-type instruction format, which fields together determine
   the exact operation performed?
   A) opcode only  B) opcode + funct3  C) opcode + funct3 + funct7
   D) rd + rs1
   ✅ C

4. True or False: B-type (branch) immediates are encoded as a simple
   contiguous bit field, same as I-type.
   ✅ False — B-type immediate bits are scrambled/non-contiguous in
   the encoding for hardware decode efficiency.

5. What does JALR do, and which register typically holds the address
   used for a function return?
   ✅ Jumps to a register+offset address and saves the return address;
   `ra` (x1) typically holds the return address.

6. True or False: RISC-V traps on integer division by zero.
   ✅ False — DIV/DIVU by zero returns a defined result (all 1s) rather
   than trapping.

7. What problem does the C (compressed) extension primarily solve?
   ✅ Code density — reduces instruction-fetch bandwidth and program
   size using 16-bit encodings for common instructions.

8. True or False: most small embedded RISC-V cores implement all three
   privilege modes (M/S/U).
   ✅ False — many implement M-mode only, since full multi-mode support
   is unnecessary overhead for simple embedded use cases.

9. What is a CSR, and how does CSRRW differ from a normal register
   write?
   ✅ Control and Status Register — special-purpose register accessed
   via dedicated CSR instructions; CSRRW atomically swaps a value in
   while returning the old value, unlike an ordinary register write.

10. Which CSR holds the cause of the most recent trap?
    ✅ mcause

11. True or False: exceptions and interrupts are handled identically
    because both use the same trap mechanism.
    ✅ False — mechanism is similar, but exceptions are synchronous
    (caused by the current instruction) while interrupts are
    asynchronous (external events); mcause distinguishes them.

12. What does the FENCE instruction do, conceptually?
    ✅ Enforces ordering constraints on memory operations before/after
    it, relevant for DMA/multi-core/memory-mapped I/O correctness.

13. True or False: RV32IMC is a common choice for microcontroller-class
    cores.
    ✅ True — integer base + multiply/divide + compressed instructions,
    a practical balance for small embedded cores.

14. What's the difference between LW and LB in terms of what ends up
    in the destination register?
    ✅ LW loads a full 32-bit word; LB loads a single byte and sign-
    extends it to fill the register.

15. True or False: `objdump` can be used to disassemble a compiled
    binary back into assembly instructions.
    ✅ True.

---

## Part 2 — Applied Round

1. Given the raw instruction hex `0x00A50533`, walk through identifying
   the opcode field and explain the general process you'd use to fully
   decode it (exact mnemonic not required — process must be correct).
   ✅ Extract bits [6:0] as opcode, use it to determine format (R-type
   here based on opcode value), then extract funct3/funct7/rd/rs1/rs2
   fields per the R-type layout and cross-reference the ISA manual's
   opcode table.

2. A function's disassembly shows `sw ra, 0(sp)` near the start and
   `lw ra, 0(sp)` near the end, with `addi sp, sp, -16` / `addi sp,
   sp, 16` bracketing them. Explain what's happening and why.
   ✅ Standard prologue/epilogue: saving the return address to the
   stack (since the function calls another function and would
   otherwise clobber ra) and allocating/deallocating stack space,
   restoring ra before returning.

3. A trap handler reads mcause and gets a value indicating an
   "illegal instruction" exception. What are two realistic causes of
   this in a real system?
   ✅ e.g. Program counter jumped to invalid/corrupted memory (executing
   data as instructions), or code compiled for an extension not
   implemented by the actual core (e.g. using M-extension instructions
   on a core without it).

4. Why would a chip designer choose RV32E over RV32I for a specific
   product, and what's the real cost of that choice?
   ✅ Chosen for area/register-file size savings in extremely
   constrained embedded targets; cost is fewer registers available,
   more spilling to memory, potentially more instructions/less performance.

5. Explain why FENCE instructions matter even on a single-core system
   with no other CPU, using a concrete example involving a peripheral.
   ✅ E.g. writing a DMA descriptor then triggering DMA start — without
   ordering guarantees, the write to memory might not be visible to
   the DMA engine yet when the trigger write occurs; FENCE ensures
   ordering.

---

## Part 3 — Practical Proof

**Task 1 — Manual instruction decode**
Given three provided raw 32-bit hex instructions, manually decode each
to its full assembly mnemonic and operands, showing the field
extraction work (not just the final answer).

**Task 2 — Assembly from scratch**
Write a RISC-V assembly function (no C, no compiler) that computes the
sum of an array of integers, correctly following the standard calling
convention (proper prologue/epilogue, correct argument/return registers).

**Task 3 — Trap handler trace**
Given a provided minimal trap handler and a triggering scenario (in
`/quizzes/track-02-trap`), trace exactly which CSRs are read/written
and in what order, and identify the trap cause from the provided
mcause value.

**Task 4 — Disassembly detective work**
Given a provided stripped binary and GDB register dump at a crash
point (no source code), identify the likely bug class (e.g. stack
corruption, bad jump target, ABI violation) using only objdump output
and register state.

---

## Completion state
Shown as: "Forge Check — Track 02: Cleared ✓" once all three parts
are attempted.
