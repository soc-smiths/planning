# Track 09 — Forge Check

## Status: DRAFT v1

---

## Part 1 — Recall Round

1. Why is Python generally used for automation/verification glue while
   TCL is used for EDA tool control, rather than one language for both?
   ✅ Nearly every EDA tool embeds a native TCL interpreter as its
   batch/scripting interface — this is a tooling reality, not a
   preference; Python is used where general scripting, data
   processing, and testing are better suited.

2. True or False: checking a subprocess's return code is optional if
   the tool "usually works."
   ✅ False — ignoring return codes can cause a regression to silently
   report a pass even when the underlying tool crashed or failed.

3. Why can't every test in a large regression suite always be run in
   full parallel across unlimited processes in an EDA context
   specifically?
   ✅ EDA tools are often license-limited (a fixed number of concurrent
   tool licenses), constraining real parallelism regardless of
   available CPU cores.

4. True or False: "the test didn't crash" is equivalent to "the test
   passed" in a regression runner.
   ✅ False — a test can complete without crashing yet still produce
   incorrect results; explicit pass/fail checking (scoreboard/
   assertion-based) is required, not just absence of a crash.

5. What is cocotb, in one sentence?
   ✅ A Python-based verification framework that lets you write
   testbenches in Python to drive a Verilog/SystemVerilog DUT via a
   simulator.

6. True or False: cocotb is always a better choice than SystemVerilog/
   UVM for verifying any design.
   ✅ False — it's a genuine tradeoff; UVM/SystemVerilog remains
   preferred for large, complex, industry-standard verification
   environments, while cocotb can be faster/simpler for smaller or
   Python-ecosystem-integrated projects.

7. Why would automation scripts themselves need unit tests (e.g. via
   pytest)?
   ✅ A bug in a report-parsing or regression script could cause false
   passes or misreported results across an entire regression,
   undermining confidence in every test it touches.

8. True or False: in TCL, `set x 5` and later using `$x` both involve
   "everything is a string" semantics under the hood.
   ✅ True — TCL's core model treats values as strings, with numeric/
   list interpretation happening contextually.

9. Why do EDA tools almost universally choose TCL as their scripting
   interface historically?
   ✅ Historically established convention across the EDA industry;
   value of a consistent, embeddable scripting language across
   different vendors' tools.

10. True or False: a Makefile that doesn't track dependencies will
    re-run synthesis even when RTL hasn't changed.
    ✅ True — without correct dependency rules, Make (or a poorly
    structured Makefile) may rebuild everything unconditionally,
    wasting real time at project scale.

11. Why can't full regressions typically run on every single commit
    in CI for a hardware project?
    ✅ Tool license limits and long runtimes make full regressions
    impractical on every commit; a smaller fast smoke-test subset on
    every PR with full regression scheduled (e.g. nightly) is the
    common real compromise.

12. True or False: hardcoding tool paths and license server addresses
    directly inside automation scripts is good practice for a shared
    team project.
    ✅ False — these should be externalized (config files/environment
    variables) so scripts work across different team members' setups
    without editing script source.

13. Why is a script that silently swallows an error worse than having
    no automation at all?
    ✅ It creates false confidence — a real failure goes unnoticed and
    unreported, potentially worse than a human manually checking and
    catching the issue.

---

## Part 2 — Applied Round

1. A regression runner reports "50/50 tests passed," but a teammate
   later finds a real bug that should have failed one of those tests.
   What are two possible automation-level explanations (not RTL bugs)
   for this false pass?
   ✅ e.g. The runner didn't actually check the subprocess's return
   code / didn't parse the log correctly to detect a failure message,
   or the scoreboard/checker itself had a bug allowing an incorrect
   result to be marked as passing.

2. You need to run the same synthesis flow across 20 different RTL
   modules with only the module name changing. Describe how you'd
   structure this using Python + TCL together, rather than manually
   editing and re-running a TCL script 20 times.
   ✅ Write a Python script that loops over the module list, generates
   (or parameterizes) a TCL script per module (e.g. via a template with
   substituted variables or passed TCL variables), invokes the
   synthesis tool via subprocess for each, and collects/parses each
   resulting report — Python orchestrates, TCL drives the tool.

3. Your team's CI pipeline runs a full regression on every single
   commit, and developers complain builds take 6 hours and often fail
   due to license exhaustion. What would you change, and why?
   ✅ Split into a fast smoke-test subset run on every PR/commit, with
   the full regression run on a schedule (e.g. nightly) or on-demand —
   matching realistic tool license/runtime constraints instead of
   running everything every time.

4. A Python report-parsing script has a regex that silently fails to
   match a slightly different log format after a tool version upgrade,
   and now reports zero timing violations across all designs. How
   would writing a unit test for this script have caught the problem
   sooner, and what should the test check?
   ✅ A unit test with a known sample log (fixture) and expected parsed
   output would fail immediately when the regex stops matching that
   format, rather than the bug being discovered later via a
   suspiciously "too good" all-passing report; the test should assert
   the parser extracts the exact expected values from representative
   sample logs.

5. Explain why a script that catches an exception from a failed tool
   invocation and just logs "done" without distinguishing success from
   failure is a dangerous automation pattern.
   ✅ It hides real failures behind a generic completion message,
   meaning downstream consumers of the regression results (or the team)
   have no way to know a run actually failed — automation should fail
   loudly/distinctly, not swallow and mask errors.

---

## Part 3 — Practical Proof

**Task 1 — Regression runner**
Write a Python regression runner that discovers a set of provided test
scripts, runs each (checking real return codes), and generates a
Markdown report summarizing pass/fail counts and failing test names.

**Task 2 — Log parser with tests**
Write a Python function parsing a provided synthesis timing report to
extract worst negative slack, and write pytest unit tests for it using
at least two sample log fixtures (one clean, one edge-case format).

**Task 3 — cocotb testbench**
Write a simple cocotb testbench for a provided small RTL module (e.g.
a counter or FIFO from earlier tracks), demonstrating correct
async/await-based stimulus and checking.

**Task 4 — TCL synthesis script**
Write a parameterized TCL script that runs a provided synthesis tool
in batch mode on a given RTL module (module name passed as a TCL
variable), producing a timing report.

**Task 5 — Python + TCL orchestration**
Write a Python script that loops over a list of module names,
generates/invokes the Task 4 TCL script for each via subprocess, parses
each resulting report using your Task 2 parser, and outputs one
combined summary table.

**Task 6 — Project Makefile**
Write a Makefile for this project with at least `sim`, `lint`, and
`regress` targets, using proper dependency rules so unchanged RTL
doesn't trigger unnecessary re-runs.

**Task 7 — CI pipeline**
Set up a GitHub Actions workflow that runs lint and a smoke-test subset
of your regression automatically on every pull request.

---

## Completion state
Shown as: "Forge Check — Track 09: Cleared ✓" once all three parts
are attempted.
