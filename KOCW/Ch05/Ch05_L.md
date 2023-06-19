# CPU Scheduling

## CPU and I/O Bursts in Program Execution

cpu burst - i/o burst - cpu burst - i/o burst - cpu burst - i/o burst ...

cpu burst : cpu만 사용하는 단계
i/o burst : i/o만 사용하는 단계

상당히 interactive하다.
(i/o가 자주 끼어든다...)

- CPU-burst Time

i/o bound job : i/o 가 많이 끼어드는 잡. cpu를 잡고 계산하는 시간보다 i/o에 많은 시간이 필요
CPU bound job : CPU를 많이 쓰는 잡. 계산 위주

Hyperexponential distribution이다..

cpu의 사용 빈도는 cpu bound job이 많다.
여러 종류의 job이 섞여 있기 때문에 CPU 스케줄링이 필요하다.
interactive한 job의 경우 response 제공 요망.

-> cpu scheduling이 필요하다.

## CPU Scheduler & Dispatcher

- CPU Scheuler
    Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.
    운영체제 안에서 코드임.

- Dispatcher
    실제로 주는 과정임.

    CPU의 제어권을 CPU Scheduler에 의해 선택된 프로세스에게 넘긴다. context switch 
    이것도 운영체제 커널 코드

CPU 스케줄링이 필요한 경우
1. Running -> Blocked : I/o 요청하는 시스템콜
2. Running -> Ready : 할당시간 만료로 타이머 인터럽트
3. Blocked -> Ready : i/o 완료후 인터럽트
4. Terminate

1, 4의 스케줄링은 nonpreemptive 자진 반납
2, 3은 preemptive 강제 반납.



