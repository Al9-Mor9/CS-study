그냥 정리해서 올려둠

---
# [2] System Structure & Program Execution

# 1. System Structure

## (1) 컴퓨터 시스템 구조

### CPU

- registers : 메모리보다 빠른 기억 공간
- mode bit : 커널인지 유저인지
    - 사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호 장치.
    - 하드웨어적으로 두 가지 모드의 operation을 지원
        - 0 : 커널 모드. (모니터 모드, 시스템 모드)
            - 모든 instruction을 수행 가능
            - 보안을 해칠 수 있는 중요한 명령어는 모니터 모드에서만 수행 가능한 **특권명령**으로 규정
            - Interrupt나 Exception이 발생하면 하드웨어가 mode bit을 0으로 바꿈
        - 1 : 유저 모드
            - 제한적인 instruction만 수행 가능.
            - 유저 프로세스에 CPU를 넘기기 전에 mode bit을 1로 세팅
- interrupt line : 디스크나 I/O device에서의 명령, 또한 변화를 받을 때
    - user process는 직접 I/O device에 접근할 수 없음. I/O device에 접근하는 모든 instruction은 OS를 통해서만 가능함.

### timer

- 무한루프를 도는 프로그램이 있다고 하자. I/O 작업도 없다면 한 프로세스가 CPU를 독접하게 될 것.
- 위와 같은 경우를 방지하기 위해, 즉 한 프로세스가 CPU를 독점하는 경우를 방지하기 위해서 timer에 시간을 설정함.
    - 타이머는 매 clock 1씩 감소.
    - 0이 되면 timer가 CPU에 timer interrupt를 발생시켜 CPU의 사용권을 사용자 프로세스에서 OS에 넘겨줌.
- 타이머는 time sharing을 구현하기 위해 널리 이용됨
- 현재 시간을 계산하기 위해서도 사용됨

### device controller - local buffer - disk, I/O device

- device controller(장치 제어기)
    - 해당 I/O 장치 유형을 관리하는 일종의 작은 CPU
    - 제어 정보를 위해 control register, status register를 가짐
        - control register : 뭘 수행할건지를 담아놓음
    - local buffer를 가짐(일종의 data register)
        - 요청된 데이터를 여기에 넣어 놓음
    - I/O는 실제 device와 local buffer 사이에서 일어남
    - device controller가 I/O가 끝났을 경우 CPU에 interrupt를 보냄
        - 이후에는 local buffer에 있는 내용을 memory에 복사
- device driver(장치 구동기)
    - OS 코드 중 각 장치별 처리 루틴을 처리하기 위한 software

### DMA(Direct Memory Access) controller

- I/O 장치에서 interrupt가 빈번하게 발생하면 CPU가 너무 자주 멈춰야 함.
    - CPU가 device local buffer의 내용을 메모리로 불러오는 데에도 overhead가 너무 큼
- 이런 경우 성능저하가 일어날 수 있기 때문에 메모리에 직접 접근할 수 있는 컨트롤러를 둬서 CPU에 interrupt를 발생하는 빈도를 줄이면서 local buffer의 내용을 대신 memory에 올림.

### memory controller - Memory

- DMA에서도 메모리에 접근하기 때문에 충돌이 일어날 수 있음. memory controller는 이를 해결하기 위한 기능을 제공함

## (2) 입출력의 수행

- 모든 입출력 명령은 특권 명령
- 사용자 프로그램의 I/O 수행
    - System call
        - 사용자 프로그램이 커널의 서비스를 받기 위해 커널 함수를 호출하는 것
        - 사용자 프로그램이 직접 interrupt를 발생시켜 mode bit을 0으로 바꾸고 커널의 함수를 호출한 후 커널이 해당 함수를 수행.
        - 사용자 프로그램은 커널에 I/O를 요청
            - I/O 요청을 위해서는 소프트웨어 인터럽트가 필요
    - trap을 사용해 interrupt vector의 특정 위치로 이동
    - 제어권이 interrupt vector가 가리키는 interrupt service routine으로 이동
    - 올바른 I/O 요청인지 확인 후 I/O를 수행
    - I/O 완료 시 제어권을 시스템콜 다음 명령으로 옮김
        - I/O 수행 완료를 보고할 때에는 하드웨어 인터럽트를 사용

## (3) 인터럽트(Interrupt)

- 인터럽트
    - 인터럽트 당한 시점의 레지스터와 PC를 저장한 후 CPU의 제어를 인터럽트 처리 루틴에 넘김
- 넓은 의미로는 하드웨어 & 소프트웨어 인터럽트를 모두 포함해서 말함
    - 하드웨어 인터럽트 : 좁은 의미의 인터럽트는 하드웨어 인터럽트만을 말함(보통은 이 의미)
    - 소프트웨어 인터럽트, 트랩
        - Exception : 프로그램이 오류를 범한 경우
        - System call : 프로그램이 커널 함수를 호출하는 경우
