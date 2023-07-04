# [8] Memory Management

# 1. Logical vs. Physical Address

## (1) Logical vs. Physical Address

### Logical Address ( = Virtual Address)

- 프로세스마다 독립적으로 가지는 주소 공간
- 각 프로세스 마다 0번지부터 시작
- **CPU가 보는 주소는 logical address**
    - 메모리의 시작 위치는 physical address에 따라 달라지지만 실행 파일 내에서의 logical address는 변하지 않음
    - 이 logical address를 매번 변환해서 physical address에 접근

### Physical Address

- 메모리에 실제 올라가는 위치
- 맨 아래에는 커널이, 그 위에 다른 프로세스들이 올라갈 것

🔔 주소 바인딩 : 주소 변환 주소를 결정하는 것.

- 어떤 프로세스가 **물리적 주소**의 어디에 올라갈 것인가를 결정
- Symbolic Address ⇒ Logical Address ⇒ Physical Address

## (2) Address Binding

주소 바인딩이 어떤 시점에서 이루어지는 가에 따라서 다음과 같이 분류 가능

### Compile Time binding

- 물리적 메모리 주소가 컴파일 시 알려짐
- 시작 위치 변경 시 재컴파일해야 함
- 비효율적이지만 컴퓨터 안에서 하나의 프로그램만 돌리던 옛날에는 이 방법을 쓰기도 했음
- 컴파일러는 절대 코드(absolute code) 생성
    - ❓ Absolute Code

### Load Time Binding

- Loader의 책임하에 물리적 메모리 주소 부여
- 컴파일러가 재배치가능 코드(relocatable code)를 생성한 경우 가능
    - ❓ 재배치가능 코드

### Execution Time Binding ( = Runtime Binding)

- 수행이 시작된 이후에도 프로세스의 메모리 상 위치를 옮길 수 있음
- 여기도 relocatable code
    - 컴파일 타임 바인딩 & 로드 타임 바인딩은 프로그램이 시작될 때 주소가 정해지고 변경되지 않지만 런타임 바인딩의 경우에는 바뀔 수 있음
- CPU가 주소를 참조할 때마다 binding을 점검(address mapping table)
- 하드웨어적인 지원이 필요(MMU)

## (3) Memory-Management Unit(MMU)

### MMU

Logical Address를 Physical address로 매핑해주는 하드웨어 장치

### MMU scheme

사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 base register ( = relocation register)의 값을 더한다

### user program

Logical Address만을 다루며, 실제 Physical Address는 볼 수도, 알 필요도 없다. 

## (4) Hardware Support for Address Translation

MMU는 relocation register & limit register를 이용해 주소를 변환함 

- Relocation Register ( = base register)
    - 접근할 수 있는 물리적 메모리 주소의 최소값
    - 시작위치
        - 여기에다가 logical address를 더한 곳에 접근
- Limit Register
    - 논리적 주소의 범위
    - 프로그램이 만약 악의적인 프로그램이라 필요한 범위를 벗어난 곳에 접근하려고 하는 경우를 막기 위해 범위를 걸어 놓음
    - 여기를 벗어나는 경우 trap을 발생시켜 커널에서 확인

## (5) 용어들

### Dynamic Loading

- 프로세스 전체를 메모리에 미리 올려놓는 게 아니라 해당 루틴이 불려질 때 메모리에 load하는 것.
- memory utilization 향상
- 가끔씩 사용되는 많은 양의 코드의 경우에 유용함
- 운영체제의 특별한 지원 없이 프로그램에서 구현 가능.
    - 그럼 프로그래머가 이걸 일일이 다 구현해줘야하느냐?
    - 그건 아님. **OS가 제공하는 라이브러리**를 이용.

### Overlays

- 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림
- 프로세스의 크기가 메모리보다 클 때 유용
- 운영체제의 지원없이 사용자에 의해 구현
- 작은 공간의 메모리를 사용하던 초창기 시스템에서 **수작업으로 프로그래머가 구현**
    - Manual Overlay
    - 구현이 매우 복잡

### Swapping

- 프로세스를 일시적으로 메모리에서 *backing store*로 쫓아내는 것
    - Backing store ( = swap area)
        - 디스크. 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간.
