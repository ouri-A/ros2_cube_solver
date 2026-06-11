# STATE.md — Cube Solver Project

## Where we are
Git track (Unit 1 / 1b / 1c) DONE. Unit 2 NOT yet started. This session was a curriculum-design + process-tuning pass, not a teaching unit — we reworked the ROS2 plan AND overhauled the instructions/context system before starting (see "Plan changes this session"). Next action is to begin Unit 2 in a fresh chat, under the revised plan + instructions.

## Context system (NEW — three files, distinct jobs)
- STATE.md (this file) = curriculum bookmark. Paste at chat start.
- notes/learning_log.md = content in my words. Free-form.
- learning_ledger.md = process meta-layer (how teaching is going). Updated each session-end; paste at chat start when process-continuity matters.
- all_chats.odt = raw forensic transcript, append-only, never compressed, pulled only for evidence (produced the Jun 11 instruction fixes).

## Plan changes this session (fold the instruction edits in before the next chat)
- Architecture is now REOPENABLE, not a handed-down blueprint. The 7-package layout + comms map are a starting proposal; Anshul derives each boundary/comms decision himself as he gains the primitive, and defends it. Where his reasoning wins, the proposal changes. Locked guardrails (Gazebo / digital-twin / kociemba+LAB / no sim motors) are NOT reopened this way.
- Curriculum additions decided:
  - ROS1-vs-ROS2 framing → top of Unit 3 (not its own unit).
  - QoS → woven into Unit 4 (topics).
  - Testing + CI + Docker → ONE fused unit after Unit 7, bounded scope (see instructions).
  - tf2 → parked for Phase 1 (description/Gazebo).
  - Debugging/introspection → woven into each unit, not standalone.
- Distributed-system model (no broker, peer-to-peer discovery, topic = name on a bus) to be front-loaded as a teaching stance.

## Phases
- Phase 0: Setup (install verify, repo, scaffold, CI)  ← WE ARE HERE (Git track done)
- Phase 1: Description + Gazebo  (tf2 lands here)
- Phase 2: Perception
- Phase 3: Planner
- Phase 4: Hardware abstraction + sim visualization
- Phase 5: Integration + polish + v1.0.0

## Unit sequence (revised)
- Unit 0a: Verify ROS2 Jazzy, install Gazebo Harmonic, verify webcam  ← DONE
- Unit 0b: SSH check, create scaffold commit  ← DONE
- Unit 1: Git basics (throwaway repo first)  ← DONE
- Unit 1b: Undoing, .gitignore, remotes  ← DONE
- Unit 1c: stash, force/force-with-lease, rebase + early-history capstone  ← DONE
- Unit 2: ROS2 workspace + package mechanics  ← NEXT
- Unit 3: The Node (hello world) — OPENS with ROS1-vs-ROS2 framing + distributed-system model
- Unit 4: Topics (pub/sub) — includes QoS
- Unit 5: Services (revisit CaptureFace.srv here)
- Unit 6: Parameters
- Unit 7: Launch files
- Unit 7.5 (NEW): Testing + CI + Docker — professional-workflow fused unit, bounded scope
- Unit 8: Scaffold real 7-package workspace (architecture-derivation payoff)
- Unit 9+: Project build (camera node, perception, ...)
- (Actions — the 4th comms primitive, /solve_cube — slot explicitly; likely a Unit 6.5 or folded where the planner is introduced. Decide at the time.)

