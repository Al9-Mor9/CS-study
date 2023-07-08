# File System


Memory - 주소를 통해 접근
File - 이름을 통해 접근

- File
일반적으로 비휘발성의 보조기억장치에 저장.
운영체제는 다양한 저장 장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해줌. 
    HD1, HD2 등 Device special
operation
    create, read, write, reposition(l seek) 파일 포인터 및 시작 부분 위치 수정?,
     delete, open, close (read write하려면 열고 닫아야함, 파일의 메타데이터를 메모리에 올려놓는 역할)
     etc...


- File attribute
파일 관리를 위한 정보가 있다.
    파일이름, 유형, 저장 위치, 파일 사이즈
    권한, 시간, 소유자

- File System.

    운영체제에서 파일을 관리하는 부분
    파일 및 메타데이터, 디렉토리 정보 등을 관리
    파일의 저장 방법 결정

### Directory

안에 들어있느 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일
그 디렉토리에 속한 파일 이름 및 파일 attribute 등

메타데이터를 디렉토리에 전부 두지는 않는듯

Operation
    file search, create file, delete file
    rename file, traverse file system 전체 탐색?

- Partitioin(=Logical Disk)

    하나의 물리적 디스크 안에 여러 논리적 파티션을 둠.
    여러 물리적인 디스크를 하나의 파티션으로 구성하기도함

    각 파티션을 file system을 두고, swapping 하는 용도로 사용가능.

#### open

내용을 가리키는 포인터, 저장위치 등 메타데이터를 올림

open("a/b/c")

디스크로부터 파일 c의 메타데이터를 메모리로 가지고 옴. 
루트 디렉토리 위치는 알고있음. 거기서부터 찾아감.

사용자 메모리 영역에서 file을 open하는 system call 함
-> cpu가 운영체제로 넘어감. 
1. root의 metadata를 올려둠
2. root의 content는 디렉토리 이하 파일들의 메타데이터를 가지고 있음.
3. 그걸보고~ a의 메타데이터 올림.
4. 그걸보고~ b의 메타데이터 올림.
5. 그걸보고~ c의 메타데이터를 올리고, 그 올린 위치를 
프로세스 내 pcb file descriptor table(배열)의 인덱스가 반환됨... 뭔소리고

    file descriptor가 메모리에 올려진 메타데이터 위치를 알고있음.

fd = open("a/b/c")
read(fd...) <- 여기도 시스템 콜임. 또 cpu가 운영체제에 넘어감.

운영체제가 다시 읽어서 커널메모리 영역에 먼저 넣고, 
프로그램한테 사용자 메모리 영역에 카피해서 전달해줌.

만약 다른 프로그램이 동일 파일의 동일 위치를 read한다.. 그럼 커널 메모리 영역에서 가져감
buffer cache

vm에서 페이징 기법 - 메모리에 올라와있는 페이지에 대해선 운영체제가 끼어들지 못하고,,,
주소변환 하드웨어가 함. 페이지 fault가 나면 그때 운영체제가 swap area에서 가져옴. 

파일시스템은 (buffer cache)메모리에 올라와 있어도 제어권이 cpu에게 한 번 넘어감
그래서 file system에선 LRU를 쓸 수 있다..

per-process file descriptor table
system-wide open file table -> 올라와있는건 구분안하고 관리.
    프로세스마다 해당 파일을 읽고있는 위치가 다를 수 있으니, 메타데이터가 추가된다.
    따로 관리할 부분도 table로 가지고 있게됨.

### File Protection

접근 권한. (read/write/execution)
접근 권한이 누구에게 있는가, 가능한 연산이 무엇이 있는가.

- Access Control

1. Access control Matrix
    각 사용자가 각 파일에 대해 어떤 권한이 있는지 2차원 행렬로 관리
    -> sparcy matrix 희소행렬이 됨.

    linked-list형태로 저장 가능.
    Access control list: 파일 별로 누구에게 어떤 접근 권한이 있는지 표시.
    Capability : 사용자를 기준으로 파일들을 연결해 놓는 linked list.
    그렇게 해도 overhead가 크다

2. Grouping

    전체 user를 owner, group, public의 세 그룹으로 구분
    각 파일에 대해 세 그룹의 권한을 3비트씩으로 표시

    UNIX
    owner group other
    rwx r-- r--
    9비트면 떡을친다.

3. Password

파일마다 password를 두는 방법(디렉토리 파일에 두는 방법도 가능)
모든 접근 권한에 대해 하나의 password: all-or-nothing
접근 권한별 password: 암기, 관리 문제



#### File System의 Mounting

