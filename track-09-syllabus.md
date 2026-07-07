# Track 09 — Scripting & Automation: Python & TCL

## Status: DRAFT v1 — for team review
## Standard: Builds on Tracks 00–08. Industry depth throughout.

## Why this track exists
Every track before this one involved manually running commands,
manually checking waveforms, manually comparing outputs. Real chip
teams don't scale that way — this track turns manual work into
regression suites, automated reports, and tool-driven flows. Python
handles verification/data/automation; TCL handles EDA tool control,
because that's genuinely how the industry splits the two languages.

---

## Module 1 — Why Two Languages, and Where Each Is Actually Used
1.1 Python's role: test automation, data processing, log parsing,
    glue scripts, cocotb-based verification
1.2 TCL's role: nearly every EDA tool (synthesis, simulation, place &
    route) embeds a TCL interpreter as its native scripting/control
    language — this is a real, unavoidable industry fact, not a choice
1.3 Why you can't substitute one for the other in this domain, even
    though both are general-purpose scripting languages

## Module 2 — Python Fundamentals for Automation (from zero, if needed)
2.1 Core syntax refresher applied to automation contexts: functions,
    control flow, data structures (lists, dicts) used the way scripts
    actually use them (not toy examples)
2.2 File I/O: reading/writing text, JSON, CSV — the actual formats
    tool logs and reports come in
2.3 String processing and regular expressions in Python — parsing real
    tool output (synthesis reports, simulation logs) reliably
2.4 Working with paths and directories (pathlib) for scripts that
    operate across a real project's file structure

## Module 3 — Subprocess Management & Process Automation
3.1 Running external commands from Python (subprocess module) —
    capturing output, checking return codes, handling timeouts
3.2 Why silently ignoring a tool's exit code is a real, common
    automation bug (a "passing" regression that actually crashed silently)
3.3 Running commands in parallel for regression speed — concurrent.
    futures or multiprocessing basics, and the real tradeoffs (CPU
    cores, tool license limits — a genuine EDA-specific constraint)

## Module 4 — Building a Regression Runner From Scratch
4.1 Designing a regression runner's architecture: test discovery, test
    execution, result collection, reporting — the same shape as any
    real company's internal regression infrastructure
