# [5] CPU Scheduling

# 0. CPU and I/O Bursts in Program Execution

- 어떤 프로세스든 CPU 작업을 연속적으로하는 단계(CPU Burst), I/O 작업을 연속적으로 하는 단계(I/O Burst)를 번갈아가면서 한다.
    - 프로그램에 따라 CPU Burst와 I/O Burst의 비중 등은 달라질 수 있겠지만 아무튼 일반적으로는 그럼

## 프로세스의 특성 분류

- I/O Bound Job : CPU는 짧게 쓰고 I/O 작업은 오래하는 경우
    - 사람들과 interaction하는 경우
- CPU Bound Job : CPU를 오래 쓰는 경우
    - 계산 위주의 job
- 컴퓨터 프로그램에는 I/O Bound Job과 CPU Bound Job이 섞여있다.
- 여러 종류의 job(=process)이 섞여 있기 때문에 CPU 스케줄링이 필요하다
    - interactive job에게 적절한 response를 제공
    - CPU, I/O 장치 등 시스템 자원을 골고루 효율적으로 사용

## CPU Scheduler & Dispatcher

### CPU Scheduler

- READY 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.
- 하드웨어가 아니라 소프트웨어. 운영체제에서 CPU Scheduling을 하는 코드가 있음

### Dispatcher

- CPU 제어권을 CPU Scheduler에 의해 선택된 프로세스에 넘김
- 이 과정을 **context switching**이라 한다.

### CPU Scheduling이 필요한 경우

- CPU Scheduling이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우이다.
    1. RUNNING → BLOCKED (ex. I/O 요청 시스템 콜)
    2. RUNNING → READY(ex. 할당시간 만료로 timer interrupt가 발생)
    3. BLOCKED → READY(ex. I/O가 완료됐을 때 device controller가 인터럽트 발생)
        1. I/O가 끝났다고 해서 해당 프로세스에 바로 CPU를 주는 건 아님
        2. 근데 I/O가 끝나고 바로 해당 프로세스에 CPU를 줘야할 때도 있음
            1. priority에 기반한 스케줄링에서, 해당 프로세스의 priority가 매우 높은 경우
    4. TERMINATE
- 1번과 4번의 경우는 **non-preemptive(자진 반납)**
    - CPU를 가지고 있어도 더 이상 쓸 수 없어서 다른 프로세스에 넘겨줘야 경우
- 나머지의 경우는 **preemptive(강제 반납)**
    - CPU를 쓰고 싶은데도 강제로 뺏겨서 다른 프로세스에 넘겨줘야 하는 경우

# 1. CPU Scheduling

CPU를 얻고자 하는 프로세스들 중에서 어떤 프로세스에 CPU를 넘겨줄지를 결정하는 메커니즘

- CPU를 누구에게 줄 것인가?
- CPU를 얼마나 줄 것인가?
    - 만약 CPU를 가지고 오래 있으면 나머지 프로세스는 오랫동안 기다려야 할 것이기 때문에

# 2. Scheduling Algorithm

## 2-1. Scheduling Criteria

CPU 스케줄링 알고리즘들을 위한 성능의 척도들

- 시스템 입장의 성능 척도
    - CPU Utilization (이용률)
        - Keep the **CPU as busy as possible**
        - 전체 시간 중에서 CPU가 놀지 않고 일한 시간의 비율
    - Throughput (처리량)
        - **The number of processes that complete their execution per time unit**
- 프로그램 입장의 성능 척도
    - Turnaround time (소요시간, 반환시간)
        - Amount of time to **execute a particular process**
        - 프로세스가 CPU를 쓰러 와서 다쓰고 나갈 때까지  데 걸린 전체 시간.
        - 아래의 waiting time을 포함
    - Waiting time (대기시간)
        - Amount of time a process has been **waiting in the ready queue**
        - preemptive에서는 CPU를 쓰다가 제어권을 뻇겨 큐의 맨 뒤로 밀려나고, 다시 CPU를 쓸 수 있을 때까지 기다림.
        - 이 기다리는 시간들의 총합이 대기 시간
    - Response time (응답 시간)
        - Amount of time it takes **from when a request was submitted until the first response is produced,** not output. (for time-sharing environment)
        - 큐에 들어가서 맨 처음으로 CPU를 쓸 수 있게 될 때까지의 시간.
        - time-sharing 환경에서의 사용자 응답과 관련해 중요

## 2-2. Uni-processor

### ************FCFS(First-Come-First-Served)************

- Non-Preemptive
- 프로세스가 들어오는 순서대로 실행
- 앞에 어떤 프로세스가 기다리고 있느냐에 따라 대기 시간 및 평균 대기 시간에 큰 영향을 미침
    - Convoy effect : 오래 걸리는 프로세스가 먼저 들어오면 뒤의 짧은 프로세스들도 오랫동안 기다려야 하게 되는 현상

### **************SJF(Shortest-Job-First)**************

- 각 프로세스의 다음 번 CPU burst time을 가지고 스케줄링
- CPU burst time이 가장 짧은 프로세스를 가장 먼저 스케줄
- Two Schemes
    - Non-preemptive
        - 일단 CPU를 잡으면 이번 CPU burst가 완료될 때까지 CPU를 선점 당하지 않음
    - Preemptive
        - 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가지는 새 프로세스가 도착하면 CPU를 빼앗김
        - SRTF(Shortest-Remaining-Time-First)라고도 부름
