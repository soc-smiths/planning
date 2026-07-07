# Track 07 — On-Chip & Off-Chip Protocols: Deep Dive & Design

## Status: DRAFT v1 — for team review
## Standard: Builds on Tracks 00–06. Industry depth throughout.

## Why this track exists
Your core from Track 06 is useless in isolation — every real chip is a
core plus a web of standardized interconnects and peripheral protocols.
This track teaches both sides deliberately: reading a protocol spec
like an engineer, and designing/verifying real controller RTL for it,
not just "using" a peripheral as a black box.

---

## Module 1 — Why Standard Protocols Exist
1.1 The interoperability problem: what happens without standard
    protocols (every IP needs custom glue logic) and the real
    industry cost this imposes
1.2 Reading a protocol specification document properly: how to extract
    signal timing diagrams, state definitions, and edge-case rules
    from a real spec rather than a tutorial's simplified summary
1.3 The general split this track follows for every protocol studied:
    understand the spec → design a compliant controller → verify it
    (direct callback to Track 04's design discipline and Track 05's
    verification methodology, now applied to real-world protocols)

## Module 2 — On-Chip Bus Architecture Fundamentals
2.1 Why on-chip and off-chip protocols solve fundamentally different
    problems (bandwidth/latency inside a chip vs. long-distance,
    lower-pin-count communication to external devices)
2.2 Shared bus vs. crossbar/interconnect topologies — tradeoffs in
    area, arbitration complexity, and achievable bandwidth
2.3 Arbitration schemes: round-robin, priority-based, and why
    starvation is a real correctness concern in bus design

## Module 3 — AMBA APB (Simple Peripheral Bus)
3.1 Why APB exists specifically for low-bandwidth, low-complexity
    peripherals — the simplicity-vs-performance tradeoff explained
3.2 Full APB signal set and state machine: SETUP and ACCESS states,
    timing diagram read line by line
3.3 Designing an APB peripheral interface (slave) from scratch —
    direct application of Track 04's valid/ready-adjacent handshaking
    concepts to a real named industry protocol
3.4 Designing an APB requester (master/bridge) side
3.5 Common APB implementation bugs (incorrect PREADY handling, wait
    states) and how to catch them

## Module 4 — AMBA AHB-Lite
4.1 Why AHB exists above APB — pipelined, higher-bandwidth bus for
    higher-performance peripherals/memories
4.2 Full AHB-Lite signal set: address/control phase and data phase
    pipelining explained with a timing diagram
4.3 Transfer types: single, burst (INCR, WRAP) and their real use cases
4.4 Designing an AHB-Lite peripheral interface end to end
4.5 Bridging AHB-Lite to APB — the standard real-world topology
    (a fast core bus feeding a bridge down to simple peripherals),
    and why this exact structure appears in almost every real SoC

## Module 5 — AMBA AXI (Full Depth)
5.1 Why AXI exists beyond AHB — the five independent channels
    (AR, R, AW, W, B) and the real bandwidth/outstanding-transaction
    benefits this separation provides
5.2 Each channel's signals and handshaking, explained individually
5.3 Outstanding transactions and out-of-order completion: what this
    means and why it matters for high-performance systems
5.4 Burst types and sizing (AxLEN, AxSIZE, AxBURST) decoded properly
5.5 Designing a simple AXI-Lite subset interface (the realistic scope
    for this project) end to end, with correct handshaking on all
    relevant channels
5.6 Why full AXI is rarely fully implemented by student/small projects,
    and the honest scope decision to target AXI-Lite here — documented
    the same way Track 06 documented its own scope decisions

## Module 6 — Alternative Open On-Chip Buses: Wishbone & TileLink
6.1 Wishbone: a simpler, fully open on-chip bus standard — signal set
    and why open-source hardware projects often default to it
6.2 TileLink: designed for cache-coherent multi-core systems —
    conceptual overview of why coherence requires a different protocol
    shape than AHB/AXI
6.3 Practical comparison table: when a real project would choose
    Wishbone vs. AHB-Lite vs. AXI-Lite vs. TileLink

## Module 7 — Interconnect Design & Bus Bridging
7.1 Designing a simple crossbar/interconnect connecting your Track 06
    core to multiple peripherals over the chosen bus
7.2 Address decoding and memory map design — assigning each peripheral
    its address range correctly and without overlap
7.3 Bridging between two different protocols (e.g. AHB-Lite core bus
    to an APB peripheral bus) — a real, common integration task

