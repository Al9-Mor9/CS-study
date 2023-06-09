# [Ch3] 운영체제

운영체제(Operating System) : 사용자가 컴퓨터를 쉽게 다루게 해주는 **인터페이스**

(c.f. 펌웨어 : 운영체제와 유사하지만 소프트웨어를 추가로 설치할 수는 없는 것)

---

## 3.1 운영체제와 컴퓨터

### 3.1.1 운영체제의 역할과 구조

- 운영체제의 역할
    1. CPU 스케줄링 및 프로세스 관리 : CPU 소유권을 어떤 프로세스에 할당할지, 프로세스의 생성 및 삭제, 자원 할당 및 반환을 관리
    2. 메모리 관리 : 한정된 메모리를 어떤 프로세스에 얼마나 할당할지 관리
    3. 디스크 파일 간리 : 디스크 파일을 어떤 방법으로 보관할지 관리
    4. I/O 디바이스 관리 : I/O 디바이스와 컴퓨터 간에 데이터를 주고받는 것을 관리
- 운영체제의 구조
    
    ![Untitled](%5BCh3%5D%20%E1%84%8B%E1%85%AE%E1%86%AB%E1%84%8B%E1%85%A7%E1%86%BC%E1%84%8E%E1%85%A6%E1%84%8C%E1%85%A6%20465bac9710e74734af19f27acd19b559/Untitled.png)
    
    - GUI : UI의 한 형태. 단순 명령어 창이 아니라, 아이콘을 마우스로 클릭하는 등의 동작으로 컴퓨터와 상호 작용할 수 있도록 해줌
        - c.f. 그래픽 아닌 명려어로 처리하는 인터페이스
    - 드라이버 : 하드웨어 제어를 위한 소프트웨어
    - 시스템콜
        - 운영체제가 커널에 접근하기 위한 인터페이스. 유저 프로그램이 운영체제의 서비스를 받기 위해 커널 함수를 호출할 때 사용.
        - 유저 프로그램이 I/O 요청으로 트랩을 발동하면 → 올바른 요청인지 확인한 후, 유저 모드가 시스템콜을 통해 커널 모드로 변환되어 실행됨.
        - 유저 모드에서 파일을 읽지 않고 커널 모드에서 파일을 읽고 다시 유저 모드로 돌아가서 이후 유저 프로그램의 로직을 수행.
        - 컴퓨터 자원에 대한 직접 접근을 차단할 수 있고, 프로그램을 다른 프로그램으로부터 보호할 수 있음
        - 하나의 추상화 계층으로서, 낮은 단계의 영역 처리에 대한 부분을 크게 신경 쓰지 않고 프로그램을 구현할 수 있음.
            - 유저 모드 : 유저가 접근할 수 있는 영역을 제한적으로 두며 컴퓨터 자원에 함부로 침범하지 못하는 모드
            - 커널 모드 : 모든 컴퓨터 자원에 접근할 수 있는 모드
            - 커널 : 운영체제의 핵심. 시스템콜 인터페이스를 제공. 보안, 메모리, 프로세스, 파일 시스템, I/O디바이스 등등 운영체제의 중추적 역할

### 3.1.2 컴퓨터의 요소

컴퓨터는 CPU, DMA 컨트롤러, 메모리, 타이머, 디바이스 컨트롤러 등으로 이루어져 있다.

