# STATE.md — Cube Solver Project

## Where we are
Unit 2 (ROS2 workspace + package mechanics) DONE. Unit 3 (The Node) NEXT. First hands-on ROS2 unit complete — built a real ament_python package in a throwaway workspace and walked the entire build pipeline from the inside (src/build/install/log all opened up by hand). Session ran long on a curiosity tail (compiled-vs-interpreted, .pyc/bytecode, CPython/Cython/ctypes) after targets were met — all extra, all solid.

## Context system (three files, distinct jobs)
- STATE.md (this file) = curriculum bookmark. Paste at chat start.
- notes/learning_log.md = content in my words. Free-form.
- learning_ledger.md = process meta-layer (how teaching is going). Updated each session-end; paste at chat start when process-continuity matters.
- all_chats.odt = raw forensic transcript, append-only, never compressed, pulled only for evidence.

## Phases
- Phase 0: Setup (install verify, repo, scaffold, CI)  ← WE ARE HERE (Git track + workspace mechanics done)
- Phase 1: Description + Gazebo  (tf2 lands here)
- Phase 2: Perception
- Phase 3: Planner
- Phase 4: Hardware abstraction + sim visualization
- Phase 5: Integration + polish + v1.0.0

## Unit sequence
- Unit 0a: Verify ROS2 Jazzy, install Gazebo Harmonic, verify webcam  ← DONE
- Unit 0b: SSH check, create scaffold commit  ← DONE
- Unit 1: Git basics (throwaway repo first)  ← DONE
- Unit 1b: Undoing, .gitignore, remotes  ← DONE
- Unit 1c: stash, force/force-with-lease, rebase + early-history capstone  ← DONE
- Unit 2: ROS2 workspace + package mechanics  ← DONE
- Unit 3: The Node (hello world) — OPENS with ROS1-vs-ROS2 framing + distributed-system model  ← NEXT
- Unit 4: Topics (pub/sub) — includes QoS
- Unit 5: Services (revisit CaptureFace.srv here)
- Unit 6: Parameters
- Unit 7: Launch files
- Unit 7.5: Testing + CI + Docker — professional-workflow fused unit, bounded scope
- Unit 8: Scaffold real 7-package workspace (architecture-derivation payoff)
- Unit 9+: Project build (camera node, perception, ...)
- (Actions — the 4th comms primitive, /solve_cube — slot explicitly; likely Unit 6.5 or folded where the planner is introduced. Decide at the time.)

