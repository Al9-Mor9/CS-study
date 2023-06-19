# 5장 CPU Scheduling

### CPU and I/O Bursts in Program Execution

어떤 프로그램이든 실행이 되면 아래 과정을 거침.

![Untitled](src_H/Untitled.png)

프로그램의 Path는 CPU만 연속적으로 하는 단계와 I/O만 하는 단계가 번갈아 실행됨. 이 CPU만 연속적으로 실행하는 단계를 CPU Burst, I/O를 실행하는 단계를 I/O Burst라고 표현함. 단, 프로그램에 따라 스위칭하는 빈도나 주기가 다름.

### CPU-Burst Time의 분포

![Untitled](src_H/Untitled%201.png)

- 여러 종류의 job(=process)이 섞여있기 때문에 CPU 스케줄링이 필요하다.
    - interactive job에게 적절한 response 제공 요망
    - CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용

### 프로세스의 특성 분류

프로세스는 그 특성에 따라 두 가지로 나눔

- I/O-bound process
    - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
    - (many short CPU bursts)
- CPU-bound process
    - 계산 위주의 job
    - (few very long CPU bursts)

### CPU Scheduler & Dispatcher

- CPU Scheduler
    - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다
- Dispatcher
    - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다
    - 이 과정을 context switch(문맥 교환)이라고 한다
- CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우이다.
    1. Running → Blocked(예 : I/O 요청하는 시스템 콜)
    2. Running → Ready(예 : 할당 시간 만료로 timer interrupt)
    3. Blocked → Ready(예 : I/O 완료 후 인터럽트)
    4. Terminate

⭐ 1, 4에서의 스케줄링은 **nonpreemptive**(=강제로 뺏지 않고 자진 반납)

⭐ 나머지 스케줄링은 **preemptive**(=강제로 빼앗음)