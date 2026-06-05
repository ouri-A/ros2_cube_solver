# STATE.md — Cube Solver Project

## Where we are
Unit 0a complete. Environment verified (ROS2 Jazzy, Gazebo Harmonic, webcam publishing). About to start Unit 0b. No project code or repo scaffold yet.

## Phases
- Phase 0: Setup (install verify, repo, scaffold, CI)  ← WE ARE HERE
- Phase 1: Description + Gazebo
- Phase 2: Perception
- Phase 3: Planner
- Phase 4: Hardware abstraction + sim visualization
- Phase 5: Integration + polish + v1.0.0

## Unit sequence
- Unit 0a: Verify ROS2 Jazzy, install Gazebo Harmonic, verify webcam  ← DONE
- Unit 0b: SSH check, create scaffold commit  ← NEXT
- Unit 1: Git basics (throwaway repo first)
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

## Immediate next action
Start Unit 0b. First step: verify SSH auth to GitHub (`ssh -T git@github.com`).

## Parked questions (revisit at the named unit — NOT before)
- CaptureFace.srv design (Unit 5): request = {face: string}; response = {success: bool, colors: <shape TBD>, reason: string}. The "shape of the 9 colors" (flat string vs array vs int+enum) is open.
- Repo name for packages confirmed as cube_solver_*. Repo itself is ros2_cube_solver.
- Camera pixel format/resolution (Unit 6): default rgb8 caps ~9 fps; lift format+resolution into params, consider MJPEG. Not a blocker now.
- cv_bridge color ordering (Unit 9 perception): /image_raw is rgb8, but OpenCV/LAB classifier expects bgr8 (hence old code's COLOR_BGR2LAB). cv_bridge conversion must get R/B order right or all colors misread.

## Guardrails — do NOT re-litigate these (locked decisions)
- Simulator is Gazebo Harmonic. Don't reopen Isaac vs Gazebo.
- Digital-twin: real webcam = truth, sim = visualizer. Don't simulate the camera.
- No motors/gantry in sim yet.
- kociemba + LAB classifier stay. Don't rewrite the algorithm.
- One concept per response; teach the primitive before building.

## Definition of done (current unit → 0b)
Unit 0b done when: `ssh -T git@github.com` authenticates successfully, and an initial scaffold commit (README, LICENSE, .gitignore, STATE.md, notes/) is pushed to the ros2_cube_solver repo. Update this each unit with the next unit's done-criteria.

## Open setup items
- ssh -T git@github.com not yet verified
