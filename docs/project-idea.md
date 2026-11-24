# Fleet simulator — new Alignerr project idea (user's words, 24 Nov 2026)

What the project does and who it is for, why you are excited to work on it, and what makes it hard. Name the main parts that must work together. Skip basic apps, simple CRUD, lists and small databases: today's models handle those easily.

Pick a large, complex, challenging system you are genuinely excited to see come to life, and once it is approved, keep expanding it into something truly ambitious and interesting. Examples of the scale we want: a real-time simulation of the entire world's supply chain; a full refactor of a massive legacy codebase; an accurate particle collider simulator.

## The idea

**The eight-hour objective is a working fleet simulator that keeps completing jobs after disruptions.** We need to see whether Claude can connect job assignment, shared-space routing and recovery into one coherent system.

Ten robots moving around a screen is insufficient. The difficult part is making progress when robots compete for the same space, priorities change and a route becomes unavailable.

## 1. Define the operating rules first

Use a fictional warehouse and discrete simulation ticks. This keeps the behaviour testable and repeatable.

| Element | Session scope |
|---|---|
| Warehouse | A grid with shelves, intersections, alternate routes and a narrow shared passage |
| Fleet | Ten robots with distinct starting positions |
| Workload | Jobs with pickup, delivery and priority |
| Movement | One adjacent grid cell per tick. A robot can also wait |
| Shared space | Robots cannot occupy the same cell or swap positions across an edge in the same tick |
| Stations | Two service stations with defined processing times and queues |
| Disruptions | Close an empty aisle segment, disable a robot in its current position and introduce urgent jobs |
| Interface | Map, robot states, job queue, controls, event history and performance measures |
| Repeatability | Saved scenario inputs, fixed seed and recorded operator events |

The disabled robot remains an obstacle. It cannot disappear or teleport to make planning easier.

Leave battery optimisation, physical robot dynamics, live integrations and production deployment outside this session. Remove the charging station from the first-session requirements. It adds another scheduling problem before we have proved the central one.

These are simulation guarantees. They do not establish physical robot safety.

## 2. Start Claude with the whole operating problem

The first message should explain the intended system and the difficult case. We should not spend several turns commissioning disconnected foundations.

The opening direction would be:

> I want to build a warehouse fleet simulator where ten robots collect and deliver jobs through shared aisles.
>
> I want to see how the fleet behaves under congestion. Job assignment and routing must work together. Choosing the closest robot can be a poor decision if its route is blocked or heavily contested.
>
> Start with a simple visual warehouse. Coloured shapes are sufficient. Show each robot's job, planned route and reason for waiting.
>
> The central case is an operating warehouse where I close an aisle, disable a robot near an intersection and add urgent work. The remaining fleet must continue completing reachable jobs. It must not resolve conflicts by moving through another robot or permanently stopping the whole fleet.
>
> Use fictional data and a repeatable simulation. Keep the simulation separate from rendering so we can check its behaviour without the interface.
>
> First explain your movement rules, conflict handling and approach to avoiding permanent waiting. Then implement the smallest connected version that assigns jobs, moves robots and completes deliveries. Run it before handing it back.

Claude chooses the architecture and planning approach. We judge whether its choices meet the operating requirements.

## 3. Work through six checkpoints

The allocations below total eight hours. They are planning estimates. We record actual work through Hubstaff and move between checkpoints based on evidence.

| Allocation | Work | Required result before advancing |
|---|---|---|
| 45 minutes | Settle movement, station and job rules. Create the warehouse and scenario inputs | Rules are explicit. The map supports the intended routes and alternate paths |
| 90 minutes | Build the connected simulation and basic display | Robots receive jobs, travel to pickup, deliver and release the job correctly |
| 90 minutes | Exercise shared-space conflicts and congestion | Conflicting moves are prevented. Feasible encounters eventually clear |
| 90 minutes | Add and test operator disruptions | Aisle closure and robot failure trigger visible, understandable responses |
| 75 minutes | Run a separate fictional acceptance workload and correct defects | Results meet expectations fixed before execution |
| 90 minutes | Run the final exercise, repeat it and prepare the evidence package | Reproducible outcome, complete checks and clear remaining limits |

**If shared-space coordination is still wrong, we stay there.** We do not move on because the interface looks finished.

## 4. Use each build before responding

At each checkpoint:

1. Claude runs its own checks and provides the launch command.
2. You run the simulator and inspect the relevant behaviour.
3. We compare what happened with the operating rules.
4. Your next message describes actual observations.
5. Claude reproduces the issue, corrects it and reruns the affected checks.

Useful things to inspect include:

