# [10] File Systems

# 1. File Systems

## (1) File and File System

### File

- A named collection of related information
- 일반적으로 비휘발성 보조기억장치에 저장
- 운영체제는 다양한 저장 장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해준다.
- 연산
    - create, read, write, reposition(lseek), delete, open, close, …
    - open? 파일의 메타데이터를 올려 놓는 것

### File Attribute (혹은 metadata)

- 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
    - 파일 이름, 유형, 저장된 위치, 파일 사이즈
    - 접근 권한(R/W/X), 시간(C/M/A), 소유자 등

### File System

- 운영체제에서 파일을 관리하는 부분
- 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
- 파일의 저장 방법을 결정
- 파일 보호 등

## (2) Directory and Logical Disk

### Directory

- 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일
- 그 디렉토리에 속한 파일 이름 및 파일 attribute들
- operation
    - search, create, delete a file
    - list a dir., rename a file, traverse the file system

### Partition (= Logical Disk)

- 하나의 물리적 디스크 안에 여러 파티션을 두는 게 일반적
- 여러 물리 디스크를 하나의 파티션으로 구성하기도 함
- 물리적 디스크를 파티션으로 구성한 뒤 각 파티션에 file system을 깔거나 swapping 용도 등으로 사용할 수도 있음

## (3) open()

- 디스크에 있는 메타데이터를 메인 메모리에 올리는 것
- 이를 위해 directory path를 search
    - 루트 디렉토리 “/”를 open하고, dir. 파일 “a” 위치 획득
    - “a” open(meta), read(content), dir. “b” 위치 획득
    - “b” open, read, “c” 위치 획득
    - “c” open
- Directory path의 search에 너무 많은 시간을 소요
    - open을 read/write와 별도로 두는 이유
    - 한 번 open한 파일은 read/write 시 다시 directory search를 할 필요가 없음
- Open File Table
    - 현재 open된 파일들의 메타데이터 보관소 (메모리에 있음)
    - 디스크의 메타데이터 + 몇 가지 정보
        - Open한 프로세스의 수
        - File offset : 파일의 어느 위치에 접근 중인지를 표시 (별도 테이블 필요)
    - system-wide (kernel이 가지고 사용)
- File descriptor(file handle, file control block)
    - Open file table에 대한 위치 정보 (프로세스 별)
    - per-process

## (4) File Protection

- 각 파일에 대해 누구에게 어떤 유형의 접근을 허락할 것인가?
- Access Control 방법
    - Access Control Matrix
        - Access control list : 파일별로 누구에게 어떤 접근 권한이 있는지를 표시
        - Capability : 사용자별로 자신이 접근 권한을 가진 파일 및 해당 권한을 표시
    - Grouping
        - 전체 user를 owner, group, public의 세 그룹으로 구분
        - 각 파일에 대해 세 그룹의 접근 권한을 3비트씩으로 표시
    - Password
        - 파일마다 password를 둘 수도 있음
        - 모든 접근 권한에 대해 **하나의 password**만을 둠
        - 접근 권한별로 password를 두면 암기 및 관리에서 문제가 있을 수 있음

## (5) Mounting

한 파일 시스템의 한 디렉토리에다가 다른 파일 시스템을 올리는 것.

## (6) Access Methods

시스템이 제공하는 파일 정보의 접근 방식

- 순차 접근(sequential access)
    - 읽거나 쓰면 offset은 자동 증가
- 직접 접근(direct access, random access)
    - 파일을 구성하는 레코드에 임의의 순서로 접근할 수 있음

# 2. File System Implementation

## (1) Allocation of File Data in Disk

### Contiguous Allocation

n번에서부터 연속적으로 m개를 할당

- 단점
    - External Fragmentation
    - File grow의 어려움
        - 사용하면서 file이 커질 수도 있는데, 뒤에 공간이 없어서 더 키우지 못할 수도 있다
        - 물론 미리 뒤에 여유 공간을 할당해놓을 수도 있다.
            - 근데 file 생성시 얼마나 큰 hole을 배당할지?
            - grow 가능 vs 낭비 (internal fragmentation)
