# OSTEP Ch.4 Homework Answers

## Q1
- Prediction / 预测: 
```
Time    PID 0    PID 1    CPU    IOs
1       RUN:cpu  READY    1
2       RUN:cpu  READY    1
3       RUN:cpu  READY    1
4       RUN:cpu  READY    1
5       RUN:cpu  READY    1
6       DONE     RUN:cpu  1
7       DONE     RUN:cpu  1
8       DONE     RUN:cpu  1
9       DONE     RUN:cpu  1
10      DONE     RUN:cpu  1
```
- Reasoning / 理由: 两个进程都是纯 CPU 指令，CPU 不会空闲。PID 0 先执行 5 个 tick，然后 PID 1 执行 5 个 tick，总时间为 10 ticks，CPU 利用率为 100%。
- Verified result / 验证结果: Total Time 10, CPU Busy 100% (10/10).
- Analysis / 分析: 预测与验证结果完全一致。说明纯 CPU 密集型进程会一直占用 CPU，不会产生状态切换。

## Q2
- Prediction / 预测: 
```
Time    PID 0    PID 1    CPU    IOs
1       RUN:cpu  READY    1
2       RUN:cpu  READY    1
3       RUN:cpu  READY    1
4       RUN:cpu  READY    1
5       RUN:cpu  RUN:io   1      1
6       DONE     BLOCKED         1
7       DONE     BLOCKED         1
8       DONE     BLOCKED         1
9       DONE     BLOCKED         1
10      DONE     BLOCKED         1
11      DONE     RUN:io_done 1
```
- Reasoning / 理由: PID 0 先执行 4 个纯 CPU tick，PID 1 等待。PID 0 结束后，PID 1 开始 I/O（1 tick 发起，5 ticks 阻塞，1 tick 完成，共 7 ticks）。总时间 11 ticks。CPU 忙碌 6 ticks（PID 0 的 4 + PID 1 的 2），CPU 利用率 = 6/11 ≈ 54.55%。
- Verified result / 验证结果: Total Time 11, CPU Busy 54.55% (6/11).
- Analysis / 分析: 预测与验证结果一致。I/O 阻塞期间 CPU 空闲，导致利用率下降。验证了 I/O 操作的 7 tick 模型。

## Q3
- Prediction / 预测: 
```
Time    PID 0    PID 1    CPU    IOs
1       RUN:io   READY    1      1
2       BLOCKED  RUN:cpu         1
3       BLOCKED  RUN:cpu         1
4       BLOCKED  RUN:cpu         1
5       BLOCKED  RUN:cpu         1
6       BLOCKED  DONE            1
7       RUN:io_done DONE     1
```
- Reasoning / 理由: PID 0 先发起 I/O（1 tick），随后阻塞 5 ticks。在 PID 0 阻塞期间，CPU 空闲，因此调度器切换 PID 1 运行 4 个 CPU tick。PID 1 结束后，PID 0 I/O 完成，占用 1 tick。总时间 7 ticks。CPU 忙碌 6 ticks，利用率 = 6/7 ≈ 85.71%。
- Verified result / 验证结果: Total Time 7, CPU Busy 85.71% (6/7).
- Analysis / 分析: 预测与验证结果一致。通过将 PID 1 放入空档期，提高了 CPU 利用率，体现了多道程序设计的好处。

## Q4
- Prediction / 预测: 
```
Time    PID 0    PID 1    CPU    IOs
1       RUN:io   READY    1      1
2       BLOCKED  READY          1
3       BLOCKED  READY          1
4       BLOCKED  READY          1
5       BLOCKED  READY          1
6       BLOCKED  READY          1
7       RUN:io_done READY   1
8       DONE     RUN:cpu  1
9       DONE     RUN:cpu  1
10      DONE     RUN:cpu  1
11      DONE     RUN:cpu  1
```
- Reasoning / 理由: 使用 `SWITCH_ON_END` 时，进程在 I/O 阻塞期间不会切换到其他进程。因此 CPU 在 PID 0 阻塞的 5 个 tick 内空闲。PID 0 完成后，PID 1 才开始运行 4 个 CPU tick。总时间 11 ticks，CPU 利用率 = 6/11 ≈ 54.55%。
- Verified result / 验证结果: Total Time 11, CPU Busy 54.55% (6/11).
- Analysis / 分析: 预测与验证结果一致。`SWITCH_ON_END` 导致 CPU 在等待 I/O 时无法利用，严重降低了 CPU 利用率。

## Q5
- Prediction / 预测: 
```
Time    PID 0    PID 1    CPU    IOs
1       RUN:io   READY    1      1
2       BLOCKED  RUN:cpu         1
3       BLOCKED  RUN:cpu         1
4       BLOCKED  RUN:cpu         1
5       BLOCKED  RUN:cpu         1
6       BLOCKED  DONE            1
7       RUN:io_done DONE     1
```
- Reasoning / 理由: `SWITCH_ON_IO` 允许在 I/O 阻塞时立即切换。与 Q3 一致。总时间 7 ticks，CPU 利用率 = 6/7 ≈ 85.71%。
- Verified result / 验证结果: Total Time 7, CPU Busy 85.71% (6/7).
- Analysis / 分析: 预测与验证结果一致。`SWITCH_ON_IO` 比 `SWITCH_ON_END` 更优，能有效利用 CPU 处理其他进程。

