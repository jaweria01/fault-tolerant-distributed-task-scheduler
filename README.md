# Fault-Tolerant Distributed Task Scheduler

A lightweight distributed task scheduling system that balances workloads across multiple workers while providing resilience to worker failures through checkpoint-based recovery. The system supports multiple scheduling strategies, monitors worker health using heartbeats, and automatically reassigns tasks when failures occur.

---

## 🚀 Features

- Distributed task scheduling architecture
- Two scheduling strategies:
  - Round Robin
  - Least Loaded
- Worker heartbeat monitoring
- Failure detection
- Checkpoint-based task recovery
- Automatic task reassignment after worker failure
- Runtime monitoring endpoint (`/metrics`)
- Experimental evaluation with fault injection
- Graph-based performance analysis

---

## 🏗 System Architecture

The system consists of three main components:
- **Scheduler**
- **Worker Nodes**
- **Client (Task Submission)**
  
                 +--------------------+
                 |       Client       |
                 |  (Submit Tasks)    |
                 +----------+---------+
                            |
                            v
                +----------------------+
                |      Scheduler       |
                |----------------------|
                | - Task Queue         |
                | - Scheduling Policy  |
                | - Failure Detection  |
                | - Checkpoint Store   |
                +----+------------+----+
                     |            |
                     |            |
             +-------v--+    +----v-------+
             | Worker 1 |    | Worker 2   |
             |----------|    |------------|
             | Execute  |    | Execute    |
             | Tasks    |    | Tasks      |
             | Send     |    | Send       |
             | Heartbeat|    | Heartbeat  |
             | Checkpoint     | Checkpoint|
             +----------+    +------------+



Tasks are submitted to the scheduler, which assigns them to available workers based on the selected scheduling policy. Workers periodically send heartbeats and execution checkpoints back to the scheduler.

## Scheduling Policies
### Round Robin

Tasks are assigned sequentially to workers in a rotating order.
Example:
Task1 → Worker1  
Task2 → Worker2  
Task3 → Worker1
This policy ensures fair distribution of tasks across workers.
### Least Loaded

The scheduler assigns tasks to the worker with the smallest current workload.
This strategy improves performance when tasks have different execution times.

## Fault Tolerance Mechanism

The system uses checkpoint-based recovery to handle worker failures.

1. Workers periodically send execution checkpoints to the scheduler.
2. The scheduler monitors worker health using heartbeat messages.
3. If a worker fails (heartbeat timeout):

- The scheduler detects the failure
- The task is reassigned to another worker
- Execution resumes from the last saved checkpoint
- This reduces recomputation and improves fault tolerance.

## Experimental Evaluation

To evaluate the system’s fault tolerance, controlled experiments were conducted using three workload sizes:

| Task Duration | Failure Injection Point | Runs |
|---------------|------------------------|------|
| 30 seconds    | 15 seconds             | 5    |
| 60 seconds    | 30 seconds             | 5    |
| 90 seconds    | 45 seconds             | 5    |

**Total experiments:**
`15 runs`

**Metrics collected:**

Completion time
Resume progress
Recovery latency

## Results

The results show that:

- Tasks resume execution close to the last checkpoint
- Recovery latency remains below 0.06 seconds
- Checkpoint recovery significantly reduces recomputation after failures
Performance graphs were generated to visualize:

- Average completion time
- Recovery latency across different task durations
