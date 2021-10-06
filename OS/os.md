# 운영체제 #
## OS 개론 ##
Operating system is a software that opreates a computer system
ex) windows, linux
Computer is a machine that process the information

**컴퓨터가 정보를 처리하는 방법**
- 정보의 최소 단위 : bit(binary digit)
- 정보의 처리 : 정보의 상태 변환
- Boolean Algebra : NOT, AND, OR
- 논리 게이트 : NOT, AND, OR, XOR, NAND, NOR
- 논리 회로 : VLSI, SOC
- 정보의 저장 : 플립-플롭
- 정보의 전송 : 데이터 버스
- 덧셈 : 가산기
- 뺄셈 : 2의 보수
- 실수 연산 : 부동 소수점 표현
  
**범용성(universality)**
- NOT, AND, OR 게이트 만으로 모든 계산을 할 수 있다
- NAND 게이트 만으로 모든 계산을 할 수 있다

**계산가능성(computability)**
- Turing-computable : 튜링머신으로 계산 가능한 것
- Halting Problem  튜링 머신으로 풀 수 없는 문제


**Operating System**
- a program running at all times on the computer
- to provide system services to application programs
- manage process, resource, user interface and so on
- manage a computer's hardware


![](./images/image1.jpg)

### Computer System Organization ###

*Modern Computer System*
- CPU, Disk Controller, USB Controller, Graphics adapter, Memory

*Bootstrap Progam*: First Program to run on the computer power on

*Interrupts*
- Hardware may trigger an interrupt at any time
- Sending a signal to CPU(CPU와 I/O device 간 의 통신방법)

*폰 노이만 구조*
- typical instruction-execution cycle
- fetches an instruction from memory
- stores that instruction in the *instruction register*
- The instruction is decoded, then instruction will be execcuted, result may be stored back in memory


![Storage Hierarchy](./images/image2.jpg)

*I/O structure*
- 보통 cpu 와 device가 interrupt, I/O request로 통신하고
- CPU가 메모리와 통신하지만
- device와 memory가 직접 통신하는 것을 DMA(Directed Memory Access)라고 한다

*Computer System Architecture*
- Symmetric Multiprocessing(SMP): 메모리는 하나이지만 메모리에 연결된 CPU가 여러개(CPU는 각각 register와 cache를 갖는다)
- Multi-core design : 하나의 CPU안에 여러개의 CPU core를 갖도록 구조(CPU core는 각각 register와 cache를 갖는다)
- Multiprocessing(Multitasking) 
    - CPU는 time sharing으로 일들을 자주 바꿔가며 처리한다(Concurrency)
    - CPU Scheduling : 어떤 프로세스가 끝나고나면 시스템은 실행준비가 된 프로세스들 중 어떤 프로세스를 실행할지 선택해야한다

### Operating System Operations ##
*1. User Mode*

*2. Kernel Mode*

프로그램이 오작동하는 것을 방지하기 위해 두 가지 모드가 있다.
user process(system call 호출, user process execution)가 진행중일 때는 user mode, execute system call을 처리할 때는 kernel 모드이다

### Virtualization ###
가상화는 하나의 컴퓨터를 여러개의 실행환경(execution environments)로 나누는 것을 가능하게 해준다.

*VMM(Virtual Machine Manager)* : VMware, XEN, WSL

### Operating System Services ###
OS는 프로그램을 실행할 환경을 제공한다.
- User Interface
- Program Execution
- I/O Operation
- File System Manipulation
- Communications
- Error Detection
- Resource Allocation
- Logging
- Protection and Security

## Process ##

- 프로세스는 실행중인프로그램이다
- 프로세스는 OS에서 unit of work 이다
- Process는 실행중이기 때문에 Stack 과 Heap이존재
- Program은 Data와 Text로만 이루어짐
- Stack은 지연변수, 임시 데이터 함수 등이 저장
- Heap은 프로세스 실행 중 동적으로 메모리가 할당되는 경우 저장됨

### Process State ###

프로세스는 여러 상태를 갖는다.
- New : 프로세스가 생성
- Running : 실행 중
- Waiting : 프로세스가 I/O operation 등 특정 이벤트로 인해 대기중(sleep 함수 등), 절대 프로세스가 실행될 수 없는 상황
- Ready : 프로세스가 CPU에 할당받기 대기중인 상태, 프로세스가 수행될 수 있는 상태
- Terminated : 프로세스가 종료됨