- CPU(Central Processing Unit)
    - 산술논리장치(ALU), 제어장치(CU), 레지스터로 구성되어 있음.
    - 인터럽트에 의해 메모리에 존재하는 명령어를 해석, 실행.
        - CU : 프로세스 조작을 지시하는 CPU의 한 부품. 입출력 장치 간 통신을 제어하고 명령어를 읽고 해석. 데이터 처리를 위한 순서를 결정
        - 레지스터 : CPU에 있는 임시기억장치. 연산 속도가 메모리보다 수십~수백 배 빠름. CPU는 자체적으로 데이터를 저장할 수 없으므로 레지스터를 거쳐 데이터를 전달함
        - ALU : 산술 연산 및 논리 연산을 계산하는 디지털 회로.
        - 인터럽트 : 어떤 신호가 들어왔을 때 CPU를 잠시 정지시키는 것. I/O 디바이스 인터럽트, 0으로 나누는 산술 연산의 인터럽트 등등이 있음.
            - 인터럽트 발생 시 인터럽트 핸들러 함수들이 모인 인터럽트 벡터에서 해당 함수가 실행됨.
            - 인터럽트 간에는 우선 순위가 있음.
            - 하드웨어 인터럽트 : I/O 디바이스에서 발생하는 인터럽트
            - 소프트웨어 인터럽트 : Trap. 프로세스가 시스템콜을 호출할 때 발동
- DMA 컨트롤러
    - I/O 디바이스가 메모리에 직접 접근할 수 있도록 하는 하드웨어 장치.
    - CPU의 부하를 막아주며 CPU의 일을 부담.
    - 한 작업을 CPU와 DMA 컨트롤러가 동시에 하는 것을 방지
- 메모리
- 타이머
    - 시간이 많이 걸리는 프로그램이 작동할 때 시간에 제한을 걸기 위해 사용
- 디바이스 컨트롤러
    - 컴퓨터와 연결되어 있는 IO 디바이스들의 작은 CPU

## 3.2 메모리

### 3.2.1 메모리 계층

메모리 계층은 레지스터, 캐시, 메모리, 저장 장치로 이루어져있다. 높은 계층으로 올라갈 수록 비싸지고, 용량은 작아지고, 속도는 빨라짐.

- 레지스터 : CPUT 안에 있는 작은 메모리. 휘발성. 빠른 속도. 적은 용량
- 캐시 : L1, L2, (L3) 캐시. 휘발. 빠른 속도 적은 용량.
    - 데이터를 미리 복사해 놓는 임시 저장소.
    - 병목 현상을 줄이기 위한 메모리. 데이터에 접근하는 시간이 오래 걸리는 경우를 해결
    - 캐싱 계층 : 두 계층 사이의 속도 차이를 해결하기 위해 두는 계층.
    - 캐시를 직접 설정해야하는 경우는 자주 사용하는 데이터를 기반으로 설정해야 함
        - 시간 지역성: 최근 사용한 데이터에 재접근
        - 공간 지역성: 최근 접근한 데이터를 이루고 있는 공간이나, 그 가까운 공간에 접근하는 특성
    - 캐시 히트와 캐시 미스
        - 캐시 히트 : 캐시에서 원하는 데이터를 찾는 경우
            - 해당 데이터를 제어장치를 거쳐 가져옴. CPU 내부 버스를 이용해 빠름
        - 캐시 미스 : 캐시에서 해당 데이터를 찾지 못해 주 메모리로 가서 데이터를 찾는 경우
            - 메모리에서 해당 데이터를 가져옴. 시스템 버스를 기반으로 하므로 느림
    - 캐시 매핑
        - 캐시가 히트되기 위해 매핑하는 방법.
            - 직접 매핑 : 메모리와 캐시를 직접 매핑. 빠르지만 충돌 가능성 높음
            - 연관 매핑 : 순서를 일치시키지 않고 관련된 캐시와 메모리를 매핑. 충돌 가능성은 낮지만 느림
            - 집합 연관 매핑 : 직접 매핑 + 연관 매핑. 순서를 일치시키되 집합을 둬서 저장. 블록화.
    - 웹 브라우저의 캐시
        - 쿠키, 로컬 스토리지, 세션 스토리지.
        - 커스텀 정보나 인증 모듈 관련 사항들을 브라우저에 저장하고 추후에 사용
            - 쿠키
                - 만료 기한이 있는 키-값 저장소.
                - 기본적으로는 도메인 단위로.
                - 최대 4KB까지 데이터 저장 가능.
                - 만료 기한 설정 가능 (보통은 서버 측에서 정함)
            - 로컬 스토리지
                - 만료 기한이 없는 키-값 저장소.
                - 10MB까지 저장 가능
                - 브라우저를 닫아도 유지되고, 도메인 단위로 저장 및 생성.
                - HTML5를 지원하지 않는 브라우저에서는 사용 불가능함.
                - 클라이언트에서만 수정 가능
            - 세션 스토리지
                - 만료 기한이 없는 키-값 저장소.
                - 탭 단위로 세션 스토리지를 생성, 탭을 닫을 때 해당 데이터는 삭제됨
                - 5MB까지 저장가능.
                - HTML5를 지원하지 않는 브라우저에서는 사용 불가.
                - 클라이언트에서만 수정 가능
    - DB의 캐싱 계층

