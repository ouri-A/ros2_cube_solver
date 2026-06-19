# STATE.md — Cube Solver Project

## Where we are
Unit 4 (Topics / pub-sub) DONE. Unit 5 (Services) NEXT. Built a real publisher node and a real subscriber node from scratch in the sandbox (`first_pkg`), wired them over `/gibberish`, watched messages flow, introspected the whole graph, and shipped the work the real way with branch → conventional commits → merge (plus a reset/branch-surgery recovery from a self-made workflow slip). QoS covered conceptually (what + why), deeper tuning deferred to real-project build.

## Phases
- Phase 0: Setup <- WE ARE HERE (Git + workspace + first node + pub/sub done)
- Phase 1: Description + Gazebo (tf2 lands here)
- Phase 2: Perception
- Phase 3: Planner
- Phase 4: Hardware abstraction + sim visualization
- Phase 5: Integration + polish + v1.0.0

## Unit sequence
- Units 0a-2 <- DONE
- Unit 3: The Node <- DONE
- Unit 4: Topics (pub/sub) — incl. QoS conceptual <- DONE
- Unit 5: Services (revisit CaptureFace.srv) <- NEXT
- Unit 6: Parameters
- Unit 7: Launch files
- Unit 7.5: Testing + CI + Docker (bounded)
- Unit 8: Scaffold real 7-package workspace
- Unit 9+: Project build
- (Actions — 4th comms primitive, /solve_cube — slot ~Unit 6.5 or with planner)
- Review unit: deferred until AFTER all three comms primitives (topics/services/actions) covered — enables comparative interleaving. Anshul's call on exact slot.

## Completed units
- Unit 3: The Node. (concepts unchanged — see prior STATE; distributed discovery, rclpy lifecycle, double-shutdown root cause, console_scripts grammar, logger->/rosout.)
- Unit 4: Topics (pub/sub). Concepts owned:
  - Topic = named channel; publishers write, subscribers read, many-to-many, matched by NAME + TYPE. Both must agree or DDS silently won't connect (one of two "why isn't my sub getting anything" causes).
  - Decoupling: publisher fires into the void, doesn't know/care who (if anyone) subscribes. Sub count can be 0 and that's a valid running state. Late-joining sub finds the stream purely by name.
  - Topics are a STREAM (continuous, one-way, fire-and-forget) — no request/reply, no history replay. A subscriber (incl. `ros2 topic echo`) catches the stream from "now," not from the start.
  - Messages: `.msg` file = text schema → compiled to generated code (Python class / C++ struct) at build time. That's WHY name+type matching is airtight (both sides use code from same schema). `std_msgs/String` = `string data`. Default-default values are type-zero (`""`, 0, false, empty). Messages compose (a field can be another msg type) — basis for future `cube_solver_msgs`.
  - Machinery / trigger asymmetry (CONCEPTUAL CORE):
    - Publisher: internal trigger = TIMER. `create_timer(period, cb)`; spin fires cb on schedule; cb does construct→fill→publish. No callback arg passed to `create_publisher`.
    - Subscriber: external trigger = message ARRIVAL. `create_subscription(type, name, cb, qos)`; spin fires cb when DDS delivers. Callback takes a `msg` arg (the delivered message); timer cb takes none.
    - Same spin underneath; only the trigger differs.
  - QoS (covered conceptually — what + why, not deep tuning): per-topic delivery contract. Three load-bearing knobs:
    - Reliability: reliable (retry until delivered) vs best-effort (fire-and-forget).
    - Durability: volatile (late sub gets only new msgs) vs transient-local (publisher holds last msg(s), delivers to late joiners).
    - History: keep-last-N (ring buffer, the `10`) vs keep-all.
    - Incompatible QoS = DDS silently refuses to connect (2nd "why isn't my sub getting anything" cause).
    - /image_raw (sensor stream): best-effort + volatile (latest beats complete; stale frame is misleading). /cube/state (latched-ish state): reliable + transient-local (late-joining planner must get the one-shot value).
  - Introspection toolkit: list / info / echo (knew); type (redundant), find (reverse type→topics lookup), hz / bw / delay (the diagnostics trio — rate / bandwidth / staleness, key for /image_raw later), pub (CLI publish, isolation-test a sub), --include-hidden-topics. rqt_graph (visual graph; tool is itself a node, shows up in own graph).