### PCB(Proicess Control Block) ###

프로세스에 대한 모든 정보를 담고 있는 구조체

- Process State
- Program Counter
- CPU Register
- CPU Scheduling

## CPU Scheduling ##

용어 정리

- Scheduling Criteria : 스케쥴링을 하는 기준
- CPU utilization: CPU 이용율(일한 시간/ 전체 시간)
- Waiting Time : 순수하게 Ready Queue에서 기다린 시간
- Response Time: 첫 번째 응답이 올 때 까지의 시간
- Average Waiting Time: 여러개의 프로세스의 평균 waiting time
   
### *Scheduling Algorithms* ###

- FCFS: First Come First Served
  - Convoy Effecct: 수행시간이 긴 프로세스가 수행시간이 짧은 프로세스 앞에 와서 AWT가 커지게 된다.
  - CPU Utilization이 낮아질 수 있다.
- SJF: Shortest Job First
  - 수행시간이 짧은 프로세스일수록 우선적으로 스케쥴링된다.
  - Nonpreemptive(비선점형) : CPU에 할당이 되면 해당 CPU burst를 끝내고 다음 프로세스가 들어온다.
  - preemptive(선점형) : CPU에 할당이 되어있더라도 새로 들어온 프로세스의 CPU burst time이 현재 수행중인 프로세스의 남은 CPU burst time보다 작으면 context-switching이 발생한다.
- Priority Scheduling
  - task에게 우선순위를 부여하여 우선순위가 높은 task를 먼저 CPU burst한다.
  - Preemptive, Nonpreemptive가 있다.
  - SJF는 CPU burst time에 따른 우선순위를 부여한 priority scheduling이다.
  - Aging 기법을 사용하여 수행되지 않는 프로세스는 계속해서 우선순위를 높여준다.
  - Static Priorty: 우선순위를 유지하는 방식으로 Real Time OS에서는 Static 방식을 사용
  - Dynamic Priorty: 우선순위가 변동되는 방식
- Round robin Scheduling
  - 각 프로세스는 정해진 시간만큼 CPU burst를 수행한다.
  - Response time 측면에서는 굉장히 좋다.
  - 프로세스는 CPU burst를 수행하고 남은 CPU burst가 있다면 Ready Queue의 마지막으로 보낸다.
  - 단점은 Queue가가 크면 FIFO와 비슷하고 Queue가 작으면 context switching이 자주 발생한다.
  


## DeadLock ##

- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태

