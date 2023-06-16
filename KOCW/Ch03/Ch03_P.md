# [3] Process

# 1. 프로세스

### 프로세스의 개념

- 프로세스는 실행 중인 프로그램
    - 프로그램의 인스턴스
- 프로세스의 문맥(context)
    - 코드의 몇 번째 instruction까지 수행했는가, data에 어떤 게 저장되어 있는가 등을 밝히기 위해 필요
    - 프로세스의 현재 상태를 규명하기 위해 필요한 모든 요소들
        - CPU 수행 상태를 나타내는 하드웨어 문맥
            - PC
            - 각종 register
            - …
        - 프로세스의 주소 공간
            - code, data, stack
        - 프로세스 관련 커널 자료 구조
            - PCB(Process Control Block)
            - Kernel Stack
                - 커널의 함수를 호출하는 스택
                - 프로세스들이 공유하는 부분

### 프로세스의 상태

- 프로세스는 상태가 변경되며 수행됨
    - RUNNING
        - CPU를 잡고 instruction을 수행하는 상태
    - READY
        - CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족하고)
    - BLOCKED(WAIT, SLEEP)
        - CPU를 주어도 당장 instruction을 수행할 수 없는 상태
        - process 자신이 요청한 event가 즉시 만족되지 않아 이를 기다리는 상태
    - NEW : 프로세스가 생성 중인 상태
    - TERMINATED : 수행이 끝난 상태
        - 프로세스의 수행이 끝났지만 아직 정리할 게 남아있는 상태

### Process Control Block(PCB)

- 운영체제가 각 프로세스를 관리하기 위해 프로세스 당 유지하는 정보
- 구조체의 형식으로 다음의 정보들을 가짐
    - (1) OS가 관다음의 구성요소를 구조체로 가짐리상 사용하는 정보
        - Process state, Process ID
        - scheduling information, priority
    - (2) CPU 수행 관련 하드웨어 값
        - PC, registers
    - (3) 메모리 관련
        - Code, data, stack의 위치 정보
    - (4) 파일 관련
        - Open file descriptors…

### Context Switch

- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
- CPU가 다른 프로셋에게 넘어갈 때 운영체제는 다음을 수행
    - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장하고
    - CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어 옴
- System call이나 Interrupt가 발생했다고 반드시 context switch가 일어나는 것은 아님
    - ex) 한 프로세스가 CPU 제어권을 OS에 넘겨주는 경우
    - 프로세스의 상태를 저장하기는 하지만 다른 프로세스로 옮겨가는 context switch에 비해서는 overhead가 적음

### 프로세스를 스케줄링하기 위한 큐

각 프로세스의 PCB들을 넣어놓는 큐들

- Job Queue
    - 현재 시스템 내 모든 프로세스의 집합
- Ready Queue
    - 현재 메모리 내에 있으면서 CPU를 대기하고 있는 프로세스의 집합
- Device Queues
    - I/O device의 처리를 기다리는 프로세스의 집합

### 스케줄러

- Long-term scheduler(job scheduler)
    - 시작 프로세스 중 어떤 것들을 ready queue로 보낼지를 결정
        - 처음 프로세스가 시작되고 나서 메모리 상의 ready queue에 올리기 위해 admit할 때~
    - 프로세스에 memory 및 각종 자원들을 주는 문제
    - degree of Multiprogramming 제어
        - degree of multi-programming : memory에 올라가 있는 프로세스의 수
    - time-sharing system에는 보통 장기 스케줄러가 없음
        - 무조건 ready
- Short-term scheduler(CPU scheduler)
    - 어떤 프로세스를 다음 번에 running시킬지를 결정
    - 프로세스에 CPU를 주는 문제
    - 충분히 빨라야 함
- Medium-term scheduler(Swapper)
    - 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
    - 프로세스에게서 memory를 빼앗는 문제
    - degree of Multiprogramming을 제어
    - 중기 스케줄러로 인해 프로세스의 상태에 몇 가지가 추가됨
        - Suspended(Stopped)
            - 외부적인 이유로 프로세스의 수행이 정지된 상태
            - 프로세스는 통째로 디스크에 swap out됨
            - ex) 사용자가 프로세스를 일시 정지시키는 경우. 메모리에 너무 많은 프로세스가 올라와 있어 시스템이 일부 프로세스를 정지시키는 경우
        - Blocked와의 차이
            - Blocked : 자신이 요청한 event가 만족되면 Ready
            - Suspended : 외부에서 재개(resume)시켜줘야 Active

# 2. Thread

- 프로세스 내부에 CPU 수행 단위를 여러 개 두는 것
- 같은 일을 하는 프로세스가 여러 개 필요한 경우, 여러 프로세스를 사용하기 보다는 한 프로세스의 다른 부분을 수행할 수 있게 함.
- 한 프로세스에서 공유할 수 있는 것들은 최대한 공유함.
    - PCB는 하나만 만들어짐.
    - 각 스레드는 동료 스레드와 code section, data section, OS resources를 공유
- 각 스레드는 PC, register set, stack space는 따로 가짐
- 전통적인 개념의 heavy-weight process는 하나의 thread를 가지고 있는 task로 볼 수 있음
    - thread를 여러 개 두는 프로세스는 반대로 light-weight process라 부름

### Thread의 장점

- Responsiveness : 다중 스레드로 구성된 태스크 구조에서, 한 스레드가 blocked 상태인 동안에도 동일한 task의 다른 스레드는 running 상태가 되어 빠른 처리가 가능하다
- Resource Sharing : 자원을 공유해 자원 절약이 가능
- Economy : 프로세스를 새로 만들고 switch하는 것보다 overhead가 적은 작업임
- Utilization of MP Architectures : 병렬성을 높일 수 있다.(multi-processor의 경우)
- 동일한 일을 수행하는 다중 스레드가 협력해 높은 처리율(throughput)과 성능 향상을 얻을 수 있다

### Thread의 구현

- Kernel Threads : Kernel에 의해 지원
    - 커널이 프로세스에 여러 스레드가 있음을 알고 관리
    - Windows 95/98/NT
    - Solaris
    - Digital UNIX, Mach
- User Threads : Library에 의해 지원.
    - 커널은 프로세스에 여러 스레드가 있음을 모르고 있고, 유저 프로세스가 알아서
    - POSIX Pthreads
    - Mach C-threads
    - Solaris threads
- 실시간 스레드들도 있음
