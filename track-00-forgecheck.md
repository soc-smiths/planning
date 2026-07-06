# Track 00 — Forge Check

## Status: DRAFT v1.1 — template for all future tracks (Q4 corrected)

---

## Part 1 — Recall Round
(MCQ / True-False — explanation shown after every answer, right or wrong)

1. What does `chmod 754 file.sh` set the permissions to?
   A) rwxr-xr--  B) rwxr-x---  C) rw-r-xr-x  D) r-xr-xr-x
   ✅ A — 7=rwx(owner), 5=r-x(group), 4=r--(others)

2. True or False: `kill -9` should always be your first choice to stop
   a stuck process.
   ✅ False — SIGTERM (default kill) allows graceful shutdown; SIGKILL
   (-9) skips cleanup and should be a last resort.

3. Which command shows disk space used by files/directories, not just
   free space on a drive?
   A) df  B) du  C) lsblk  D) mount
   ✅ B — df shows filesystem-level free space; du shows usage per file/dir.

4. In `grep -r "TODO" .`, what does `-r` do?
   ✅ Searches recursively through subdirectories.

5. What's the actual difference between a hard link and a symlink?
   ✅ A hard link points directly to the same inode (same data, no
   original file needed); a symlink is a separate file pointing to a
   path, breaks if the target moves.

6. True or False: `git pull` is exactly the same as `git fetch`.
   ✅ False — fetch only downloads changes; pull = fetch + merge (or
   rebase, depending on config).

7. In a Makefile, what is a "phony target" used for?
   ✅ A target that doesn't produce a file (e.g. `clean`, `all`) — tells
   Make not to check for a file of that name.

8. Which environment variable determines where the shell looks for
   executables?
   ✅ PATH

9. What does `set -e` do at the top of a bash script?
   ✅ Causes the script to exit immediately if any command fails
   (returns non-zero), preventing silent failures.

10. True or False: `git rebase` rewrites commit history; `git merge`
    does not.
    ✅ True.

11. What does `.gitignore` do, and why does it matter more for
    hardware repos specifically?
    ✅ Excludes files from version control; hardware repos generate
    large binary artifacts (waveforms, netlists) that shouldn't be
    committed.

12. Which tool would you use to see which process is holding a file
    open?
    A) top  B) lsof  C) grep  D) du
    ✅ B

13. What's the difference between `git reset` and `git revert`?
    ✅ Reset rewrites history (moves the branch pointer, can discard
    commits); revert creates a new commit that undoes changes, safe
    for shared/public history.

14. True or False: SSH key authentication is generally considered less
    secure than a strong password.
    ✅ False — key-based auth is standard/preferred in industry.

15. What does `awk '{print $2}'` do on a text file?
    ✅ Prints the second whitespace-separated field of each line.

---

## Part 2 — Applied Round
(Short answer / diagnose-the-problem — tests real understanding)

1. You run a script with `./deploy.sh` and get "Permission denied."
   What's the fix, and why did this happen?
   ✅ `chmod +x deploy.sh` — the file lacks execute permission.

2. A teammate says "git pull isn't working, it says I have merge
   conflicts." Walk through, in order, what they should do to resolve
   this safely.
   ✅ Open conflicted files, resolve markers (<<<<<<< / ======= /
   >>>>>>>), `git add` resolved files, `git commit` to complete the
   merge — verify with `git status` at each step.

3. You installed a tool from source but running it gives
   "command not found." What are the two most likely causes and how
   do you check each?
   ✅ (a) Binary not on PATH — check with `echo $PATH` and `which
   toolname`; (b) `make install` failed silently — check build output/
   logs for errors.

4. Given this log line, extract just the timestamp (no brackets) using
   a single command pipeline:
   `[2026-07-06 14:02:11] ERROR: build failed`
   ✅ e.g. `grep ERROR log.txt | sed 's/\[\(.*\)\].*/\1/'`
   or `awk -F'[][]' '{print $2}' log.txt`
   (accept any equivalent grep/sed/awk answer that strips the brackets)

5. Why would a team specifically choose `git rebase` over `git merge`
   before opening a pull request, and what's the risk if done on a
   shared branch?
   ✅ Rebase keeps history linear/clean for review; risk is rewriting
   history that others have already pulled, causing conflicts for them.

---

## Part 3 — Practical Proof
(Hands-on task, self-checked against provided answer key)

**Task 1 — Environment sanity check**
Run and paste the output of:
uname -a && whoami && echo $PATH && df -h
Self-check: Confirm you can explain what each command's output means,
line by line, in your own words.

**Task 2 — Scripting**
Write a bash script `disk_check.sh` that:
- Prints available disk space
- Warns (prints a message) if usage on `/` exceeds 80%
Answer key provided separately with one valid solution — yours doesn't
need to match exactly, but must satisfy both bullet points.

**Task 3 — Git**
Starting from a provided sample repo (linked in `/quizzes/track-00-repo`):
- Create a branch, make a change, commit it
- Intentionally create a merge conflict with a provided second branch
- Resolve it and open a PR
Self-check against the provided walkthrough diff.

---

## Completion state
Shown as: "Forge Check — Track 00: Cleared ✓" once all three parts
are attempted (no scored pass/fail gate — this is revision).