## Completed units
- Unit 0a: ROS2 Jazzy; Gazebo Harmonic via ros-jazzy-ros-gz; v4l2_camera → /image_raw. Concepts: dependency resolution; camera chain; device-node vs ROS-node; publish-to-name decoupling; sourcing/PATH.
- Unit 0b: Cloned over SSH, added notes/learning_log.md. Concepts: scaffold commit = root; repo vs folder = history; tri-state model; add = snapshot-at-add-time; push moves origin/main.
- Unit 1: Git fundamentals. Concepts: branch = 41-byte pointer; HEAD two-level indirection; commits point backward only; commit advances current branch; divergence needs both sides past common ancestor; merge = two-parent commit, never linearizes; deletion safety gate; conflict = marker state, never picks/discards; diff always pairwise.
- Unit 1b: Undoing + .gitignore + remotes. Concepts: restore (staging→working-dir vs HEAD→staging); clean (only unrecoverable file op); object-store recoverability principle; reset --soft/--mixed/--hard; reflog recovery; revert = inverse-diff new commit, safe for shared; conflict = context mismatch not absence; .gitignore = entry gate, no power over tracked files; remotes (origin/main = stale snapshot, fetch vs pull vs push, non-fast-forward rejection).
- Unit 1c: History rewriting + real capstone. stash, force/force-with-lease, rebase (replay = new objects), amend, reflog recovery; rewrote 10→4 real commits on ros2_cube_solver, force-with-lease pushed.
- Unit 2: ROS2 workspace + package mechanics. Concepts owned:
  - Workspace = a dir with src/ inside; you make src/, `colcon build` generates build/ install/ log/. Four-dir model walked from the inside.
  - Two lookups, two env vars: PATH finds the `ros2` program; AMENT_PREFIX_PATH (colon-list of prefixes) is how ros2 then finds packages under <prefix>/lib, /share, /include. "prefix" = base install dir (noun), look UNDER it.
  - ament = build system + on-disk layout convention, NOT an apt-style fetcher (rosdep does fetching — parked to ~Unit 9).
  - package.xml = identity (name/version/license/people) + phase-split deps (<depend>=build+exec+export, build_depend, exec_depend, test_depend, buildtool_depend) + build_type tag routing colcon to setuptools (ament_python) vs CMake (ament_cmake). Phase split → lean deployment (robot installs exec-only).
  - Python pkg structure: outer pkg dir (has package.xml) vs inner module (has __init__.py, code goes here) vs resource/ marker. setup.py holds entry_points/console_scripts (empty now → fills in Unit 3 to make `ros2 run` work).
  - Building ≠ compiling. Compile is ONE kind of build (C++ → machine code). For Python, build = arrange + register into prefix layout, no transformation.
  - src (source, untouched) → build (workbench: build-time-only stuff = staging + records, disposable) → install (the sourceable product). install.log = manifest of what got promoted build→install (the scratch/product dividing line). log/ = console transcript per run (stdout/stderr/command) for diagnosing failures; latest/ + latest_build/ are symlinks (ls -R won't recurse them by default).
  - colcon = orchestrating build tool; walks src/, builds in dep order, delegates per build_type. catkin = ROS1, ignore.
  - overlay/underlay: /opt/ros/jazzy = underlay, workspace install = overlay; source underlay THEN overlay; overlay found first (can override). Sourcing only affects current shell (new shell = fresh env, re-source).
  - --symlink-install: install/ links back to live code instead of copying → edit source, no rebuild. Python code via .egg-link (a path-forwarder; routes install→build→src, NOT a direct install→src hop — confirmed by reading the link target); non-code (package.xml) via real symlink. ls -l reveals links, cat hides them. New entry point / package.xml change still needs one rebuild. For C++ source: no benefit (binary must be recompiled); but non-compiled files (launch/config) still live-link.
  - Curiosity tail: .pyc = CPython BYTECODE (for the interpreter), NOT machine code (for the CPU); Python compiles source→bytecode then interprets it; .pyc is a disk cache, regenerated by Python (not colcon) when source mtime is newer; __pycache__ = the cache folder (gitignored). colcon build does NOT execute .py files, so does not generate .pyc. CPython = standard interpreter; Cython = tool compiling Python-ish→C/machine code for speed; ctypes = call existing C libs from Python. NumPy/OpenCV fast because hot loops are compiled native, Python is the glue.

## Immediate next action
Start Unit 3: The Node. OPENS with ROS1-vs-ROS2 framing + the distributed-system model (no central broker, peer-to-peer DDS discovery, topic = a name on a bus nobody owns). Then write the first rclpy node in first_pkg, fill console_scripts, `ros2 run` it.

## Parked questions (revisit at the named unit — NOT before)
- .git/objects layer: commits/blobs as compressed objects + what GC removes. Open only if it surfaces.
- CaptureFace.srv design (Unit 5): request = {face: string}; response = {success: bool, colors: <shape TBD>, reason: string}.
- Camera pixel format/resolution (Unit 6): default rgb8 caps ~9 fps; lift into params, consider MJPEG.
- Perception algorithm rethink (Unit 9): make detection calibration-free; the lock on kociemba+LAB comes off HERE, on my say, with a specified change.
- cv_bridge color ordering (Unit 9 perception): /image_raw is rgb8, OpenCV/LAB expects bgr8.
- rosdep (~Unit 9, or first time a real external dependency is missing): the apt-style fetcher that reads package.xml depends and installs them. ament is NOT this.
- --packages-select / --packages-up-to (Unit 8): build one package / that package + its dep chain, instead of whole workspace. (Cmds: `colcon build --packages-select <pkg>`, `--packages-up-to <pkg>`.) Have used select in AUV/ROV practice.

## Guardrails — do NOT re-litigate these (locked decisions)
- Simulator is Gazebo Harmonic. Don't reopen Isaac vs Gazebo.
- Digital-twin: real webcam = truth, sim = visualizer. Don't simulate the camera.
- No motors/gantry in sim yet.
- kociemba + LAB classifier stay UNTIL Unit 9 (the rethink is parked there, not forbidden).
- One concept per response; teach the primitive before building.

## Definition of done (Unit 3 → The Node)
Unit 3 done when I can: explain the ROS1→ROS2 shift (master/roscore → peer-to-peer DDS discovery) and why it matters; state the distributed-system model (no central broker, topic = name on a shared bus nobody owns); write a minimal rclpy node (import rclpy, Node subclass, init/spin/shutdown) and explain what each line does; declare it in console_scripts and `ros2 run` it; explain the node lifecycle and what spin actually does. Refine at unit start.