- 주기억장치 : RAM. 휘발성. 속도와 용량은 보통
    - RAM은 하드디스크로부터 일정량의 데이터를 복사, 임시 저장하고 필요할 때마다 CPU에 빠르게 전달.
- 보조기억장치 : HDD, SSD. 낮은 속도. 큰 용량

### 3.2.2 메모리 관리

- **가상 메모리**
    - 컴퓨터가 실제로 이용 가능한 메모리 자원을 추상화 해 이를 사용하는 사용자들에게 매우 큰 메모리로 보이게 만드는 것
    - 가상 주소를 MMU로 실제 주소로 변환.
    - 가상 주소와 실제 주소는 프로세스의 주소 정보가 들어 있는 페이지 테이블로 관리됨
        - TLB : 메모리와 CPU 사이의 주소 변환을 위한 캐시. 페이지 테이블의 리스트를 보관하고 CPU가 페이지 테이블까지 가지 않도록 해 속도를 향상
    - 페이지 폴트
        - 프로세스의 주소 공간(메모리)에는 있으나 RAM에는 없는 데이터에 접근할 때 페이지 폴트가 발생.
        - 이때 운영체제는 스와핑 등으로 페이징 폴트가 일어나지 않은 것처럼 프로그램을 작동 시킴
            - 스와핑 : 당장 사용하지 않는 영역을 하드디스크로 옮겨 필요할 때 다시 RAM으로 불러와 올리고, 사용하지 않으면 다시 하드 디스크로 내려 RAM을 관리
                - 1. 물리 메모리 확인. 페이지가 없으면 트랩 발생
                - 2. CPU 동작 멈춤
                - 3. 페이지 테이블을 확인, 가상 메모리에 페이지가 있는지 확인. 없으면 프로세스를 중단하고 물리 메모리에 빈 프레임 찾음. 없으면 스와핑
                - 4. 빈 프레임에 페이지를 로드, 테이블 갱신
                - 5. CPU 재시작
- **스레싱**
    - 메모리의 페이지 폴트율이 높은 것 → 성능 저하
    - 메모리에 많은 프로세스가 동시에 올라가면 스와핑이 많이 발생, CPU 이용률이 낮아짐.
        
        → 운영체제는 CPU 이용률을 높이기 위해 더 많은 프로세스를 올림
        
        → 스레싱
        
    - 메모리를 늘리거나 HDD → SSD로 바꾸기
    - 작업 세트 : 프로세스의 지역성을 통해 결정된 페이지 집합을 만들어 미리 메모리에 로드.
        - 미리 메모리에 올리면 탐색 비용 및 스와핑을 줄일 수 있음
    - PFF
        - 페이지 폴트의 빈도를 조절하는 방법. 상-하한선을 만들어 상한선에 도달 시 페이지를 늘리고 하한선 도달 시 페이지를 줄임.