파티셔닝. disk1,2,3..

root file system.

다른 파티션에 있는 file system에 접근해야한다면? -> mount를 해둠



#### Access Methods

파일 접근 방식

- 순차 접근(sequential access)
    카세트 테이프, 읽거나 쓰면 offset은 자동 증가.

- 직접 접근(direct access, random access)
    LP 레코드 판과 같이 접근
    파일을 구성하는 레코드를 임의의 순서로 접근 가능.



# File System Implementation


## Allocation of File Data in Disk

매체에 따라 접근 방식을 설명했음.
관리 방식에 따라서도 접근 방식이 갈리기도함.

- Contiguous Allocation

동일한 크기의 저장단위. 논리적인 블럭
실제 디스크. 섹터 단위
임의 파일을 임의의 블럭으로 관리

하나의 파일이 디스크 상에 연속으로 저장됨. 

con
    파일들의 길이가 다르므로 external fragmentation 생성 가능.
    file grow 어려움. (수정)
        file 생성시 커질껄 대비해서 블럭을 얼마나 더 배당할 것인가.
        -> internal fragmentation

pros
    접근 시간이 주로 성능에 영향
    Fast I/O
        한번의 seek/rotation으로 많은 바이트 transfer
        Realtime file용, run 중이던 process의 swapping 용 (공간효율성보단 시간효율성이 중요하다.)
    Direct access(=random access가능.)
        시작과 길이를 알고있음. 앞에서 부터 k번째 블럭을 읽으면 됨.


- Linked Allocation
빈 위치면 아무데나 배치함. 
시작 끝은 directory가 알고있음.
그 블럭 내에 다음 블럭에 대한 포인터가 있다.
순차 접근

pros
    외부 조각이 없음.

con   
    직접 접근 불가.
    seek를 해서 디스크 헤더가 움직임 오래걸림
    Realiability 문제
        한 sector가 고장나 pointer가 유실되면 많은 부분이 미아가됨.
    Pointer를 위한 공간이 block의 일부가 되어 공간 효율성을 떨어뜨림.
        512 bytes/sector -> 4 bytes/pointer
        디스크와 컴퓨터가 데이터를 주고받는 인터페이스가 512의 배수임.
        근데 저장공간도 512 그래서 4bytes 갉아 먹히면 2칸에 저장되기도함.

    
    -> File-allocation table(FAT) 파일 시스템
        포인터를 별도의 위치에 공간. 두 문제 해결.



- Indexed Allocation

파일이 어디 저장되어있는지에 대한 위치정보를 특정 블럭에다가 저장.
직접 접근이 가능함.

pros
    외부조각이 생기지않음
    직접접근 가능

cons 
    small file의 경우 공간 낭비(실제로 많은 file이 작음) - 인덱스 블럭
    Too large file의 경우 하나의 block에 index 저장하기 부족
        -> linked scheme 마지막에 다음 인덱스 블럭의 위치 저장.
        -> multi-level index


#### UNIX 파일 시스템의 구조

가장 기본적인 거임!

논리적 디스크. 파티션에 파일시스템을 저장.

    Boot block - 유닉스 말고도 모든 파일시스템은 맨 앞에 둠.
        bootstrap loader 부팅에 필요한 정보
    Super block
        파일 시스템에 관한 총체정보. 어디가 비어있고,...
    Inode list
        파일의 메타데이터를 따로 저장. index node
        파일하나당 inode가 할당됨.

            direct block
            indirect block - 큰 파일은 인덱스를 나눠둠.
                single
                double
                triple
    Data block
        directory 파일은 
        파일 이름과 inode 번호를 가짐.




#### FAT 파일 시스템


    Boot block
    FAT
        메타데이터 중 파일의 위치정보만 가지고있음.
        디스크내 블럭의 갯수만큼이 배열을 가짐.
        다음 블럭을 저장해둠. 배열의 217에 618이 적혀져있음. 다음 블럭은 배열의 618을 봄.
        끝은 EOF를 숫자로 정해두면 됨.

        FAT은 어짜피 메모리에 올라가있고, 작은 테이블이기 때문에 직접접근 수준임. 
        곧바로 파악가능.

        대신 중요함. 그래서 디스크에 두 카피 이상은 가지고 있다.


    Root directory
    Data block
        directory가 이름 등등 번호까지 다 가지고있음.


#### Free-Space Management

비어있는 블럭 관리

- Bit map or bit Vector

    각각의 블럭별 번호가 있다.
    사용중인지 비어있는지 표기
    
    Bit map은 부가 공간을 필요로함
    연속적인 n개의 free block을 찾는데 효과적임.


