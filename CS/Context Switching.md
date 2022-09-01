# Context Switching

## 컴퓨터가 하나의 Task만 처리할 수 있다면?

해당 Task가 끝날때까지 다음 Task는 대기해야한다.

## 동시에 처리하는것처럼 보이기 위해 사용

Context Switching이란 CPU가 한 개의 Task(Process/Thread)를 실행하고 있는 상태에서 Interrupt 요청에 의해 다른 Task로 실행이 전환되는 과정이다. <br> 이 때, 기존의 Task 상태 및 Register 값들에 대한 정보(Context)를 저장하고 새로운 Task의 Context 정보로 교체된다. <br><br>

여기서 Context란, CPU가 다루는 Task(Process / Thread)에 대한 정보로, 대부분의 정보는 Register에 저장되며 PCB(Process Control Block)로 관리된다. <br><br>
![image](https://user-images.githubusercontent.com/89785501/187870344-087443c1-b323-4034-9def-a7f93f7ae24f.png)

- Process State : 프로세스 상태(Create, Ready, Running, waiting, terminated)
- Process Counter : 다음 실행할 명령어의 주소값
- CPU Registers: accumulator, index register, stack pointers, general purpose registers.

여기서 Process와 Thread를 처리하는 Context Switching은 조금 다른데, PCB는 OS에 의해 스케줄링되는 Process Control Block이고 Thread는 Process 내의 TCB(Task Control Block)라는 내부 구조를 통해 관리된다. <br><br>

Task의 PCB 정보는 Process Stack, Ready Queue 라는 자료구조로 관리가 되며, Context Switching시 PCB의 정보를 바탕으로 이전에 수행하던 작업 혹은 신규 작업의 수행이 가능하게 된다. <br><br>

PCB는 주로 다음과 같은 정보를 저장한다.

1. PRocess State: 프로세스 상태
2. Program Counter: 다음에 실행할 명령어 Address
3. Register: 프로세스 레지스터 정보
4. Process number: 프로세스 번호

### Context Switching Cost

Context Switching시, Context Switching을 수행하는 CPU는 Cache와 Memory Mapping을 초기화하는 작업을 하기 때문에 다른 작업을 하지 못한다.<br> 그래서 잦은 Context Switching은 성능 저하를 가져온다. <br><br>

### Process vs Thread

일반적으로 멀티 프로세스를 통해 PCB를 Context Switching하는 것보다 멀티 스레드를 통해 TCB를 Context Switching하는 비용이 더 적다. <br>

- Thread는 Stack영역을 제외한 모든 메모리를 공유하기 때문이다. 그래서 Context Switching 발생시 Stack영역만 변경하면 된다.
  <br><br>

주로 Context Switching은 Interrupt에 의해 발생되는데, 하드웨어를 통한 I/O 요청이나, OS / Driver 레벨의 Timer 기반 Scheduling에 의해 발생한다.

#### 출처

- https://jins-dev.tistory.com/entry/%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8-%EC%8A%A4%EC%9C%84%EC%B9%98Context-Switching-%EC%97%90-%EB%8C%80%ED%95%9C-%EC%A0%95%EB%A6%AC
- https://nesoy.github.io/articles/2018-11/Context-Switching
