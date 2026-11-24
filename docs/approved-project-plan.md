# Fleet Simulator Project Plan

**Project:** A warehouse fleet simulator that continues completing serviceable jobs after disruptions  
**Approved reviewer direction:** Grow from ten robots to hundreds. Check movement and job rules on every tick of every disruption.  
**Time budget:** Eight hours of active work, planned across six checkpoints. Record actual time separately.  
**Repository:** https://github.com/deepakgupta5/fleet-simulator

## Objective

Build an operable, repeatable warehouse simulator where job assignment, shared-space routing, station queues and recovery work as one system. Start with ten robots. Increase the fleet through 25, 50, 100, 300 and 500 only after each prior tier passes its acceptance gate.

The small fleet must exercise the difficult operating cases first. Larger tiers must preserve those rules and test how coordination behaves under increasing contention. Do not add robots by bypassing checks, simplifying movement rules or scripting routes.

This is a simulation project. Results do not establish physical robot safety.

## Operating rules and test obligations

- Use a fictional grid warehouse, discrete ticks, a fixed seed and saved scenario inputs.
- Robots move at most one adjacent cell per tick or wait.
- No two robots may occupy one cell. Two robots may not swap cells across an edge in one tick.
- Shelves, closed aisles, service stations and a disabled robot’s occupied cell constrain movement.
- Jobs have pickup, delivery and priority. Completion requires the declared pickup and delivery steps, and can be recorded only once.
- Stations have explicit capacity, processing time and queues.
- Record disruptions as simulation events so a run can be replayed.
- Keep simulation state and logic separate from rendering.
- Run an independent history checker that recomputes movement and job invariants for every tick. Do not rely on a planner’s own collision-free or completion claims.
- Apply the checker to every acceptance and disruption run at every fleet tier.
- Record fleet size, scenario, seed, tick count, completed and outstanding jobs, waits and their reasons, runtime, and any invariant failures.

## Fleet ramp and advancement gate

Use these tiers in order: **10 → 25 → 50 → 100 → 300 → 500 robots**.

Advance only when the current tier passes all movement and job invariants on every tick, produces no false completions, and completes all work declared serviceable in the finite scenario. Any unresolved case blocks the next tier. Keep the failing run and its evidence; do not hide it by changing the seed or scenario.

At each tier, record simulation runtime and throughput. Establish practical performance limits from observed runs before the final acceptance exercise. Do not invent a universal performance claim.

## Tricky situations

Build small, understandable cases at ten robots before combining them. Repeat relevant cases at later fleet tiers and under higher contention.

1. Two robots request the same intersection or narrow passage at the same time.
2. Two robots approach each other in a narrow aisle, with a passing bay and a feasible yielding resolution.
3. A robot is disabled on an intersection or aisle cell and remains an obstacle.
4. An aisle closes while robots are en route. Test both an available alternate route and a destination made unreachable by the closure.
5. A station reaches capacity while robots arrive together.
6. Urgent jobs arrive during congestion. The declared priority policy must affect assignment or scheduling without silently losing existing work or starving ordinary jobs in a finite workload.
7. A robot with a blocked route must explain why it waits. Unrelated reachable work must continue.
8. Rerouting must not oscillate forever between equivalent paths.
9. A robot must not repeatedly lose access to a contested resource while feasible work remains.
10. Combine an aisle closure, a disabled robot near an intersection and urgent job arrivals. Record and replay the sequence.
11. Include jobs stranded on a disabled robot or made unreachable by the map. Report them as outstanding with reasons, never completed.

A wait is not automatically a defect. Check its cause and whether the fleet can still make progress. Define the deadlock and progress-detection policy from observed system states. Demonstrate recovery in the specified feasible cases. Do not claim universal deadlock freedom without a proof.

## Eight-hour checkpoints

| Time | Checkpoint | Gate before advancing |
|---|---|---|
| 45 min | Inspect the repository. Agree movement, job, station, disruption and progress rules. Define the first scenarios and independent per-tick checker. | Rules and scenario inputs are explicit. The map supports the intended routes and bottlenecks. |
| 90 min | Build the smallest connected simulator at 10 robots: assign jobs, travel to pickup, deliver and release jobs. Add basic visual controls. | Deterministic run. Job lifecycle and per-tick movement checks pass. |
| 90 min | Resolve shared-space conflicts at 10 robots. Exercise intersections, head-on movement, passing bays, station queues, fairness and reroute stability. | No prohibited moves. Feasible cases clear. Waits have inspectable reasons. |
| 90 min | Add operator events and recovery: aisle closure, disabled robot and urgent work. Run isolated cases, then the combined disruption. | Every tick is checked. The fleet completes serviceable work and accounts for stranded work. Event history replays the run. |
| 75 min | Scale in order through 25, 50 and 100 robots. Use repeatable workloads and increase contention. | Each tier passes its gate before the next starts. Runtime and throughput are recorded. |
| 90 min | Scale through 300 and 500 robots if all previous gates pass. Run the independently prepared acceptance workload and final operator exercise. Package results. | Do not skip a tier. If a gate fails, stop scaling, reproduce and document the failure. Report the highest tier actually passed. |

