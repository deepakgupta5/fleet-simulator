# Fleet Simulator

Warehouse fleet simulator. Ten robots collect and deliver jobs through shared aisles. The eight-hour objective is a working fleet simulator that keeps completing jobs after disruptions.

## Operating rules

- Fictional warehouse, discrete simulation ticks. Repeatable: saved scenario inputs, fixed seed, recorded operator events.
- Grid with shelves, intersections, alternate routes and a narrow shared passage.
- Ten robots with distinct starting positions. One adjacent grid cell per tick. A robot can also wait.
- Jobs have pickup, delivery and priority.
- Robots cannot occupy the same cell or swap positions across an edge in the same tick.
- Two service stations with defined processing times and queues.
- Disruptions: close an empty aisle segment, disable a robot in its current position, introduce urgent jobs.
- The disabled robot remains an obstacle. It cannot disappear or teleport.
- Interface: map, robot states, job queue, controls, event history and performance measures.
- Simulation kept separate from rendering, so behaviour can be checked without the interface.

Out of scope for the first session: battery optimisation, charging station, physical robot dynamics, live integrations, production deployment.

These are simulation guarantees. They do not establish physical robot safety.

## Project idea

The full idea, in the owner's words: `docs/project-idea.md`.
