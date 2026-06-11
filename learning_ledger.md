# LEARNING LEDGER — process meta-layer
# Job: track HOW the teaching is going, not what was taught. STATE.md = curriculum, learning_log.md = content (Anshul's words), THIS = process.
# Updated every session-end. Kept SMALL: fixed patterns roll up or age out. The raw transcript (all_chats.odt) is the forensic backup — pull it when a pattern needs evidence.
# How to update: append a dated session line under "Per-session signal". When a recurring friction is FIXED, move it from "Open frictions" to "Resolved (with the fix that worked)". Don't let "Resolved" grow forever — prune entries older than ~5 sessions once stable.

## Open frictions (active — watch for recurrence)
- [SALIENCE, not ignorance] Every recurring miss so far was a rule Claude KNEW but lost under mid-flow load. Fixes are triggers/gates, not more explanation. Backstop remains Anshul's one-line catch.
- (none currently unaddressed — the three below just got instruction edits; verify they hold next session)

## Recently addressed (verify the fix holds, then prune)
- Mechanical/concept misclassification → verb-trigger added (run/install/check/verify/source/paste = mechanical, no quiz, batch). WATCH: did a predict-prompt land on a pure execute step this session? Y/N.
- Predict-spam (109 prompts across full history, mid-run peak) → loop step 2 gated on "has a basis". WATCH: count predict-prompts this session; any where Anshul had no basis? 
- Over-correction whiplash (fixed quiz-spam → swung into banned cold build-log voice) → no-overshoot rule added. WATCH: any correction this session that overshot into the opposite failure?

## Working well (don't break these)
- Deep concept teaching once in the right mode: the sourcing/PATH deep-dive, V4L2→ROS translation, cv_bridge park — all landed well when Anshul pushed into them.
- Repo/machine-state honesty: when pushed to "go check my github," Claude correctly held the line (can't see your machine, ask for a command). Working — keep.
- Anshul's independent investigation (opening setup files, running extra commands) consistently produces the best sessions. Feed it, don't pre-empt it.

## Per-session signal (append one dated block per session; keep each to a few lines)
### Seed (from transcript analysis, Jun 11 planning session — covers Units 0a–1c retrospectively)
- Friction pattern: over-quizzing mechanical steps + one-command round-trips, esp. EARLY in each track. Cost: two explicit "read instructions again" corrections in session 1 alone.
- Voice: strong in prose teaching; failure mode is collapsing to terse/clinical under correction pressure.
- Anshul signal: pushes back fast and precisely; vague phrasing from him is rare and is the real gap-signal when it appears. Reasons from disk-level mechanics.
- Metric baseline: ~109 predict-prompts total across 0a–1c. Target: sharp drop now that the gate is in.
