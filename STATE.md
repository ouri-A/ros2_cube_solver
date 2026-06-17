# STATE.md — Cube Solver Project

## Where we are
Unit 3 (The Node) DONE. Unit 4 (Topics / pub-sub) NEXT. First real rclpy node written, run, introspected, and debugged to the metal. Wrote the conventional node (subclass + main(args) + guard), wired console_scripts, ran via `ros2 run`, confirmed discovery with `ros2 node list`, introspected with `ros2 node info`. Hit and fully resolved the double-shutdown RCLError — root cause verified against rclpy docs, not guessed.

## Phases
- Phase 0: Setup <- WE ARE HERE (Git + workspace + first node done)
- Phase 1: Description + Gazebo (tf2 lands here)
- Phase 2: Perception
- Phase 3: Planner
- Phase 4: Hardware abstraction + sim visualization
- Phase 5: Integration + polish + v1.0.0

## Unit sequence
- Units 0a-2 <- DONE
- Unit 3: The Node <- DONE
- Unit 4: Topics (pub/sub) — includes QoS <- NEXT
- Unit 5: Services (revisit CaptureFace.srv)
- Unit 6: Parameters
- Unit 7: Launch files
- Unit 7.5: Testing + CI + Docker (bounded)
- Unit 8: Scaffold real 7-package workspace
- Unit 9+: Project build
- (Actions — 4th comms primitive, /solve_cube — slot ~Unit 6.5 or with planner)

## Completed units
- Unit 3: The Node. Concepts owned:
  - ROS1 master (roscore) = central matchmaker: nodes register, it hands over addresses, then steps OUT of the data path. Verified live: kill roscore and existing peer-to-peer connections keep flowing; only NEW discovery dies. roslaunch-auto-started roscore dies with the launch (gotcha).
  - ROS2 deleted the master. Distributed discovery: every node announces itself on startup + listens for others' announcements over DDS; matching is done by every node independently. Knowledge of who-publishes-what lives "everywhere," not in one registry.
  - DDS = the middleware underneath (industrial standard); rclpy = Python client library = thin doorway onto the compiled C core (rcl). "client" = your code is the client of the core.
  - Node lifecycle: rclpy.init() (stand up process context, hook into DDS, AND install SIGINT/SIGTERM handlers by default) -> create Node (announce on graph) -> spin (hand thread to ROS, park, wake on work — the engine that fires every callback) -> shutdown (unhook).
  - Convention: subclass Node; main(args=None) + rclpy.init(args=args); if __name__=="__main__" guard. main name + guard are convention; only console_scripts ":main" binding is load-bearing.
  - console_scripts entry: command_name = package.module:function. Entry-point name != node graph name.
  - Double-shutdown RCLError ROOT CAUSE (verified, rclpy Jazzy docs): rclpy.init() installs a SIGINT handler -> on Ctrl+C it shuts the context down (#1) -> explicit rclpy.shutdown() is redundant (#2). Fix: `finally: if rclpy.ok(): rclpy.shutdown()`. Two pieces two jobs: `except KeyboardInterrupt: pass` = no traceback; `if rclpy.ok()` guard = no double-shutdown. Need both.
  - get_logger().info/warn/error -> /rosout (logging IS a publisher on a topic).
  - ros2 node info <name>: full interface surface. Empty-node baseline = /rosout + /parameter_events + parameter service servers (freebies).

## Immediate next action
Start Unit 4: Topics (pub/sub). Build a publisher node + subscriber node, wire over a topic, watch messages flow. Timer+callback pattern lands here. QoS woven in (reliability/durability — the "why didn't my subscriber get it" lesson; /image_raw stream vs /cube/state latched). Messages (std_msgs) introduced.

## Review-debt tally (NEW — spaced-retrieval layer)
Weighted concepts since last review (heavy=3 / standard=2 / light=1).
Git as a performed skill is EXCLUDED (owned by the per-unit Git task).
- Setup units (0a/0b): ~3 (light)
- Unit 2 (workspace/package/colcon): ~4-5 (standard)
- Unit 3 (The Node): ~16
    - heavy: distributed discovery, rclpy lifecycle, double-shutdown root cause
    - standard: subclass-Node convention, console_scripts grammar, logger->/rosout
    - light: empty-node freebie baseline
- CURRENT DEBT: ~23. Status: HIGH — review unit worth calling soon. Anshul's call.

## Parked questions (unchanged unless noted)
- .git/objects layer: open only if it surfaces.
- CaptureFace.srv (Unit 5): request {face}, response {success, colors, reason}.
- Camera pixel format/resolution (Unit 6): rgb8 ~9fps; lift to params, consider MJPEG.
- Perception rethink (Unit 9): calibration-free; kociemba+LAB lock comes off here on my say.
- cv_bridge color ordering (Unit 9): /image_raw rgb8 vs OpenCV bgr8.
- rosdep (~Unit 9): apt-style fetcher reading package.xml; ament is NOT this.
- --packages-select / --packages-up-to (Unit 8).
- timer+callback formally lands Unit 4; namespaces/remapping (`--ros-args -r`) deferred to Unit 7.

## Guardrails (unchanged)
- Gazebo Harmonic; digital-twin (real webcam=truth, sim=visualizer); no motors in sim; kociemba+LAB until Unit 9; one concept per response, primitive before building.

## Definition of done (Unit 4 -> Topics)
Done when I can: explain pub/sub and the publisher-doesn't-know-subscribers decoupling; write a publisher node (timer-driven) and a subscriber node, each with a callback, and explain what spin does for each; create/use a std_msgs message; explain QoS (reliability/durability/history) and why a sensor stream and a latched state want different profiles; introspect both with node info / topic echo / topic info. PLUS the per-unit Git task (baseline ship + a challenge scaled to the unit). Refine at unit start.