- Resource(자원) : 하드웨어, 소프트웨어 등을 포함하는 개념(I/O device, CPU cycle, memory space, semaphore 등

프로세스가 자원을 사용하는 절차 
Request Allocate Use Release

Deadlock Example 1
- 시스템에 2개의 tape drive가 있다

- 프로세스 p1, p2 각각이 하나의 tape drive를 보유한 채 다른 하나를 기다리고 있다.

Deadlock Example 2

- Binary semaphores A and B 가있고 P0은 A, B 순으로 자원을 요청, 
- P1은 B, A순으로 요청

- P0 : P(A) P(B)
- P1 : P(B) P(A)

데드락의 발생조건

1. Mutual exclusion(상호배제) : 매 순간 하나의 프로세스만이 자원을 사용할 수 있음
2. No preemption(비선점) : 프로세스 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음
3. Hold and wait(보유 대기) : 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 가지고 있음
4. Circular wait(환형 대기) :자원을 기다리는 프로세스 간에 사이클이 형성된 경우

Resource-Allocation Graph
Vertex
Process P={P1, P2, … Pn}
Resource R={R1, R2, .. Rm}
Edge
Request edge Pi->Rj
Assignment edge Rj -> Pi

그래프에 사이클이 없으면 deadlock이 아니다
그래프에 cycle이 있으면
-if only one instance per resource type, then deadlock
-if several instances per resource type, possibility of deadlock

Deadlock 의 처리방법

1. Deadlock Prevention
자원할당시 Deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것
2. Deadlock Avoidance
자원요청에 대한 부가적인 정보를 이용해서 deadlock의 가능성이 없는 경우에만 자원을 할당
시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원 할당
3. Deadlock Detection and Recovery
Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock 발견 시 recover
4. Deadlock Ignorance
Deadlock을 시스템이 책임지지 않음, Unix 등 대부분의 OS가 채택


*Deadlock Prevention
Mutual Exclusion : 공유해서는 안되는 자원의 경우 반드시 성립해야 함
(자원자체의 성격이라 이걸 이용할 수는 없다)
Hold and Wait : 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다

1. 프로세스 시작 시 모든 필요한 자원을 할당 받게 하는 방법
2. 자원이 필요한 경우 보유자원을 모두 놓고 다시 요청
(소모값이 크다)
   - No Preemption – (빼앗을 수 없는 자원들이 있다)
프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작된다
State를 쉽게 Save하고 restore할 수 있는 자원에서 주로 사용(CPU, memory)
   - Circular Wait:
모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
예를 들어 순서가 3인 자원 Ri을 보유중인 프로세스 순서가 1인 자원 R_j를 할당 받기 위해서는 우선 Ri를 release해야 한다.
결론 : Utilization 저하, throughput 감소, starvation 문제

### Deadlock Avoidance ###

- 자원 요청에 대한 부가정보를 이용해서 자원할당이 deadlock으로부터 안전(safe)한지를 동적으로 조사해서 안전한 경우에만 할당
- 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원 별 최대 사용량을 미리 선언하도록 하는 방법
- Safe State : 시스템 내의 프로세스들에 대한 safe sequence 가 존재하는 상태
- Safe Sequence
-프로세스의 sequence가 safe하려면 pi의 자원 요청이 가용 자원 + 모든 pj(j<i)의 보유자원에 의해 충족되어야 함
조건을 만족하면 Pi의 자원 요청이 즉시 충족될 수 없으면 모든 pj가 종료될 때까지 기다린다
Pi-1이 종료되면 pi의 자원요청을 만족시켜 수행한다.
시스템이 unsafe state에 있으면 possibility of deadlock
Deadlock avoidance : 시스템이 unsafe state에 들어가지 않는 것을 보장

2가지 경우의 avoidance

1) Single instance per resource types : Resource Allocation Graph Algorithm 사용(자원당 프로세스 하나)
Claim Edge Pi -> Rj
프로세스 Pi가 Rj를 미래에 요청할 수 있음(점선)
해당 자원 요청시 request edge로 바꾸고(실선)
Rj 가 release 되면 assignment edge는 다시 claim edge로 바뀐다
Request edge의 assignment edge 변경 시 cycle 이 생기지 않는 경우에만 요청 자원을 할당한다(Claim edge 포함)
Cycle 생성 여부 조사 시 프로세스 수가 n일 때 O(n^2)이 걸린다.
2) Multiple instances per resource types : Banker’s Algorithm
테이블 형태로 만든다.
P0 P1 P2 P3 P4
A(10) B(5) C(7) (instance개수)
Allocation(이미 할당된 자원)
0 1 0
2 0 0
3 0 2
2 1 1
0 0 2
	
MAX(최대로 요청할 자원)
	7 5 3
	3 2 2
	9 0 2
	2 2 2
	4 3 3
	Need(Max-Allocation)
	7 4 3
	1 2 2
	6 0 0
	0 1 1
	4 3 1
	Available(남아있는 자원)
	3 3 2 P1 P3만 가능, 이후 Release 된 자원으로 P4 P2 P0 순으로 실행 가능하므로 safe state

### Deadlock Detection and recovery ###

-Deadlock Detection Part
Resource type 당 single instance인 경우
Resource Allocation graph에서 cycle이 곧 deadlock을 의미
Resource type 당 multiple instance 인 경우
Banker’s algorithm과 유사한 방법 활용
-Wait-for graph알고리즘
Resource type 당 single instance인 경우
Wait-for graph : 자원할당그래프의 변형
프로세스만으로 node 구성
Pi가 가지고 있는 자원을 Pk가 기다리는 경우 Pk->Pi(edge)
-Algorithm
Wait-for graph에 사이클이 있는지를 주기적으로 조사(O(n^2))
자원의 최대 사용량을 미리 알 필요가 없음 -> 그래프에 점선이 없음
Banker’s algorithm의 변형
Allocation , Request, Available
(Max가 아니라 Request이다)
되는대로 최대한 준다,
그리고 모든 프로세스가 끝날 수 있는 지 확인한다.