- ******************************메모리 할당******************************
    - 연속 할당
        - 고정 분할 : 메모리를 미리 나누어 관리. 내부 단편화 발생
        - 가변 분할 : 매 시점 프로그램의 크기에 맞게 동적으로 메모리를 나눠 사용
            - 외부 단편화 발생 가능
            - 최초 적합 : 위나 아래서 시작해서 찾으면 바로 할당
            - 최적 적합 : 프로세스 크기 이상의 공간 중 가장 작은 공간부터 할당
            - 최악 적합 : 프로세스 크기와 가장 많이 차이나는 곳에 할당
    - 불연속 할당
        - 메모리를 동일한 크기의 페이지로 나누고 프로그램마다 페이지 테이블을 두어 메모리에 프로그램을 할당.
            - 페이징 : 동일한 크기의 페이지로 나워 메모리의 서로 다른 위치에 프로세스를 할당. 홀의 크기가 균일하지 않은 문제가 없어지지만 주소 변환이 복잡해짐
            - 세그멘테이션 : 페이지 단위가 아니라 세그먼트로 나누는 방식. 코드, 데이터, 함수 등을 기준으로 프로세스를 나눔. 공유 및 보안 측면에서 좋지만 홀 크기가 균일하지 않은 문제가 발생.
            - 페이지드 세그멘테이션 : 공유/보안은 세그먼트로, 물리적 메모리는 페이지로
- 내부 단편화 : 프로그램을 넣을 수 없을 공간이 안 남
- 외부 단편화 : 전체 공간 합으로는 넣을 수 있는데 넣을 수 없는 경우

### 페이지 교체 알고리즘

스와핑을 줄이기 위한 알고리즘들

- 오프라인 알고리즘 : 먼 미래에 참조될 페이지와 현재 할당하는 페이지를 바꾸는 알고리즘
- FIFO ; 가장 먼저 온 페이지를 교체 영역에 가장 먼저 놓음
- LRU : 참조가 가장 오래된 페이지를 바꿈.
- NRU : 최근에 사용되지 않은 페이지를 바꿈
- LFU : 가장 참조 횟수가 적은 페이지를 교체

## 3.3 프로세스와 스레드

프로세스 : 컴퓨터에서 **실행되고 있는** 프로그램. 프로세스의 인스턴스

스레드 : 프로세스 내 작업의 흐름

### 3.3.1 프로세스와 컴파일 과정

프로그램은 컴파일 과정 후 기계어로 번역되어 실행될 수 있는 파일이 됨.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/37ea90fa-ecc3-4f2d-b0e5-89792e57908b/Untitled.png)

**전처리** : 소스 코드의 주석을 제거하고 헤더 파일을 병합해 매크로를 치환

**컴파일러** : 오류 처리, 코드 최적화 작업을 하며 어셈블리어로 변환합니다.

**어셈블러** : 어셈블리어 → object code로 변환. (.o 파일)

**링커** : 라이브러리 함수 또는 다른 파일들과 목적 코드를 결합해 실행 파일을 만듦. (.exe, .out)

- 정적 라이브러리 : 프로그램 빌드 시 라이브러리가 제공하는 모든 코드를 실행 파일에 넣음
    - 시스템 환경 등 외부 의존도가 낮고 코드 중복이 일어남. 메모리 효율성 떨어짐
- 동적 라이브러리 : 프로그램 실행 시 필요할 때만 DLL이라는 함수 정보를 통해 참조하는 방식.
    - 메모리 효율성에서의 장점. 외부 의존도가 높아짐.

### 3.3.2 프로세스의 상태

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/355a3173-8cc7-47f7-ba5f-7c1d9af970a6/Untitled.png)

- **생성 상태**(create)
    - 프로세스가 생성된 상태. fork() 또는 exec() 함수를 통해 생성.
    - `fork()` : 부모 프로세스의 주소 공간 복사해 자식 프로세스를 생성함.
        - fork는 한 번 호출되지만 두 번 return 함. (자식 프로세스와 부모 프로세스에 각각)
        - 부모와 자식 프로세스는 동시에 실행되는 별개의 프로세스
        - 부모 프로세스의 비동기 작업 등을 상속하지는 않음
        - 자식 프로세스는 부모 프로세스에서 열린 파일들도 상속
            
            ```c
            #include <sys/types.h>
            #include <unistd.h>
            
            pid_t fork(void);
            //Returns: 0 to child, PID of child to parent, -1 on error
            ```
            
    - `exec()` : 새롭게 프로세스를 생성하는 함수
