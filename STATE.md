# STATE.md — Cube Solver Project
## Where we are
Unit 1 complete. Git fundamentals drilled hands-on on a throwaway repo (github_playaround). Branching, merging, conflict resolution, and diff all owned mechanically via the pointer + tri-state model. About to start Unit 2.
## Phases
- Phase 0: Setup (install verify, repo, scaffold, CI)  ← WE ARE HERE
- Phase 1: Description + Gazebo
- Phase 2: Perception
- Phase 3: Planner
- Phase 4: Hardware abstraction + sim visualization
- Phase 5: Integration + polish + v1.0.0
## Unit sequence
- Unit 0a: Verify ROS2 Jazzy, install Gazebo Harmonic, verify webcam  ← DONE
- Unit 0b: SSH check, create scaffold commit  ← DONE
- Unit 1: Git basics (throwaway repo first)  ← DONE
- Unit 2: ROS2 workspace + package mechanics  ← NEXT
- Unit 3: The Node (hello world)
- Unit 4: Topics (pub/sub)
- Unit 5: Services (revisit CaptureFace.srv here)
- Unit 6: Parameters
- Unit 7: Launch files
- Unit 8: Scaffold real 7-package workspace
- Unit 9+: Project build (camera node, perception, ...)
## Completed units
- Unit 0a: ROS2 Jazzy confirmed; Gazebo Harmonic via ros-jazzy-ros-gz (gz sim 8.11.0); v4l2_camera publishing sensor_msgs/msg/Image to /image_raw. Concepts: declared (versioned) dependency as the apt resolution mechanism; camera chain (UVC→uvcvideo→V4L2→/dev/videoN→v4l2_camera→topic); device-node vs ROS-node distinction; publish-to-name-not-device decoupling; sourcing/PATH.
- Unit 0b: Cloned existing GitHub repo over SSH, added notes/learning_log.md as deliberate commit. Concepts: scaffold commit = root of history; repo vs folder = history; Git tracks files not folders; tri-state model; add = snapshot-at-add-time into staging (not a live link); commit saves staging; push moves origin/main.
- Unit 1: Git fundamentals hands-on (throwaway repo). Concepts owned: branch = plain-text pointer file under .git/refs/heads/ holding one commit hash (41 bytes — branching is cheap, literally); HEAD = file holding "ref: refs/heads/<branch>", two-level indirection HEAD→branch→commit; lookup traced off disk; commits point backward only (child→parent), immutable, never forward; commit advances the branch HEAD names (not "main" specifically), HEAD only retargets on switch; git branch creates file w/o switching, git switch -c creates+switches; divergence needs BOTH sides past common ancestor (one side moving = still straight line); merge = NEW commit with TWO parents that keeps both lines intact (does NOT linearize/flatten/disconnect); merge advances the branch you're ON, source branch unmoved; branch deletion (-d) is a safety gate against orphaning unreachable commits, -D overrides; deleting a branch never affects another branch's commit; conflict = paused merge state, Git writes BOTH versions w/ <<<< ==== >>>> markers (never picks/discards — never-lose-work), git merge --abort escapes; resolve = edit file correct AND marker-free, git add signals resolved (reuses staging primitive), git commit concludes; clean merge and conflict merge produce identical graph structure; git diff is always pairwise — plain = working↔staging, --staged/--cached = staging↔history; +/- = new/old side.
## Immediate next action
Start Unit 2: ROS2 workspace + package mechanics. First real ros2_cube_solver structure work (but scaffolding the 7 packages for real is Unit 8 — Unit 2 is learning the mechanics).
## Parked questions (revisit at the named unit — NOT before)
- Early-history cleanup capstone: ros2_cube_solver's early commits are accreted web edits (Initial commit → title → "state" → Update STATE.md ×N). Now that branches/merge/diff are owned, the history-rewrite exercise is unlocked — but it's a real rewrite on the actual repo, so do it as its OWN focused session, not tacked onto a heavy one. Needs rebase/reset, not yet taught. Not a blocker.
- .git/objects layer: commits/blobs are compressed objects (not plain text like refs) — why you can't cat a commit. Parked; open only if needed.
- commit parent vs "previous in time" — fully resolved this unit (branches separate them). Closed.
- CaptureFace.srv design (Unit 5): request = {face: string}; response = {success: bool, colors: <shape TBD>, reason: string}.
- Camera pixel format/resolution (Unit 6): default rgb8 caps ~9 fps; lift into params, consider MJPEG.
- cv_bridge color ordering (Unit 9 perception): /image_raw is rgb8, OpenCV/LAB expects bgr8.
## Guardrails — do NOT re-litigate these (locked decisions)
- Simulator is Gazebo Harmonic. Don't reopen Isaac vs Gazebo.
- Digital-twin: real webcam = truth, sim = visualizer. Don't simulate the camera.
- No motors/gantry in sim yet.
- kociemba + LAB classifier stay. Don't rewrite the algorithm.
- One concept per response; teach the primitive before building.
## Definition of done (current unit → Unit 2)
Unit 2 done when: you understand what a ROS2 workspace is (src/ + build/install/log, the colcon build cycle), what a package is and the two build types (ament_python vs ament_cmake) and when each, the anatomy of a package (package.xml, setup.py/CMakeLists.txt, the role of each), and how sourcing the workspace overlay relates to the underlay (ties back to 0a's sourcing/PATH). Mechanics only — not scaffolding the real 7 packages yet (that's Unit 8). Refine at unit start.