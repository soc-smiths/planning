# Track 03 — RISC-V GNU Toolchain Internals

## Status: DRAFT v1 — for team review
## Standard: Builds on Tracks 00–02. Industry depth throughout.

## Why this track exists
This is where "I can write C and understand the ISA" becomes "I know
exactly what every stage between source code and a flashable binary
does, and can fix it when it breaks." Toolchain problems are one of
the most common real blockers in embedded work, and almost never
taught properly — most courses just say "run this command."

---

## Module 1 — Anatomy of the RISC-V GNU Toolchain
1.1 What "the toolchain" actually is: it's not one program — binutils,
    GCC, a C library (newlib/picolibc/glibc), and GDB are separate
    projects glued together
1.2 Why RISC-V toolchains are typically built as one bundle
    (riscv-gnu-toolchain repo) rather than installed piece by piece
1.3 Cross-compilation concept, precisely: host (where you compile) vs.
    target (where it runs) vs. build (rare third case) — and why this
    distinction breaks naive assumptions from desktop programming
1.4 Toolchain naming convention decoded: `riscv32-unknown-elf-gcc` —
    what each field (arch-vendor-os-abi) actually means and why

## Module 2 — Building the Toolchain From Source
2.1 Why you'd build from source instead of using a prebuilt package
    (target ISA/ABI customization, latest features, reproducibility)
2.2 Newlib vs. Linux-targeted builds — why bare-metal work uses the
    `elf`/newlib target, not `linux-gnu`
2.3 Step-by-step build walkthrough: dependencies, configure flags,
    what each major flag controls (--with-arch, --with-abi)
2.4 Common build failures and how to actually diagnose them (missing
    dependency vs. version mismatch vs. bad configure flag)

## Module 3 — The Compilation Pipeline, Stage by Stage
3.1 Preprocessing: macro expansion, #include resolution, conditional
    compilation — viewing real preprocessor output (`-E`)
3.2 Compiling proper: C source to assembly (`-S`), reading and
    understanding the output (ties directly to Track 02 Module 17)
3.3 Assembling: assembly to object code (`-c`), what an object file
    contains at this intermediate stage
3.4 Linking: combining object files and libraries into a final
    executable — the stage most learners don't actually understand
3.5 Running each stage manually and independently, rather than always
    letting gcc do all four silently — removes the "magic"

## Module 4 — Linker Scripts, Properly
4.1 What a linker script actually controls: memory regions, section
    placement, symbol definition
4.2 MEMORY command: defining flash/RAM regions with correct origin/length
4.3 SECTIONS command: mapping .text, .data, .bss, .rodata into memory
    regions, VMA vs. LMA explained with a concrete flash-to-RAM example
4.4 Symbols defined in linker scripts (`_end`, `_stack_top`, etc.) and
    how startup code (Track 01 Module 9) actually uses them
4.5 Writing a complete, correct linker script from scratch for a
    simple memory map
4.6 Common linker script mistakes: overlapping regions, forgetting
    alignment, orphan sections

## Module 5 — Object Files & the ELF Format
5.1 ELF header structure: what's actually in the first bytes of every
    ELF file
5.2 Sections vs. segments — a genuinely confusing distinction, explained
    clearly with why both exist (linking-time view vs. loading-time view)
5.3 The symbol table: what's in it, how symbols get resolved across
    object files
5.4 Relocations: what they are and why they're necessary before final
    addresses are known
5.5 Reading ELF structure directly with readelf — header, sections,
    symbols, relocations, hands-on

## Module 6 — From ELF to Flashable Binary
6.1 Why a `.elf` file usually isn't what gets flashed to hardware
6.2 objcopy: converting ELF to raw binary (`.bin`) and Intel HEX (`.hex`)
    — what information is lost in each conversion and why that's okay
6.3 Understanding a HEX file's actual text format (address + data +
    checksum per line) — decoded by hand at least once
6.4 Map files: what a linker map file shows, and how to read one to
    diagnose "why is my binary so large" or "where did this symbol end up"

## Module 7 — Disassembly & Static Inspection
7.1 objdump in depth: disassembly (`-d`), section headers (`-h`),
    all headers (`-x`)