- 관련 용어
    - 인터럽트 벡터
        - 해당 인터럽트의 처리 루틴 주소를 가지고 있음
        - 어떤 인터럽트가 들어왔을 떄 어떤 함수를 실행할 것인지를 담아 놓은 것
    - 인터럽트 처리 루틴(Interrupt Service Routine, Interrupt Handler)
        - 해당 인터럽트를 처리하는 커널 함수

---

- CPU와 memory 부분을 host라고도 부름.
- CPU는 매순간 메모리에 저장된 instruction을 수행함.
    - 어떤 instruction을 수행?
    - CPU의 레지스터들에 포함된 program counter가 가리키는 주소의 instruction.

## (1) 동기식 입출력과 비동기식 입출력

### Synchronous I/O

- I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로세스에 넘어감
- 구현법 1
    - I/O가 요청된 후 끝날 때까지 CPU를 대기시킴
    - 매 시점 하나의 I/O만 일어날 수 있음
    - CPU와 I/O 장치가 모두 낭비됨
- 구현법 2
    - I/O가 완료될 때까지 해당 프로세스에게서 CPU를 빼앗음
    - I/O 처리를 기다리는 줄에 그 프로세스를 줄세움
    - 다른 프로세스에 CPU 사용권을 넘겨줌
    - CPU와 I/O 장치가 모두 쉼없이 일할 수 있음
    - 보통은 이런 방식으로 구현함
- 데이터를 read해와야 하는 경우, 데이터를 요청하고 받고 나서야 읽을 수 있을 것이므로 synchronous해야 할 것 같음

### Asynchronous I/O

- I/O가 시작된 후 I/O 작업이 끝나기를 기다리지 않고 제어가 사용자 프로세스에 즉시 넘어감
- Read더라도 I/O와 상관없이 할 수 있는 일들이 있다면 Async하게 할 수 있을 것
- Write의 경우는 보통 Async하게 구현됨. 물론 Sync로도 할 때도 있음.

### ⭐ 두 경우 모두 I/O의 완료는 (하드웨어) 인터럽트로 알림

## (2) DMA(세부)

- Interrupt를 더 빈번하게 발생시키는 빠른 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용
- CPU의 중재 없이 device controller가 device의 buffer의 내용을 메모리에 block 단위로 직접 전송
- byte 단위가 아니라 block 단위로 인터럽트를 발생시킴

## (3) 서로 다른 입출력 명령어

- I/O를 수행하는 special instruction들에 의해(`load`, `store`)
- Memory Mapped I/O에 의해

## (4) 저장 장치 계층 구조

Primary(Executable) : Register ↔ Cache memory ↔ Main memory ↔

Secondary : Magnetic Disk ↔ Optical Disk ↔ Magnetic Tape

레지스터 쪽으로 갈 수록 작고, 빠르고, 비싸고, 휘발성.

### Caching

데이터를 빠르게 재사용할 수 있도록 더 빠른 저장 시스템으로 복사해놓는 것  

# 2. Program Execution

- 프로그램을 실행시키면 프로세스의 주소 공간이 생겨남. 모든 프로세스는 독자적인 주소 공간을 가짐.
- 이것들을 모두 실제 물리적 메모리에 올려놓는 건 아니고, 일부만 올려놓음.
- 사용되지 않게 되면 메모리에서 삭제되거나 / 지금 사용되지는 않지만 저장해놓기는 해야하는 경우에는 swap area로 내려감
    - swap area : 메모리의 연장 공간으로 사용하는 영역

### 커널 주소 공간의 내용

- Code
    - 시스템콜, 인터럽트 처리 코드
    - 자원 관리를 위한 코드
    - 편리한 서비스 제공을 위한 코드
- Data
    - 각 프로세스, CPU, 메모리, 디스크 등을 관리하기 위한 자료구조들을 담아 놓은 곳
- Stack
    - 각 프로세스들의 커널 스택

### 사용자 프로그램이 사용하는 함수

- 프로세스의 주소 공간
    - 사용자 정의 함수
        - 자신의 프로그램에서 정의한 함수
    - 라이브러리 함수
        - 자신의 프로그램에서 정의하지 않고 갖다 쓴 함수
        - 자신의 프로그램의 실행 파일에 포함되어 있음
- 커널 주소 공간
    - 커널 함수
        - 운영체제의 프로그램 함수
        - 커널 함수 호출 = 시스템 콜

### 프로그램의 실행

- Process A
    - Program begins
    - User defined function call

---

- Kernel
    - System call
    - Kernel function
    - Return from kernel

---

- Process A
    - Library function call

---

- Kernel
    - System call
    - Kernel function
    - Return from kernel

---

- Program ends
