# Memory Management

주소를 통해 접근하는 매체

주소는 두 가지고 구분됨.

    - Logical address(=virtual address)
        프로세스마다 독립적으로 가지는 주소 공간
        각 프로세스마다 0번지부터 시작
        cpu가 보는 주소는 logical address
        
    - Physical address
        메모리에 실제 올라가는 위치
        물리적 메모리는 하나. 
        운영체제 커널. + 여러 프로그램이 섞임. 

        프로그램마다 0번지부터 시작하는 독자적인 주소가 있지만 실행되려면 물리적인 메모리 어딘가로 올라가게되고, 주소가 바뀌게됨.
        -> 주소 바인딩(프로그램이 물리적인 메모리 어디로 올라갈지)

        Symbolic Address -> Logical Address -> Physical address
        프로그래머는 심볼 주소를 사용함.

        주소바인딩 시점 3가지


## Address Binding

- Complie time binding 
컴파일 시점에 물리 주소가 정해짐. 
물리주소가 fix 되어있기 때문에 비효율적이다. 
논리적 주소 = 물리적 주소 

컴파일러는 absolute code 생성 
물리 주소를 바꾸고 싶으면 컴파일을 다시해야됨. 

- Load time binding 
프로그램이 시작되서 메모리에 올라갈 떄, 물리적 메모리 주소가 결정됨. 
컴파일 때는 논리적 주소만 결정됨. 실행시킬 때, 논리적 주소를 물리적 주소 몇 부터 올린다. 
 
relocatable code 
비어있는 위치는 실행시에 어디든지 올라갈 수 있다. 

위 두 방식은 실행시에 물리적 주소 변화가 없다. 

- Execution time binding(=Run time binding)[현재 컴퓨터 시스템이 채택] 
실행시 주소 결정되는건 동일함. 주소가 실행시 바뀔 수도 있음 
일단 바인딩이 되었다가 실행되는 도중에 물리적 주소가 바뀔 수도 있다.  

메모리에서 쫓겨날 수도 있고, 다시 올릴 때는 비어있는 곳에 올리고.. 등등 

실행중 주소가 바뀜. cpu가 메모리 주소를 요청할 때 마다 binding을 체크해봐야함. 
이 내용이 물리적 메모리 어디 올라가 있는지 주소변환을 그 때 그 때 해야한다? 

CPU가 주소를 참조할 때마다 binding을 점검(address mapping table) 
하드웨어적인 지원 필요.(MMU) 



CPU는 논리주소만 바라봄. 20번지에 있는 데이터를 달라. 그 때 주소 변환을 해서 물리적 주소를 찾은 다음 가져감. 
위 두가지는 별거 없음. 컴바는 바로 가져감. 로바는 숫자 더해줌. 

근데 런바는 주소변환용 하드웨어가 필요함. 

## Memory-Management Unit(MMU)

이전에 짤리고, 일부분은 디스크로 쫓겨나고를 설명 지금은 일단. 프로그램이 통채로 메모리에 올라가는 상황.

logical address를 physical address로 매핑해주는 주소변환 하드웨어임.

- MMU scheme Dynamic Relocation
    레지스터 2개로 주소변환해줌. 
    Relocation register(base register)
    Limit register

    프로세스 p0의 가상메모리 범위가 0 ~ 3000이고 cpu가 346을 요구했다. 물리 위치엔 14000부터 올라가 있다고하면.

    base register에 프로그램 시작위치 14000을 저장해두고.  프로그램의 최대 크기를 적어놔서 논리 주소가 범위에 해당하는지 확인하고, base에 논리 주소를 더해줌.(만약 범위 넘어가면 trap 검)

user program. 
    논리 주소만 다룸.


## Some terminologies

- Dynamic Loading (동적 메모리 올리기)

운영체제가 해주는게 아니라 프로그래머가 하게 하는 개념...transparent 한 개념이 아니다?
라이브러리로 함. paging과 원래는 다른건데 섞어 쓰긴함. 
운영체제가 하는 paging도 뭐 다이나믹 로딩이라고 하기도함.

프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 대 메모리에 load 하는 것. 
memory utilization 향상.
가끔식 사용되는 많은 양의 코드의 경우 유용(한정적인 부분만 사용. 예외처리 루틴은 많은데 사용되진 않음.)
운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능. (OS는 라이브러리를 통해 지원 가능.)

쪼금 올리고 쫓겨나고는 paging 시스템에 의해 관리. 이건 운영체제가 관리. 

- Overlays

메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림.
역사적으로 다이나믹 로딩과 다름. 

예전에는 프로그램 하나를 메모리에 올리기 힘듦. 
작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현.
이건 자세하게 복잡하게 코딩함.

- Swaping 

프로세스를 일시적으로 메모리에서 backing store로 쫓아내는 것.
backing store(swap area)
    디스크 - 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간.

