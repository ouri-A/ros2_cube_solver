# STATE.md — Cube Solver Project
## Where we are
Unit 0b complete. Repo cloned locally, scaffold confirmed (README, LICENSE, .gitignore, STATE.md, notes/learning_log.md), SSH auth verified, all pushed. About to start Unit 1.
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
- Unit 1: Git basics (throwaway repo first)  ← NEXT
- Unit 2: ROS2 workspace + package mechanics
- Unit 3: The Node (hello world)
- Unit 4: Topics (pub/sub)
- Unit 5: Services (revisit CaptureFace.srv here)
- Unit 6: Parameters
- Unit 7: Launch files
- Unit 8: Scaffold real 7-package workspace
- Unit 9+: Project build (camera node, perception, ...)
## Completed units
- Unit 0a: ROS2 Jazzy confirmed (ROS_DISTRO=jazzy); Gazebo Harmonic installed via ros-jazzy-ros-gz (gz sim 8.11.0, shapes.sdf launches); v4l2_camera publishing sensor_msgs/msg/Image to /image_raw (~9 fps, rgb8, 640x480).
  Concepts owned: dependency-driven version selection; the camera stack (UVC→uvcvideo→V4L2→/dev/video0→v4l2_camera→topic) and why V4L2 vs the ROS pkg each exist; driver-node device→topic bridge; publish-to-name-not-device decoupling; sensor_msgs/Image layout (step = width*channels*bytes); what a shell is + PATH command resolution; what sourcing does (edits current shell env, prepends bin to PATH) and source-vs-execute; `which` as PATH search made visible; one-distro-per-shell rule.
- Unit 0b: Repo was created on GitHub web (so root commit + scaffold files already existed remotely); cloned down over SSH; added the one missing piece (notes/learning_log.md) as a deliberate commit and pushed.
  Concepts owned: scaffold commit = root of project history, the foundation all later commits descend from; repo vs bare folder = history (the parent chain enabling return to any prior state); commit's parent ≠ always "previous in time" (branches separate them — parked for Unit 1); Git tracks files, not folders (empty dirs invisible; need a placeholder file, .gitkeep by convention, but a real file like learning_log.md is preferred); the tri-state model (working dir → staging area/index → committed history); add = snapshot-at-add-time into staging, NOT a save and NOT a live link to the file (edit-after-add leaves the edit unstaged); commit = the actual save; clone copies the whole repo (files + full history), not just files; git status collapses fully-untracked dirs to dir-name (`-u` lists files individually); push moves origin/main to follow local main (saw the local-ahead-of-remote gap close).
## Immediate next action
Start Unit 1: Git basics, on a throwaway repo first (not ros2_cube_solver).
## Parked questions (revisit at the named unit — NOT before)
- Early-history cleanup (Unit 1): ros2_cube_solver's first commits are accreted web edits (Initial commit → title → "state" → Update STATE.md ×N) rather than one clean scaffold root. Optional history-rewrite exercise once history tools are learned safely on a throwaway repo. Not a blocker.
- commit parent vs "previous commit" distinction (Unit 1): lands properly when branching is taught.
- CaptureFace.srv design (Unit 5): request = {face: string}; response = {success: bool, colors: <shape TBD>, reason: string}. Shape of the 9 colors open.
- Camera pixel format/resolution (Unit 6): default rgb8 caps ~9 fps; lift into params, consider MJPEG.
- cv_bridge color ordering (Unit 9 perception): /image_raw is rgb8, OpenCV/LAB expects bgr8; conversion must get R/B order right or colors misread.
## Guardrails — do NOT re-litigate these (locked decisions)
- Simulator is Gazebo Harmonic. Don't reopen Isaac vs Gazebo.
- Digital-twin: real webcam = truth, sim = visualizer. Don't simulate the camera.
- No motors/gantry in sim yet.
- kociemba + LAB classifier stay. Don't rewrite the algorithm.
- One concept per response; teach the primitive before building.
## Definition of done (current unit → Unit 1)
Unit 1 done when: on a throwaway repo, you can confidently init/add/commit, read git status/log/diff, create and switch branches, merge, and understand what each does mechanically (not just the command). Possibly: redo the ros2_cube_solver early-history cleanup as the capstone exercise. Refine criteria at unit start.