- **대기 상태**(ready)
    - 메모리 공간이 충분하면 메모리를 할당 받고, 아니면 아닌 상태로 대기.
    - CPU 스케줄러로부터 CPU 소유권이 넘어오기를 기다리는 상태
- **대기 중단 상태** (ready suspended)
    - 메모리 부족으로 일시 중단된 상태
- **실행 상태** (running)
    - CPU 소유권과 메모리를 할당 받고 인스트럭션을 수행 중인 상태
- **********************중단 상태********************** (blocked)
    - 이벤트 발생 후 프로세스가 차단된 상태. I/O 디바이스에 의한 인터럽트로 많이 발생
- ********************************일시 중단 상태******************************** (blocked suspended)
    - 중단된 상태에서 메모리 부족으로 프로세스를 실행하지 못하고 중단된 상태
- **종료 상태** (terminated)
    - 메모리와 CPU 소유권을 모두 놓음
    - 자연스러운 종료도 있지만, 부모 프로세스에서 자식 프로세스를 강제 종료시킬 수도 있음.

### 3.3.3 프로세스의 메모리 구조

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7ed14af2-8f27-4715-8000-efc85386c733/Untitled.png)

스택, 힙을 동적 영역, 데이터, 코드 영역을 정적 영역이라 함

- 스택
    - 지역 변수, 매개 변수, 함수. 컴파일 시 크기 결정. 동적
    - 함수의 재귀적 호출로 크기가 늘어날 수 있는데, 이때 힙 영역과 겹치지 않게 사이를 비워놓음
- 힙
    - 동적 할당 시 사용. 런타임 시 크기 결정.
- 데이터 영역
    - 전역 변수, 정적 변수 저장. 프로그램 종료 시 사라지는 변수가 들어 있는 영역
    - BSS 영역과 Data 영역으로 나누어지고, BSS에는 초기화되지 않은 변수가 0으로 초기화되어 저장되고, Data에는 0이 아닌 다른 값으로 할당된 변수를 저장
- 코드 영역
    - 소스 코드가 들어가는 영역. 수정 불가능한 기계어로 저장

### 3.3.4 PCB

운영체제에서 프로세스에 대한 메타데이터를 저장한 데이터. 프로세스가 생성되면 운영체제는 해당 PCB를 생성.

프로그램이 실행되면 프로세스가 생성되고, 메모리가 할당 됨. 이 프로세스의 메타데이터들이 PCB에 저장되어 관리됨. 일반 사용자가 접근하지 못하게 커널 스택의 가장 앞부분에서 관리됨.

- **PCB의 구조**
    - **프로세스 스케줄링 상태** : ‘준비’, ‘일시중단’ 등 프로세스가 CPU에 대한 소유권을 얻은 이후의 상태.
    - **프로세스 아이디(PID)** : 프로세스 ID, 해당 프로세스의 자식 프로세스 ID
    - **프로세스 권한** : 컴퓨터 자원, I/O 디바이스에 대한 권한 정보
    - **프로그램 카운터(PC)** : 프로세스에서 실행해야 할 **다음 명령어의 주소에 대한 포인터**
    - **CPU 레지스터** : 프로세스를 실행하기 위해 저장해야 할 레지스터에 대한 정보
    - **CPU 스케줄링 정보** : CPU 스케줄러에 의해 중단된 시간 등에 대한 정보
    - **계정 정보** : 프로세스 실행에 사용된 CPU 사용량. 실행한 유저의 정보
    - **I/O 상태 정보** : 프로세스에 할당된 I/O 디바이스 목록

