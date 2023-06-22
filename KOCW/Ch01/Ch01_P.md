그냥 정리해서 올려둠 

---
# [1] Introduction
## (1) 운영체제란 무엇인가?

- 컴퓨터 하드 웨어 바로 위에 설치되어 사용자 및 다른 모든 소프트웨어와 하드웨어를 연결하는 소프트웨어 계층
- 협의의 운영체제(커널)
    - 운영체제의 핵심. 메모리에 상주하는 부분
- 광의의 운영체제
    - 커널 뿐만 아니라 각종 주변 시스템 유틸리티를 포함한 개념

## (2) 운영체제의 목적

- 컴퓨터 시스템의 자원을 효율적으로 관리
    - 프로세서, 메모리, IO 장치의 효율적 관리
        - 사용자 간의 형평성 있는 자원 분배
        - 주어진 자원으로 최대한의 성능을 내도록
        - 주어진 하드웨어 자원을 최대한으로 사용
    - 사용자 및 운영체제 자신의 보호
    - 프로세스, 파일, 메시지 등의 소프트웨어 자원을 관리
- 컴퓨터 시스템을 편리하게 사용할 수 있는 환경을 제공
    - 운영체제는 동시 사용자 / 프로그램들이 각각 독자적으로 컴퓨터에서 수행되는 것 같은 환상을 제공
    - 하드웨어를 직접 다루는 복잡한 부분을 운영체제가 대행

## (3) 운영 체제의 분류

### 동시 작업 가능 여부

- 단일 작업
    - 한 번에 하나의 작업만을 처리
    - ex) MS-DOS
- 다중 작업
    - 동시에 둘 이상의 작업 처리
    - ex) UNIX, MS Windows 등의 현대 운영체제

### 사용자의 수

하나의 컴퓨터에 여러 사용자가 동시에 접근할 수 있는가?

- 단일 사용자
    - MS_DOS, MS Windows(요즘은 된다고도 할 수 있음)
- 다중 사용자
    - UNIX, NT server

### 처리 방식

- 일괄 처리 방식(batch processing)
    - 작업 요청을 일정량 모아서 한꺼번에 처리
    - 작업이 완전히 종료될 때까지 기다려야 함
    - ex) 초기 Punch Card 처리 시스템
- 시분할 방식
    - 여러 작업을 수행할 때 컴퓨터 처리 능력을 일정한 시간 단위로 분할하여 사용
    - 일괄 처리 방식에 비해 짧은 응답 시간을 가짐
    - Interactive한 방식(입력에 응답이 바로바로 나오도록 함)
- 실시간 방식
    - 정해진 시간 안에 어떠한 일이 바로 종료됨이 보장되어야 하는 실시간 시스템을 위한 OS
        - 특수한 목적을 가진 시스템.
        - 정해진 시간 안에 task가 반드시 종료되어야 함
    - 실시간 시스템의 개념 확장
        - Hard Real-time System(경성 실시간 시스템)
            - 데드라인을 어기면 치명적인 결과
            - ex) 원자로/공장 제어, 미사일 제어, 반도체 장비, 로봇 제어
        - Soft Real-time System(연성 실시간 시스템)
            - 그렇게 치명적이지는 않음.
            - ex) 미디어 스트리밍..

### 용어들

- Multitasking
    - 동시에 여러 task를 할 수 있음을 강조
- Multiprogramming
    - 메모리에 여러 프로그램이 동시에 올라가 있음을 강조
- Time Sharing
    - CPU가 시간을 분할해 나누어 쓴다는 의미를 강조
- Multiprocess
    - CPU가 여러 프로세스를 동시에 처리함을 강조
    - 🚧 Multiprocessor는 한 컴퓨터에 CPU가 여러 개 붙어 있음을 의미함

## (4) 운영체제의 예

### 유닉스 계열

- 유닉스
    - 코드 대부분을 C로 작성
    - 높은 이식성
    - 최소한의 커널 구조
    - 확장 용이
    - 소스 코드 공개
    - 프로그램 개발에 용이
    - 다양한 버전

### MS 계열

- DOS(Disk Operating System)
    - MS사에서 1981년 IBM-PC를 위해 개발
    - 단일 사용자. 메모리 관리 능력의 한계(주 기억 장치가 640KB)
- MS Windows
    - MS사의 다중 작업용 GUI 기반 운영 체제
    - Plug and Play. 네트워크 환경 강화
    - DOS용 응용프로그램과 호환
    - 불안정성
    - 풍부한 자원 소프트웨어(초창기)

이외 애플 ios 등등…

## (5) 운영 체제의 구조