- 장점
    - I/O가 빠름
        - 한번 seek/rotation으로 많은 바이트를 transfer 가능
        - Real-time file용, 혹은 이미 run 중인 process의 swapping용
            
            (임시로 넣어놨다가 사라질 내용이므로)
            
    - Random access가 가능
        - 중간에 있는 블럭까지 순차적으로 접근할 필요없이 바로 접근 가능

### Linked Allocation

- start, end만 주어져있음.
- start에 가면 다음 블럭이 어디에 있고, 그 다음 블럭은 또 어디에 있고…가 있음
    - 원하는 블럭까지 **순차적**으로만 접근할 수 있음
- 쟝점
    - External fragmentation이 발생하지 않음
- 장점
    - Random access 불가
    - Reliability 문제
        - 한 sector가 고장나 pointer가 유실되면 많은 부분을 잃음
    - Pointer를 위한 공간이 block의 일부가 되어 공간 효율성을 떨어트림
        - 512-bytes/sector, 4-bytes/pointer.
        - 디스크에 저장할 때의 용량 단위는 512-bytes의 배수로 정해져있음(인터페이스)
        - 그런데 4-bytes를 포인터를 위해서 사용해야하기 때문에 한 섹터에 저장할 수 있었을 데이터를 두 섹터에 나눠서 저장해야하는 경우가 발생할 수도 있음
- 변형
    - File-Allocation Table(FAT) 파일 시스템
        - 포인터를 별도의 위치에 보관하여 reliability와 공간효율성 문제 해결

### Indexed Allocation

직접 접근을 가능하게 하기 위해 디렉토리에 파일의 블럭이 아니라 해당 파일의 인덱스 블럭을 저장.

이 인덱스 블럭에는 이 파일의 몇 번째 블럭이 어디에 있는지, 그 위치를 담아놓음

- 장점
    - External fragmentation이 발생하지 않음
    - Random access 가능
- 단점
    - 작은 파일의 경우 공간 낭비가 일어남. 실제로 많은 file들은 작음
    - 너무 큰 파일의 경우 한 블럭에 모든 인덱스를 저장하기에는 부족
        - 해결 1) linked scheme
            - 인덱스 블럭에 파일의 블럭 위치를 쭉 쓰다가 끝에 가서는 다른 인덱스 블럭의 위치를 가리키게 함
        - 해결 2) multi-level index
            - 인덱스 블럭의 각 내용이 다음 레벨의 인덱스 블럭을 가리키게 함

## (2) UNIX File System

하나의 partition은 다음과 같이 구성됨

| Boot block | Super block | Inode list | Data block |
| --- | --- | --- | --- |
| 부팅에 필요한 정보
(bootstrap loader) | 파일 시스템에 관한 총체적 정보(어디가 빈 곳이고 아닌지) | 파일 이름을 제외한 파일의 모든 메타 데이터 | 파일 이름의 실제 내용.
여기의 directory file이 파일이름과 inode index를 가지고 있음 |

## (3) FAT File System

| Boot block | FAT | Root directory | Data block |
| --- | --- | --- | --- |
|  | 파일의 위치 데이터 |  | 파이의 이름을 비롯한 모든 메타데이터들을 디렉토리가 가지고 있음. 파일의 첫 번째 위치가 어디인지도. |

FAT은 메모리에 캐시될 수 있음. 메모리에 캐시된 FAT을 가지고 해당 블럭의 위치를 빠르게 계산한 후 Disk head를 여러 번 움직이지 않고 random access가 가능.

만약 FAT이 캐시되어 있지 않은 경우에는 해당 블럭의 위치를 찾기 위해 Disk head를 여러 번 움직여야 해서 성능 하락이 발생할 수 있다.

## (4) Free-Space Management

### Bit map or bit vector

- 디스크의 크기만한 bit vector를 만듦
    - 0이면 해당 블럭이 free, 1이면 occupied
- Bit map은 부가적인 공간을 필요로 함
- 연속적인 n개의 free block을 찾는 데 효과적

### Linked list