- **컨텍스트 스위칭**
    - PCB를 교환하는 과정.
    - 한 프로세스에 할당된 시간이 끝나거나 인터럽트에 의해 발생.
    - 싱글 코어를 기준으로
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4629f6c6-db23-4c1a-9648-66670579453a/Untitled.png)
        
    - 컨텍스트 스위칭 시 유후 시간(idle time) 발생.
    - 캐시미스 : 컨텍스트 스위칭의 비용. 프로세스가 가지고 있는 메모리 주소가 그대로 있으면 잘못된 주소 변환이 발생해 캐시 클리어 과정을 겪음. (→?)

### 3.3.5 멀티프로세싱

여러 개의 프로세스를 통해 동시에 두 가지 이상의 일을 수행. 병렬 처리로 특정 프로세스나 그 일부에 문제가 생겨도 다른 프로세스를 이용해 처리할 수 있어 신뢰성이 높음

- **웹 브라우저**
    - 브라우저 프로세스 : 주소 표시줄, 북마크 막대, 앞뒤로 가기 버튼 등. 네트워크 요청이나 파일 접근 권한 담당
    - 렌더러 프로세스
    - 플러그인 프로세스
    - GPU 프로세스
- **IPC(Inter Process Communication)**
    - 프로세스끼리 데이터를 주고 받으며 공유 데이터를 관리하는 메커니즘.
    - 클라이언트 - 서버
    - 메모리가 완전히 공유되는 스레드보다는 속도가 떨어짐
    - **************************IPC의 종류**************************
        - 공유 메모리
            - 여러 프로세스에 동일한 메모리 블록에 대한 접근 권한이 부여되어 서로 통신할 수 있도록 공유 버퍼를 생성하는 것.
            - 기본적으로는 다른 프로세스의 메모리에는 접근할 수 없지만 공유 메모리를 이용하면 가능.
            - 메모리 자체를 공유하기 때문에 불필요한 데이터 복사 오버헤드가 발생하지 않음
            - 같은 메모리를 공유하므로 동기화가 필요
        - 파일
            - 디스크에 저장된 데이터, 파일 서버에서 제공한 데이터
        - 소켓
            - 동일한 컴퓨터의 다른 프로세스, 또는 네트워크의 다른 컴퓨터로 네트워크 인터페이스를 통해 전송하는 데이터. TCP, UDP
        - 익명 파이프
            - 파이프 : 프로세스 간에 FIFO 방식으로 읽히는 임시 공간
            - 단방향 방식의 읽기 전용, 쓰기 전용 파이프를 만들어 작동
            - 부모-자식 프로세스 간에만 사용 가능. 네트워크 상에서는 불가능
        - 명명 파이프
            - 파이프 서버와 하나 이상의 파이프 클라이언트 간의 통신을 위한 단방향 또는 이중 파이프.
            - 클라이언트-서버 통신을 위한 파이프를 제공. 여러 파이프 동시에 사용 가능.
            - 프로세스끼리, 또는 다른 네트워크상의 컴퓨터와 통신
        - 메시지 큐
            - 메시지를 큐로 관리.
            - 커널에서 전역적으로 관리
            - 다른 IPC 방식에 비해 사용 방법이 직관적이고 간단함
            - 다른 코드의 수정 없이 간단히 메시지 큐에 접근할 수 있음
            - IPC를 구현할 때 쓰기-읽기 빈도가 높으면 동기화 때문에 구현이 복잡해지는데 이때 대안으로 사용하기도 함

### 3.3.6 스레드와 멀티스레딩

+ **스레드**
  + 프로세스의 실행 가능한 가장 작은 단위. 한 프로세스는 여러 스레드를 가질 수 있다.
  + 프로세스는 코드, 데이터, 스택, 힙을 각각 생성하지만 스레드는 코드, 데이, 힙을 공유한다.
