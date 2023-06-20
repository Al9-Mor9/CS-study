
# [5] CPU Scheduling

## CPU and I/O Bursts in Program Execution

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