swap out/in 
    일반적으로 중기 스케줄러(swapper)에 의해 swap out 시킬 프로세스를 선정
    priority-based CPU scheduling algorithm

    물리 주소를 정해두는 바인딩의 경우 비어있는 메모리 공간을 활용하지 못해 비효율적이다.
    즉, Run time binding으로 비어있는 곳에 swap in시켜야 효율적.

    swap되는 양이 많아서 swap time 대부분은 transfer time임.
    (하드디스크 접근 시간이 디스크 헤드가 이동하는 seak time이 대부분이고 transfer time이 낮은것과 대비)

원래 스와핑은 프로그램이 통채로 쫓겨나는걸 의미.(원칙적으로는 전부)
프로그램 주소공간이 잘게 짤려져서 일부페이지가 메모리로 쫓겨나고, 일부 페이지는 올라오고하는것도
그 페이지가 swap in/out 했다라고 표현하기도함.

- Dynamic Linking

linking을 실행 시간까지 미루는 기법

프로그램을 작성, 컴파일, 그다음 링크해서 실행파일을 만듦. 

링크 - 여러군데 존재하던 컴파일된 파일들을 묶어서 실행파일을 만드는것.

라이브러리들도 링킹이 됨. 
라이브러리가 프로그램의 실행 파일 코드에 포함되는건 static linking이다. 


라이브러리 실행시 연결되는 걸 다이나믹 링킹. 
실행 전 미리 두는게 아니라 실행 파일에는 
라이브러리 루틴의 위치를 찾기 위한 stub이라는 작은 코드를 둔다. 소위 포인터만.

라이브러리가 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어옴.

printf를 사용하는 프로그램 100가지 실행시키면, 같더라도 100copy가 메모리에 올라감.
다이나믹 링킹은 해당 코드를 찾는 코드만 집어넣음. 추가로 메모리에 올릴 필요없이 공유해서 사용하게됨. 
다이나믹 링킹을 하는 라이브러리를 shared library 라고 하기도함.


## Allocation of Physical Memory

메모리는 일반저그올 두 영여긍로 나뉘어 사용

OS 상주 영역

사용자 프로세스 영역(높은 주소 영역)

- 사용자 프로세스 영역의 할당 방법.
    Contiguous allocation
    각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는것.

    Noncontiguous allocation(현대 시스템)
    하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음. 

### Contiguous Allocation

- Fixed partition
    미리 사용자 프로세스 영역을 나눠놓음
    낭비되는 메모리 조각
        내부조각(올리고 나서 남은 영역, 할당되었지만 미사용)
        외부조각(올리려는 프로그램보다 미리 나눠놓은 영역이 작음, 근데 이건 나중에 작은 프로그램 넣으면 내부조각으로 바뀜)
    동시에 메모리에 load되는 프로그램 수, 최대 수행 가능 프로그램 크기 제한. 

internal fragmentation, external ``

- Variable partition
    프로그램이 실행될 때 마다. 차곡차곡 메모리에 올려두는 방식.
    프로그램 크기들이 균일하지 않기 때문에 외부조각이 생김. 

external fragmentation


가변 분할 방식에선 Hole이 생김
Hole
    가용 메모리 공간.
    다양한 hole들이 메모리 여러 곳에 흩어져 있다.
    프로세스가 도착하면 수용가능한 hole을 할당. 
    운영체제는 다음의 정보를 유지
        할당공간, 가용공간(hole)

#### Dynamic Storage-Allocation Problem

가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제

- First fit
    size가 n 이상인 것 중 최초로 찾아지는 hole에 할당

- Best fit
    n 이상인 가장 작은 hole을 찾아서 할당
    전부 탐색.
    많은 수의 아주 작은 hole들이 생성함.

- Worst fit
    가장 큰 hole에 할당
    전부 탐색
    상대적으로 큰 hole들이 생성됨.


#### compaction

exteral fragmentation 문제 해결.

hole들을 모두 모아둔거. 호락호락하지 않아.
전체 프로그램의 바인딩과 관련. 비용이 비쌈

runtime bind으로 메모리 위치가 실행중에도 변경 될 수 있어야 가능. 

최소한의 메모리 이동으로 compaction하는 방법. 어렵다. 



### Noncontiguous Allocation

### Paging

물리적 메모리 공간은 page frame
프로세스는 page로 짤라놓음.
-> hole이 안생김.
mmu에 의해 주소변환이 너무 복잡해짐. address binding이 복잡.

process의 virtual memory를 동일한 사이즈의 page 단위로 나눔. 
일종의 배열 page table도 생김. 

external fragmentation은 안 생김. 
짜투리는 internal fragmentation은 생길 수 있음. 근데 영향력이 크진 않음.

cpu가 논리 주소를 주게되면, 
페이지 번호, 페이지 오프셋. 
페이지 오프셋은 내부에서의 상대적인 위치를 뜻함. 그대로 써도 된담. 

