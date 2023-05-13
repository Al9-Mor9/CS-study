# 3. 운영체제(Operating System)
- 사용자가 컴퓨터를 쉽게 다루게 해주는 *인터페이스*
- **한정된 메모리나 시스템 resource를 효율적으로 분배함**
- [참고] 운영체제와 유사하지만 소프트웨어를 추가로 설치할 수 없는 것 &rarr; 펌웨어(firmware)

# 3.1 운영체제와 컴퓨터
## 📌 3.1.1 운영체제의 역할과 구조
### ✔️ 운영체제의 역할
1. CPU 스케줄링과 프로세스 관리
2. 메모리 관리
3. 디스크 파일 관리
4. I/O 디바이스 관리

### ✔️ 운영체제의 구조
![image](https://user-images.githubusercontent.com/108309396/236749215-ac9bb4e4-c09e-41dd-a734-52fa4a66197b.png)
- [참고] Linux server: CUI만 있음(GUI 없음)
  - CUI? 그래픽이 아닌 명령어로 처리하는 인터페이스

### 🔅 시스템콜
- 운영체제가 커널에 접근하기 위한 인터페이스
- 유저 프로그램이 운영체제의 서비스를 받기 위해 커널 함수를 호출할 때 사용
- resource에 대한 **직접 접근을 차단**할 수 있고 다른 프로그램으로부터 **보호 가능**  
- 유저 프로그램이 I/O 요청으로 **trap 발동**
&rarr; 올바른 I/O 요청인지 확인   
&rarr; 유저 모드가 시스템콜을 통해 **커널 모드로 변환**되어 실행  
  - I/O 요청이란? 입출력 함수, 데이터베이스, 네트워크, 파일 접근 등)  
