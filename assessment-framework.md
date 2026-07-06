# Assessment Framework — "Forge Check" (LOCKED)

## Status: LOCKED — v1

Every track ends with a Forge Check: three parts —
1. Recall round (MCQ/true-false, explanation shown for every answer)
2. Applied round (short-answer / code-or-log analysis questions)
3. Practical proof (hands-on task, self-checked against answer key)

No hard pass/fail gate — this is revision, not an exam.
Visible completion state per track, tracked locally in-browser.

## Technical plan
Phase A (launch): quiz content as JSON per track in /quizzes/track-XX.json,
one reusable quiz component, progress stored client-side only. No backend,
no cost.

Phase B (later, optional): GitHub OAuth sign-in + Supabase free tier for
cross-device progress sync. Not required for launch.
