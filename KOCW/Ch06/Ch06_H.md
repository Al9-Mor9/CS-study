# 6장 Process Synchronization

### 데이터의 접근

![Untitled](src_H/Untitled.png)

### Race Condition

![Untitled](src_H/Untitled%201.png)

### OS에서 race condition은 언제 생기는가?

1. kernel 수행 중 인터럽트 발생 시
2. Process가 system call을 하여 kernel mode로 수행 중인데 context switch가 일어나는 경우
3. Multiprocessor에서 shared memory 내의 kernel data

### interrupt handler VS kernel

![Untitled](src_H/Untitled%202.png)

### Preempt a process running in kernel

![Untitled](src_H/Untitled%203.png)

- 두 프로세스의 address space 간에는 data sharing이 없음
- 그러나 sysemcall을 하는 동안에는 kernel address space의 data를 access하게 됨(share)
- 이 작업 중간에 CPU를 preempt해가면 race condition 발생

### If you preempt CPU while in kernel mode

![Untitled](src_H/Untitled%204.png)

- 해결책
    - 커널 모드에서 수행 중일 때는 CPU를 preempt하지 않음.
    - 커널 모드에서 사용자 모드로 돌아갈 때 preempt

### OS에서의 race condition

![Untitled](src_H/Untitled%205.png)

- 어떤 CPU가 마지막으로 count를 store했는가? → race condition
- multiprocessor의 경우 interrupt enable/disable로 해결 불가능

해결방법

1. 한 번에 하나의 CPU만이 커널에 들어갈 수 있게 하는 방법
2. 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 lock / unlock을 하는 방법

### Process Synchronization 문제

- 공유 데이터(shared data)의 동시 접근(concurrent access)은 데이터의 불일치 문제(inconsistency)를 발생시킬 수 있다
- 일관성(consistency) 유지를 위해서는 협력 프로세스(cooperating process)간의 실행순서(orderly execution)를 정해주는 메커니즘 필요
- **Race Condition**
    - 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
    - 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐
- race condition을 막기 위해서는 concurrent process는 동기화(synchronize)되어야 한다

### The Critical-Section Problem

- n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
- 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 ***ciritical section***이 존재
- Problem
    - 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 한다
    
    ![Untitled](src_H/Untitled%206.png)