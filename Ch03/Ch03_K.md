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