- SJF의 장단점
    - 장점 : SJF is optimal
        - 주어진 프로세스들에 대해 minimum average waiting time을 보장함 (특히 preemptive)
    - 단점
        - Starvation
            - 나보다 짧은 프로세스들이 계속 들어오면 긴 프로세스는 영원히 실행되지 못할 수 있음
        - 다음 번 CPU burst time을 미리 알 수 없음
            - 추정만이 가능함
            - 과거의 CPU burst time을 이용해서 추정
                
                ```markdown
                **exponential averaging**
                
                1. t_n = n번째 CPU burst의 실제 길이
                2. t_{n+1} = 다음 CPU burst의 추정값
                3. a, 0 <= a <= 1
                4. define : r_{n+1} = a * t_n + (1-a)r_n
                
                식을 쭉 풀면
                a와 (1 - a)가 모두 1 이하이므로 후속 term은 선행 term보다 적은 가중치를 가짐
                ```
                

### Priority Scheduling

- 각 프로세스가 정수 우선 순위를 가지고, 이 우선 순위가 높은 프로세스에게 CPU를 우선 할당
    - smallest integer = highest priority
- Preemptive, Non-preemptive 구현이 모두 가능
- SJF도 일종의 priority scheduling이라 할 수 있음
- Starvation 문제가 마찬가지로 발생 가능
    - Solution : Aging
        - 시간이 지나면 오래된 프로세스의 우선 순위를 높여줌

### Round Robin(RR)

- 현대적인 컴퓨터 시스템은 RR에 기반
- 각 프로세스는 동일한 크기와 할당 시간(time quantum)을 가짐.
- 할당 시간이 지나면 프로세스는 선점당하고 ready queue의 제일 뒤로 감
- n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit인 경우 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1/n을 얻는다.
    - 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않는다.
    - 일반적으로 SJF보다 평균 turnaround time은 길지만, 응답 시간은 더 빠르다.
- 성능
    - q가 커지면? FCFS
    - q가 작아지면? 문맥 전환 오버헤드가 커져서 전체 성능이 나빠질 수 있음.
    - 적당한 q를 정하는 게 좋음. 일반적으로는 10-100ms

### Multilevel Queue

- Ready Queue를 여러 개로 분할
    - foreground (interactive)
    - background (batch - no human interaction)
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
    - foreground - RR
    - background - FCFS
- 각 큐에 대한 스케줄링이 필요
    - Fixed priority scheduling
        - foreground 큐의 프로세스를 모두 마치고 나서 background 큐의 프로세스를 처리
        - starvation이 일어날 수 있음
    - Time slice
        - 각 큐에 CPU time을 적절한 비율로 할당
        - 예) foreground에는 80%, background에는 20%…
- 각 프로세스를 어떤 큐에 넣어야 할지도 스케줄링 필요함

### Multilevel Feedback Queue

- 프로세스가 다른 큐로도 이동할 수 있음
- Aging을 이와 같은 방식으로 구현할 수 있음
- Multilevel Feedback Queue 스케줄러를 정의하는 파라미터들
    - Queue의 수
    - 각 큐의 scheduling algorithm
    - 프로세스를 상위 큐로 보내는 기준
    - 프로세스를 하위 큐로 보내는 기준
    - 프로세스가 처음 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준
- 일반적으로 처음 들어오는 프로세스는 우선 순위가 가장 높은 큐(짧은 quantum)에 넣고, 주어진 시간 동안 프로세스 수행이 끝나지 않으면 낮은 우선 순위의 큐(좀 더 긴 quantum)로 강등시킴.
- CPU burst time에 대한 계산이 불필요

## 2-3. Multiple-Processor Scheduling

CPU가 여러 개 있는 경우에는 더 복잡한 스케줄링이 필요함

### Homogeneous Processor

- 큐에 한 줄로 세워서 각 프로세서가 알아서 꺼내가게 함
- 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해짐

### Load Sharing

- 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
- 별개의 큐를 두는 방법 vs. 공동 큐를 사용하는 방법

### Symmetric Multiprocessing(SMP)

- 모든 프로세서가 서로 대등한 관계를 맺고 있음
- 각 프로세서가 각자 알아서 스케줄링을 결정

### Asymmetric Multiprocessing

- 한 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따르는 방식

## 2-4. Real-time Scheduling

### Hard real-time systems

- 정해진 시간 안에 반드시 주어진 task가 끝내도록 스케줄링되어야 함

### Soft real-time computing

- 일반 프로세스에 비해 높은 priority를 갖도록 스케줄링해야 함
- deadline을 꼭 보장하지는 못

## 2-5. Thread Scheduling

### Local Scheduling

- User-level thread의 경우, 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지를 결정

### Global Scheduling

- Kernel-level thread의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지를 결정

## 2-6. Algorithm Evaluation

위 알고리즘들을 평가하는 방법들

### Queueing Models

- 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index 값을 계산

### Implementation & Measurement

- 실제 시스템에 알고리즘을 구현해보고 실제 작업(workload)에 대해 성능을 측정, 비교

### Simulation

- 알고리즘을 모의 프로그램으로 작성 후 trace(샘플 입력 데이터)를 입력으로 하여 결과를 비교