+ **멀티스레딩**
  + 프로세스 내 작업을 여러 개의 스레드로 처리하는 기법.
  + 스레드끼리 서로 자원을 공유하기에 효율성이 온ㅍ다.
  + 한 스레드가 중단되어도 다른 스레드는 실행 중일 수 있으므로 빠른 처리가 가능하다.
  + 한 스레드에 문제가 생기면 다른 스레드에도 영향을 끼쳐 프로세스에 영향을 줄 수 있다는 단점이 있다.

### 3.3.7 공유 자원과 임계 영역

+ **공유 자원**
  + 시스템 안에서 각 프로세스, 또는 스레드가 함께 접근할 수 있는 자원과 변수
  + 이 공유 자원을 둘 이상의 프로세스가 동시에 읽거나 쓰는 상황을 경쟁 상태(race condition)라 함
    + 경쟁 상태에 있을 때, 접근의 타이밍이나 순서가 결과값에 영향을 줄 수 있다.
+ **임계 영역**
  + 공유 자원에 접근할 때 순서 등의 이유로 결과가 달라지는 영역을 임계 영역이라고 함.
  + 임계 영역을 해결하기 위한 방법에는 뮤텍스, 세마포어, 모니터가 있다. 이들은 모두 다음의 세 조건을 만족한다.
    + 상호 배제
      + 한 프로세스가 임계 영역에 들어가면 다른 프로세스는 들어갈 수 없다.
    + 한정 대기
      + 특정 프로세스가 영원히 임계 영역에 들어가지 못하면 안 된다.
    + 융통성
      + 한 프로세스가 다른 프로세스의 일을 방해해서는 안 된다.
  + **뮤텍스**
    + 공유 자원을 사용하기 전에 설정하고 사용한 후에 해제하는 잠금.
    + 잠금이 설정되면 다른 스레드는 잠긴 코드 영역에 접근할 수 없음.
    + 뮤텍스는 하나의 상태(잠금 or 잠금 해제)만 가짐.
  + **세마포어**
    + 일반화된 뮤텍스
    + 정수 값과 두 가지 함수(wait, P & signal, V)로 공유 자원에 대한 접근을 처리함
      + wait() 자신의 차례가 올 때까지 기다리는 함수
      + signal() 다음 프로세스로 순서를 넘겨주는 함수
    + 프로세스가 공유 자원에 접근하면 세마포어에서 wait(), 프로세스가 공유 자원을 해제하면 세마포어에서 signal() 작업을 수행.
    + 세마포어에는 조건 변수가 없고, 프로세스가 세마포어 값을 수정할 때 다른 프로세스는 동시에 세마포어 값을 수정할 수 없다.
      + 바이너리 세마포어
        + 0과 1의 두 가지 값만 가질 수 있는 세마포어.
        + 뮤텍스도 바이너리 세마포어라 할 수 있음.
          + 단 뮤텍스는 자원에 대한 접근 동기화에 사용되는 메커니즘이고 세마포어는 신호를 기반으로 상호 배제가 일어나는 신호 메커니즘.
      + 카운팅 세마포어
        + 여러 개의 값을 가질 수 있는 세마포어. 여러 자원에 대한 접근을 제어하는 데 사용됨
   + **모니터**
      + 둘 이상의 스레드나 프로세스가 공유 자원에 안전하게 접근할 수 있도록 공유 자원을 숨기고 해당 접근에 대한 인터페이스만을 제공함.
      + 모니터 큐를 통해 공유 자원에 대한 작업들을 순차적으로 처리함.
      + 모니터는 세마포어보다 구현하기 쉽고, 상호 배제 또한 자동. (세마포어는 상호 배제를 명시적으로 구현해야 함)

### 3.3.8  교착 상태(deadlock)

​	두 개 이상의 프로세스들이 서로가 가진 자원을 기다리며 중단된 상태