- Linked list

    모든 free block을 링크로 연결
    연속적인 가용공간을 찾는 것은 쉽지 않다.
    공간의 낭비가 없다. 어짜피 비어있어서 그 안에 저장해놔도됨.


- Grouping

    첫 번째 free block이 n개읜 pointer를 가짐
    n-1 pointer는 free data block을 가리킴
    마지막 pointer가 가리키는 block은 또 다시 n pointer를 가짐.
    역시 연속 빈블럭 찾기는 어려움.


- Counting

    프로그램들이 종종 여러 개의 연속적인 block을 할당하고 반납한다는 성질.
    first free block, # of contiguous free blocks를 유지



### Driectory Implementation


디렉토리 구현
디렉토리 이하의 메타데이터를 저장

- Linear list
    파일의 이름 파일의 메타데이터 리스트를 순차저장
    메타데이터 크기 고정.
    구현이 간단
    디렉토리 내에 파일이 있는지 찾기 위해서는 linear, search필요
    (time-consuming)


- Hash Table

    linear list + hashing
    Hash table은 file name을 이 파일의 linear list의 위치로 바꾸어줌

    파일 해쉬함수 결과값에 대한 엔트리에 해당 파일의 메타데이터 저장.

    search time을 없앰
    Collision 발생 가능(해쉬에 따른..)



- 파일의 메타데이터 보관 위치
    디렉토리 직접 보관
    디렉토리에는 포인터를 두고 다른 곳에 보관
        inode, FAT
    
- Long file naem의 지원
    파일 이름 파일 메타데이터 리스트에서 각 entry는 일반적으로 고정 크기

    file 이름이 고정 크기의 entry길이보다 길어질 경우 entry이ㅡ 마지막 부분에 이름의 뒷부분이 위치한 곳의 포인터를 두는 방법
    이름의 나머지 부분은 동일한 directory 파일의 일부에 존재.


### VFS, NFS

- Virtual File System

사용자는 system call을 해야함. 파일 시스템 별로 다른 시스템 콜을 해야한다면 혼란 스러움
인터페이스가 필요함

서로 다른 다양한 파일 시스템에 대해 동일한 시스템 콜 인터페이스(api)를 통해 접근할 수 있게 해주는 OS의 Layer

- Network File System

client - NFS client

분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음
NFS는 분산 환경에서의 대표적인 파일 공유 방법


### Page Cache and Buffer Cache

- Page Cache
    가장 메모리의 페이징 시스템에서 사용하는 페이지 프레임을 캐슁의 관점에서 설명하는 용어
    Memory-mapped I/O를 쓰는 경우 file의 I/O 에서도 페이지 캐쉬 사용

- Memory-mapped I/O
    파일의 일부를 가상 메모리에 매핑시킴
    매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 함.


- Buffer Cache

    파일 시스템을 통한 I/O 연산은 메모리으 특정 영역인 BUffer cache 사용.
    File 사용의 locality 활용
        한번 읽어온 block에 대한 후속 요청시 buffer cache에서 즉시 전달
    모든 프로세스가 공용으로 사용
    Replacement algorithm 필요(LRU, LFU)

- Unified BUffer cache
    최근 OS에서 버퍼캐쉬와 페이지 캐쉬가 통합됨.
    버퍼캐쉬도 페이지 단위로 관리를 한다.



파일 접근하려면 open하고 read, write 하는 시스템콜 BC
vs
파일의 일부분을 메모리에다가 매핑해놓고 작업 MMIO


### 

페이지 캐쉬 - 프로세스의 주소공간을 구성하는 페이지가 
swap area에 내려가있는가 또는 page cache에 올라가 있는가
4kb 단위

버퍼 캐쉬는 파일 데이터가 파일시스템에 저장되어있는가 버퍼캐쉬에 올라와있는가
섹터 단위 512byte. 단위
disk i/o 단위라고함

최근에 통합되면서 버퍼 캐쉬 단위도 4k 페이지 단위를 씀

메모리 맵드 아이오


### UBC를 이용하지 않는 하는 File IO

이용안하면 둘 다 buffer cache에 복제해야함.

이용하면 경로가 단순해짐.
buffer cache = page cache가 됨.

### 프로그램의 실행

코드는 물리 메모리에서 swap area에 내려가지 않음.
파일시스템내 파일이 프로세스 주소공간에 매핑되있기때문..

실행파일, 데이터파일 파일 시스템에 저장되어있음.
mmap

mmap은 swap area 로 안쫓겨나게하는대신 file system에 쓰게 됨.

뭐라는건지..




















