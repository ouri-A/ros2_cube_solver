# STATE.md — Cube Solver Project

## Where we are
Unit 3 (The Node) DONE. Unit 4 (Topics / pub-sub) NEXT. First real rclpy node written, run, introspected, and debugged to the metal. Wrote the conventional node (subclass + main(args) + guard), wired console_scripts, ran via `ros2 run`, confirmed discovery with `ros2 node list`, introspected with `ros2 node info`. Hit and fully resolved the double-shutdown RCLError — root cause verified against rclpy docs, not guessed.

## Phases
- Phase 0: Setup ← WE ARE HERE (Git + workspace + first node done)
- Phase 1: Description + Gazebo (tf2 lands here)
- Phase 2: Perception
- Phase 3: Planner
- Phase 4: Hardware abstraction + sim visualization
- Phase 5: Integration + polish + v1.0.0

## Unit sequence
- Units 0a–2 ← DONE
- Unit 3: The Node ← DONE
- Unit 4: Topics (pub/sub) — includes QoS ← NEXT
- Unit 5: Services (revisit CaptureFace.srv)
- Unit 6: Parameters
- Unit 7: Launch files
- Unit 7.5: Testing + CI + Docker (bounded)
- Unit 8: Scaffold real 7-package workspace
- Unit 9+: Project build
- (Actions — 4th comms primitive, /solve_cube — slot ~Unit 6.5 or with planner)

## Completed units (add to existing)
- Unit 3: The Node. Concepts owned:
  - ROS1 master (roscore) = central matchmaker: nodes register, it hands over addresses, then steps OUT of the data path. Verified live: kill roscore and existing peer-to-peer connections keep flowing; only NEW discovery dies. roslaunch-auto-started roscore dies with the launch (gotcha).
  - ROS2 deleted the master. Distributed discovery: every node announces itself on startup + listens for others' announcements over DDS; matching is done by every node independently. Knowledge of who-publishes-what lives "everywhere," not in one registry.
  - DDS = the middleware underneath (industrial standard); rclpy = Python client library = thin doorway onto the compiled C core (rcl). "client" = your code is the client of the core.
  - Node lifecycle: rclpy.init() (stand up process context, hook into DDS, AND install SIGINT/SIGTERM handlers by default) → create Node (announce on graph) → spin (hand thread to ROS, park, wake on work — the engine that fires every callback) → shutdown (unhook).
  - Convention: subclass Node (gives the node a body — self holds state + publishers + callbacks; bare Node spills into globals); main(args=None) + rclpy.init(args=args) (thread CLI/ROS args through, esp. --ros-args remaps); if __name__=="__main__" guard (file safe to import — nothing runs as import side-effect — and runnable standalone). main name + guard are convention, not mechanism; only console_scripts ":main" binding is load-bearing.
  - console_scripts entry: command_name = package.module:function. ":" splits file vs function, "." splits package vs file. Entry-point name ≠ node graph name (ran `first_node`, graph showed `/first_node_bih`).
  - Double-shutdown RCLError ROOT CAUSE (verified, rclpy Jazzy docs): rclpy.init() installs a SIGINT handler by default → on Ctrl+C that handler shuts the context down (shutdown #1) → explicit rclpy.shutdown() is redundant #2 → "rcl_shutdown already called". Proven by single clean Ctrl+C still erroring. Fix: `finally: if rclpy.ok(): rclpy.shutdown()` (or rclpy.try_shutdown()).
  - Two pieces, two jobs (both confirmed by experiment): `except KeyboardInterrupt: pass` = clean exit, no traceback (interrupt DOES surface out of spin in Jazzy — tested bare-spin, got the traceback); `if rclpy.ok()` guard = no double-shutdown. Need both.
  - get_logger().info/warn/error → /rosout (logging IS a publisher on a topic). Stamps level + timestamp + node name; beats print() once multiple nodes run.
  - ros2 node info <name>: a node's full interface surface. Empty-node baseline = /rosout + /parameter_events publishers + the parameter service servers, all freebies. Your own pubs/subs appear against this baseline.

## Immediate next action
Start Unit 4: Topics (pub/sub). Build a publisher node + subscriber node, wire over a topic, watch messages flow. Timer+callback pattern lands here (the concrete thing spin drives). QoS woven in (reliability/durability — the "why didn't my subscriber get it" lesson; /image_raw stream vs /cube/state latched). Messages (std_msgs) introduced.

## Parked questions (unchanged unless noted)
- .git/objects layer: open only if it surfaces.
- CaptureFace.srv (Unit 5): request {face}, response {success, colors, reason}.
- Camera pixel format/resolution (Unit 6): rgb8 ~9fps; lift to params, consider MJPEG.
- Perception rethink (Unit 9): calibration-free; kociemba+LAB lock comes off here on my say.
- cv_bridge color ordering (Unit 9): /image_raw rgb8 vs OpenCV bgr8.
- rosdep (~Unit 9): apt-style fetcher reading package.xml; ament is NOT this.
- --packages-select / --packages-up-to (Unit 8).
- NEW (tiny, optional): timer+callback formally lands Unit 4; namespaces/remapping (`--ros-args -r`) deferred to Unit 7 (launch era).

## Guardrails (unchanged)
- Gazebo Harmonic; digital-twin (real webcam=truth, sim=visualizer); no motors in sim; kociemba+LAB until Unit 9; one concept per response, primitive before building.

## Definition of done (Unit 4 → Topics)
Done when I can: explain pub/sub and the publisher-doesn't-know-subscribers decoupling; write a publisher node (timer-driven) and a subscriber node, each with a callback, and explain what spin does for each; create/use a std_msgs message; explain QoS (reliability/durability/history) and why a sensor stream and a latched state want different profiles; introspect both with node info / topic echo / topic info. Refine at unit start.