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