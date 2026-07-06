# Track 00 — Linux, Git & Dev Environment Foundations

## Status: DRAFT v2 — for team review
## Standard: Zero prior knowledge assumed. Industry depth throughout.

## Why this track exists
Every later track — toolchains, RTL sims, FPGA tools, scripting — runs
on Linux and lives in Git. Skipping this and "picking it up along the
way" is exactly how courses produce people who can follow a tutorial
but can't operate independently in a real team or lab environment.
This track exists so nothing later ever has to pause to explain a
terminal command, a file permission error, or a merge conflict.

---

## Module 1 — Why Linux, and Getting One Running
1.1 Why virtually all embedded/RTL/EDA tooling assumes Linux — licensing
    history, HPC/cluster convention, package ecosystems, why companies
    standardize on specific distros (commonly RHEL/CentOS in industry,
    Ubuntu in most open-source/academic settings — explain both)
1.2 Choosing your setup: WSL2 (Windows), native dual-boot, VM, or cloud
    dev machine — honest tradeoffs (performance, USB/hardware passthrough
    for FPGA boards later, disk space, snapshotting/rollback)
1.3 Installing Ubuntu LTS step by step, partitioning choices explained
1.4 First boot: updates, locale, timezone, hostname, basic sanity checks
1.5 Understanding kernel vs. distro vs. desktop environment — what's
    actually different between distros and what genuinely isn't

## Module 2 — The Shell and Filesystem, For Real
2.1 What a shell actually is (shell vs. terminal emulator vs. TTY vs.
    login shell — commonly confused, causes real debugging pain later)
2.2 Filesystem Hierarchy Standard — /etc, /usr, /var, /home, /dev, /proc,
    /opt, /tmp — what lives where and why, with real examples of tools
    installing into each
2.3 Inodes, hard links vs. symlinks, and why "the file is gone but disk
    space isn't freed" happens (directly relevant once large sim/build
    artifacts appear in later tracks)
2.4 Navigation and file operations: cd, ls, cp, mv, rm, mkdir, find —
    real flags used daily (find -name, -mtime, -exec; ls -la, -lh)
2.5 Permissions model in depth: users, groups, rwx, chmod numeric and
    symbolic, chown/chgrp, umask, setuid/setgid basics, sudo vs. su and
    why "just sudo everything" is a bad habit explained honestly
2.6 Disk and mount basics: df, du, lsblk, mount/umount, understanding
    what "out of disk space" actually means and how to find the cause
2.7 Archiving and compression: tar, gzip/bzip2/xz — flags that matter,
    why source releases and IP packages are distributed this way

## Module 3 — Text Processing (the most-skipped, most-used skill)
3.1 Why this matters specifically for hardware/embedded work — parsing
    log files, simulation output, synthesis reports, lint results
3.2 grep in depth: regex basics, -r, -i, -v, -E, context flags
3.3 sed for stream editing: substitution, in-place edits, real examples
    on log/report files
3.4 awk fundamentals: field processing, simple reports from tabular
    tool output (this alone saves hours once EDA tools enter the picture)
3.5 Combining pipelines: grep | sed | awk | sort | uniq -c as a real
    daily workflow, not isolated toy examples
3.6 Regular expressions properly — enough to read/write patterns
    confidently, not just copy-paste from Stack Overflow

## Module 4 — Package Management & Building From Source
4.1 apt in depth: install, search, remove, autoremove, dependency
    resolution, what "broken packages" actually means and how to fix
4.2 Reading a README's build instructions: configure/make/make install
    explained step by step — what configure detects, what make actually
    does, why "make install" needs sudo and what it touches
4.3 Environment variables and PATH in depth — why a tool "isn't found,"
    LD_LIBRARY_PATH issues (a very common real pain point with EDA/
    toolchain installs), how to diagnose instead of guessing
4.4 Installing tools from source, practiced deliberately here so it's
    routine by the time Track 03 (toolchain) and EDA tools arrive
4.5 Version managers and why tool version mismatches break builds
    silently (a very real, very common industry problem)

## Module 5 — Processes, Jobs & System Monitoring
5.1 Processes: ps, ps aux, process states, PID/PPID
5.2 top/htop — reading CPU, memory, load average correctly
5.3 kill, signals (SIGTERM vs SIGKILL vs SIGHUP) — what each actually
    does and why "kill -9 everything" is a bad default habit
5.4 Foreground/background jobs: &, jobs, fg, bg, nohup, disown — needed
    for long-running simulation/synthesis jobs later
5.5 Basic debugging tools: strace and lsof — what they're for, one
    real example each (diagnosing "why won't this tool run")
