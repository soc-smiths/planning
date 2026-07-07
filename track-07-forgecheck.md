# Track 07 — Forge Check

## Status: DRAFT v1

---

## Part 1 — Recall Round

1. Why do standard on-chip/off-chip protocols exist instead of every
   IP block using custom signaling?
   ✅ Interoperability — without a shared standard, every pair of
   IP blocks would need custom glue logic, multiplying design and
   verification effort across a chip and ecosystem.

2. True or False: APB is designed for high-bandwidth, high-performance
   peripherals.
   ✅ False — APB is intentionally simple, targeting low-bandwidth,
   low-complexity peripherals; AHB/AXI serve higher-performance needs.

3. What are the two phases of an AHB-Lite transfer called?
   ✅ Address/control phase and data phase (pipelined relative to
   each other across back-to-back transfers).

4. True or False: AXI uses a single shared channel for both read and
   write address/data.
   ✅ False — AXI uses five independent channels: read address (AR),
   read data (R), write address (AW), write data (W), write response (B).

5. Why does AXI's multi-channel design allow higher performance than
   AHB's single pipelined channel?
   ✅ It allows outstanding/overlapping transactions and out-of-order
   completion, rather than strictly ordered single-pipeline transfers.

6. True or False: this project targets full AXI implementation rather
   than AXI-Lite.
   ✅ False — full AXI is a substantial undertaking beyond this
   project's realistic scope; AXI-Lite is the documented target.

7. Why do open-source hardware projects often default to Wishbone
   instead of AMBA protocols?
   ✅ Wishbone is a fully open standard with no licensing
   considerations, simpler than AHB/AXI, well-suited to open hardware projects.

8. What does a UART's baud rate divider actually compute, conceptually?
   ✅ How many system clock cycles correspond to one bit period at the
   target baud rate, used to time when to sample/shift each bit.

9. True or False: SPI requires device addressing similar to I2C.
   ✅ False — SPI uses dedicated chip-select lines per slave instead of
   addressing; I2C uses addressing precisely because it doesn't have
   per-device select lines.

10. What do CPOL and CPHA together define in SPI?
    ✅ The clock's idle polarity (CPOL) and which clock edge data is
    sampled/shifted on (CPHA) — together defining one of SPI's four modes.

11. Why is I2C's open-drain signaling necessary for its multi-master
    arbitration scheme to work?
    ✅ Open-drain allows multiple devices to drive the bus low without
    conflict (wired-AND behavior), letting a master detect if another
    master is simultaneously driving the bus differently, enabling
    arbitration without a dedicated arbiter.

12. True or False: clock stretching in I2C is performed by the master.
    ✅ False — clock stretching is performed by a slave holding the
    clock line low to pause the transaction when it needs more time.

13. What is the practical difference between MAC and PHY in an
    Ethernet interface, and where does each typically sit in a real SoC?
    ✅ MAC (Media Access Control) handles framing/protocol logic and is
    often implemented on-chip; PHY handles the physical electrical/
    optical signaling and is often a separate chip.

14. True or False: this project's curriculum expects a full DDR
    memory controller implementation.
    ✅ False — DDR controller design is specialized and often
    licensed-IP-based; this track builds literacy (row/column
    addressing, refresh, timing parameters) rather than requiring
    full implementation.

15. Why is address map design (assigning non-overlapping address
    ranges to peripherals) a correctness issue, not just an
    organizational detail?
    ✅ Overlapping address ranges cause ambiguous or incorrect
    peripheral selection during address decoding, leading to real
    functional bugs, not just messy documentation.

---

## Part 2 — Applied Round

1. A UART receiver samples incoming data at exactly the bit rate (1x
   oversampling) rather than an oversampled rate (e.g. 16x). Explain
   why this is fragile in practice.
   ✅ With only 1x sampling, any small clock frequency mismatch or jitter
   between transmitter and receiver accumulates across a byte and can
   cause the receiver to sample at the wrong point in a bit period,
   misreading bits; oversampling (typically 16x) allows sampling near
   the center of each bit and tolerating small timing deviations.

2. Two SPI devices are connected to the same bus, one expecting Mode 0
   and one expecting Mode 3. The master is configured for Mode 0 only.
   What will likely happen with the Mode 3 device, and why?
   ✅ Data will likely be sampled/shifted at the wrong clock edges
   relative to what the Mode 3 device expects, causing corrupted or
   misaligned data transfer — SPI mode must match between master and
   each slave being addressed.

3. An I2C slave holds SCL low mid-transaction. What is this, why is
   the slave doing it, and what must the controller (master) correctly
   do in response?
   ✅ This is clock stretching — the slave needs more time to process
   before continuing; the master must detect SCL being held low and
   wait (not proceed assuming its own timing) until the slave releases it.

4. You're designing an SoC with your Track 06 core plus a UART, an
   SPI flash controller, and a timer. Explain your reasoning for which
   bus (APB, AHB-Lite, or a bridged combination) you'd connect each to.
   ✅ E.g. core connects to a fast AHB-Lite bus for higher-bandwidth
   needs (SPI flash, if used for code fetch, might need more bandwidth);
   UART and timer are low-bandwidth and simple, suited to APB via an
   AHB-to-APB bridge — reasoning should reflect matching protocol
   complexity/bandwidth to actual peripheral needs.

5. Two peripherals in your address map are accidentally assigned
   overlapping address ranges. Describe a concrete symptom this bug
   would cause during system operation.
   ✅ E.g. writing to what's intended as peripheral A's control register
   might also (or instead) affect peripheral B if the decoder can't
   distinguish them, causing unexplained behavior in one or both
   peripherals depending on decode priority/logic.

6. Why would a team write a reusable SVA-based protocol monitor for
   their AHB-Lite bus rather than only checking correctness inside
   each individual peripheral's testbench?
   ✅ A protocol-level monitor catches interconnect/bus-level violations
   (e.g. incorrect handshake sequencing) regardless of which peripheral
   is involved, and can be reused across every peripheral test and
   future project without being rewritten — direct application of the
   VIP reusability principle from Track 05.

---

## Part 3 — Practical Proof

**Task 1 — APB peripheral**
Design and implement an APB slave interface for a simple register-based
peripheral (e.g. a GPIO controller), correctly handling SETUP/ACCESS
states and wait states via PREADY.

**Task 2 — AHB-Lite to APB bridge**
Design a bridge connecting an AHB-Lite master interface to your Task 1
APB peripheral, and verify a read and write transaction pass through
correctly end to end.

**Task 3 — UART controller**
Design a UART transmitter and receiver in RTL with configurable baud
rate and 16x oversampling on receive, and verify correct byte transfer
at a specified baud rate with a provided testbench.

**Task 4 — SPI controller**
Design an SPI master controller supporting at least Mode 0 and Mode 3,
and demonstrate correct operation against a provided SPI slave model
in both modes.

**Task 5 — I2C controller with clock stretching**
Design an I2C master controller that correctly detects and waits
during clock stretching, verified against a provided slave model that
stretches the clock during a transaction.

**Task 6 — Address map & interconnect**
Design a memory map and simple interconnect connecting your Track 06
core to at least three peripherals (from Tasks 1–5) with no address
overlap, and verify each peripheral is independently addressable.

**Task 7 — Protocol assertion monitor**
Write a reusable SVA-based protocol monitor for your chosen on-chip
bus, and demonstrate it correctly flagging a deliberately injected
protocol violation (provided in `/quizzes/track-07-protocol-bug`).

---

## Completion state
Shown as: "Forge Check — Track 07: Cleared ✓" once all three parts
are attempted.