The allocations total eight hours. If coordination is still wrong, stay on that checkpoint. A finished interface does not satisfy the operating objective.

## Independent acceptance and operator exercise

Prepare a second warehouse layout and finite workload that Claude did not use to tune the demonstration. Include robot positions, jobs and priorities, station capacity and service times, reachable and unreachable destinations, and disruptions at specified ticks. Check the inputs for feasibility before running them.

Run a baseline from a saved initial state. Restart with the same seed and inputs. Close the designated aisle, disable a robot near an intersection, and add urgent work at the specified ticks. Inspect revised routes, wait reasons, queues and job status. Let the fleet finish all work still serviceable. Save the event log and replay the same run. The independent checker must verify every tick in both runs.

The operator view should support run, pause, one-tick advance, robot selection and disruption injection. Show routes, robot/job state, waiting reasons, completed and outstanding work, station queues, waiting time, travel distance and lateness where defined.

## Outcome standards

- **Success:** All declared invariants pass on every tick in every required run. The fleet advances through the required scale gates to hundreds, completes serviceable work, accounts for stranded work and reproduces the recorded run.
- **Partial completion:** A connected simulator works, but a named scale or coordination gate fails. The failure is reproducible, visible and documented. Report the highest tier passed.
- **Failure:** The demo relies on scripted routes, permits prohibited movement, records false completion, hides stalled work, skips failed gates or cannot reproduce its claims.

Passing finite scenarios demonstrates those scenarios. It does not by itself prove universal deadlock freedom, optimal routing or physical safety.

## Closeout evidence

- Source and launch instructions.
- Scenario inputs, seeds and recorded operator events.
- Independent checker and its raw output.
- Per-tier results, including runtime and throughput.
- Baseline and disrupted-run job, movement and wait histories.
- Screenshots or a short recording of the operator exercise.
- Defects, corrections and unresolved cases.
- A report tracing the decisions and evidence that produced the final result.

## First instruction to Claude

```text
I want to build a warehouse fleet simulator that keeps completing serviceable jobs after disruptions. The hard part is coordinating assignment, routing and recovery when robots compete for shared space, priorities change and routes become unavailable.

The project is approved. The reviewer specifically asked us to grow from ten robots to hundreds and check movement and job rules on every tick of every disruption. I want to do that gradually: 10, 25, 50, 100, 300 and 500 robots. Do not jump to a large fleet before the smaller tier passes. Do not skip a tier to make the final result look stronger.

The difficult cases include simultaneous intersection requests, head-on traffic in a narrow aisle, a disabled robot blocking its cell, aisle closures and rerouting, station queues, urgent jobs during congestion, fairness, route oscillation, and jobs that become unreachable. Start with understandable cases at ten robots. Then repeat and combine them as the fleet grows. A disabled robot remains where it stopped. Unreachable work stays outstanding with a reason. Never fake completion.

Use fictional data, discrete ticks, a fixed seed and recorded disruption events. Keep simulation logic separate from rendering. We need an independent checker that reads the recorded history and verifies every movement and job invariant on every tick. Do not rely on the planner's own claims that a run was collision-free or complete.

First inspect this repository and tell me what is already there. Then explain the movement and job rules you propose, how you will resolve conflicts, how you will detect stalled progress, and what evidence must pass before you advance from one fleet tier to the next. Be precise about the limits of any deadlock claim. Do not claim universal deadlock freedom unless you can prove it.

After that explanation, build the smallest connected version at ten robots. It must assign jobs, move robots through pickup and delivery, and record a deterministic event history. Add the independent per-tick checker and the first focused tests for collisions, edge swaps, job completion and replay. Run them and give me the exact commands and results, plus the launch command so I can inspect the simulator.

Do not begin scaling until the ten-robot version and its tricky cases pass the agreed checks. If the repository already contains relevant work, inspect it and preserve it unless you can show why a change is needed. Keep the implementation your own. Do not copy source code from another implementation.
```
