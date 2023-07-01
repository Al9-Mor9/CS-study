# Process Synchronization

누가 먼저 읽어서 조작하는지에 따라 문제가 발생할 수 있다.
데이터 접근 패턴.

Execution Box  < - > Storage Box
    CPU - Memory
    컴퓨터내부 - 디스크
    프로세스 - 그 프로세스의 주소 공간


- Race Condition

Storage box(Memory Address Space)를 공유하는 여러 E-box(CPU Process)가 있는 경우 Race Condition의 가능성이 있다. 

Mulriprocessor system에서 다양한 cpu가 동일한 메모리를 참조하면 경쟁 상태가 벌어짐. 
공유 메모리를 다양한 프로세스가 읽을 떄,,, 

커널 내부 데이터를 접근하는 루틴들 간 

운영체제에 시스템 콜을 넣음 -> 커널을 사용.  
커널을 사용중일 때, 커널에 인터럽트가 들어옴. 커널이 커널을 건드려서  


1. kernel 수행 중 인터럽트 발생 시 

- 커널이 일하는 동안 interrupt를 disable하게 만들어 주면 해결이 가능하긴 하다. 


2. process가 system call을 하여 kernel mode로 수행 중인데 context switch가 발생한 경우 
 user mode. kernel mode 
 
user mode에 있는 동안 context switch가 발생하면 괜찮다. 그런데 kernel mode로 값을 변경하던 중 
발생하면. cpu를 preempt 당하면....  

-> kernel mode 수행 중일 때, cpu를 preempt 하지 않게함.  

3. multiprocessor에서 shared memory 내 kernel data 

인터럽트를 제어한다고 race condition이 해결되지 않는다. 

-> 한번에 하나의 cpu만이 커널에 들어갈 수 있게 하는 방법. 

-> 커널 내부에 있는 각 공유데이터에 접근 할 때마다 그 데이터에 대한 lock/unlock을 하는 방법. 


- Critical-Section Problem 

공유 데이터를 접근하는 코드 

보통 코드는 공유데이터를 접근하거나 아니거나로 구분할 수 있다.

------------------------------------------------------------------------------------------------------
## Race condition 해결
소프트웨어적으로 해결해보자~
두 개의 프로세스가 있다고 가정 p0, p1
entry section
    critical section
exit section


## 프로그램적 해결법의 충족 조건

### Mutual Exclusion(상호 배제)

    프로세스 P1이 critical section 부분을 수행 중이면 다른 프로세스들은 critical sction에 들어가면 안됨.

### Progress(진행)

    아무도 critical section에 있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있으면 critical section에 들어가게 해주어야함.

### Bounded Waiting(유한 대기)
    프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 한다.

    starvation 문제.


### 1. Algorithm 1

- Synchronization variable 
int turn;
initially turn = 0;

- Process P0

    while(turn != 0)
    critical section
    trun = 1;

본인 끝나고 mutual exclusion은 만족함. 하지만 turn이 0이나 1이 아니면 Progress가 안됨. 한 프로세스가 critical section에 들어갔다 나와야지만 차례를 넘겨줌. 상대방이 한 번만 들어갈 경우. 다음 사람도 한 번만 들어 갈 수 있다.

### 2. Algorithm 2

- Synchronization variable 
boolean flag[2];
initially flag[모두] = false;

- process p0
    flag[i] = true;
    while flag[j]:
    critical section
    flag[i] = false;

서로 깃발을 들고있으면 영원히 서로 못 들어감.
깃발 들 때 context switch가 발생하면...?


### 3. Peterson/s Algorithm

상위 두 동기화 변수를 합침

process pi

    flag[i] = true;
    turn = j;
    while (flag[j] && trun==j);
    flag[i] = false;
    
3가지 충족 조건을 만족한다.
하지만 Busy waiting이 발생할 수 있다.(spin lock?)
들어와서 while문만 돌다가 나갈 수 있다.

instruction 하나 실행되고 빼앗길 수 있다.!