## Q6
- Prediction / 预测: 
```
Time    PID 0    PID 1    PID 2    PID 3    CPU    IOs
1       RUN:io   READY    READY    READY    1      1
2-6     BLOCKED  RUN:cpu  READY    READY    1
7       RUN:io_done DONE READY    READY    1
8       RUN:io   DONE     RUN:cpu  READY    1      1
9-13    BLOCKED  DONE     RUN:cpu  READY    1
14      RUN:io_done DONE DONE     RUN:cpu  1
15      RUN:io   DONE     DONE     RUN:cpu  1      1
16-20   BLOCKED  DONE     DONE     RUN:cpu  1
21      RUN:io_done DONE DONE     DONE     1
```
- Reasoning / 理由: PID 0 有三段 I/O。`IO_RUN_LATER` 表示 I/O 完成后进程进入就绪队列末尾。CPU 完全被 PID 1,2,3 的纯计算填满。总时间 21 ticks，CPU 利用率 = 100%。
- Verified result / 验证结果: Total Time 21, CPU Busy 100% (21/21).
- Analysis / 分析: 预测与验证结果一致。CPU 密集型进程完美填充了 I/O 密集型进程留下的空隙，实现了 100% 的 CPU 利用率。

## Q7
- Prediction / 预测: 
```
（与 Q6 表格类似，但 PID 0 完成 I/O 后会立即抢占 CPU）
Time    PID 0    PID 1    PID 2    PID 3    CPU    IOs
1       RUN:io   READY    READY    READY    1      1
2-6     BLOCKED  RUN:cpu  READY    READY    1
7       RUN:io_done DONE READY    READY    1
8       RUN:io   DONE     RUN:cpu  READY    1      1
9-13    BLOCKED  DONE     RUN:cpu  READY    1
14      RUN:io_done DONE DONE     RUN:cpu  1
15      RUN:io   DONE     DONE     RUN:cpu  1      1
16-20   BLOCKED  DONE     DONE     RUN:cpu  1
21      RUN:io_done DONE DONE     DONE     1
```
- Reasoning / 理由: `IO_RUN_IMMEDIATE` 让 I/O 进程在完成等待后立即重新获得 CPU。总时间 21 ticks，CPU 利用率 = 100%。
- Verified result / 验证结果: Total Time 21, CPU Busy 100% (21/21).
- Analysis / 分析: 预测与验证结果一致。对于 I/O 密集型进程，立即重新运行有助于更快完成它们的 I/O 操作，尽快释放 I/O 设备。

## Q8
### Seed 1
- Prediction / 预测: 
```
Time    PID 0    PID 1    CPU    IOs
1       RUN:cpu  READY    1
2       RUN:io   RUN:cpu  1      1
3       BLOCKED  RUN:cpu  1      1
4       BLOCKED  RUN:cpu  1      1
5       BLOCKED  DONE            1
6       BLOCKED  DONE            1
7       BLOCKED  DONE            1
8       RUN:io_done DONE  1
9       RUN:io   DONE     1      1
10      BLOCKED  DONE            1
11      BLOCKED  DONE            1
12      BLOCKED  DONE            1
13      BLOCKED  DONE            1
14      BLOCKED  DONE            1
15      RUN:io_done DONE  1
```
- Reasoning / 理由: PID 0 先执行 cpu，然后发起 I/O 并阻塞 5 ticks。此时 CPU 切换给 PID 1 运行 3 个 cpu 并结束。PID 0 的 I/O 在 Tick 7 完成后，根据 IO_RUN_LATER 规则，PID 0 必须等它排到队才能运行 io_done。之后 PID 0 再次发起第二次 I/O，重复此过程。总时间为 15 ticks，CPU Busy 为 7 ticks（利用率约 46.67%）。
- Verified result / 验证结果: Total Time 15, CPU Busy 46.67% (7/15).
- Analysis / 分析: 预测基本一致。由于 PID 0 是 I/O 密集型，在 I/O 期间 CPU 常常空闲，导致总时间较长。IO_RUN_LATER 使其在 I/O 完成后需要排队等待，直到 CPU 空闲。

### Seed 2
- Prediction / 预测: 
```
Process 0
io
io_done
io
io_done
cpu

Process 1
cpu
io
io_done
io
io_done
```
- Reasoning / 理由: PID 0 首先发起 I/O，在它阻塞的 5 个 tick 内，CPU 切换给 PID 1。因为默认使用的是 IO_RUN_LATER，完成 I/O 的进程需要排队，直到轮到它才能继续运行。总时间将由后续完整验证得出。
- Verified result / 验证结果: Total Time 30, CPU Busy 33.33% (10/30). 
- Analysis / 分析: 由于两个进程都包含大量 I/O，CPU 空闲时间非常长。预测的指令列表与验证结果吻合，I/O 阻塞是导致 CPU 利用率低的主要原因。

### Seed 3
- Prediction / 预测: 
```
Process 0
cpu
io
io_done
cpu

Process 1
io
io_done
io
io_done
cpu
```
- Reasoning / 理由: PID 0 先执行一个 cpu，之后发起 I/O 阻塞；PID 1 发起两次 I/O。在 I/O 期间，CPU 会在两个进程之间切换。因为使用了 IO_RUN_LATER，完成 I/O 的进程会进入就绪队列末尾等待。总时间将由后续完整验证得出。
- Verified result / 验证结果: Total Time 24
，CPU Busy 37.50%（3/8）
- Analysis / 分析: 预测与验证结果基本一致。由于两个进程频繁进行 I/O，CPU 利用率仍不足一半。验证了 I/O 密集型进程对 CPU 利用率的负面影响。