5.6 cron and systemd timers — scheduling recurring tasks (relevant for
    automation in Track 09)

## Module 6 — Editors & Terminal Workflow
6.1 vim fundamentals — enough to comfortably edit any file on any
    remote machine with nothing else installed (a genuine industry
    expectation, not optional trivia)
6.2 tmux — persistent sessions, panes, detach/reattach, why this
    matters for jobs that run for hours (synthesis, regressions)
6.3 Setting up VS Code with Remote-SSH for daily comfortable work,
    explained as a complement to vim, not a replacement for understanding it

## Module 7 — Shell Scripting
7.1 Writing your first bash script — shebang, execute permission, why
    "sh script.sh" vs "./script.sh" behave differently
7.2 Variables, quoting rules (a real source of subtle bugs), conditionals
7.3 Loops: for, while, iterating over files and command output correctly
7.4 Functions, argument handling ($1, $@, $#, getopts for real option
    parsing)
7.5 Exit codes and error handling: set -e, checking $?, why silent
    failures in scripts cause real problems in automated flows
7.6 Real capstone script: automating a multi-step repetitive task
    end-to-end (sets the expectation that scripting is a working tool,
    not a classroom exercise)

## Module 8 — Networking & Remote Access Basics
8.1 IP addressing, ports, and localhost — just enough to understand
    what a "connection refused" error means
8.2 SSH: generating and using keys, config file shortcuts, why
    passwordless key auth is the norm, not a shortcut
8.3 Connecting to remote machines/lab servers (directly relevant once
    shared FPGA boards or lab machines appear in later tracks)
8.4 scp and rsync — moving files without a GUI, and why rsync is
    preferred for large/repeated transfers

## Module 9 — Git Fundamentals, Properly
9.1 What version control actually solves — not "backup," but history,
    collaboration, and safe experimentation
9.2 init, add, commit, status, log, diff — and what's really happening
    underneath (blobs, trees, commits — not treated as magic)
9.3 Branching and merging — real conflict resolution practiced
    deliberately, not avoided
9.4 Rebase vs. merge: what each does to history, when industry teams
    use which, and why this choice matters for shared repos
9.5 Stashing, cherry-picking, reverting vs. resetting — the difference
    between undoing safely and rewriting history dangerously
9.6 git bisect — finding which commit broke something (a genuinely
    useful skill rarely taught to beginners)
9.7 .gitignore written properly, especially for EDA/simulation
    byproducts — waveform files, build directories, generated netlists

## Module 10 — GitHub & Collaborative Workflow
10.1 Remotes: origin, upstream, fetch vs. pull vs. clone
10.2 Forking vs. branching — when each applies, and why open-source
     projects (like this one) typically use forks for outside
     contributors and branches for core team members
10.3 Pull requests done properly: descriptive commits, small focused
     diffs, review etiquette, responding to review comments
10.4 Issues and project boards — how real teams (including SoC Smiths)
     track and prioritize work
10.5 Basic GitHub Actions / CI concept — automatically running a check
     (like a lint or build) on every push, introduced conceptually here,
     used practically once there's code worth checking

## Module 11 — Git & Environments for Hardware Projects
11.1 Why hardware repos break generic Git advice: large waveform dumps,
     generated netlists, IP-XACT files, binary bitstreams
11.2 Git LFS — when and how to use it, and its real tradeoffs
11.3 Structuring a hardware repo cleanly: separating RTL, testbenches,
     scripts, and generated/ignored output
11.4 Reproducible environments: why "it works on my machine" is
     especially dangerous in hardware flows, and an introduction to
     containers (Docker) as one real solution — just enough to run and
     use an existing container, full authoring covered later if needed

## Module 12 — Make & Build Automation Basics
12.1 Why Makefiles are everywhere in embedded/EDA flows specifically
12.2 Targets, dependencies, variables, phony targets — enough to read
     and confidently modify any Makefile encountered from Track 03 on
12.3 Writing a simple Makefile from scratch, including a multi-file
     build with dependencies

## Capstone Checkpoint for Track 00
A learner should be able to, entirely unaided and without a GUI:
- Set up a fresh Linux environment from nothing and diagnose common
  install/path problems independently
- Navigate, search, and process text (grep/sed/awk) fluently
- Write and debug a non-trivial bash script with proper error handling
- Build a tool from source and resolve a dependency/path issue
- Clone a repo, branch, rebase, resolve a real conflict, open a clean PR
- Use git bisect to find a breaking commit
- Read and extend an existing Makefile
- Connect to and work comfortably on a remote machine over SSH

No GUI dependency should remain for any of the above by the end of
this track.