-Recovery Part
Process termination : 
Abort all deadlocked processes
Abort one process at a time until the deadlock cycle is eliminated
Resource Preemption
비용을 최소화할 victim 선정
Safe state로 rollback하여 process를 restart
Starvation 문제
-동일한 프로세스가 계속해서 victim으로 선정되는 경우
-cost factor에 rollback 횟수도 같이 고려
*Deadlock ignorance
-데드락은 매우 드물게 발생하므로 deadlock에 대한 조치자체가 더 큰 overhead일 수 있다
시스템에 deadlock이 발생한 경우 시스템이 비정상적으로 작동하는 것을 사람이 느낀 후 직접 process를 죽이는 방법 등으로 대처
Unix, Windows 등 대부분 범용 OS가  채택


## Main Memory ##

- 프로세스는 실행중인 프로그램이다.
  - set of instructions kept in a main memory
- 메모리는 large array of bytes 이고 각각의 address가 있다.

### Memory Space ###

- 각각의 프로세스가 독립적인 메모리 공간을 갖도록 보장해야 한다. 

### Memory Management Strategies ###

- Address Translation: Virtual Address를 Physical Address로 변환

### Background ###

- Memory Management Unit(MMU)
  - Address Translation은 매우 빨라야 하므로 하드웨어(MMU)가 필요
  - MMU는 TLB와 Register들로 구성됨


### Basic Hardware ###

- Memory Protection을 위해 base와 limit register 사용
- base register는 Process의 시작주소
- limit register는 Process의 크기
- base <= 새롭게 할당되는 프로세스 주소 < base+Limit 


## Paging ##

- Continuous Allocation의 특징중 하나는 연속적으로 프로세스가 할당되는 부분이다. Paging에서는 연속적이지 않다.
- Logical Memory는 정해진 크기의 Block Page로, 정해진 크기의 Block인 Frame으로 나눌 수 있다.
- n개의 page를 갖는 프로그램을 구동하기 위해서는 n개의 frame이 존재해야 한다.
- Memory에 Page Table을 따로 두고 Logical Memory를 Physical Meomory로 변환한다.
- Paging에서는 Process가 연속적일 필요가 없다.

### Paging Basic Method ###

- Page Number(p): Page Table의 Index, 각 Page별 Physical Memory에서 시작주소를 포함한다.
- Page Offset(d): Physical Addres로 변환된다.

### Paging-Fragmentation ###

- External Fragmentation: 연속적으로 저장되지 않기 때문에 발생하지 않음
- Internal Fragmentation: 프로세스의 size가 있고 Page size가 있을 때, 낭비되는 메모리
- Page는 보통 4KB or 8KB

## Page Table Structure ##

- Hierarchial Paging
- 계층을 통한 Paging 기법으로 테이블의 크기를 줄이기 위한 방법
- Linux에서는 3-level page table을 사용

## Virtual Memory ##

- 현재 수행되는 Process가 메모리에만 존재한다는 보장이 없다.
- 수행되는 프로그램의 일부분만 메모리로 올라간다.
- Logical Address Space는 Physical Address Space보다 크거나 같아야 한다.
- Page 를 교체해야 할 필요가 있다.

### Demanding Page ###

- Page가 필요할 때만 Memory에 올린다.
- Page가 필요하지 않다면 Physical Memory에 로드되지 않는다.


### Page Fault Handling ###

- OS는 PCB에 존재하는 Table을 보고 Invalid Reference인지, 메모리에 Page가 존재하는지 확인한다.
- Invalid reference라면 abort시키고, 메모리에 Page가 존재하지 않는 것이라면 비어있는 Frame을 찾아낸다.
- Frame에 Page를 swap in 하고 Process는 끝날때 까지 waiting state이다.
- Page Fault를 발생시킨 명령어를 다시 실행한다.

## Page Replacement ##

- 비어있는 Frame이 존재하지 않을 때 Replacement가 필요하다.
- Page Fault를 최소한으로 줄여야 한다.

### Page Replacement Algorithm ###

- FIFO Algorithm
- Optimal Algorithm
- LRU Algorithm
  - Counter Implementation : 페이지에 접근한 시간을 기록
  - Stack Implementation : 페이지에 참조되면 Stack의 top으로 이동
- LRU approximation algorithm
- Counter Based Algorithms: 시간개념없이 호출횟수기반으로 교체
- *Belady's Anomaly* : 메모리의 크기가 증가하였지만 Page Fault가 늘어나는 현상