### 하드웨어 동기화

하드웨어적으로 test & modify를 atomic하게 수행할 수 있도록 지원하는 경우 앞의 문제는 간단히 해결.
읽고 연산하고 저장하는걸 하나의 instruction으로 실행가능하다면... lock을 해결 할 수 있다.
읽고 쓰기 한 번에...

boolean lock = false;

process pi

    while (Test_and_Set(lock));
    critical section
    lock = false;


1이 읽히면 계속 기다림
0이면 쓰고 들어감.


## Semaphores 

추상 자료형. 오브젝트하고 오퍼레이션으로 구성된다.
논리적으로 정의한 것.

Semaphore 도 추상 자료형임.

Semaphore S
    숫자 변수. 자원이라고 생각하셈.
    아래 두 가지 atomic 연산에 의해서만 접근 가능

P와 S는 atomic 연산에 의해서만 접근 가능.(구현은 몰라도돼~)

자원이 없으면 기다림. 있으면 빼고 들어감. busy-wait 발생할 수 있어.
P(S):
    while(S<=0) do no-op;
    S--;

끝나면 반납을 함.
V(S):
    S++;


동기화 variable
semaphore mutex;

- process pi
    P(mutex);
    critical section;
    V(mutex);


### Block & Wakeup 방식 (sleep lock)

    (어떤 프로세스가 CPU를 사용하다 i/o같이 오래걸리는 작업을 하러가면 프로세스 상태가 block되고 cpu를 얻을 권한이 없어짐.
     i/o가 끝나면 Ready Queue로 돌아올 수 있는 권한이 생김.)

누가 공유데이터를 쓰고있으면 프로세스를 blocked 시킴. sleep 시킴..

Semaphore를 다음과 같이 정의
typedef struct
    int value;
    struct process *L;
block과 wakeup을 가음과 같이 가정.
block
    커널은 block을 호출한 프로세스를 suspend시킴. 이 프로세스의 PCB를 semaphore에 대한 wait queue에 넣음
wakeup(P)
    block된 프로세스 P를 wakeup시킴 이 프로세스의 PCB를 ready queue로 옮김.

P(S):
    S.value--;
    if (S.value <0 ) 
        add this process to S.L;
        block();

V(S):
    S.value++;
    if(S.value <= 0)
        remove a process P from S.L;
        wwakeup(P);

빼고 시작하니까. 자원을 내놓았는데 0이하라면 잠든놈이 있다!
이전에서 자원의 갯수를 세는거하곤 좀 다르다. 대기표 정도로 생각하면 될 듯?


Block/wakeup overhead vs Critical section 길이

    프로세스의 상태를 바꾸는 문제. 
    critical section의 길이가 짧으면 busy wait해도 됨. 
    근데 길면 낭비가 심해짐. 왜냐 time 기준으로 자르니까 


Counting semaphore - 자원 카운팅에 씀
Binary semaphore - mutual exclusion에 씀


### Deadlock & Starvation

-  Deadlock

세마포어 2가지를 쓰는경우

P0
P(S); <- 딱 획득하고 넘어갈 때~~~~~ context 스위치가 들어가면....
P(Q);

V(S);
V(Q);

P1
P(Q);
P(S);

V(Q);
V(S);

-> 순서를 맞춰주면 됨 ㅇㅇ

- Starvation

    indefinite blocking 프로세스가 suspend된 이유에 해당되는 세마포어 큐에서 빠져나갈 수 없는 현상.


Dining - philosophers problem


## Classical Problems of Synchronization


### Bounded-Buffer Problem(Producer-Consumer Problem)

임시로 데이터를 저장하는 공간. 버퍼. 버퍼의 크기는 유한하다.

1. 공유버퍼에 여러 프로세스가 동시에 작성, 삭제하는 경우 race condition발생.
-> lock으로 해결.

2. 버퍼 자원의 한계. 
-> semaphore로 자원 counting이 가능하다. (비어있는 버퍼의 갯수)


