# Chapter 7: CPU Scheduling — Analysis

## Q1. Three jobs of length 200: FIFO and SJF

All three jobs have length 200, so FIFO and SJF run them in the same order.

| Job | Length | Response time | Turnaround time |
|---|---:|---:|---:|
| A | 200 | 0 | 200 |
| B | 200 | 200 | 400 |
| C | 200 | 400 | 600 |
| Average | — | 200 | 400 |

Average response time = 200. Average turnaround time = 400.

## Q2. Jobs of lengths 100, 200, and 300: FIFO and SJF

The jobs are already ordered from shortest to longest, so both policies run A, B, C.

| Job | Length | Response time | Turnaround time |
|---|---:|---:|---:|
| A | 100 | 0 | 100 |
| B | 200 | 100 | 300 |
| C | 300 | 300 | 600 |
| Average | — | 133.33 | 333.33 |

Average response time = 133.33. Average turnaround time = 333.33.

## Q3. RR with quantum 1

For jobs of lengths 100, 200, and 300, all arriving at time 0, RR gives each ready job one time unit per turn.

The response times are 0, 1, and 2, so average response time = 1.

The completion times are 298, 499, and 600, so average turnaround time = (298 + 499 + 600) / 3 = 465.67. 

## Q4. When does SJF give the same turnaround time as FIFO?

When jobs are already arranged in nondecreasing order of length, SJF does not change their execution order. Therefore, each job has the same completion and turnaround time under FIFO and SJF.

## Q5. When do SJF and RR give the same response time?

Their response times are the same when each job's first-start time is the same under both policies. This depends on the job lengths, initial order, and RR quantum. Test different workloads and quantum values with the simulator.

## Q6. How does SJF response time change as job lengths increase?

With all jobs arriving at time 0, longer jobs make later jobs wait longer before their first run. The first job's response time remains 0. Compare these experiments:

```bash
python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 10,20,30 -p SJF -c
python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 100,200,300 -p SJF -c
python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 1000,2000,3000 -p SJF -c
```

## Q7. How does RR response time change as quantum increases?

As the quantum increases, each job can run longer before the scheduler switches to the next job. Later jobs generally wait longer for their first run.

For N jobs that are all ready at time 0, if each job is still unfinished after its first quantum, the first-run response times are 0, q, 2q, ..., (N-1)q. The average response time is:

[
rac{0+q+2q+cdots+(N-1)q}{N}=rac{q(N-1)}{2}
]

This formula assumes no scheduling overhead and that every job remains unfinished during its first quantum. If a job finishes earlier, the actual response times can be smaller.

Compare several quantum values using the RR simulator and record the outputs.