- Swap in / out
    - 일반적으로 중기 스케줄러(swapper)에 의해 swap out 시킬 프로세스를 선정
    - priority-based CPU scheduling algorithm
        - priority가 낮은 프로세스는 swap out, 높은 프로세스는 swap in
    - Compile time, load time binding에서는 원래 메모리 위치로 swap in 할 수 있어야 함
        - 원래 있던 자리를 비워놔야(?) 하기 때문에 swapping을 효과적으로 사용할 수 없음
    - Runtime binding에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음
    - swap time은 대부분 transfer time(데이터 전송 시간)

### Dynamic Linking

- Linking을 실행 시간까지 미루는 기법
    - Linking : 컴파일된 여러 파일들을 묶어서 하나의 실행 파일로 실행
- Static Linking
    - 라이브러리가 프로그램의 실행 파일 코드에 포함됨
    - 실행 파일의 크기가 커짐
    - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리를 낭비함
- Dynamic Linking
    - 라이브러리가 실행될 때 link됨
    - 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 작은 코드를 둠(stub)
    - 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고, 없으면 디스크에서 읽음
    - 운영체제의 도움이 필요함
    

# 2. Allocation of Physical Memory

## (1) Allocation of Physical Memory

### 메모리의 두 영역

메모리는 일반적으로 두 영역으로 나뉘어 사용됨

- OS 상주 영역
    - Interrupt vector와 함께 낮은 주소의 영역을 사용
- 사용자 프로세스 영역
    - 높은 주소 영역 사용

## (2) Contiguous Allocation (연속 할당)

각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것

### **Fixed partition allocation(고정 분할 방식)**

- 물리적 메모리를 몇 개의 **영구적 분할**로 나눔
- 분할의 크기가 모두 동일한 방식과 서로 다른 방식이 존재
- 분할 당 하나의 프로그램을 적재
- 융통성 없음
    - 동시에 메모리에 load되는 프로그램의 수가 고정됨
    - 최대 수행 가능 프로그램의 크기에도 제한이 있음
- Internal fragmentation & external fragmentation 모두 발생
    - Internal fragmentation
        - 프로그램이 들어갈 수 있을 만한 크기의 공간이 없는 경우
    - External fragmentation
        - 프로그램이 들어갈 만한 크기의 공간은 있지만 그 공간이 쪼개져 있어서 올릴 수 없는 경우

### **Variable partition allocation(가변 분할 방식)**

- 프로그램의 크기를 고려해서 할당
- 분할의 크기, 개수가 동적으로 변함
- 기술적 관리 기법이 필요
- External fragmentation 발생

> ⚠️ ********Hole********
> 
> - 가용 메모리 공간
> - 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 있음
> - 프로세스가 도착하면 수용 가능한 hole을 할당
> - 운영체제는 다음의 정보를 유지함
>     - 할당 공간 (프로세스가 올라가 있는 곳)
>     - 가용 공간 (프로세스를 올릴 수 있는 곳)

### Dynamic Storage-Allocation Problem

: 가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제

- First-fit
    - size가 n 이상인 것 중 최초로 찾아지는 hole에 할당
    
    ⚠️ Next-fit? 메모리의 시작부터보는 first-fit과 달리 최근에 할당된 곳에서부터 시작
    
- Best-fit
    - size가 n 이상인 가장 작은 hole에 할당
    - hole의 리스트가 크기 순으로 정렬되지 않은 경우 모든 hole의 리스트를 탐색해야 함
    - 많은 수의 작은 hole들이 생성됨
- Worst-fit
    - 가장 큰 hole에 할당
    - 모든 리스트를 탐색해야 함
    - 상대적으로 아주 큰 hole들이 생성됨
- First-fit과 best-fit이 worst-fit보다 속도와 공간 이용률 측면에서 효과적인 것으로 알려짐

> ⚠️ **Compaction**
> 
> - external fragmentation을 해결하는 한 방법
> - 사용 중인 메모리 영역을 한 군데로 몰고 hole은 다른 곳으로 몰아 큰 block을 만드는 것
> - 매우 비용이 많이 듦
> - 최소한의 메모리 이동으로 compaction하는 것은 매우 복잡한 문제
> - 프로세스 주소가 실행 시간에 동적으로 재배치 가능한 경우에만 수행될 수 있음

# 3. Non-contiguous Allocation (불연속 할당)

