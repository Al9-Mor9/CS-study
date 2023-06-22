# [6] Process Synchronization

# 0. 컴퓨터 시스템 내에서의 데이터 접근 패턴

(1) 컴퓨터의 어떤 스토리지(Storage-Box)에 데이터가 저장되어 있음

(2) 데이터를 읽어옴

(3) 연산(Execution-Box)

(4) 연산 결과를 재저장함

| E-box | S-box |
| --- | --- |
| CPU | Memory |
| 컴퓨터 내부 | 디스크 |
| 프로세스 | 프로세스의 주소 공간 |

# 1. Race Condition

S-box를 공유하는 E-box가 여럿 있는 경우 **Race  Condition**의 가능성이 있음

- 메모리를 공유하는 여러 CPU(멀티프로세서 시스템에서)
- 주소 공간을 공유하는 여러 프로세스
    - shared-memory의 경우
    - 커널 내부 데이터에 접근하는 루틴의 경우
        - 예) 커널 모드 수행 중 인터럽트로 커널 모드의 다른 루틴 수행 시

## OS에서의 race condition

### **Kernel 수행 중 인터럽트 발생시**

- 커널모드로 수행 중 인터럽트가 발생해 인터럽트 핸들러가 수행됨
    - 양쪽 다 커널 코드이므로 커널 주소 공간을 공유함
    - 현재 수행 중인 것과 인터럽트 핸들러가 동일한 주소 공간에 접근하려 하는 경우 경쟁 상태 발생
- 해결
    - 중요한 변수 값을 건드리는 동안에는 인터럽트가 들어와도 핸들러로 넘어가지 않고 작업이 끝난 후에 핸들러로 넘어가기

### **유저 프로세스가 system call을 해 kernel mode로 수행 중인데 context switch가 일어나는 경우**

- 두 프로세스의 주소 공간에는 data sharing이 없음
    - 그러나 system call을 하는 동안에는 커널 주소 공간의 데이터를 공유하게 됨
    - 이 작업 중 context switch로 다른 프로세스가 CPU를 선점하려하면 race condition 발생
- 해결
    - 커널 모드로 수행 중일 때에는 CPU를 다른 프로세스가 선점하지 못하도록 하고, 커널 모드에서 사용자 모드로 돌아갈 때 넘겨주기.

### **멀티프로세서에서 공유 메모리 내의 커널 데이터에 접근**

- 여러 프로세서가 메모리의 동일한 곳에 동시에 접근하려 하는 경우
- Interrupt enable/disable로 해결 불가
- 해결
    - 방법 1) 한 번에 하나의 CPU만이 커널에 들어갈 수 있게 함
        - CPU가 여럿 있어도 커널에 접근할 수 있는 것은 단 하나
        - 비효율적
    - 방법 2) 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 lock/unlock을 설정

## Process Synchronization 문제

- 공유 데이터의 동시 접근은 데이터의 불일치 문제를 발생시킬 수 있다
- 일관성의 유지를 위해서는 협력 프로세스 간의 **실행 순서를 정해주는 메커니즘**이 필요하다
- Race Condition
    - 여러 프로세스들이 동시에 공유 데이터에 접근하는 상황
    - 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라질 수 있다.
- Race Condition을 막기 위해서 concurrent process는 동기화되어야 한다.

### Critical-Section Problem

- n개의 프로세스가 공유 데이터를 동시에 사용하고자 하는 경우, 각 프로세스의 code segment에는 공유 데이터에 접근하는 코드인 ***critical section***이 존재
- 한 프로세스가 critical section에 있을 때, 다른 모든 프로세스는 critical section에 들어갈 수 없어야 한다.

### 프로그램적 해결법의 충족 조건

- Mutual Exclusion
    - 한 프로세스가 critical section 부분을 수행 중이면 다른 모든 프로세스들은 그들의 critical section에 들어가면 안 된다.
- Progress
    - 아무도 critical section에 있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있으면 들어가게 해줘야 한다
- Bounded Waiting
    - 프로세스가 critical section에 들어가려 요청한 후로부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 한다.

☝️ 단, 모든 프로세스의 수행 속도는 0보다 크다고 가정하고, 프로세스들 간의 상대적 수행 속도는 가정하지 않는다.