+ 교착 상태의 원인
  + **상호 배제** 한 프로세스가 자원을 독점하고 다른 프로세스들은 접근이 불가
  + **점유 대기** 특정 프로세스가 점유한 자원을 다른 프로세스가 요청하는 상태
  + **비선점** 다른 프로세스의 자원을 강제로 가져올 수 없음
  + **환형 대기** 서로 다른 프로세스가 서로의 자원을 요구하는 상황
+ 교착 상태의 해결 방법
  + 자원 할당 시 교착 상태에 빠지는 조건이 성립하지 않도록 설계
  + 교착 상태의 가능성이 없을 때만 자원을 할당하고 프로세스 당 요청할 자원의 최대치를 통해 자원 할당 가능 여부를 파악하는 알고리즘 사용
  + 교착 상태 발생 시 사이클을 찾아보고 관련 프로세스를 하나 씩 지우기
  + 교착 상태 처리 비용이 더 비싸므로 교착 상태 발생시 작업을 종료.

## 3.4 CPU 스케줄링 알고리즘

CPU 스케줄러는 CPU 스케줄링 알고리즘에 따라 프로세스에서 해야 하는 일을 스레드 단위로 CPU에 할당. 어떤 프로그램에 CPU 소유권을 줄 것인지를 결정함.

CPU 이용률은 높게, 주어진 시간에 많은 일을 하게, ready queue에 있는 프로세스는 적게, 응답 시간은 짧게.

### 3.4.1 비선점형(non-preemptive) 방식

프로세스가 스스로 CPU 소유권을 포기하는 방식. 강제로 프로세스를 중지하지 않음. 컨텍스트 스위칭으로 인한 부하가 적다.

+ **FCFS**
  + 가장 먼저 온 것을 가장 먼저 처리하는 알고리즘
  + 길게 수행되는 프로세스 때문에 준비 큐에서 오래 기다리는 convoy effect가 발생함
+ **SJF**
  + 실행 시간이 가장 짧은 프로세스를 가장 먼저 실행하는 알고리즘
  + 긴 시간을 가진 프로세스가 실행되지 않는 현상(starvation)이 일어남.
  + 평균 대기 시간은 가장 짧지만, 실제 실행 시간을 알 수 없으므로 과거의 실행 시간을 토대로 추측해서 사용
+ **우선순위**
  + 오래된 작업일 수록 우선순위를 높이는 방법(aging)을 통해 SJF의 단점을 보완

### 3.4.2 선점형 방식

현대 운영체제가 쓰고 있는 방식. 지금 사용하고 있는 프로세스를 알고리즘에 의해 중단시키고 강제로 다른 프로세스에 CPU 소유권을 할당하는 방식

+ **라운드 로빈**
  + 우선순위 스케줄링의 일종.
  + 각 프로세스에 동일한 할당 시간을 주고, 그 시간 안에 끝나지 않으면 다시 준비 큐의 뒤로 감.
  + 할당 시간이 크면 FCFS가 되고, 짧으면 컨텍스트 스위칭이 잦아져서 오버헤드가 커짐.
  + 일반적으로는 전체 작업 시간이 길어지지만 평균 응답 시간은 짧아짐.
  + 로드밸런서에서 트래픽 분산 알고리즘으로도 쓰임
+ **SRF**
  + SJF는 실행 시간이 더 짧은 작업이 들어와도 기존의 짧은 작업들을 모두 수행하고 그 다음 짧은 작업을 이어나감.
  + SRF는 도중에 더 짧은 작업이 들어오면 기존에 수행하던 프로세스를 중지하고 해당 프로세스를 수행
+ **다단계 큐**
  + 다단계 큐는 우선순위에 따른 준비 큐를 여러 개 사용하고, 큐마다 라운드 로빈이나 FCFS 등 다른 스케줄링 알고리즘을 적용한 것.
  + 큐 간 프로세스 이동이 안되므로 스케줄링 부담이 적지만 유연성은 떨어짐.