- 모든 free block들을 링크로 연결
    - free-space list head로부터 시작해서 다음 가용 공간을 가리키게 함
- 연속적인 가용공간을 찾는 것은 쉽지 않음
- 공간 낭비가 없음

### Grouping

- Linked list 방법의 변형
- 첫 번째 free block이 n개의 pointer를 가짐
    - n-1 pointer는 free data block을 가리킴
    - 마지막 pointer가 가리키는 block은 또 다시 n pointer를 가짐

### Counting

- 연속적인 빈 블럭을 찾는 데에 유리
- 프로그램들이 종종 여러 개의 연속적인 block을 할당/반납한다는 성질에 착안
- (first free block, # of contiguous free blocks)를 쌍으로 관리
    - 몇 번째 블럭부터 몇 개가 가용 블럭이다

## (5) Directory Implementation

### Linear list

- (file name, file metadata)의 리스트로 관리
- 구현이 간단
- 디렉토리 내에 파일이 있는지를 찾으려면 linear search가 필요 $O(n)$

### Hash table

- Linear list + hashing
- Hash table은 file name을 이 파일의 linear list의 위치로 바꾸어줌
- Search time은 줄어듦 $O(1)$
- Collision이 발생할 수는 있음

### File metadata의 관리

- 디렉토리 내에 직접 보관할 수도 있음
- 디렉토리에 푄터를 두고 다른 곳에 보관할 수도 있음(UNIX, FAT 등)

### Long file name의 지원

- (file name, file metadata) 리스트에서 각 entry는 일반적으로 고정된 크기를 가짐
- file name이 고정된 크기보다 길어지면 list의 마지막 부분에 이름 뒷부분이 위치한 곳의 포인터를 두는 방법을 사용
- 나머지 부분은 동일한 directory file의 일부에 존재

## (6) VFS and NFS

### Virtual File System

서로 다른 다양한 file system에 대해 동일한 시스템 콜 **인터페이스**를 통해 접근할 수 있게 해주는 OS의 layer

### Network File System

- 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음
- NFS는 분산 환경에서의 대표적인 파일 공유 방법

## (7) Page Cache and Buffer Cache

### Page Cache

- **Virtual memory**의 paging system에서 사용하는 page frame을 caching의 관점에서 설명하는 용어
- Memory-Mapped I/O를 쓰는 경우 file의 I/O에서도 page cache 사용
- Page fault가 나는 경우가 아니면 OS는 알지 못함

### Memory-Mapped I/O

- File의 일부를 virtual memory(프로세스의 일부 공간)에 mapping시킴
    - 처음에 mapping 시킬 때에는 system call을 사용함
- 매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 함
- 원래는 파일에 접근할 때에는 open하고 read/write system call을 써야 했음
    - **Memory-Mapped I/O는 마치 메모리에서 쓰는 것처럼 OS의 관여없이 I/O 작업을 할 수 있게 해줌**
    - 대표적으로는 프로세스의 code 영역. 이 영역은 read-only이므로 굳이 swap area로 가는 경우가 없이 memory-mapped i/o로 바로 매핑함.
    - 데이터 영역도 물론 mmap이 가능함
        - 이 경우도 swap out될 때에는 swap area가 아니라 file system으로
- 주의점
    - 다른 프로세스도 같은 공간에 매핑해버리는 경우 일관성이 깨질 수 있음
        - read/write system call의 경우에는 OS가 복사 및 중재하기 때문에 그런 걱정이 없음.

### Buffer Cache

- **파일 시스템을 통한 I/O 연산**은 메모리의 특정 영역인 buffer cache 사용
- File 사용의 locality 활용
    - 한번 읽어온 block에 대해 또 요청이 오면 cache에서 바로 전달함
- 모든 프로세스가 공용으로 사용 (커널 소유)
- Replacement algorithm 필요
- File에 접근할 때에는 system call을 해야하고 운영권이 OS에 넘어가서 상당한 정보를 알 수 있음.

### Unified Buffer Cache

- 최근 OS에서는 buffer cache가 page cache에 통합됨.
- 합쳐졌다고 해서 관리 방식이 다른 것은 아니다