하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음

## Paging

프로그램을 이루는 logical memory들을 일정한 크기의 page로 나누고 이 page들을 non-contiguous하게, 일부는 backing storage, 일부는 physical memory에 집어 넣음

### Basic Method

- physical memory를 동일한 크기의 frame으로 나눔 (주로 4kb)
- logical memory를 동일한 크기의  page로 나눔 (frame의 크기와 동일)
- 모든 가용 frame들을 관리
- page table을 사용해 logical address를 physical address로 변환
    
    > logical address는 페이지 번호 + 페이지 오프셋으로 구성되어 있음
    페이지 번호부분이 page table을 통해 프레임 번호로 바뀌고 오프셋은 그대로 가져감
    > 
- external fragmentation 발생하지 않음
- internal fragmentation은 발생 가능

### page table

- 이 페이지들이 physical memory의 어디에 들어가 있는지를 넣어 놓은 테이블
- Page table은 main memory에 상주.
    - 이 테이블은 레지스터, 캐시 등에는 넣기에는 너무 큼
- Page-table base register(PTBR)가 page table을 가리킴
- Page-table length register(PTLS)는 테이블의 크기를 보관
- 모든 메모리 접근 연산에는 2번의 메모리 접근이 필요
    - page table에 1번, 실제 data/instruction에 1번
    - 큰 오버헤드
- 속도 향상을 위해 associative register 또는 translation look-aside buffer(TLB)라 불리는 고속의 lookup hardware cache를 사용함

### TLB

- 주소 변환을 위한 캐시
- 페이지 번호와 프레임의 번호를 쌍으로 가지고 있음
- TLB를 통해 주소 변환이 가능한지를 살펴봄
    - hit 시 바로 물리 메모리
    - miss 시 page table을 보고, TLB를 갱신
- TLB 조회 시에는 TLB의 전체 항목을 살펴보아야 함
    - 이 또한 overhead가 크기 때문에, 일반적으로 병렬적으로 search할 수 있는 associative register를 이용해서 구현함.
- TLB는 논리적 주소를 다루고 있으므로 프로세스마다 각자 가져야 함
    - 따라서 문맥 전환이 이루어지면 flush

> Effective Access Time
> 
> - Associative Register Lookup Time = $\epsilon$
> - Memory Cycle Time = 1
> - Hit ratio = $\alpha$
> - Effective Access Time
> 
> $$
> EAT = (1 + \epsilon)\alpha + (2 + \epsilon)(1 - \alpha) \\
> = 2 + \epsilon - \alpha
> $$
> 

### Two-level Page Table

- 현대 컴퓨터는 주소 공간이 매우 큰 프로그램을 지원함
- 32-bit 주소의 경우 4G의 주소 공간이 필요
    - page size가 4K일 경우 1M개의 page table entry가 필요
    - 각 page entry가 4B일 때, 프로세스당 4M의 page table이 필요
    - 그러나 대부분의 프로그램은 4G의 주소 공간 중 지극히 일부만 사용하므로 page table 공간이 심하게 낭비되는 문제가 발생
- Inner table & Outer table로 구성
    - logical address의 구성
        
        
        | P1(1) : outer page table의 index | P2(10): outer page table의 page에서의 변위(displacement) | d(12) |
        | --- | --- | --- |
        - 20-bit의 page number
            - page table이 page로 구성되므로 page number도 다음과 같이 나뉨
                - 10-bit의 page number
                - 10-bit의 page offset
                    - Inner page의 엔트리 수가 1K(4K / 4)개 있으므로 10bit가 필요
        - 12-bit의 page offset
            - physical address의 한 프레임이 4KB이므로 byte 단위로 주소 구분을 하려면 12bit이 필요하므로
    - P1을 보고 페이지를 찾고, 찾은 페이지에서 P2로 가서 frame number를 찾음. 마지막 offset을 보고 physical address에 접근
- 사실 Inner page table이 frame 개수만큼 만들어지는데 시공간적으로 모두 손해 아닌가?
    - 사용하지 않는 경우, 가능한 모든 logical address에 대해서 그 크기만큼의 page table의 엔트리가 만들어져야 함
    - two-level page table은 사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 NULL이 되어 inner table이 만들어지지 않고 결과적으로 이득

### Segmentation

### Paged Segmentation
