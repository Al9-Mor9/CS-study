# [9] Virtual Memory

# 1. Demand Paging

- 실제로 ****************필요할 때**************** page를 메모리에 올리는 것
    - I/O 양의 감소
    - Memory 사용량 감소
    - 빠른 응답 시간
    - 더 많은 사용자 수용
- Valid / Invalid bit의 사용
    - Invalid?
        - 사용되지 않는 주소 영역인 경우
            
            ⇒ page entry는 주소 영역만큼 만들어짐
            
        - 페이지가 물리적 메모리에 없는 경우
            
            ex) 현재 물리 메모리에 올라가 있지 않고 backing store에 올라가 있는 경우
            
    - 처음에는 모든 page entry가 invalid로 초기화되어 있음
    - 주소 변환 시에 invalid bit으로 set 되어 있으면 ⇒ “page fault”
        - CPU는 자동으로 운영체제에게 넘어감(page trap)

## Page Fault

- Invalid page에 접근하면 MMU가 page fault trap을 발생시킴
- OS로 CPU가 넘어가 page fault handler가 invoke됨
    1. Invalid reference?
        
        ex) bad address, protection violation ⇒ abort process
        
    2. Get an empty page frame
    3. 해당 페이지를 disk에서 memory로 읽어옴
        1. disk I/O가 끝나기까지 이 프로세스는 CPU를 preempt 당함
        2. Disk read가 끝나면 page tables entry에 기록. valid/invalid bit를 valid로 set
        3. ready queue에 process를 insert ⇒ dispatch later
    4. 이 프로세스가 CPU를 잡고 다시 running
    5. 중단되었던 instruction을 재개

## Performance of Demand Paging

- Page Fault Rate 0 ≤ p ≤ 1
    - 대부분의 경우 0.01…정도로 0에 아주 가까움
- EAT = (1 - p) * (메모리 접근 시간) + p * (OS & HW page fault overhead + [swap page out if needed] + swap page in + OS & HW restart overhead)

# 2. Free frame이 없는 경우 - Page replacement

- victim의 frame을 swap out, 요청된 페이지를 swap in.
- 어떤 frame을 빼앗아올지 결정
- 곧바로 사용되지 않을 page의 frame을 빼앗는 게 좋음
- 동일한 페이지가 여러 번 메모리에서 쫓겨났다 들어올 수 있음

## (Page) Replacement Algorithm

- page-fault rate을 최소화하는 것이 목표
- 평가 : 주어진 page reference string에 대해 page fault를 얼마나 내는지를 조사
    - reference string의 예) 1, 2, 3, 4, 1, 2, 5, 1, 2, …

### Optimal Algorithm

- 미래에 참조될 메모리들에 대해 미리 안다고 가정함.
- 가장 먼 미래에 참조되는 page를 replace하는 게 가장 optimal.
- Offline algorithm이라고도 부름
- 미래에 어떻게 메모리가 사용될지를 알 수 없으므로 실제로 사용할 수는 없지만
    - 다른 알고리즘의 성능의 upper bound를 제공함
    - Belady’s optimal algorithm, MIN, OPT 등으로 불림

### FIFO

- 먼저 들어온 것을 먼저 내쫓음
- 페이지 프레임 수를 들리면 보통 성능이 늘어날 것으로 예상하지만, FIFO 알고리즘을 사용했을 때에는 오히려 성능이 낮아지는 경우가 발생할 수도 있음
    
    ⇒ 이 현상을 FIFO Anomaly(Belady’s Anomaly)라 부름
    

### LRU (Least Recently Used)

- 가장 오래 전에 참조된 것을 내쫓음

### LFU (Least Frequently Used)

- 참조 횟수가 가장 적은 페이지를 지움
- 최저 참조 횟수인 page가 여럿 있는 경우
    - LFU에서는 여러 page 중 쫓아낼 것을 임의로 선정함
    - 성능 향상을 위해 가장 오래 전에 참조된 page를 지우게 할 수도 있음
- 장점
    - 장기적인 시간 규모를 보기 때문에 page의 인기도를 좀 더 정확히 반영할 수 있음
- 단점
    - 참조 시점의 최근성 반영불가
    - LRU보다 구현이 복잡
    - LRU는 Linked-list로 구현시 $O(1)$
    - LFU는 heap으로 $O(\log n)$

### 다양한 캐싱 환경

- 캐싱 기법
    - 한정된 빠른 공간에 요청된 데이터를 저장해뒀다가 후속 요청시 캐시로부터 직접 서비스하는 방식
    - paging system 외에도 cache memory, buffer caching, web caching 등 다양한 분야에서 사용
- 캐시 운영의 시간 제약
    - 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리면 실제 시스템에서 사용불가
    - Buffer Caching, Web Caching : $O(1)$ ~ $O(\log n)$
    - Paging System의 경우
        - paging system에서 LRU, LFU를 사용할 수 있는가???
            - 메모리에 이미 올라온 페이지에 대해서는 OS에 CPU가 넘어가지 않음
            - page fault인 경우에만 OS가 관여
            - 페이지가 이미 메모리에 존재하면 참조 시각 등의 정보를 OS가 알 수 없음
            - $O(1)$인 LRU의 list 조작도 불가능
            
            ⇒ 사실은 Virtual Memory 시스템에서 LRU, LFU를 사용할 수 없음!
            

### Clock Algorithm

