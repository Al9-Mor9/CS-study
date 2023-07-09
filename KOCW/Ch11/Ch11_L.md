# Ch 11 Disk Management and Scheduling

(책은 ch12임)

### Disk Structure


요청 할 때 block으로 봄.

- logical block

    디스크 외부에서보는 논리적 단위. 정보 저장 공간
    주소를 가진 1차원의 배열처럼 취급
    정보를 전송하는 최소 단위

- Sector
    디스크를 관리하는 최소단위
    logical block이 물리적인 디스크에 매핑된 위치
    Sector 0은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터(약속. 부팅과 관련된 정보)


### Disk Management

- Physical formatting(low-level formatting)

    디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
    각 섹터는 header + 실제 data(보통 512bytes) + trailer로 구성
    header와 trailer는 sector number, ECC(Error-correcting code) 등의 정보가 저장.
    controller가 직접 접근 및 운영. 


- partitioning


- Logical formatting

- Booting



### Disk Scheduling


- Access time
    seek time
    rotational latency
    transfer time

- Disk bandwidth

- Disk Scheduling

### Disk Scheduling Algorithm

Access time을 고려한 스케줄링

- FCFS 그냥 순서대로
- SSTF 지금 가장 가까운놈 순 -> Starvation 문제 
- SCAN 엘베 스케줄링이라고도함. disk arm이 한쪽 끝에서 다른 쪽 끝으로 이동하며 가는 길목이 있으면 요청 처리.
        실린더 위치에 따라 대기 시간이 다름. 끝에 있는놈은 오래기다림.
- C-SCAN 헤드가 한쪽 끝에서 다른쪽 끝으로 이동. 이건 단방향으로만 쭉. 이동거리가 더 생기긴하지만 균일 대기 시간 제공함.
- N-SCAN 지나가는 도중에 오는 놈들은 다음번에 하겠다.
- LOOK 요청이없으면 방향을 바꿈. 끝까지 안돔.
- C-LOCK 이것도 단방향.

file 할당 방법에 따라 시스크 요청이 영향 받음.

디스크 스케줄링 알고리즘은 교체가능하게 OS와 별도의 모듈화 하는게 좋다.


### Swap-Space Management

Disk를 사용하는 두 가지 이유

    memory의 volatile한 특성 - > file system
    프로그램 실행을 위한 memory 공간 부족 -> swap space (swap area)
    [멀티프로그래밍 디그리가 높아짐]

Swap-space

    virtual memory system에서는 디스크를 memory의 연장 공간으로 사용
    파일 시스템 내부에 둘 수도 있으나 별도 partition 사용이 일반적
        공간효율성보단 속도 효율성이 우선
        일반 파일보다 훨씬 짦은 시간만 존재 자주 참조
        따라서 block의 크기 및 저장 방식이 일반 파일시스템과 다름 
    

RAID

Redundant Array of Independent Disks
여러 개의 디스크를 묶어서 사용

디스크 처리 속도 향상
    분산 저장된 block 병렬적 읽어옴. Interleaving striping

신뢰성 향상
    동일 정보를 여러 디스크에 중복 저장
    fault tolerance?

    mirroring, shadowing
    단순한 중복 저장이 아니라 일부 디스크에 parity를 저장하여 공간의 효율성을 높일 수 있다.





























