# [4] Process Management

# 1. 프로세스 생성과 종료

## (1) 프로세스 생성 (Process Creation)

- 부모 프로세스가 자식 프로세스를 생성
    - 기본적으로는 복제 생성
        - ********Copy-on-write, COW********
            - 효율성을 위해서 생성되었을 때 바로 복제하지는 않을 수 있다.
            - 생성되었을 때에는 부모의 PC만 복제해서 가지고 있다가, 부모와 자식의 context가 서로 달라지려 할 때(write가 발생할 때) 부모의 context를 복사한다.
    - 자식은 부모의 공간을 복사한다(binary, OS data)
        - 자식은 그 공간에 새로운 프로그램을 올린다.
    - 프로세스의 생성은 사용자 레벨에서 이루어지는 게 아니라, 시스템 콜을 통해 커널 레벨에서 이루어진다.
        - 유닉스의 예
            - `fork()` 시스템 콜이 새로운 프로세스를 생성
                - 부모를 그대로 복사하고
                - 주소 공간을 할당한다.
            - `fork()` 다음에 이어지는 `exec()` 시스템 콜을 통해 새로운 프로그램을 메모리에 올린다.
- 프로세스는 트리(계층 구조)를 형성한다.
- 프로세스는 자원을 필요로 한다
    - 운영체제로부터 받는다.
    - 부모와 공유한다
- 자원의 공유
    - 일반적으로 부모와 자식은 자원을 공유하지 않고 CPU 활용에 대해 경쟁한다.
        - 부모와 자식이 모든 자원을 공유하는 모델
        - 일부를 공유하는 모델
        - 전혀 공유하지 않는 모델
- 수행(Execution)
    - 부모와 자식은 공존하며 수행되는 모델
    - 자식이 종료(TERMINATE)될 때까지 부모가 기다리는(WAIT, BLOCKED) 모델

### 프로세스 생성 관련 시스템 콜

- `fork()` 시스템
    
    ```c
    /*
    	NAME : fork - create a child process
    	RETURN VALUE : 부모 프로세스에게는 새로 생성된 자식 프로세스의 PID, 자식 프로세스는 0
    							 : fork 실패시 부모 프로세스에서 -1을 리턴하고, 자식 프로세스는 생기지 않음
    	***************fork는 한 번 call되지만 두 번 return한다!***************
    */
    
    int main(){
    	int pid;
    	pid = fork(); //fork가 되면 이 아래부터 실행이 된다. (main이 재시작되는 게 아니라)
    	if (pid == 0) {
    		printf("Hello. I am children!\n");
    	}
    	else if (pid > 0) {
    		printf("Hello, I am parent!\n");
    	}
    }
    ```
    
- `exec()` 시스템 콜
    
    ```c
    /*
    	NAME : execl, execlp, execle, execv, execvp, execvpe
    	RETURN VALUE : 에러가 발생했을 때만 -1을 리턴하고, 나머지의 경우 리턴하지 않는다. 
    */
    int main(){
    	int pid;
    	pid = fork();
    
    	if (pid == 0) {
    		printf("Hello, I am child! Now I'll run date\n");
    		execlp("/bin/date", "/bin/date", (char*) 0);
    	}
    	else if (pid > 0){
    		printf("Hello, I am parent!\n");
    	}
    }
    ```
    
- `wait()` 시스템 콜
    
    ```c
    /*
    	프로세스 A가 wait() 시스템 콜을 호출하면
    		커널은 child가 종료될 때까지 프로세스 A를 sleep시킨다(BLOCK 상태)
    		child가 종료되면 커널은 프로세스 A를 깨운다(READY)
    */
    
    int main(){
    	int childPID;
    	/*
    		이것 저것하기
    	*/
    	childPID = fork();
    	
    	if (childPID == 0){
    		/*
    			child process에서 할 일 하기
    		*/	
    	}
    	else {
    		wait();//부모 process는 자식 process가 종료될 때까지 기다림
    	}
    	/*
    		이것 저것하기
    	*/
    }
    ```
    

## (2) 프로세스 종료(Process Termination)

### **자발적 종료**

- 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려준다. (`exit()`)
    - 프로세스가 끝나면, 프로그래머가 명시적으로 `exit()` 시스템 콜을 적어놓지 않더라도 컴파일러가 자동으로 `exit()` 시스템 콜을 넣어준다.
    - `exit()` 이 콜되면
        - 자식은 부모에게 output data를 보낸다.
        - 프로세스의 각종 자원들이 운영체제에게 반납된다.

### 비자발적 종료

- 부모가 프로세스가 자식의 수행을 강제로 종료시킬 수 있다. (`abort()`)
    - 자식이 할당 자원의 한계치를 넘어설 때
    - 자식에게 할당된 태스크가 더 이상 필요하지 않을 때
    - 키보드로 kill, break등을 친 경우
    - 부모가 종료(`exit()`)되는 경우
        - 운영체제는 부모 프로세스가 종료되는 경우 자식이 더 이상 수행되도록 두지 않는다.
        - 단계적인 종료
            - 트리 구조로 따진다면 리프노드부터 루트노드까지의 순서로 종료된다

# 2. 프로세스 간 협력

### 독립적 프로세스(Independent Process)

- 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 한 프로세스는 다른 프로세스의 수행에 영향을 미치지 못한다.

### 협력 프로세스(Cooperating Process)

- 프로세스 협력 메커니즘을 통해 한 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있다.

### 프로세스 간 협력 메커니즘(IPC, Interprocess Communication)

- 프로세스 간의 협력을 가능케 하는 데에는 여러 방법이 있다.
    - 메시지 전달 방법(message passing)
        - process 사이에 공유 변수를 일체 사용하지 않고 통신하는 시스템
            - process끼리 메시지를 주고 받음으로써 협력
            - 하지만 process는 서로 독립적이므로 **커널을 통해서 메시지를 전달해야 함**
        - **Direct Communication**
            - 통신하려는 프로세스의 이름을 명시적으로 표시
        - **Indirect Communication**
            - mailbox 또는 port를 통해 메시지를 간접적으로 전달. 누가 받을지는 명시하지 않음
    - 주소 공간을 공유하는 방법
        - shared memory
            - 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있다.
            - 물리적 메모리에 매핑될 때 일부 주소 공간은 공유되도록 매핑
                - 처음 주소 공간을 공유할 때에는 kernel의 도움을 받지만, 이후 협력에서는 kernel을 거치지 않고서도 가능
        - thread
            - thread는 사실 하나의 프로세스에 CPU 수행 단위가 여럿 있는 것이므로 프로세스 간 협력으로 보기는 어렵지만, 동일한 process를 구성하는 thread 간에는 주소 공간을 공유하므로 협력이 가능
