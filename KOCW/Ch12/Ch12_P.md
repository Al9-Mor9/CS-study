# [11] Disk Management and Scheduling

## 1. Disk Structure

### Logical Block

- 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간
- 주소를 가진 1차원 배열처럼 취급
- 정보를 전송하는 최소 단위

### Sector

- Logical block이 물리적인 디스크에 매핑된 위치
- Sector 0은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터(논리적 블럭에서도 0번째 블럭에 해당)
    - 여기는 어떤 파일 시스템에서든 부팅 관련한 것들이 들어있는 섹터임

## 2. Disk Management

### Physical Formatting (Low-level formatting)

- 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
- 요즘은 디스크가 나올 때 이미 physical formatting이 다 돼서 나옴
- 각 섹터는 header + 실제 데이터(보통 512 bytes) + trailer로 구성
- header & trailer는 sector number, ECC(Error-Correcting Code) 등의 정보가 저장되고, controller가 직접 접근 및 운영.
    - ECC : 위 데이터를 아주 축약해서 넣어놓은 결과라고 생각하면 됨. 배드 섹터가 발생했는지 확인할 때 사용. 모든 에러를 다 검출하지는 못하지만 상당 부분은 잡아낼 수 있음.

### Partitioning

- 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
- OS는 이를 독립적인 디스크로 취급(logical disk)
- 각자의 디스크는 파일 시스템, 또는 스와핑의 용도 등으로 사용될 수 있을 것.

### Logical Formatting

- 파일 시스템을 만드는 것
- FAT, inode, free space 등의 구조를 포함

### Booting

- ROM에 있는 “small bootstrap loader”의 실행
- 섹터 0을 load해서 실행
- sector 0은 “full Bootstrap loader program”
- OS를 디스크에서 load해 실행

## 3. Disk Scheduling

### Access time의 구성

- Seek time : 헤드를 해당 실린더로 움직이는 데 걸리는 시간
- Rotational latency : 헤드가 원하는 섹터에 도달하기까지 걸리는 회전 지연시간
- Transfer time : 실제데이터의 전송 시간

### Disk Bandwidth

- 단위 시간 당 전송된 바이트의 수

### Disk Scheduling

- Seek time을 최소화하는 것이 목표
- Seek time ≈ seed distance

## 4. Disk Scheduling Algorithm

큐에 들어온 요청을 어떻게 처리할 것인가에 대한 알고리즘

### FCFS (First Come First Service)

들어온 순서대로 이동

### SSTF (Shortest Seek Time First)

현재 위치에서 가장 가까운 곳을 먼저 처리. Starvation 문제가 발생할 수 있음

### SCAN

disk arm이 한 쪽 끝에서 다른 쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리. 다른 쪽 끝에 도착하면 역방향으로 다시 이동. 실린더의 위치에 따라 대기 시간이 달라질 수 있음. (가운데 쪽은 반만 이동하면 되는데 가장자리 쪽은 다 이동해야 함)

### C-SCAN

헤드가 한 쪽 끝에서 다른 쪽 끝으로 이동하면서 가는 길목에 있는 모든 요청을 처리. 단 끝에 도달하면 요청을 처리하지 않고 곧바로 출발점으로 다시 이동. SCAN보다는 균일한 대기 시간을 제공함. 이동 거리는 좀더 길어질 수 있음.

### N-SCAN

SCAN의 변형 알고리즘. 일단 arm이 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 job은 되돌아올 때 서비스

### LOOK and C-LOOK

헤드가 끝에서 끝으로 이동하지 않고, 진행 중이다가 그 방향에 더 이상 기다리는 요청이 없으면 헤드의 이동방향을 즉시 반대로 이동함. 

### Disk-Scheduling Algorithm의 결정

- SCAN, C-SCAN 및 그 응용 알고리즘은 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져 있음
- File 할당 방법에 따라 디스크 요청이 영향을 받음
    - 요청을 하나하나 보는 게 아니라 묶어서 (merge) 처리하도록 만들기도 함
- 디스크 스케줄링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체될 수 있도록 OS와 별도의 모듈로 작성되는 것이 바람직함.

## 4. Swap-Space Management

### 디스크를 사용하는 두 이유

- 메모리의 휘발성 → file system은 휘발하면 안 됨
- 프로그램 실행을 위한 메모리 공간 부족 → swap space (swap area) 용도로 사용

### Swap-space

- 가상 메모리 시스템에서는 디스크를 메모리의 연장 공간으로 사용
- 파일 시스템 내부에 둘 수도 있으나 별도 파티션 사용이 일반적
    - 공간효율성보다는 속도 효율성이 우선
    - 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨
    - 그렇기 때문에 block크기 및 저장 방식이 일반 파일시스템과는 다름

### RAID (Redundant Array of Independent Disks)

- 여러 개의 디스크를 묶어서 사용
- 디스크 처리 속도 향상
    - 여러 디스크에 block의 내용을 분산 저장, 병렬적으로 읽어 옴(Interleaving, striping)
- 신뢰성 향상
    - 동일 정보를 여러 디스크에 중복 저장
    - 한 디스크가 고장나면 다른 디스크에서 읽어 옴 (Mirroring, shadowing)
    - 단순히 중복 저장이 아니라, 일부 디스크에 parity를 저장해 공간 효율성을 높일 수도 있음
        - parity : 중복을 최대한 줄이고 오류 검출 정도만 할 수 있을 정도로만 중복하게