7.2 nm: listing symbols, understanding symbol type letters (T, D, B, U)
7.3 size: understanding the text/data/bss breakdown and what it tells
    you about memory footprint
7.4 strings and its practical debugging use on embedded binaries

## Module 8 — The C Library on Bare Metal: Newlib & Picolibc
8.1 Why bare-metal targets can't just use a normal libc — no OS to
    provide file I/O, memory management, etc.
8.2 Newlib's architecture: what it provides vs. what it expects the
    platform to supply
8.3 Syscall stubs: `_write`, `_read`, `_sbrk`, `_exit` and friends —
    what each is for and why you must implement them yourself
8.4 Retargeting printf to a UART: a complete worked example, tracing
    printf all the way down to a single register write
8.5 Picolibc as a modern, smaller alternative — what's different and
    why some projects prefer it
8.6 Nano vs. full newlib variants — the size/functionality tradeoff and
    when it matters for constrained targets

## Module 9 — Debugging with GDB + OpenOCD
9.1 Why embedded debugging needs a debug adapter/probe — GDB alone
    can't talk to real hardware
9.2 OpenOCD's role: bridging GDB to a JTAG/SWD debug interface
9.3 A full real debug session walkthrough: launching OpenOCD, connecting
    GDB as a remote target, setting breakpoints, flashing via GDB
9.4 Hardware vs. software breakpoints — the real difference and why it
    matters (limited hardware breakpoint count on real silicon)
9.5 Inspecting memory-mapped peripherals live through GDB during
    debugging — connecting this back to Track 01's register-struct work

## Module 10 — Build Systems for Embedded Projects
10.1 Why raw gcc commands don't scale — the case for a build system
10.2 Make revisited at an embedded-specific level: per-file rules,
     automatic dependency generation, cross-compiler variable overrides
10.3 CMake for embedded: toolchain files, why they're structured the
     way they are, a real minimal example
10.4 Choosing between Make and CMake for a given project size — honest
     tradeoffs, not dogma

## Module 11 — Optimization, Debug Info & Compiler Flags
11.1 Optimization levels (-O0 through -O3, -Os) — what each actually
     changes, and why -Os matters specifically for flash-constrained
     embedded targets
11.2 Debug info (-g) and its interaction with optimization — why
     debugging optimized code is harder, and the practical debug/release
     compromise
11.3 Link-Time Optimization (LTO): what it does across translation
     units, and its real cost/benefit for embedded builds
11.4 Warning flags that matter in practice (-Wall, -Wextra, -Werror)
     and treating warnings as a real code-quality gate, not noise

## Module 12 — Multilib & ABI Variants
12.1 What "multilib" means: one toolchain supporting multiple
     arch/ABI combinations (e.g. both RV32IMC and RV32IMAC)
12.2 How -march and -mabi flags select the right library variant
12.3 What happens (and what error you get) when object files built
     for mismatched ABIs are linked together — a real, common mistake

## Module 13 — Toolchain-Level Troubleshooting (industry reality check)
13.1 Diagnosing "undefined reference" errors — is it a missing source
     file, wrong link order, or a missing syscall stub?
13.2 Diagnosing size/memory-related link failures ("region overflowed")
     and reading them back to the linker script/MEMORY definition
13.3 Diagnosing a binary that "compiles fine but doesn't run" —
     systematic checklist connecting back to startup code, linker
     script correctness, and vector table placement

## Capstone Checkpoint for Track 03
A learner should be able to, unaided:
- Manually run each compilation stage separately and explain what
  changed at each step
- Write a correct linker script from scratch for a given memory map
- Read ELF header/section/symbol info directly with readelf and objdump
- Convert an ELF file to HEX and explain the resulting file's format
- Implement newlib syscall stubs to retarget printf to a UART
- Run a complete GDB+OpenOCD debug session against real or simulated
  hardware, including inspecting a memory-mapped peripheral
- Diagnose an "undefined reference" and a "region overflowed" linker
  error from first principles, not trial and error
- Explain what LTO does and when it would/wouldn't be used