## Immediate next action
Start Unit 5: Services. Request/response primitive (vs topic's stream). Revisit CaptureFace.srv. Carry the parked topic-vs-service question for /cube/state into this unit.

## Review-debt tally (spaced-retrieval layer)
Weighted concepts since last review (heavy=3 / standard=2 / light=1).
Git as a PERFORMED skill is EXCLUDED (owned by per-unit Git task). EXCEPTION fired this unit: Git "why"s I had to explain (not derive) rejoin the recall tally.
- Setup units (0a/0b): ~3 (light)
- Unit 2 (workspace/package/colcon): ~4-5 (standard)
- Unit 3 (The Node): ~16
- Unit 4 (Topics): +16
    - heavy: timer-vs-arrival trigger asymmetry; QoS (3 knobs + silent mismatch)
    - standard: pub/sub decoupling; name+type contract; .msg→generated code; git reset flavors (the WHY, rejoined per rule)
    - light: topic introspection toolkit; conventional-commit rationale (the WHY)
- CURRENT DEBT: ~39. Status: HIGH. Review deferred until after services + actions (Anshul's locked call). Debt rolls forward.

## Parked questions
- /cube/state: TOPIC vs SERVICE? (NEW — Anshul raised it Unit 4.) A one-shot computed value read on demand smells like request/response. Decide in Unit 5 once services are understood. Tagged to Unit 5 targets.
- .git/objects layer: open only if it surfaces.
- CaptureFace.srv (Unit 5): request {face}, response {success, colors, reason}.
- Camera pixel format/resolution (Unit 6): rgb8 ~9fps; lift to params, consider MJPEG. (bw command now in toolkit for diagnosing this.)
- Perception rethink (Unit 9): calibration-free; kociemba+LAB lock comes off here on my say.
- cv_bridge color ordering (Unit 9): /image_raw rgb8 vs OpenCV bgr8.
- rosdep (~Unit 9): apt-style fetcher reading package.xml; ament is NOT this.
- --packages-select / --packages-up-to (Unit 8).
- namespaces/remapping (`--ros-args -r`) deferred to Unit 7.
- QoS deep tuning (real profiles in code): deferred to perception/planner build (Phase 2-3).
- Workflow note (not a question, a habit fix): branch FIRST, then all commits land on the branch — master moves only via merge. Slipped once this unit, recovered. Reflex for next unit.

## Guardrails (unchanged)
- Gazebo Harmonic; digital-twin (real webcam=truth, sim=visualizer); no motors in sim; kociemba+LAB until Unit 9; one concept per response, primitive before building.
- ARCHITECTURE IS ANSHUL'S TO DERIVE: the 7-package structure + listed comms-primitive assignments (/cube/state as topic, etc.) are a PROPOSAL / reference point, NOT a locked blueprint. Anshul decides each architectural call himself, with enough understanding to defend it, when building the real project. Never present these as settled/handed-down.

## Definition of done (Unit 5 -> Services)
Done when I can: explain request/response and how it differs from a topic's one-way stream (when do you NEED an answer back?); explain client vs server roles; write a service server node and a client node; create/use a .srv interface (request/response structure); call a service via CLI (`ros2 service call`) and introspect (`ros2 service list/type`); explain blocking-vs-async client calls and why spin matters for the server. PLUS decide the parked /cube/state topic-vs-service question. PLUS the per-unit Git task (baseline ship + challenge scaled to unit). Refine at unit start.