- Clock Algorithm
    - LRU의 근사 알고리즘
        - 가장 옛날에 참조된 페이지를 쫓아내는 게 아니라, 최근에 사용된 페이지는 쫓아내지 않겠다는 것 (적어도 clock 한 바퀴에 참조되지 않은 페이지를 쫓아내겠다~)
    - Second change algorithm, NUR, NRU(Not Recently Used) 등으로 불림
    - Reference bit을 사용해서 교체 대상 페이지를 선정 (원형 리스트)
    - Ref bit이 0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동
    - 포인터 이동 중 ref bit 1은 모두 0으로 바꿈
    - Ref bit이 0인 것을 찾으면 해당 페이지를 교체하고 ref bit을 1로 둠
    - 한 바퀴 되돌아와서도 0이면 그때에는 replace됨
    - 자주 사용되는 페이지는 second chance에서 1이 됨
- 개선
    - reference bit & modified bit (dirty bit)을 함께 사용
        - modified bit을 사용하지 않는 경우
            - 내려갈 페이지가 backing storage에서 올라온 이후 바뀐 것이 없더라도 다시 backing에 write해야 하므로 data transfer가 두 번 일어나야 함. (내려갈 것을 backing storage에 쓸 때 + 올라올 것을 backing storage로부터 부를 때)
        - modified bit을 사용하는 경우
            - 내려갈 페이지가 backing storage에서 올라온 이후 바뀐 것이 없다면 data transfer를 일으킬 필요가 없게 됨. 👍
    - reference bit = 1 : 최근에 참조된 페이지
    - modified bit = 1 : 최근에 변경된 페이지 (I/O 동반)
    - modified bit이 0인 것과 1인 게 있으면 0인 것을 우선 쫓아내도록 하기

# 3. Page Frame의 Allocation

- Allocation problem : 각 process에 얼마만캄의 page frame을 할당할 것인가?
- Allocation의 필요성
    - 메모리 참조 명령어 수행시, 명령어, 데이터 등 여러 페이지를 동시에 참조함
        - 명령어 수행을 위해 최소한 할당되어야 하는 frame의 수가 있음
            
            ex) Loop를 구성하는 page들은 한꺼번에 allocate 되는 것이 유리
            
            그렇지 않으면 매 loop마다 page fault가 일어날 것이기에
            
- Allocation Scheme
    - Equal Allocation : 모든 프로세스에 같은 개수를 할당
    - Proportional Allocation : 프로세스 크기에 비례해 할당
    - Priority Allocation : 프로세스의 CPU 우선순위에 따라 다르게 할당
- 그런데 사실 이런 할당을 해주지 않더라도 replacement 알고리즘을 사용하다보면 어느 정도 그런 효과를 누릴 수도 있음

## Global vs. Local Replacement

### Global Replacement

- Replace 시 다른 프로세스에 할당된 frame을 뺏어 옴
- Process별 할당량을 조절하는 또 다른 방법
- FIFO, LRU, LFU 등의 알고리즘을 global replacement로 사용할 경우에 해당
    - 할당 효과는 없음
- Working set, PFF 알고리즘 사용
    - 할당 효과가 있음

### Local Replacement

- 자신에게 할당된 frame 내에서만 replacement
- Replacement algorithm을 각 프로세스 별로 운영하는 경우

# 4. Thrashing

- 프로세스의 원활한 수행에 필요한 최소한의 page frame 수를 할당 받지 못한 경우에 발생
    - page fault rate가 매우 높아짐
    - CPU utilization 낮아짐
    - OS는 다른 프로세스도 돌릴 수 있겠다 싶어서 MPD(Multi-programming degree)를 높여야 한다고 판단
    - 또 다른 프로세스가 시스템에 추가됨(higher MPD)
    - 프로세스 당 할당된 frame의 수가 더욱 감소
    - 프로세스는 page의 swap in / out으로 매우 바쁨
        - 하지만 대부분의 시간에 CPU는 한가함
    - low throughput

## Working-Set Model

### Locality of Reference

- 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조한다.
- 이 집중적으로 참조되는 해당 page들의 집합을 locality set이라 함

### Working-Set Model

- Locality에 기반, 프로세스가 일정 시간 동안 원활히 수행되기 위해 한꺼번에 메모리에 올라와 있어야 하는 page들의 집합
- Working-set Model에서는 process의 working set 전체가 메모리에 올라와 있어야 수행되고, 그렇지 않을 경우 모든 frame을 반납하고 swap out
- Thrashing 방지
- MPD 결정

### Working-Set Algorithm

- Working Set 결정
    - 미리 알아낼 수는 없고, 과거를 보고 추정
    - Working set window를 통해 알아냄
    - Window size가 $\Delta$인 경우,
        - 시각 $t_i$에서의 working set $WS(t_i)$
            - time interval $[t_i-\Delta, t_i]$ 사이에 참조된 서로 다른 페이지들의 집합
        - Working set에 속한 page는 메모리에 유지, 그렇지 않은 것들은 버림
            - 참조된 후 $\Delta$ 시간 동안 해당 page를 메모리에 유지한 후 버림

## PFF (Page-Fault Frequency) Scheme

- Page-fault rate의 상하한을 둔다.
    - 상한을 넘으면 frame을 더 할당하고
    - 하한 이하면 할당 frame 수를 줄인다.
- 빈 frame이 없어 더 줄 메모리가 없으면, 그 메모리를 통째로 swap out해 남아 있는 프로세스들이라도 잘 돌아가게 한다.

# 5. Page Size의 결정

- Page size를 감소시키면
    - 페이지 수 증가
    - 페이지 테이블 크기 증가
    - Internal Fragmentation 감소
    - Disk transfer 효율성 감소
        - Seek/rotation vs transfer
        - 디스크는 디스크 헤드가 이동해서 찾는데 이 시간이 오래 걸림
        - 웬만하면 한 번에 많이 찾아서 많이 올려 놓는 게 좋음
    - 필요한 정보만 올라오므로 메모리 이용이 효율적
        - locality 활용 측면에서는 좋지 않음
- Trend
    - Larger page size
