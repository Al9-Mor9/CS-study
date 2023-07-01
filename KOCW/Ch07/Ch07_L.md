# Dead Lock

교착 상태

overhead가 큰 상황.

- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태

- Resource
    하드웨어, 소프트웨어 등을 포함
    I/O device, CPU cycle, memory space, semaphore
    프로세스가 자원을 사용하는 절차
        Request, Allocate, Use, Release

- ex
    1. 시스템에 2개의 tape drive가 있다
        프로세스 p1, p2 각각이 하나의 tape drive를 보유한 채 다른 하나를 기다리고 있다.
    2. Binary semaphore A and B
        p0      p1
        P(A);   P(B);
        P(B);   P(A);


## Deadlock 발생 조건 4가지

오랜 전문가들이 세워놓음ㅋ

1. Mutual exclusion(상호 배제)
    매 순간 하나의 프로세스만이 자원을 독점적으로 사용

2. No preemption(비선점)
    가지고 있는 자원을 빼앗기지 않는다.

3. Hold and wait(보유 대기)
    자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있다.

4. Circular wait(순환 대기)
    자원을 기다리는 프로세스간에 사이클이 형성.
    교통 # ..


### Resource-Allocation Graph (자원 할당 그래프)

- Vertex
    Process P
    Resource R

- Edge
    request edge
    assignment edge

그래프에 cycle이 없으면 deadlock이 아닌다
cycle이 있으면 .. 
    여분의 instance가 없다면, 데드락
    하지만 자원의 instance가 여러개 있다면 데드락 일수도 아닐 수도 있당. 


### Deadlock의 처리방법

#### Deadlock Prevention
자원할 당시 Deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것

Mutual exclusion은 하기 힘듦.

hold and wait. 기다릴 경우는 자원을 가지고 있지않아야함.
1. 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법 - 안써도 계속 들고있어야하면 비효율적임
2. 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청.

No Preemption
process가 기다려야 하는 경우 보유한 자원이 선점됨.
모든 필요한 자원을 얻을 수 있을 때 그 프로세스가 다시 시작.
state를 쉽게 save, restore할 수 있는 자원에서 주로 사용.

circular wait 
모든 자원을 할당 순서를 정하여 정해진 순서대로만 자원 할당
순서대로 자원을 보유함.

Utilization저하, throughput 감소, starvation 문제.

#### Deadlock Avoidance

프로세스가 최대 사용량을 declare한다.

자원 요청에 대한 부가적 정보 이용
deadlock 가능성이 없을 경우 할당.

시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원 할당.

- single 자원 instance은 자원할당 그래프에서 사이클 쳌함. 
평생동안 쓸 가능성이 있는 놈들은 점선으로 구성.  점선을 실선으로 바꿀 때 사이클 쳌. n^2 시간 복잡도임.

- 여러개 자원이 있는 경우
banker's algorithm
    해당 프로세스의 최대요
    청이 가용자원으로 커버되면 어떤 요청이든 허용함. 미리 최대치를 다 준다
    조금 줘 보자? 그리고 남들이 반환하니까 데드락은 아니다? <- 뭔 소리임.
    뭐 정리하면 나중에 쓸꺼까지 미리 준다는 느낌...?

이때 safe sequence가 존재하면 시스템은 safe state

자원이 남아도 혹시모를 상황을 대비해서 자원을 안 줌. -> 비효율적

그냥 요청 확인해서 최대요청이 가용자원보다 낮으면 줌. safe한 상황을 가정한다.


#### Deadlock Detection and recovery

Detect.

자원당 instance가 single이면 그래프로 
    이거는 Corresponding wait-for graph로 치환 가능하다.
    n^2 만큼 걸림

여러개면 테이블 그려서 확인(single도 가능)
그냥 원하는 만큼 줌. 

요청에서 줄 자원이 없어도, 다른 프로세스가 반납할 것을 낙관...
데드락 발견 -> sequence가 있는지

Recovery
    process termination
        연루된 프로세스를 모두 사살
    resource preemption
        victim을 선정. safe state로 rollback하여 process를 restart
        -> Starvation 문제
        한 놈이 계속 victim으로 선정되는 경우
        cost factor에 rollback 횟수도 같이 고려


#### Deadlock Ignorance

아무런 조치를 취하지 않음.
조치가 더큰 overhead다.

사람이 직접 process를 죽임. 
UNIX, Windows등 대부분의 범용 OS가 채택