Synchronization variables
    mutual exclustion -> binary semaphore (lock -> mutex)
    resource count -> integer semaphore (full, empty)


생산자
    - 빈 버퍼가 있는지.
    - lock을 검
    - 데이터 입력 및 buffer 조작
    - lock을 푼다
    - full buffer 하나 증가.

소비자
    - full 버퍼가 있는지
    - 공유데이터에 lock을 건다
    - full buffer에서 데이터 꺼내고 buffer 조작
    - lock을 품
    - empty buffer하나 증가

### Readers and Writers Problem

process가 db에 write 중일 때 다른 process 가 접근하면 안됨.
read는 동시에 여럿이 해도 됨.
solution
    writer가 db 접근 허가를 못 얻은 상태는 reader들은 db 접근가능
    writer는 대기 중이 reader가 하나도 없을 때 db 접근이 허용
    writer가 접근 중이면 reader들은 접근 금지
    writer가 db에서 빠져나가야만 reader의 접근이 허용된다.

semaphore mutex = 1, db= 1;

readcount==1 -> 최초의 reader만 P(db)로 block하고 V(db)

mutex는 readcount라는 공유 변수에 대한 lock이다.

쓰고있는 프로세스, 읽고있는 프로세스 둘 다 도착함. 
Reader는 계속읽을 수 있다. 근데 writer는 계속 기다림....ㅠㅜ
starvation이 발생가능하다.

어느정도의 reader가 지나가면 writer에게 주는 식으로 해결함.

### Dining- Philosophers Problem

식사하는 철학자(석철아님 ㅋ)

본인 기준 왼쪽 젓가락을 잡으면, 오른쪽 젓가락을 잡을 수 없다.

    P(chopstick[i]);
    P(chopstick[(i+1)%5]);
    ~
    V(chopstick[i]);
    V(chopstick[(i+1)%5]);
    
해결법

- 4명만 잡게함.
- 두 개를 잡을 수 있을 때에만 젓가락 잡게함.
- 짝/홀 사람마다 젓가락 우선순위 다르게함.

세마포어 말고 모니터가 조타.


젓가락 두 개를 다 잡는 권한을 먹고 있는지 여부로 판단함.
젓가락 내려놓을 때 양 옆 철학자가 먹을 수 있는지 확인. 


semaphore의 문제점

    코딩힘들어
    correctness 입증이 어렵다
    자발적 협력이 필요하다.
    한번의 실수가 모든 시스템에 치명적 영향.


## Monitor

high-level synchronization construct

프로시져??

모니터 내부에 프로시져를 정의해놓고 프로시져를 통해서 공유데이터를 접근하게
하나의 프로세스만 모니터를 접근할 수 있다.
-> 락을 걸필요가 없다.

세마포어에서 자원의 개수를 세는건 모니터에도 필요해
-> condition 변수

여분이 없으면 x.wait(), 생기면, x.signal()

condition full, empty
condition var는 값을 가지지 않고 자신의 큐에 프로세스를 매달아서 sleep 시키거나 큐에서 프로세스를 깨우는 역할만 함.


## concurrenct control 병행 제어 - 모니터
프로세스 동기화

모니모니터 - 동시접근 제어를 해줌.
모니터 안에 정의된 코드를 프로세스가 실행.
active한 프로세스를 하나만 수용함.

모니터에 active한 프로세스가 있다면 다른 프로세스는 entry queue로 들어감.

condition var는 프로세스를 잠들게하거나 깨게하거나의 용도. 조건이 만족하지 않아서 잠들게하겠다....
프로세스를 줄세움.

condition full, empty

full <- 내용이 들어있는 버퍼를 기다리는 프로세스를 줄세우는 큐....?
empty <- 빈버퍼가 있는지...


목적부터가 다르다. 
-> 코드는 비슷함.

식철문제에서 세마포어와 거의 유사한데 공유데이터에 대한 락을 안 걸고 진행됨.


