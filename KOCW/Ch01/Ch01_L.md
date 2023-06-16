# Intro

- 운영체제(Operating System)란?

컴퓨터 하드웨어 바로 위에 설치되어 사용자 및 다른 모든 소프트웨어와 하드웨어를 연결하는 소프트웨어 계층

커널은 부팅 이후 메모리에 상주함. 넓은 의미로 유틸리티까지 포함하기도함.


---
$ 4가지 요소
1. 하드웨어 - cpu, memory, i/o device
2. Operating System 하드웨어 제어.
3. application program - 프로세서, 컴파일러, 웹 브라우저 ...
4. user -> 사용자의 퍼포먼스 향상.

---

- 목적 

1. 컴퓨터 시스템의 한정된 자원을 최대 성능을 내도록 효율적 관리 (자원 : 프로세서, 기억장치, 입출력 장치)

사용자간 형평성 있는 자원 분배. 

-> 효율성, 형평성

소프트웨어 자원(프로세스, 파일, 메시지)도 관리함.
resource allocator

2. 컴퓨터 시스템을 편리하게 사용할 수 있는 환경을 제공
(유저 관점)


---
$ 정의

 유저의 문제를 더 쉽게 푸는 것이 컴퓨터 시스템의 목적.
하드웨어를 쉽게 사용하게 해주는 application program은 특정한 common operations(i/o device 제어하는)를 필요로 한다.

1. resource를 할당, 제어하는 소프트웨어가 합쳐져서 운영체제가 됨.

2. all time running program -> kernel

kernel/ system programs /application programs

+ 개인컴의 보급으로 운영체제의 구성이 중요해짐.

미들웨어까지 포함하는게 맞다..

kernel (상주하는 거)

middleware framework(어플 개발 용이하게 해줌 )

- 잠깐 미들웨어가 뭐지?

```
미들(Middle)과 소프트웨어(Software)의 합성어로, 운영체제와 해당 운영체제에서 실행되는 응용 프로그램 사이에서 운영체제가 제공하는 서비스 이외에 추가적인 서비스를 제공하는 소프트웨어이다. 혹은 클라이언트와 서버 간의 통신을 담당하는 소프트웨어라고도 한다.

 미들웨어는 표준화된 인터페이스를 제공하며 시스템 간의 데이터 교환에 일관성을 보장한다. 

DB, RPC, MOM, TP-Monitor, ORB, WAS 
```
----
 

- 분류

1. 동시작업 여부

single tasking
    한 번에 하나의 작업만 처리
    MS-DOS 프롬프트. 한 명령의 수행을 끝내기 전에 다른 명령 수행 불가.
    

multi tasking
    동시에 두 개 이상의 작업 처리
    UNIX, MS Windows


2. 사용자의 수

single user
    MS-DOS, MS Windows

multi user
    UNIX, NT server


3. 처리 방식

batch processing
    작업 요청의 일정량 모아서 한꺼번에 처리
    작업이 완전 종료될 때까지 대기
    역사 속 시스템.. 프린터

time sharing
    여러 작업을 수행할 때 컴퓨터 처리 능력을 일정한 시간 단위로 분할.
    batch에 비해 짦은 응답 시간
    Interactive한 방식 (유저가 느끼기에 interactive) 쿨럭/시간 에 비해서 사람의 반응속도 
    Deadline은 없다...
    UNIX

Realtime OS
    정해진 시간 안에 반드시 종료됨이 보장.
    원자로/공장, 미사일, 반도체 장비 ..
    Hard/Soft Realtime system

---

- 유사한 용어

multitasking
하나의 프로그램이 끝나기전에 다른 프로그램을 실행

multiprogramming
메모리에 여러 프로그램이 올라가는것. 메모리 강조.

time sharing
cpu강조

multiprocess

multiprocessor - 하나의 컴퓨터에 CPU가 여러 개 붙어 있음을 의미.

UNIX
DOS(Disk Operating System)
MS Windows


- 운영 체제의 구조.

CPU, Memory, I/O device, Disk

CPU 스케줄링, 메모리 관리, 디스크 파일 관리(헤드가 움직임. 늦게와도 가까우면 먼저. 엘리베이터), 입출력 관리
+ 프로세스관리 .. 등등