- Does a robot wait because of another robot, a station queue or an unavailable route?
- Can you tell which reservation prevents movement?
- Does urgent work interrupt an existing assignment? If so, what happens to that assignment?
- Does one robot repeatedly lose access to an intersection?
- Does rerouting create oscillation between two equally attractive paths?
- Does a disconnected destination remain visibly unserviceable?

We will not prepare fictional complaints or assume a bug exists. The next instruction follows the run.

## 5. Check the critical paths separately

Before the final exercise, use small scenarios where the expected behaviour is understandable.

| Check | Expected behaviour | Failure |
|---|---|---|
| Single delivery | Pickup precedes delivery. One completion is recorded | Delivery occurs before pickup, or completion is counted twice |
| Intersection conflict | Conflicting arrivals are serialized | Two robots occupy the intersection together |
| Head-on movement | Robots cannot swap adjacent cells in one tick | Robots pass through each other |
| Narrow passage with passing space | Robots yield and eventually clear the encounter | Permanent waiting despite an available resolution |
| Station queue | Station capacity and service time are respected | Multiple robots receive service beyond capacity |
| Closed aisle | Robots avoid the closed cells and use an available alternate route | A robot enters a closed cell or never updates its route |
| Disabled robot | Its position remains occupied. Other robots route around it where possible | The failed robot disappears or another robot moves through it |
| Unreachable job | The system reports the reason and continues unrelated work | It invents completion or stalls the entire fleet |
| Priority conflict | Priority affects scheduling under the declared policy | Priority labels have no effect, or ordinary work waits indefinitely in a finite workload |
| Repeated scenario | The same inputs produce the same simulation events and result | Rendering speed changes the simulation outcome |

A robot waiting is not automatically a defect. Waiting can be the correct decision. We need its reason and evidence that the fleet can still make progress.

## 6. Prepare our acceptance data before Claude runs it

Use a second warehouse layout and workload that were not used to tune the first demonstration.

The packet should contain:

- The map and robot starting positions.
- A finite set of normal and urgent jobs.
- Station capacities and service times.
- Disruption events at specified simulation ticks.
- Expected outcomes for the small cases.
- Explicitly reachable and unreachable jobs.
- The invariants that must hold throughout execution.

Check that the inputs themselves are feasible. A blocked destination cannot be used to accuse the planner of failing to finish.

The checking code should inspect the movement and job history independently. It should not simply accept the planner's own "collision free" flag.

## 7. Run the final operator exercise

The final screen should let you run, pause, advance one tick, select a robot and inject a disruption.

The exercise proceeds as follows:

1. Run the fictional workload without disruptions. Save the baseline.
2. Restart from the same initial state.
3. Close a designated empty aisle segment while jobs are underway.
4. Disable a robot near an intersection.
5. Introduce urgent jobs.
6. Inspect waiting reasons, revised routes and station queues.
7. Let the remaining fleet finish every job still serviceable under the scenario.
8. Account explicitly for work stranded on the disabled robot or behind an unreachable destination.
9. Repeat the recorded scenario and compare results.

Disruptions should be recorded as simulation events. That lets us reproduce your operator run without relying on mouse timing.

The display should show completed jobs, outstanding jobs, waiting time, travel distance, station queues and delivery lateness. Compare the baseline and disrupted run. We should expect disruption to have a cost. Claude must explain that cost using the event history.

## 8. Decide success, partial completion or failure

| Outcome | Standard |
|---|---|
| **Success** | All declared movement and job invariants hold. The fleet handles the specified disruptions. Serviceable work completes. Stranded work is explained. Replay reproduces the result |
| **Partial completion** | A usable connected simulator exists, but a named coordination or recovery case remains unresolved. The failure is reproducible and documented |
| **Failure** | The demo relies on scripted paths, permits conflicting movement, loses jobs, hides stalled work or cannot reproduce its claimed result |

Two checks matter together: **robots must avoid prohibited moves, and the fleet must make progress.** Stopping every robot indefinitely can satisfy collision checks while failing the operating objective.

We should also reject unsupported claims of optimal routing or deadlock freedom. Passing selected scenarios demonstrates those scenarios. A broader guarantee requires a justified argument.

## 9. Close with a reviewable package

The final package should contain:

- Source and launch instructions.
- Scenario inputs and recorded operator events.
- Every test's purpose, expected result and actual result.
- Raw test output.
- Baseline and disrupted-run results.
- Movement and job histories.
- Screenshots or a short demonstration recording.
- Defects found, corrections made and unresolved cases.
- A report explaining the decisions that led to the final implementation.

**The end result is a warehouse you can operate, disrupt and inspect.** The evidence must show why the fleet recovered, where it could not recover and whether the same behaviour can be reproduced.
