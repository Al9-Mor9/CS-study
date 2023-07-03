# Virtual Memory 

물리적인 메모리의 주소변환은 운영체제가 관여하지 않음. 
가상메모리는 운영체제가 관여함. 

### Demand Paging 
실제로 필요할 때 page를 메모리에 올리는 것.

- I/O양의 감소
- Memory 사용량 감소
- 빠른 응답 시간
- 더 많은 사용자 수용


Valid/Invalid 
    Invalid
        사용되지 않는 주소 영역인 경우
        페이지가 물리적 메모리에 없는 경우
    처음에는 모든 page entry가 invalid로 초기화
    address translation시 invalid bit이 set되어있으면
    -> page fault 
    page fault trap이 걸림.


### Page Fault

invalid page 접근하면 MMU가 trap 발생

Kernel mode로 들어가서 page fault handler가 invoke됨
다음과 같은 순서로 page fault를 처리
1. Invalid reference인지 check(bad address, protection violation) -> abort process.
2. Get an empty page frame(없으면 replace)
3. disk에서 memory로 읽어온다.
    - disk i/o가 끝나기까지 이 프로세스는 cpu를 preempt 당함.(block)
    - disk read가 끝나면 page tables entry기록. valid/invalid bit => valid
    - ready queue에 process를 insert -> dispatch later
4. 이 프로세스가 cpu를 잡고 다시 running
5. 중단되었던 instruction 재개

Performance of Demand Paging

Page Fault rate 0 ~ 1.0

### Free frame이 없는 경우

- Page replacement

어떤 frame을 빼앗을 지.

- replacement algorithm
page-fault rate을 최소화하는 것이 목표
주어진 page reference string에 대해 page fault를 얼마나 내는지 조사.


#### Optimal Algorithm

- min(OPT) : 미래에 참조되는 page를 replace.
미리 다 안다고 가정. offline algorithm.

-> 다른 알고리즘 성능에 대한 upper bound 제공.

#### FIFO Algotithm

- FIFO : 먼저 들어온 것을 먼저 내쫓음 

메모리를 늘려주면 page fault가 줄어야하는데 이 알고리즘은 올라감.
FIFO anomaly
    more frames -> less page faults X


#### LRU Algorithm (Least Recently Used)

- 가장 오래 전에 참조된 것을 지움. 

FIFO와 달리 재사용시 지워지는 순서가 바뀔 수 있다.


#### LFU Algorithm (Least Frequently Used)

- 참조 횟수가 가장 적은 페이지를 지움.

최저 참조 횟수인 page가 여럿 있는 경우 -> 일단 랜덤. 오래전을 설정가능


#### LRU vs LFU

1111223324 5

LRU : 1 page 삭제
LFU : 4 page 삭제

#### LRU, LFU 구현

- LRU

linked list 형태로 저장. 

    LRU page - ... - MRU page

최근 참조된 페이지는 MRU로 보냄. 비교할 필요가 없음

O(1) complexity (쫓아내는데 시간복잡도 1)


- LFU

LFU page - ... - MFU page

linked list는 참조횟수 1이 늘어나도 계속 비교해야함. 

그래서 heap으로 구현

O(lon n) complexity 


#### 다양한 캐슁 환경

- 캐슁 기법.

    한정된 빠른 공간(=캐쉬)에 요청된 데이터를 저장해 두었다가 후속요청시 캐쉬로부터 직접 서비스하는 방식
    paging system, cache memory, buffer caching, Web caching 등 


어떤 것을 쫓아낼지 결정하는 시간에 제약이 있다.
- 캐쉬 운영의 시간 제약

    교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실제 시스템에서 사용할 수 없다.
    Buffer caching이나 Web caching의 경우
        O(1)에서 O(log n) 정도까지 허용.
    Paging system의 경우
        page fault인 경우 OS 개입.
        페이지가 이미 메모리에 존재하는 경우 참조시각 등의 정보를 os가 알 수 없다.
        o(1)인 LRU의 list 조작조차 불가능


page fault로 trap 발생해서 cpu 제어권이 os에 오지않으면 정보 관리가 안된다....

자료구조 연결하는 작업도 당연히 못함.

사실 LRU, LFU는 쓸 수가 없음.


#### Clock Algorithm


- Clock algorithm
    LRU의 근사 알고리즘
    Not Recently Used, Not Used Recently
    second chance algorithm

MMU가 reference bit 세팅함. 
reference bit이 1이면 0으로 바꿈. 그리고 0인놈은 아웃.
시계가 한 바퀴 도는 동안 1로 안바뀌면 아웃.

circular linked list를 돈다...
하드웨어는 비트 세팅. os는 비트 클리어해감.

modified bit(dirty bit)
    read인지 write인지. 
    쫓겨날 때 write이면 backing store에 저장해놔야함. 


#### Page Frame Allocation

Allocation problem: 각 process에 얼마만큼의 page frame을 할당할 것인가
Allocaiton 필요성
    메모리 참조 명령어, 수행시 명령어, 데이터 등 여러 페이지 동시 참조. 
    - 명령어 수행을 위해 최소한 할당되어야 하는 frame의 수가 있다.
    Loop를 구성하는 page들은 한꺼번에 allocate 되는 것이 유리함.
    - 최소한의 allocation이 없으면 매 loop 마다 page fault



- allocation scheme
    equal allocation
    proportional allocation
    priorty allocation


#### Global vs local replacement 

Global replacement - Process에 할당된 frame을 빼앗아 올 수 있다.
local - 미리 프로세스마다 할당.


#### Thrashing

프로세스의 원활한 수행에 필요한 최소한의 page frame수를 할당 받지 못한 경우.

cpu utilization이 낮아짐. page fault가 나서 disk io하러감. 

OS는 MPD를 높여야 한다고 판단. 프로세스는 page의 swap in out으로 매우 바쁨
cpu는 또 한가함. 


#### working set model
locality of reference
 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조.
 집중적 참조되는 page 집합을 locality set이라 함.

working-set model
    locality에 기반하여 프로세스가 일정 시간 동안 원활하게 수행되기 위해 한꺼번에 메모리에 올라와 있어야하는 page의 집합을 working-set

    working set 모델에서는 process의 working set 전체가 메모리에 올라와 있어야 수행되고 그렇지 않을 경우 모든 frame을 반납한 후 swap out(suspend)
    thrashing 방지
    multiprogramming degree를 결정.


미리 알기가 힘듬

working set window를 통해 알아냄
    window size가 d인 경우
    시각 ti에서의 working set WS(ti)
    - Time interval [ti-d ~ ti] 사이에 참조된 서로 다른 페이지들의 집합.
    working 셋에 속한 page는 메모리에 유지 속하지 않은 것은 버림.

    델타는 일정해도 WS의 크기는 변화함. 
=참조된 후 d시간 동안 해당 page를 메모리에 유지할 후 버리는 것과 같다.


#### PFF(Page-Fault Frequency) Scheme

page-fault rate의 상한값과 하한값을 둔다. 

- Page fault rate이 상한값을 넘으면 frame을 더 할당한다.
- 하한값 이하면 할당 frame을 줄임.
빈 frame이 없으면 일부 프로세스를 swap out


#### Page size의 결정

page size 감소시
    페이지 수 증가
    페이지 테이블 크기 증가
    internal fragmentation 감소
    Disk transfer의 효율성 감소
        seek/rotation vs transfer (디스크는 헤드가 원판을 돌음. seektime)
    필요한 정보만 메모리에 올라와 메모리 이용이 효율적
        locality 의 활용 측면에서는 좋지 않음. 
    
Trend 
    Lager page size