4.2 Pass/fail/error classification — why "didn't crash" isn't the same
    as "passed" (direct callback to Track 05's testbench distinctions)
4.3 Seed management integration: automatically running constrained-
    random tests (Track 05/06) across multiple seeds and aggregating results
4.4 Generating a human-readable regression report (HTML or Markdown)
    summarizing pass/fail counts, failing seeds, and links to logs/waveforms

## Module 5 — Log & Report Parsing at Scale
5.1 Parsing synthesis timing reports to extract slack/critical path
    data programmatically (direct application to Track 04/06's timing
    concepts, now automated instead of manually read)
5.2 Parsing simulation logs for assertion failures, error messages,
    and coverage summary lines
5.3 Aggregating data across many runs into structured data (e.g. a
    pandas DataFrame or plain CSV) for trend analysis — "is our
    coverage improving over time," not just one run's snapshot

## Module 6 — Python-Based Verification: cocotb
6.1 What cocotb is: writing testbenches in Python that drive a
    Verilog/SystemVerilog DUT via a simulator, instead of writing
    testbenches purely in SystemVerilog
6.2 When cocotb is genuinely a good fit vs. when SystemVerilog/UVM
    (Track 05) remains the better choice — an honest, real tradeoff
    discussion, not "Python is always better"
6.3 Writing a simple cocotb testbench for a Track 04 module,
    demonstrating async/await-based coroutine testing style
6.4 Coverage and assertion integration in a cocotb-based flow

## Module 7 — Testing Python Automation Code Itself
7.1 Why scripts that generate regression results need their own tests
    too — a script with a silent bug can report false passes across
    an entire regression
7.2 pytest fundamentals: writing unit tests for your own automation
    utilities (parsers, report generators)
7.3 Mocking subprocess calls and file I/O in tests so automation logic
    can be tested without needing a real EDA tool installed

## Module 8 — TCL Fundamentals
8.1 TCL syntax: everything is a string, command substitution, variable
    substitution — the mental model that trips up people coming from
    Python/C
8.2 Control flow, procedures (proc), lists and arrays in TCL
8.3 File I/O and string manipulation in TCL — enough to write real
    tool-control scripts, not full general-purpose programs

## Module 9 — TCL for EDA Tool Control
9.1 The universal pattern: every major EDA tool (synthesis, simulation,
    place-and-route) accepts a TCL script as its primary batch-mode
    input — reading a real tool's TCL command reference for the first time
9.2 Writing a synthesis-flow TCL script: reading RTL, setting
    constraints, running synthesis, generating reports — directly
    scripting what Track 04/13 have been doing manually
9.3 Writing a simulation-flow TCL script: compiling, elaborating,
    running, and dumping waveforms via TCL rather than manual GUI clicks
9.4 Parameterizing TCL scripts so the same script runs across multiple
    modules/configurations without hand-editing

## Module 10 — Combining TCL Flows with Python Orchestration
10.1 A very common real pattern: Python as the outer orchestrator
     (regression runner, report generator) calling TCL-driven EDA tools
     as subprocesses — tying Module 3/4's Python work directly to
     Module 9's TCL scripts
10.2 Passing parameters between Python and TCL scripts cleanly
     (environment variables, generated TCL variable files, command-
     line arguments)
10.3 Building one complete example: a Python script that generates a
     TCL synthesis script per module, runs synthesis via subprocess,
     parses the resulting timing report, and outputs a summary table

## Module 11 — Makefiles for Full-Project Automation, Revisited
11.1 Extending Track 00's basic Makefile knowledge to a real project-
     scale Makefile: targets for simulation, synthesis, linting,
     regression, and documentation generation
11.2 Dependency-driven builds: only re-running synthesis when RTL
     actually changed, not on every invocation — real time savings at
     project scale
11.3 Structuring a Makefile so new team members (your 5 collaborators)
     can run `make sim`, `make lint`, `make regress` without needing to
     know the underlying tool commands

## Module 12 — Continuous Integration for Hardware Projects
12.1 Extending Track 00 Module 10's CI introduction to a real hardware
     CI pipeline: running lint (Track 04), a quick regression subset,
     and report generation automatically on every pull request
12.2 Why full regressions often can't run on every commit in CI
     (tool license limits, runtime) and the real industry compromise:
     fast smoke tests on every PR, full regression nightly/scheduled
12.3 Setting up GitHub Actions for this project specifically: what a
     real workflow file looks like for a hardware repo, connecting
     directly to Track 00 Module 10/12's Git foundations

## Module 13 — Data Visualization & Dashboards
13.1 Turning aggregated regression/coverage data (Module 5) into
     actual visualizations — coverage trend over time, pass-rate by
     module — using a plotting library (matplotlib or similar)
13.2 Building a simple project status dashboard (even a generated
     static HTML page) summarizing regression health — a genuinely
     useful, portfolio-visible artifact for the whole team

## Module 14 — Script Quality, Maintainability & Team Practices
14.1 Why automation scripts need the same code-quality discipline as
     RTL/firmware: version control, code review, documentation
     (direct callback to Track 00's Git practices and Track 04's
     review culture, now applied to scripts)
14.2 Configuration management: keeping tool paths, license servers,
     and project-specific settings out of hardcoded scripts (config
     files/environment variables instead)
14.3 Writing scripts that fail loudly and clearly rather than silently
     — a script that swallows an error and reports false success is
     worse than no automation at all

## Capstone Checkpoint for Track 09
A learner/team should be able to, unaided:
- Write a Python regression runner that discovers, runs, and reports
  results across multiple tests and seeds
- Parse a real synthesis timing report and simulation log
  programmatically and extract meaningful structured data
- Write a working cocotb testbench for a simple module
- Write a TCL script that drives a real EDA tool's synthesis or
  simulation flow in batch mode
- Combine Python orchestration with TCL tool-control scripts into one
  working end-to-end automated flow
- Write a project-scale Makefile with dependency-aware targets
- Set up a working CI pipeline running lint and a smoke-test
  regression on every pull request
- Explain why automation scripts need version control, testing, and
  review just like RTL or firmware code
