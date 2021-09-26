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
  