페이지 번호를 통해서 앞부분만 맞추면 됨.

#### Implementation of Page Table
mmu에서는 레지스터 2개만 있으면됨.
기본적으로 페이지는 4kbyte다. 
보통 100만개 필요함. 그럼 레지스터 못씀. 
그리고 프로그램마다 페이지 테이블이 필요함.
결국 메모리에 페이지 테이블을 넣음. 

page table은 main memory에 상주
page-table base resister(PTBR) 은 페이지 테이블을 가리킴
page-tabe length register (PTLR)은 테이블 크기를 보관
메모리 접근 연산에 2번의 메모리 접근이 필요
    associative register 혹은 translation look-aside buffer(TLB)라 불리는 고속의 lookup hardware cache 사용. 
    page table에서 빈번하게 사용되는 일부 entry를 TLB에 caching해서 사용. 
    
    TLB에서 주소변환이 가능한지 검색.
    associatve register는 TLB에서 parrelel하게 서치가 가능하다. 시발

    TLB는 context switch 발생시 flush됨 ㅋㅋ 왜 쓰냐 이거?

    Effective Access time
    뭐 hit ratio가 높으면 이득이라곤 하는데..


#### Two-Level Page Table.

현대의 컴퓨터는 address space가 매우 큰 프로그램 지원.
32 bit address 사용시, 2^32(4G)의 주소 공간
    각 page의 page size가 4k시 1M개의 page table 필요
    각 page entry가 4b시 프로세스당 4M의 page table필요

사용 안되는 논리적 주소가 상당수가 있음. 

2단계 페이징. 

p1, p2 , d
페이지 엔트리를 구분하기 위해 p2 10비트..

특정 페이지 테이블을 안 만들어도 되기 때문에.. 공간 이득.

#### Multilevel Paging and Performance

Address space가 더 커지면 다단계 페이지 테이블 필요
각 단계의 페리지 테이블이 메모리에 존재하므로 logical address의 physical address 변환에 더 많은 메모리 접근 필요

TLB를 통해 메모리 접근 시간을 줄일 수 있음. 

TLB hit ratio가 매우 높으면 effective하게 메모리 접근 할 수 있다. 

TLB에 주소변환이 되고 안되고 차이가 크다. 대신 TLB hit ratio가 높으면 평균값이 낮아짐. 

#### valid(v) / Invalid(i) vit in a page Table

사실 페이지 테이블에는 vi를 표시하는 bit가 있음. 

모든 엔트리가 다 만들어져야한다는게 뭔소리지?
사용하지 않는 페이지거나 backing store에 있는 경우 , 올라와 있지 않은경우 invalid하게 해둠.

#### Memory protection bit

page에 대한 접근 권한(read/write/read-only)
접근 권한보다는 연산에 대한 권한인듯.

#### Inverted Page Table 

- page table이 매우 큰 이유.
    모든 프로세스 별로 그 논리 주소에 대응하는 모든 페이지에 대해 page table entry가 존재
    대응하는 page가 메모리에 있든 아니든 간에 page table에는 entry로 존재

원래는 페이지마다 페이지 테이블 존재.

근데 인버티드는 페이지테이블은 하나 존재. 물리적 메모리 기준으로 페이지 테이블이 동작하는 원리.

물리 주소를 보고 논리 주소를 찾는 건데.. 뭐냐

원래는 바로 찾는데 전부 탐색해야됨.
pid와 page로 page table을 모두 찾음.

페이지 테이블이 하나만 존재해서 공간은 이득. 하지만 시간 복잡도가 늘음.
-> associatable register로 parrelell하게 ..


#### Shared Page

공유가능한 코드는 같은 프레임으로 맵핑 시킬 수 있다.
Re-entrant code, 
read-only로 해야함.
physical address 뿐만 아니라 동일한 logical address space를 가져야함 -> ?


private code and data
각 메모리는 독자적으로 메모리에 올려야함.


### Segmetation

프로그램 주소 공간을 같은 크기로 자르는게 아니라 의미로 자름.
코드, 데이터, 스택 처럼. 더 잘게 자를 수 도 있음. 
함수를 세그먼트로 자르거나.

세그먼트 단위로 주소변환. 

논리 주소는 segment-number, offset으로 구성

- Segment table 

base는 시작위치. 그리고 offset d를 더해주면 구할 수 있다. 
limit는 segment의 길이를 측정. 

stbr - segment-table base register
 물리적 메모리에서의 segment의 위치
stlr - segment-table length register
 프로그램이 사용하는 segment의 수

table로 인한 메모리 낭비는 segmentation 이 paging보다 낫다.
segmentation은 의미 단위로 자르기때문에 그렇게 많지 않음. 



#### Segmentation with paging


segment-table entry가 segment의 base address를 가지고 있는 것이 아니라 segment를 구성하는 page table의 base address를 가지고 있음.

















