![image](https://user-images.githubusercontent.com/108309396/236750205-da1374c6-cdc8-4793-b549-4884920122ff.png)
![image](https://user-images.githubusercontent.com/108309396/236750710-9ba2f138-c6a3-4a9b-a85c-5243a6be988f.png)

### 🔅 유저 모드와 커널 모드
- 유저 모드: 유저가 접근할 수 있는 영역을 제한적으로 두며 컴퓨터 자원에 함부로 침범하지 못 함
- 커널 모드: 모든 컴퓨터 자원에 접근할 수 있는 모드
- 커널? 운영체제의 핵심, 시스템콜 인터페이스 제공

### 🔅 modebit
- modebit == 1: user mode
- modebit == 0: kernel mode  
![image](https://user-images.githubusercontent.com/108309396/236751435-6c34942e-3466-4bc0-a81d-5e075fd5ef07.png)

## 📌 3.1.2 컴퓨터의 요소
- CPU, DMA 컨트롤러, 메모리, 타이머, 디바이스 컨트롤러 등으로 이루어짐  
![image](https://user-images.githubusercontent.com/108309396/236751880-0217576e-1da6-46bd-8eba-023932600010.png)

### ✔️ CPU
- CPU(Central Processing Unit): ALU(산술논리연산장치), Control(제어장치), Register(레지스터)로 구성
- interrupt에 의해 단순히 메모리에 존재하는 명령어를 해석해서 실행
- OS의 kernel이 프로그램을 메모리에 올려 프로세스로 만들면 일꾼인 CPU가 처리

### ✔️ 제어장치(Control Unit)
- 프로세스 조작을 지시하는 CPU의 한 부품
- 입출력 장치 간 통신을 제어
- 명령어들을 읽고 해석
- 데이터 처리를 위한 순서를 결정

### ✔️ 레지스터(Register)
- CPU 안에 있는 매우 빠른 임시기억장치
- CPU와 직접 연결되어 있으므로 연산속도가 메모리보다 훨씬 빠름

### ✔️ ALU(Arithmetic Logic Unit)
- 산술 연산, 배타적 논리합, 논리곱 같은 논리연산을 계산하는 디지털 회로

### 🔅 CPU의 연산처리 예시  
![image](https://user-images.githubusercontent.com/108309396/236752847-7d4fb04a-7a4c-462e-88f4-dd813a2ec79b.png)

### 🔅 Interrupt
- 어떤 신호가 들어왔을 때 CPU를 잠깐 정지시키는 것
- IO 디바이스로 인한 인터럽트, 0으로 숫자를 나누는 연산에서의 인터럽트, 프로세스 오류 등으로 발생
- interrupt 발생   
&rarr; interrupt handler 함수가 모여있는 interrupt vector로 가서 인터럽트 핸들러 함수가 실행됨
&rarr; interrupt 간에는 우선순위가 있고 우선순위에 따라 실행됨
- Interrupt Handler Function: 인터럽트가 발생했을 때 이를 핸들링하기 위한 함수
  - 커널 내부의 IRQ를 통해 호출되며 request_irq()를 통해 핸들러 함수 등록 가능

### 🔅 하드웨어 인터럽트와 소프트웨어 인터럽트
1. 하드웨어 인터럽트: IO 디바이스에서 발생하는 인터럽트
   - 운영체제에 시스템콜을 요청해서 원하는 디바이스를 향해 디바이스에 있는 작은 로컬 버퍼에 접근하여 처리
2. 소프트웨어 인터럽트: trap
   - 프로세스 오류 등으로 프로세스가 시스템콜을 호출할 때 발동

### ✔️ DMA 컨트롤러
- IO 디바이스가 메모리에 직접 접근할 수 있도록 하는 하드웨어 장치
- CPU에만 너무 많은 인터럽트 요청이 들어오기 때문에 CPU 부하를 막아줌

### ✔️ 메모리
- 데이터나 상태, 명령어 등을 기록하는 장치
- 보통 RAM(Random Access Memory)
- 메모리가 클 수록 많은 일을 동시에 가능

### ✔️ 타이머
- 몇 초 안에는 작업이 끝나야 한다는 것을 정하고 특정 프로그램에 시간 제한을 다는 역할
- 시간이 많이 걸리는 프로그램이 작동할 때 제한을 걸기 위해 존재

### ✔️ 디바이스 컨트롤러
- 컴퓨터와 연결되어 있는 IO 디바이스들의 작은 CPU


# 3.2 메모리
- CPU는 단지 메모리에 올라와있는 프로그램의 instruction들을 실행함

## 📌 3.2.1 메모리 계층
1. 레지스터(register): CPU 안에 있는 작은 메모리. 휘발성O, 속도 가장 빠름, 기억 용량이 가장 적음
2. 캐시(cache): L1, L2 캐시를 지칭. 휘발성O, 속도 빠름, 기억 용량 적음. L3 캐시도 있음
3. 주기억장치(RAM): 휘발성O, 속도 보통, 기억 용량 보통
4. 보조기억장치(HDD, SDD): 휘발성O, 속도 낮음, 기억 용량 많음  
<img width="451" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/01ad2409-e392-4906-8e8b-ddf618742fde">

### ✔️ RAM
- RAM은 하드디스크로부터 일정량의 데이터를 복사해서 임시 저장하고 필요 시마다 CPU에 빠르게 전달하는 역할
- 계층이 있는 이유? 경제성과 캐시 때문!
  - 경제성 &rarr; 16기가 RAM은 비싸지만 16기가 SSD는 훨씬 싸다
- 로딩 중의 의미: 하드디스크 또는 인터넷에서 데이터를 읽어 RAM으로 전송하는 과정이 끝나지 않았음을 의미

### ✔️ 캐시
- 데이터를 미리 복사해놓는 임시 저장소
- 빠른 장치와 느린 장치에서 속도 차이에 따른 '병목 현상'을 줄이기 위한 메모리
- 메모리와 CPU의 연산 속도 차이가 크기 때문에 중간에 레지스터 계층을 둬서 속도 차이를 해결함

### 🔅 Locality Principle
- 자주 사용하는 데이터를 기반으로 캐시 설정 &rarr; temporal locality(시간 지역성) | spatial locality(공간 지역성)
- Temporal Locality: 최근 사용한 데이터에 다시 접근하려는 특성  
- Spatial Locality: 최근 접근한 데이터를 이루고 있는 공간이나 그 가까운 공간에 접근하는 특성  
<img width="592" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/4f632ab4-d618-4494-a570-fe4b69625b40">

### 🔅 Cache: Hit and Miss
- Hit: 캐시에서 원하는 데이터를 찾았을 경우
  - Control을 통해 해당 데이터를 가져옴
  - 위치도 가깝고 *CPU 내부 버스* 기반으로 작동하기 때문에 빠름
- Miss: 해당 데이터가 캐시에 없어 메모리로 가서 데이터를 찾아오는 것 
  - *시스템 버스*를 기반으로 메모리에서 가져오기 때문에 느림  
<img width="501" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/5e7486ba-9f8b-4eed-9488-02f99036bd43">

### 🔅 Cache Mapping
- 캐시가 hit되기 위해 매핑하는 방법
- CPU의 레지스터와 주 메모리(RAM) 간에 데이터를 주고받을 때를 기준
1. 직접 매핑(Direct Mapping)
   - 캐시 메모리의 각 슬롯이 메인 메모리에서 고유한 주소 범위를 가지도록 설정하는 방식
   - 메모리 주소와 캐시의 순서를 일치시킴
   - 장점: 처리속도가 빠름, 캐시 크기를 작게 유지 가능 
   - 단점: 충돌 발생이 잦음
   - 충돌 &rarr; 캐시 메모리의 슬롯 수보다 많은 수의 메인 메모리 블록이 같은 캐시 메모리 슬롯에 매핑될 때 발생
   - ex) 4개의 슬롯을 가진 캐시 메모리에서 직접 매핑을 사용하는 경우
   - <img width="357" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/7cc15966-7ad5-4de6-93a4-d325d995ce27">
   - 충돌 발생 시, 캐시 메모리에서 해당 블록을 찾기 위해 다른 블록을 대체해야 함. 이 경우, 캐시 히트율&darr; 성능 저하 발생
2. 연관 매핑(Associative Mapping)
   - 순서를 일치시키지 않고 관련 있는 캐시와 메모리를 매핑(어디든 저장 가능)
   - 이를 위해 캐시 메모리에는 *태그(Tag) 필드*가 추가되어 있으며, 이 태그 필드는 *메인 메모리 블록의 주소*를 저장함
   - 장점: 충돌 적음, 캐시 히트율 증가(직접 매핑보다 더 많은 메모리 블록을 캐시 메모리에 저장할 수 있기 때문-필요한 캐시들 위주로 저장 가능)
   - 단점: 태그 검색을 통해 모든 블록을 탐색해야 하므로 속도 느림, 태그 검색을 위한 추가적인 하드웨어 및 논리 회로가 필요하므로 구현이 복잡
   - ex) 4개의 슬롯을 가진 캐시 메모리에서 연관 매핑을 사용하는 경우
   - ![image](https://github.com/Haaarimmm/TIL/assets/108309396/342ebc79-e864-4418-a194-8607b1c18995)
3. 직접 연관 매핑(Set Associative Mapping)
   - 순서는 일치시키지만 집합을 둬서 저장하며 블록화
   - 연관매핑에 비해 검색이 더 효율적이다
   - ex) 메모리가 1~100이 있고 캐시가 1~10이 있을 때 캐시 1~5에 데이터 1~50을 무작위로 저장

### 🔅 웹 브라우저의 캐시
- 보통 사용자의 커스텀한 정보나 인증 모듈 관련 사항들을 웹 브라우저에 저장해서 추후 서버에서 요청할 때 보낼 때 쓰임
1. 쿠키
   - 만료기한이 있는 key-value 저장소
   - 4KB까지 데이터 저장 가능
   - `same site` 옵션을 `strict`로 저장하지 않으면 다른 도메인에서 요청했을 때 자동 전송됨
   - 보통 서버에서 만료기한을 정함
2. 로컬 스토리지
   - 만료기한이 없는 key-value 저장소
   - 10MB까지 저장 가능
   - 웹 브라우저를 닫아도 유지됨
   - 도메인 단위로 저장, 생성
   - 클라이언트에서만 수정 가능
3. 세션 스토리지
   - 만료기한이 없는 key-value 저장소
   - 5MB까지 저장 가능
   - 탭 단위로 세션 스토리지 생성
   - 탭 닫으면 데이터도 삭제됨
   - 클라이언트에서만 수정 가능

### 🔅 데이터베이스의 캐싱 계층
- 메인 DB 위헤 redis 데이터베이스 계층을 '캐싱 계층'으로 두면 &rarr; 성능 향상 가능   
![image](https://github.com/Haaarimmm/TIL/assets/108309396/74e1948d-07d8-402e-8bd1-f30a667aac95)


## 📌 3.2.2 메모리 관리
### ✔️ 가상 메모리(Virtual Memory)
- 컴퓨터가 실제로 이용 가능한 메모리 resource를 추상화하는 것  
![image](https://github.com/Haaarimmm/TIL/assets/108309396/0fcb5aaa-c5a5-42f1-ba23-7ba485ed62f2)
- 가상 주소(logical address), 실제 주소(physical address)
- logical address는 MMU(Memory Management Unit)에 의해 physical address로 변환됨
- logical address와 physical address가 매핑되어 있고 프로세스의 주소 정보가 들어있는 Page Table로 관리됨
  - 속도 향상을 위해서 TLB(Translation Lookaside Buffer)를 사용
  - TLB? 메모리와 CPU 사이에 있는 주소 변환을 위한 캐시. 페이지 테이블에 있는 리스트를 보관하며 CPU가 페이지 테이블까지 가지 않도록 하는 캐시 계층

### 🔅 스와핑(Swapping)
- 가상 메모리에는 존재하지만 실제 메모리인 RAM에는 없는 데이터나 코드에 접근할 경우 page fault가 발생
- 스와핑: page fault를 방지하기 위해 당장 사용하지 않는 영역을 하드디스크로 옮겨 필요할 때 다시 RAM으로 불러와 RAM을 효과적으로 관리하는 것

### 🔅 페이지 폴트(Page fault)
- 프로세스의 주소 공간에는 존재 but RAM에는 없는 데이터에 접근했을 경우 발생
1. CPU는 physical memory를 확인하여 해당 page가 없으면 trap을 발생시켜 OS에 알림
2. OS는 CPU의 동작을 잠시 멈춤(interrupt)
3. OS는 page table을 확인하여 가상 메모리에 페이지가 존재하는지 확인하고, 없으면 프로세스를 중단하고 physical memory에 비어있는 프레임이 있는지 찾음
4. physical memory에도 없다면 스와핑 발동
5. 비어 있는 프레임에 해당 페이지를 로드하고, 페이지 테이블을 최신화
6. CPU 다시 시작

### 🔅 Page & Frame
- Page: 가상 메모리를 사용하는 최소 크기 단위
- Frame: 실제 메모리를 사용하는 최소 크기 단위

### ✔️ 스레싱(Thrashing)
- 메모리의 page fault rate가 높은 것
- 컴퓨터의 심각한 성능 저하 초래  
![image](https://github.com/Haaarimmm/TIL/assets/108309396/e9a0ab99-68d2-4bb6-9e3a-1c1d4bf922c8)
- 메모리에 너무 많은 프로세스가 동시에 올라가게 되면 스와핑이 많이 일어나서 스레싱이 발생
- page fault 발생 &rarr; CPU 이용률 낮아짐 &rarr; OS가 CPU의 가용성을 높이기 위해 더 많은 프로세스를 메모리에 올림
- 해결 방안) 메모리&uarr;, HDD를 SDD로 바꾸기, OS의 작업세트나 PFF

### 🔅 작업 세트(working set)
- 프로세스의 locality를 통해 결정된 페이지 set를 만들어서 미리 메모리에 로드
- 탐색에 드는 비용을 줄일 수 있고 스와핑&darr;

### 🔅 PFF(Page Fault Frequency)
- 페이지 폴트 빈도를 조절하는 방법
- 상한선과 하한선을 만듦
- 상한선에 도달하면 페이지를 늘리고, 하한선에 도달하면 페이지를 줄임

### ✔️ 메모리 할당(Memory Allocation) - 연속 할당
- 메모리에 프로그램을 할당할 때는 시작 메모리 위치, 메모리의 할당 크기를 기반으로 할당   
![image](https://github.com/Haaarimmm/TIL/assets/108309396/b6eec895-6189-4e57-b6f8-477f9bb0048a)
   - 고정 분할 방식(fixed partition allocation): 메모리를 미리 나누어 관리하는 방식. 융통성이 없고 내부 단편화(internal fragmentation) 발생
   - 가변 분할 방식(variable partition allocation): 매 시점 프로그램의 크기에 맞게 동적으로 할당. 외부 단편화(external fragmentation) 발생

### 🔅 가변 분할 방식-  first fit, best fit, worst fit, next fit
<img width="605" alt="A917829E-25F7-4C21-89B8-82E3418B0FD9" src="https://github.com/Haaarimmm/TIL/assets/108309396/4e299038-aece-47d9-a77b-e94de6592bd5">

### 🔅 내부 단편화 & 외부 단편화
- Internal Fragmentation: 할당된 메모리 영역 내부에서 낭비되는 메모리 공간
  - 메모리 할당 단위가 고정되어 있고, 프로세스의 크기가 할당 단위보다 작은 경우에 발생
- External Fragmentation: 메모리 공간 내에서 사용 가능한 공간이 작아져서 발생하는 문제
  - 메모리 할당 단위와 프로세스의 크기와는 별개의 문제이며, 프로세스를 할당하고 해제할 때마다 발생


### ✔️ 메모리 할당(Memory Allocation) - 불연속 할당
1. Paging
   - 동일한 크기의 page로 나누어 메모리의 서로 다른 위치에 프로세스를 할당
   - 홀의 크기가 불균일한 문제가 사라짐 but 주소 변환이 복잡해짐
2. Segmentation
   - 페이지 단위가 아닌 segment 단위로 나누는 방식
   - 코드나 데이터를 기반으로 나눌 수도 있고 함수 단위로 나눌 수도 있음
   - 공유와 보안 측면에서 좋음 but 홀 크기가 균일하지 않은 문제 발생
3. Paged Segmentation
   - 공유나 보안을 의미 단위의 segment로 나누고, physical memory는 페이지로 나누는 것

### ✔️ 페이지 교체 알고리즘
- 페이지 교체 알고리즘을 기반으로 스와핑이 일어남(스와핑은 많이 일어나면 X)
1. 오프라인 알고리즘
   - 먼 미래에 참조되는 페이지와 현재 할당하는 페이지를 바꾸는 알고리즘
   - but 미래에 사용되는 프로세스를 알 수 없기에 사용 불가
   - 다른 알고리즘과의 성능 비교에 쓰임
2. FIFO(First In First Out)
   - 가장 먼저 온 페이지를 교체 영역에 먼저 놓는 방법
3. LRU(Least Recently Used)
   - 참조가 가장 오래된 페이지를 바꿈
   - 오래된 것을 파악하기 위해 각 페이지마다 counter, stack을 두어야 함
   - ![image](https://github.com/Haaarimmm/TIL/assets/108309396/9c9d8f05-e3d3-4cfe-b61a-7acbffee8ad1)
   - 구현 시 Hash Table 또는 이중 연결 리스트로 구현
4. NUR(Not Used Recently)
   - == clock 알고리즘
   - 0과 1을 가진 비트를 두고 1은 최근에 참조, 0은 참조되지 않음을 의미
   - 시계 방향으로 돌면서 0을 찾은 순간 해당 프로세스를 교체하고 1로 바꿈
   - ![image](https://github.com/Haaarimmm/TIL/assets/108309396/5af2d58f-6c98-4303-9597-6a1a52ed9a81)
5. LFU(Least Frequently Used)
   - 가장 참조 횟수가 적은 페이지를 교체

# 3.3 프로세스와 스레드
- 프로세스(process): 컴퓨터에서 실행되고 있는 프로그램 == CPU 스케줄링의 대상이 되는 task
- 스레드(thread): 프로세스 내 작업의 흐름
- 인스턴스화: 프로그램이 메모리에 올라가면 프로세스가 되는 것, 이후 OS의 CPU Scheduler에 따라 프로세스를 실행
<img width="340" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/8d0c92c9-e158-4326-b291-346e6f7e7098">

## 📌 3.3.1 프로세스와 컴파일 과정
- 프로세스 &rarr; 프로그램으로부터 인스턴스화된 것
  - ex) 프로그램: 구글 크롬 프로그램(chrome.exe)과 같은 실행 파일, 프로세스: 두 번 클릭 시 구글 크롬 프로세스 실행
- 프로그램 &rarr; 컴파일러가 컴파일 과정을 거쳐 기계어로 번역되어 실행될 수 있는 파일
- 프로그램의 컴파일 과정
  - <img width="546" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/f21ebd85-4df9-421a-9789-993dc4a28314">

### ✔️ 전처리(preprocessing)
1. 소스코드의 주석 제거
   - 소스 코드의 크기를 줄이고 가독성을 높이는 역할
2. `#include` 등 헤더파일을 병합
   - 소스 코드에 포함된 모든 헤더 파일을 읽어들여 하나의 소스 코드로 합침. 
   - 컴파일러는 소스 코드를 한 번에 처리 가능 &rarr; 컴파일 속도&uarr;
3. 매크로 치환
   - 소스 코드에서는 `#define` 지시문을 통해 매크로 정의 가능. 
   - 전처리기는 매크로의 이름과 값을 기억 &rarr; 소스 코드에서 매크로를 사용하는 경우, 전처리기는 매크로의 이름을 매크로의 값으로 대체
   - 매크로를 직접적으로 사용하는 코드로 변환 &rarr; 코드의 크기&darr; 가독성&uarr;

### ✔️ 컴파일러
1. Lexical Analysis (토큰화)
   - 소스 코드를 작은 단위인 토큰(Token)으로 분해
   - 토큰: 예약어, 식별자, 상수, 연산자 등과 같은 코드의 최소 단위
2. Syntax Analysis (구문 분석)
   - 토큰들을 문법 규칙에 따라 구조화
   - 문장 구조를 나타내는 Parse Tree 또는 Abstract Syntax Tree(AST)를 생성
   - 문법에 맞지 않는 코드는 오류로 처리
3. Semantic Analysis (의미 분석)
   - 코드의 의미와 일관성을 검사
   - 타입 체크, 변수 선언 검사, 함수 호출 검사 등이 이루어짐 
   - 오류가 발견 시 해당 코드는 오류로 처리
4. Code Optimization (코드 최적화)
   - 코드를 더 효율적으로 만들기 위해 여러 가지 최적화 기법을 사용
   - 상수 접기(Constant Folding), 불필요한 코드 제거, 루프 최적화 등
   - 실행 속도&uarr; 메모리 사용량&darr;
   - 최적화 과정에서 오류 발생 시, 최적화를 비활성화하거나 최적화 수준 조정 가능
5. Code Generation (코드 생성)
   - 소스 코드를 해당 컴퓨터 아키텍처에 맞는 어셈블리어로 변환(+ 오류 검사 포함)
   - 어셈블리어 코드는 추후 컴퓨터가 이해하는 기계어로 변환되어 실행됨

### ✔️ 어셈블러
- 어셈블러는 어셈블리어를 목적 코드(object code)로 변환
- 리눅스에서는 `.o` 확장자
- `(파일이름).c`를 컴파일 &rarr; `(파일이름).o`가 생성됨

### ✔️ 링커
- 컴파일러가 생성한 여러 개의 오브젝트 파일(Object File)을 하나의 실행 파일(Executable File)로 결합하는 과정
- 이 과정에서는 다른 오브젝트 파일과 라이브러리를 링크하며, 최종 실행 파일이 생성
- 실행 파일의 확장자는 `.exe` 또는 `.out`

### 🔅 정적 라이브러리와 동적 라이브러리
1. 정적 라이브러리
   - 프로그램 빌드 시 라이브러리가 제공하는 모든 코드를 실행 파일에 넣는 방식
   - 장점: 시스템 환경 등 외부 의존도&darr;
   - 단점: 코드 중복 &rarr; 메모리 효율성&darr;
2. 동적 라이브러리
   - 프로그램 실행 시 필요할 때만 DLL 함수 정보를 통해 참조
   - 장점: 메모리 효율성&uarr;
   - 단점: 외부 의존도&uarr;

### 🔅 DLL(Dynamic Link Library)
- 여러 프로그램에서 공통으로 사용되는 함수나 리소스 등을 묶어서 독립적으로 관리하는 파일
- DLL 파일은 프로그램 실행 시 필요할 때 로드되어 메모리에 상주하고, 프로그램이 종료될 때 메모리에서 해제됨
  - 동적로딩(Dynamic Loading)이라고 함
- 코드 재사용성&uarr;, 
  - 여러 프로그램 간에 공통으로 사용되는 함수를 한 번 구현하여 다른 프로그램에서도 사용할 수 있도록 해줌 
  - 개발 속도&uarr; 유지보수 및 업그레이드 용이
- DLL 파일의 확장자
  - Windows: `.dll` 
  - Linux: `.so`(shared object)
- DLL 파일을 메모리에 로드하는 함수
  - Windows: `LoadLibrary()`
  - Linux: `dlopen()`
- DLL 파일에서 함수의 포인터를 가져오는 함수
  - Windows: `GetProcAddress()`
  - Linux: `dlsym()`
- DLL 파일이 없거나 버전이 다를 경우 문제가 발생 가능
  - 이를 방지하기 위해 DLL 파일의 버전 관리와 라이브러리 파일의 재배포 등을 신중하게 관리해야 함

## 📌 3.3.2 프로세스의 상태
<img width="631" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/5bce6a9d-8f45-43ed-bd47-154a5aba94f1">

### ✔️ 생성 상태(Create)
- 프로세스가 생성된 상태
- `fork()`, `exec()` 함수를 통해 생성 가능
  - `fork()`
    - 부모 프로세스의 주소 공간을 그대로 복사하여 새로운 자식 프로세스를 생성함
    - 주소 공간만 복사할 뿐 부모 프로세스의 비동기 작업은 상속X
  - `exec()`
    - 새롭게 프로세스를 생성
- 생성된 프로세스에 대한 PCB를 생성하여 메모리에 할당
  - PCB(Process Control Block): 운영체제가 프로세스를 관리하기 위해 필요한 정보를 저장하는 자료구조
    - 해당 프로세스의 상태, 우선순위, 메모리 할당 정보, 입출력 요청 정보, CPU 시간 할당 정보, 프로세스 ID 등의 정보를 저장
    - PCB를 통해 운영체제는 여러 프로세스를 효율적으로 관리하고, 프로세스 간의 충돌을 방지할 수 있음

### ✔️ 대기 상태(Ready)
- 메모리 공간이 충분하면 메모리를 할당받고 충분하지 않으면 대기
- CPU Scheduler로부터 CPU 소유권이 넘어오기를 기다리는 상태

### ✔️ 대기 중단 상태(Ready suspended)
- 메모리 부족으로 일시 중단된 상태

### ✔️ 실행 상태(Running)
- CPU 소유권과 메모리를 할당받고 instrunction을 수행 중인 상태
- CPU burst가 일어났다고도 표현
  - CPU burst: 프로세스가 CPU를 사용하는 시간

### ✔️ 중단 상태(Blocked)
- 어떤 이벤트가 발생한 이후 기다리며 프로세스가 차단된 상태
- ex) I/O interrupt 발생 시

### ✔️ 일시 중단 상태(Blocked suspended)
- 중단된 상태에서 프로세스가 실행되려고 했지만 메모리 부족으로 일시 중단된 상태

### ✔️ 종료 상태(Terminated)
- 메모리와 CPU 소유권을 모두 놓고 가는 상태
- 자발적 종료
- 부모 프로세스가 자식 프로세스를 강제시키는 비자발적 종료(abort)
  - 자식 프로세스에 할당된 자원의 한계치를 넘어서거나
  - 부모 프로세스가 종료되거나
  - 사용자가 `process.kill` 등 명령어로 프로세스를 종료할 때 발생


## 📌 3.3.3 프로세스의 메모리 구조
<img width="341" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/79dce039-e538-4835-a860-264f3414f906">

### ✔️ 코드 영역(Program Code)
- 소스 코드가 들어가는 영역
- 기계어로 저장되어 있음
- 정적인 특징을 가짐

### ✔️ 데이터 영역
- 전역변수(Global variable), 정적변수(Static Variable)가 저장됨
- 정적인 특징을 갖는 프로그램이 종료되면 사라지는 변수가 들어 있는 영역
- BSS segment: 초기화 되지 않은 변수가 0으로 초기화되어 저장됨
- Data segment: 0이 아닌 다른 값으로 할당된 변수들이 저장됨

### ✔️ 힙(Heap)
- 동적 할당할 때 사용됨(Dynamically allocate memory)
- 런타임 시 크기가 결정됨
- 벡터와 같은 동적 배열은 힙에 동적 할당됨
- 동적인 특징을 가짐

### ✔️ 스택(Stack)
- 지역변수(Local variable), 매개변수(Parameter), 함수가 저장
- 컴파일 시 크기가 결정됨
- 동적인 특징을 가짐
- 힙과 스택의 영역이 겹치면 안 되므로 힙과 스택 사이의 공간을 비워 놓음


## 📌 3.3.4 PCB
- PCB(Process Control Block): OS에서 프로세스에 대한 메타데이터를 저장
  - 메타데이터: 데이터에 관한 구조화된 데이터이자 데이터를 설명ㅇ하는 작은 데이터
  - 찾고 있는 정보를 효율적으로 찾아내서 이용하기 위해 일정한 규칙에 따라 콘텐츠에 부여되는 데이터
- PCB는 프로세스의 중요한 정보를 포함 &rarr; 일반 사용자가 접근하지 못하도록 kernel stack의 가장 앞부분에서 관리

### ✔️ PCB의 구조
- 프로세스 스케줄링 상태: ready, bloked suspended 등 프로세스가 CPU에 대한 소유권을 얻은 이후의 상태
- 프로세스 ID(PID): 해당 프로세스 혹은 자식 프로세스 ID
- 프로세스 권한: 컴퓨터 resource 또는 I/O 디바이스에 대한 권한 정보
- 프로그램 카운터(PC): 프로세스에서 실행해야 할 다음 명령어의 주소에 대한 포인터
- CPU 레지스터: 프로세스를 실행하기 위해 저장해야 할 레지스터에 대한 정보
- CPU 스케줄링 정보: CPU 스케줄러에 의해 중단된 시간 등에 대한 정보
- 계정 정보: 프로세스 실행에 사용된 CPU 사용량, 실행한 유저의 정보
- I/O 상태 정보 : 프로세스에 할당된 I/O 디바이스 목록

### ✔️ Context Switching
- PCB를 교환하는 과정
- 한 프로세스에 할당된 시간이 끝나거나 interrupt에 의해 발생
- Single Core를 기준으로 설명 &rarr; 어떠한 시점에서 실행되고 있는 프로세스는 단 한개
  - 많은 프로세스가 동시에 구동되는 것처럼 보이는 것 &rarr; 빠른 context switching 때문

<img width="504" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/600dfbfa-4de1-4d0c-ae44-305cdd6fbbac">

### 🔅 Context Switching cost
1. Idle time(유휴 시간)
2. Cache Miss
   - 프로세스가 가지고 있는 메모리 주소가 그대로 있으면 잘못된 주소 변환이 생기므로 cache clear 과정을 겪게 됨

### 🔅 스레드에서의 context switching
- 스레드는 **스택 영역을 제외한 모든 메모리를 공유**하기 때문에 context switching cost&darr; time&darr;

## 📌 3.3.5 멀티프로세싱
- 여러 개의 프로세스를 통해 동시에 두 가지 이상의 일을 수행하는 것
- 하나 이상의 일을 병렬로 처리 가능
- 특정 프로세스가 문제 발생하더라도 다른 프로세스를 이용해서 처리 가능 &rarr; 신뢰성&uarr;

### ✔️ Web Browser
- 브라우저 프로세스: 주소표시줄, 북마크막대, 뒤로가기 버튼, 앞으로 가기 버튼 등을 담당하며 네트워크 요청이나 파일 접근 같은 권한을 담당
- 렌더러 프로세스: 웹사이트가 '보이는' 부분의 모든 것을 제어
- 플러그인 프로세스: 웹사이트에서 사용하는 플러그인을 제어
- GPU 프로세스: GPU를 이용해서 화면을 그리는 부분을 제어

### ✔️ IPC
- 멀티 프로세스는 IPC(Inter-Process Communication)이 가능
  - IPC: 프로세스끼리 데이터를 주고받고 공유 데이터를 관리하는 메커니즘
  - 메모리가 완전히 공유되는 스레드보다는 속도&darr;

### 🔅 IPC의 종류
1. 클라이언트-서버 구조
   - 클라이언트는 데이터를 요청
   - 서버는 클라이언트 요청에 응답
2. 공유 메모리(shared memory)
   - 여러 프로세스에 동일한 메모리 블록에 대한 접근 권한 부여
   - 프로세스가 서로 통신할 수 있도록 공유 버퍼 생성
   - 공유 메모리를 통해 여러 프로세스가 하나의 메모리를 공유 가능
   - 메모리 자체를 공유: 불필요한 데이터 복사의 오버헤드 발생X &rarr; 가장 빠름
   - 동기화 필요
   - 하드웨어 관점에서는 RAM이 공유메모리에 해당
   - <img width="336" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/f3b45fcb-5eb1-4751-b998-05442e56fb33">
3. 파일
   - 디스크에 저장된 데이터 또는 파일 서버에서 제공한 데이터
4. 소켓(Socket)
   - 컴퓨터 네트워크 상에서 프로세스 간 통신을 위한 인터페이스를 제공하는 소프트웨어 모듈
   - 소켓은 특정 프로토콜과 IP 주소, 포트 번호를 지정하여 연결할 수 있음
   - 데이터를 주고받기 위한 일련의 API를 제공
   - TCP / UDP  
   - TCP 소켓: 신뢰성 있는 데이터 전송을 위해 연결형 프로토콜 사용
   - UDP 소켓: 비연결형 프로토콜을 사용합니다
5. Anonymous pipe
   - 프로세스 간에 FIFO 방식으로 읽히는 임시 공간인 파이프를 기반으로 데이터를 주고 받음
   - 단방향 방식의 읽기 전용, 쓰기 전용 파이프를 만들어 작동
   - 부모, 자식 프로세스 간에만 사용 가능
   - 부모 프로세스에서 파이프를 생성 &rarr; 자식 프로세스를 생성 &rarr; 부모와 자식 프로세스 간에 파이프 공유
   - 부모 프로세스는 파이프를 통해 자식 프로세스에게 데이터를 보내고, 자식 프로세스는 파이프를 통해 데이터를 수신
   - 장점: 다른 IPC 기법과 비교해서 구현이 비교적 쉬우며, 속도가 빠름
   - 단점: 단방향 통신만 가능, 동일한 시스템에서만 사용할 수 있음
   - <img width="469" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/379acf95-48a7-47c4-8412-963c1d1bdaee">
6. Named pipe 
   - 파이프 서버와 하나 이상의 파이프 클라이언트 간의 통신을 위한 명명된 단방향 또는 이중 파이프
   - 클라이언트-서버 통신을 위한 별도의 파이프 제공
   - 여러 파이프를 동시에 사용 가능
   - 컴퓨터의 프로세스끼리 또는 다른 네트워크 상의 컴퓨터와도 통신 가능
   - <img width="601" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/35cebc11-5912-4eeb-a697-5de4aafe99d2">
7. 메시지 큐
   - 메시지를 큐로 관리
   - 커널에서 전역적으로 관리됨
   - 다른 IPC에 비해 사용방법이 매우 직관적이고 간단함
   - <img width="610" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/8c5a3642-aa9e-4765-89ce-d46be4a40496"> 