## Completed units
- Unit 0a: ROS2 Jazzy; Gazebo Harmonic via ros-jazzy-ros-gz; v4l2_camera → /image_raw. Concepts: dependency resolution; camera chain; device-node vs ROS-node; publish-to-name decoupling; sourcing/PATH.
- Unit 0b: Cloned over SSH, added notes/learning_log.md. Concepts: scaffold commit = root; repo vs folder = history; tri-state model; add = snapshot-at-add-time; push moves origin/main.
- Unit 1: Git fundamentals. Concepts: branch = 41-byte pointer; HEAD two-level indirection; commits point backward only; commit advances current branch; divergence needs both sides past common ancestor; merge = two-parent commit, never linearizes; deletion safety gate; conflict = marker state, never picks/discards; diff always pairwise.
- Unit 1b: Undoing + .gitignore + remotes. Concepts: restore (staging→working-dir vs HEAD→staging); clean (only unrecoverable file op); object-store recoverability principle; reset --soft/--mixed/--hard (how many of 3 states ride along); reflog recovery; revert = inverse-diff new commit, safe for shared; conflict = context mismatch not absence; .gitignore = entry gate, no power over tracked files; remotes (origin/main = stale snapshot, fetch vs pull vs push, non-fast-forward rejection).
- Unit 1c: History rewriting + real capstone. Concepts owned:
  - stash: shelve uncommitted work as off-branch commits in .git/objects; stack semantics; pop (reapply + drop ref) vs apply (reapply + keep, reusable across branches / safe net); object lingers unreachable after pop until GC.
  - force-push: bare --force skips ALL checks, moves remote branch pointer to mine, orphans whatever was there (reachability = does a branch-tip backward-walk reach it). --force-with-lease adds one guard: remote tip must still equal my origin/main (what I last saw). Permits overwriting my own rewritten work, blocks steamrolling someone else's new commit. Lease check ≠ fast-forward check. Blind `git fetch` before force defeats the lease (refreshes origin/main). force-with-lease = always-default; bare force never strictly better.
  - rebase: commits immutable → can't re-parent → rebase REPLAYS (re-creates) commits as new objects on a new base, new hashes, originals orphaned. Differs from merge: straight line vs two-parent commit; rewrites history vs preserves fork. Interactive (-i): pick = keep, squash = fold into line above, reword = change message (separate editor per reword, fired in list order), drop = delete. First line must be pick (no predecessor). --root reaches the root commit.
  - amend: replaces last commit with new hash (message feeds the hash); if already pushed → diverges → needs force-with-lease.
  - Recovery under fire: used reflog + reset --hard to escape a botched rebase; --continue / --amend / --edit-todo to drive a paused interactive rebase; verified content intact before force-pushing (rewrite touches history, not files).
  - Capstone: rewrote 10→4 real commits on ros2_cube_solver, force-with-lease pushed; confirmed all pointers (HEAD/main/origin/main) realigned.

## Immediate next action
Start Unit 2: ROS2 workspace + package mechanics. First non-Git unit — colcon workspace, package structure, build/source cycle. (Practice on a throwaway workspace before the real scaffold, same guardrail as the throwaway repo.)

## Parked questions (revisit at the named unit — NOT before)
- .git/objects layer: commits/blobs are compressed objects (why you can cat a ref but not a commit) + what GC actually removes. Deferred in 1c by choice; open only if it surfaces or you want it.
- CaptureFace.srv design (Unit 5): request = {face: string}; response = {success: bool, colors: <shape TBD>, reason: string}.
- Camera pixel format/resolution (Unit 6): default rgb8 caps ~9 fps; lift into params, consider MJPEG.
- Perception algorithm rethink (Unit 9): make detection calibration-free; the lock on kociemba+LAB comes off HERE, on Anshul's say, with a specified change — not before. (Anshul flagged wanting to change perception this session; deferred to where he can reason about it as a ROS2 component.)
- cv_bridge color ordering (Unit 9 perception): /image_raw is rgb8, OpenCV/LAB expects bgr8.

## Guardrails — do NOT re-litigate these (locked decisions)
- Simulator is Gazebo Harmonic. Don't reopen Isaac vs Gazebo.
- Digital-twin: real webcam = truth, sim = visualizer. Don't simulate the camera.
- No motors/gantry in sim yet.
- kociemba + LAB classifier stay UNTIL Unit 9 (the rethink is parked there, not forbidden).
- One concept per response; teach the primitive before building.

## Definition of done (current unit → Unit 2)
Unit 2 done when you can confidently: explain what a colcon workspace is and why ROS2 uses one (src / build / install / log layout); create a package (ament_python and/or ament_cmake) and explain the difference; read package.xml and setup.py/CMakeLists.txt and say what each declares; explain the build → source install/setup.bash → run cycle and why sourcing the overlay is required; explain workspace overlay/underlay layering on top of the /opt/ros/jazzy underlay. Refine at unit start.