## Module 8 — UART, In Full Depth
8.1 UART framing: start bit, data bits, parity, stop bit(s) — every
    field's purpose explained, not just "it sends bytes"
8.2 Baud rate generation from a system clock: the actual divider math,
    and why baud rate mismatch causes framing errors explained with
    the bit-timing math
8.3 Designing a UART transmitter and receiver in RTL from scratch,
    including oversampling for reliable receive-side bit sampling
8.4 Flow control: hardware (RTS/CTS) vs. software (XON/XOFF), and when
    each is actually used in practice

## Module 9 — SPI, In Full Depth
9.1 SPI modes (CPOL/CPHA combinations) explained with timing diagrams
    for all four modes — a frequent point of real integration bugs
9.2 Single master, multiple slave topology: chip-select behavior and
    why SPI doesn't need addressing the way I2C does
9.3 Designing an SPI controller (master) in RTL: shift register design,
    clock generation, chip-select timing
9.4 Designing an SPI peripheral (slave) side, and why slave-side clock
    domain handling needs the CDC techniques from Track 04 Module 11

## Module 10 — I2C, In Full Depth
10.1 Open-drain signaling and why I2C's electrical design differs
     fundamentally from UART/SPI's push-pull signaling
10.2 Start/stop conditions, addressing (7-bit/10-bit), ACK/NACK —
     the full protocol state machine
10.3 Clock stretching: what it is, why a slave uses it, and why a
     controller design must correctly handle it (a common real bug)
10.4 Multi-master arbitration: how I2C resolves bus contention without
     a dedicated arbiter, using the electrical properties of the bus
10.5 Designing an I2C controller in RTL, including clock stretching support

## Module 11 — USB (Conceptual Depth)
11.1 USB's layered architecture: physical, protocol, and device-class
     layers — enough structure to understand where complexity lives
11.2 Enumeration process: what happens when a device is plugged in,
     conceptually traced
11.3 Why implementing full USB in RTL is a substantial undertaking
     beyond this project's core scope — honest scope statement, with
     the option flagged as a stretch-goal/future track extension rather
     than silently skipped

## Module 12 — Ethernet (Conceptual Depth)
12.1 MAC vs. PHY split, and where that boundary typically sits in a
     real SoC (MAC often on-chip, PHY usually a separate chip)
12.2 Frame structure at a conceptual level, and the MII/RMII/GMII
     interface between MAC and PHY
12.3 Same honest scope statement as USB: full implementation flagged
     as future work, core concepts still taught for genuine literacy

## Module 13 — PCIe (Conceptual Overview)
13.1 Why PCIe exists as the high-performance off-chip interconnect
     standard, and its layered architecture (physical/data
     link/transaction) at a conceptual level
13.2 Enough literacy to read a PCIe-connected peripheral's datasheet
     and understand what's happening, without implementation depth

## Module 14 — Memory Interfaces: DDR/SDRAM Basics
14.1 Why external DRAM needs a dedicated, timing-critical controller
     rather than being treated like any other memory-mapped peripheral
14.2 Row/column addressing, refresh requirements, and why DRAM timing
     parameters (tRCD, tRP, CAS latency) matter conceptually
14.3 Honest scope statement: a full DDR controller is a specialized,
     often licensed-IP-based undertaking; this module builds literacy,
     not an implementation expectation, for this project

## Module 15 — Protocol Verification (Applying Track 05 for Real, Again)
15.1 Writing protocol-specific SVA assertions for each bus/peripheral
     designed in this track — direct, repeated application of Track 05
     Module 7's assertion methodology
15.2 Building a reusable protocol checker/monitor for your chosen
     on-chip bus — direct application of Track 05 Module 10's VIP concept
15.3 Constrained-random protocol-compliant stimulus generation for a
     peripheral controller, checked against the protocol spec's rules

## Capstone Checkpoint for Track 07
A learner/team should be able to, unaided:
- Read an unfamiliar protocol's timing diagram and correctly identify
  signal roles and valid/invalid sequences
- Design and verify an APB and an AHB-Lite peripheral interface
- Design and verify a UART, SPI, and I2C controller in RTL, correctly
  handling each protocol's real edge cases (baud mismatch, SPI mode
  mismatch, I2C clock stretching)
- Design an address map and interconnect connecting a core to multiple
  peripherals without address overlap
- Bridge between two different bus protocols correctly
- Write a reusable protocol assertion/monitor for at least one bus
- Explain, at appropriate literacy level, how USB, Ethernet, PCIe, and
  DDR work and why full implementation of each is out of this
  project's scope
