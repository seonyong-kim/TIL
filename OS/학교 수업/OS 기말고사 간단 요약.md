# 6. Synchronization Tools(2)
## Monitor
- Motivation
  - Semaphore는 unstructured construct
  - synchronization bugs(race condition) 발생하기 쉽다
  - Too low-level
- process synchronization를 위해 편리하고 효과적인 high-level abstraction이 필요하다
  - High-level mutual exclusion semantics
    - 한번에 하나의 thread만 이구조에서 활성화 상태이다
  - Locks are hidden
    - critical section에 들어가거나 나갈 때 자동으로 locks과 unlocks을 한다.
   
### Monitor
- synchronization을 위한 편리하고 효과적인 mechanism을 제공하는 high-level abstraction
- Abstract data type (ADT)으로, 내부 변수는 해당 절차 내의 코드만 접근 가능(procedure들만 내부에 접근 가능)
- 한 번에 하나의 process만 monitor 내에서 활성 상태일 수 있다.
- 하지만, 일부 synchronization 방식들을 모델링하기에는 충분히 강력하지 않다.
- monitor encapsulates
  - data structures 공유
  - shared data에 작용하는 procedures
  - 그 procedures 호출하는 concurrent threads 간의 Synchronization
```C
monitor monitor-name {
 // shared variable declarations
 …
 procedure P1 (…) { … }
 …
 procedure Pn (…) { … }
 Initialization code (…) { … }
}
 // + conditional variable(순서 동기화를 위한 장치)
```
구조를 보면 Class{변수 함수 초기화}로 동시에 호출해도 하나의 thread만 사용가능하다.
- Monitor with condition variables <br>
![image](https://github.com/user-attachments/assets/32dc9116-d031-4478-9da5-69193eb2ea80)
- Condition variables (waiting queues(x,y를 queue라고 생각)) in monitor
  - condition x, y; 순서 동기화를 위해 사용
- condition variable에 가능한 두 operations
  - x.wait()- 이 연산을 호출한 프로세스는 x.signal()이 호출될 때까지 중단됨
  - x.signal()- x.wait()를 호출한 프로세스 중 하나를 재개시킴 (있는 경우)
    - 해당 변수에 대해 x.wait()가 없으면, x.signal()은 아무 효과 없음
- Monitor - Code Example<br>
![image](https://github.com/user-attachments/assets/f5556b10-f1bf-4257-bec4-f4d797c0ba8d)

- 조건 변수 선택 사항
  - 프로세스 P가 x.signal()을 호출하고, 프로세스 Q가 x.wait()에서 중단된 상태일 때
    - Q와 P는 동시에 실행될 수 없다. Q가 재개된다면 P는 대기해야 해야되기 때문.
  - 선택지
    - Signal and wait - P는 Q가 모니터를 나가거나 다른 조건을 기다릴 때까지 기다린다.
    - Signal and continue - Q는 P가 모니터를 나가거나 다른 조건을 기다릴 때까지 기다린다.
    - Concurrent Pascal에서 구현된 모니터는 타협안 사용한다.
      - signal을 호출한 P는 즉시 모니터를 떠나고, Q가 재개된다.
  
### Monitor Implementation (signal and wait)
- Variables in each monitor
  - semaphore monitor_lock; // initially = 1
  - semaphore sig_lock;  // initially = 0
  - int sig_lock_count = 0; // signaler queue에 잠든 signal의 수
  - monitor바깥에 queue2개 존재
    1. entry queue
    2. signaler queue(signal 날리고 기다리는 큐)
- Each procedure F will be replaced by
```C
wait(monitor_lock); // 전통적인 세마포어 -> key 획득위해 기다림
  …
  body of F;
  …
if (sig_lock_count > 0) // 나올때 key를 준다.
 signal(sig_lock); // signal 날린것에 우선권(signal queue에 있는것)
else
 signal(monitor_lock); // entry queue에 있는것 중 하나에게 없으면 monitor_lock 1증가 
```

### Monitor Implementation
- Variables for implementation of condition variables
  - int x_count; \\ initially = 0  x에서 기다리는 프로세의 수
  - semaphore x_sem; \\ initially = 0  x에 대한 세마포어<br>
![image](https://github.com/user-attachments/assets/ed9ff3cf-d051-4936-9ed4-5eafb6c4bc0d)

### Monitor Implementation
![image](https://github.com/user-attachments/assets/f83b9828-aebd-4f0e-87c6-11f2eeffbea2)

-----------------------------------------------------------------------------------------------------------

# 8. Deadlocks
## System Model
- System은 resources로 구성
- 각 resource type Ri는 Wi 개의 instance를 가진다.(resource마다 다르며 자원의 수라고 생각하면 된다)
- 각 process는 resource를 다음과 같은 순서로 사용한다:
  - 요청(request) / 사용(use) / 해제(release)
 
## Deadlock
- deadlock정의 : 일련의 processes가 서로 다른 process가 가지고 있는 무언가를 기다리는 상황으로, 모든 프로세스가 대기 중이므로, 아무도 다른 프로세스가 기다리는 것을 제공할 수 없다.

## Deadlock Characterization
- Deadlock 다음 네 가지 조건이 동시에 만족될 때 발생할 수 있다(무조건 발생은 아니다)
  - Mutual exclusion: 한 번에 하나의 프로세스만 자원을 사용할 수 있다.
  - No preemption: 할당된 자원은 강제로 빼앗을 수 없다.
  - Hold and wait: 적어도 하나의 자원을 보유한 프로세스가 다른 자원을 기다리고 있다.
  - Circular wait: 자원을 가진 프로세스들이 서로 다른 자원을 기다리며 원형 형태의 대기 관계를 이룬다.(순환고리가 필요하다)
   
## Deadlock: Resource-Allocation Graph 
- Process <br>

![image](https://github.com/user-attachments/assets/441addfb-023d-4359-8fd9-5706c1421739)
- Resource Type with 4 instances. 즉, instance개수 <br>

![image](https://github.com/user-attachments/assets/ce2e8d41-8b99-45ed-9311-9c860c95794f)
- Pi requests instance of Rj. resource를 원한는 상태 <br>

![image](https://github.com/user-attachments/assets/76ddf40f-d906-4f8b-ac85-af76a5483c30)
- Pi is holding an instance of Rj resource 획득 경우 <br>

![image](https://github.com/user-attachments/assets/4262a470-fdce-461f-810c-67447998e0f9)
- Example <br>

![image](https://github.com/user-attachments/assets/2c100a1b-8688-4b79-90a8-674556a6cade)

## Deadlock Handling
- deadlock problem해결 2가지 카테고리
  - 교착 상태 예방/회피 (Deadlock prevention/avoidance)
    - deadlocks이 절대 발생하지 않도록 하지만, 자원 활용 효율이 떨어질 수 있다.
  - 교착 상태 탐지 및 복구 (Deadlock detection and recovery)
    - system이 deadlocked인지 감지하고, 하나 이상의 processes를 종료해야 한다.

## Deadlock Prevention
  - No mutual exclusion: 여러 process가 동시에 같은 공유 자원에 접근할 수 있다 -> 말이 안된다.
  - Preemption: 어떤 process가 자원을 보유한 상태에서 새로운 resources을 요청했지만 즉시 할당될 수 없다면, 현재 보유 중인 모든 자원을 반납한다. <br>
    - Process는 이전에 보유했던 resources과 새로 요청한 자원을 모두 다시 획득할 수 있을 때에만 다시 시작됨
  - No hold and wait: 모든 자원을 한꺼번에 얻거나, 모두 가능할 때까지 기다린다 -> 말이 안됨 -> 언제 어떤게 필요할지 예측 어렵다. 
    - process가 resource를 요청할 때, 다른 resources을 보유하고 있지 않음을 보장해야 한다.
    - process 입장에서 불편하다.
      - 필요한 resources을 예측하기 어려울 수 있어, resources 활용률이 낮아지거나 starvation 상태가 발생할 수 있다.
  - No circular wait: 생길만한 부분예측해서 막아본다.(실용성이 떨어진댜)
    - 순서화되거나 계층적인 방식으로 자원을 요청하게 한다. -> 모든 processes는 동일한 자원 요청 순서를 따라야 한다. <br>
    
![image](https://github.com/user-attachments/assets/7f684546-318b-4519-bd75-c6cea1d691a9)

## Deadlock Avoidance
- system이 사전에 어떤 information available를 가지고 있어야 함을 요구한다.
  - 가장 간단하고 유용한 model은 각 process가 필요할 수 있는 각 자원 유형의 최대 수량을 미리 선언하도록 요구한다.
  - deadlock-avoidance algorithm은 자원 할당 상태를 동적으로 검사하여 circular-wait condition이 발생하지 않도록 보장한다.
  - Resource-allocation state는 사용 가능한 resources 수, 이미 할당된 resources 수, 각 processes의 최대 요구량으로 정의된다.
  
## Banker’s Algorithm (1)
- 용어 
  - P = {P1, P2, …, Pn} : 시스템에 있는 모든 processes의 집합
  - R = {R1, R2, …, Rm} : 시스템에 있는 모든 resource types의 집합
- Safe state: system이 각 process에 대해 최대 필요 resources까지 어떤 순서로든 자원을 할당할 수 있으며, 그 과정에서 deadlock가 발생하지 않는 상태
  - safe state에서는 반드시 Safe sequence <P1, P2, …, Pn> 가 존재해야 한다.
    - Pi가 아직 요청할 수 있는 자원은 다음으로 충족될 수 있어야 한다: <br>
    현재 사용 가능한 resources + Pi보다 먼저 있는 모든 Pj (j < i)가 보유하고 있는 resources
      - 만약 Pi가 필요한 자원을 당장 얻을 수 없다면, Pi는 모든 Pj가 종료할 때까지 기다릴 수 있음
      - Pj가 종료되면, Pi는 필요한 자원을 얻고 실행한 후, 자원을 반환하고 종료함
      - Pi가 종료되면, Pi+1이 필요한 자원을 얻고 실행할 수 있음, 이런 식으로 계속됨
- Unsafe state: 교착 상태로 이어질 가능성이 있다. -> Safe sequence가 존재하지 않는다.

## Banker’s Algorithm (2)
- 각 process는 사전에 자원의 최대 사용량(maximum use)을 선언해야 한다.
- process가 resource을 요청할 때, 대기(wait)해야 할 수도 있다.
- process가 모든 resources을 할당받으면, 반드시 유한한 시간 내(finite amount of time)에 자원을 반환해야 한다.
- 표기법 (Notation)
  - n: 프로세스 수, m: 자원 유형 수
  - Available[1:m]: 각 자원 유형별로 현재 사용 가능한 자원 수
  - Max[1:n, 1:m]: 각 프로세스가 최대 필요로 하는 자원 수
  - Allocation[1:n, 1:m]: 각 프로세스에 현재 할당된 자원 수
  - Need[1:n, 1:m]: 각 프로세스가 추가로 필요로 하는 자원 수
  - 관계식: Max[i, j] = Allocation[i, j] + Need[i, j]

## Banker’s Algorithm (3) - Safety Algorithm(현재 safe state인지?)
- Example
  - 5 processes: P0, P1, P2, P3, P4
  - 3 resources: A (10 instance), B (5 instances), and C (7 instances)
- Snapshot at time T0 <br>
![image](https://github.com/user-attachments/assets/15a8cc56-6cc8-4ae4-a024-ba1a2a4706cb)
- Snapshot at time T1 <br>
![image](https://github.com/user-attachments/assets/b5b012c5-4acf-4530-a148-5a289ac88d37) <br>
p3,p4선택은 상관없다.
- Snapshot at time T2 <br>
![image](https://github.com/user-attachments/assets/30cb349d-e6f8-465a-8fe9-85c0f7fd9e7e)
- Snapshot at time T3 <br>
![image](https://github.com/user-attachments/assets/a5e2b78c-5b39-4c26-b708-2cdeab691e54)
- Snapshot at time T4 <br>
![image](https://github.com/user-attachments/assets/a437880b-e108-4c2c-8a49-6919d6cf0c2a)

## Banker’s Algorithm (4) - Safety Algorithm(의사 코드)
![image](https://github.com/user-attachments/assets/f9acea49-25db-4537-9468-961921d86863)

## Banker’s Algorithm (5) - Resource-Request Algorithm(어떤 resource request 있을때 이를 허가해도 되는지?)
- Example
  - 5 processes: P0, P1, P2, P3, P4
  - 3 resources: A (10 instance), B (5 instances), and C (7 instances)
- Snapshot at time T1
![image](https://github.com/user-attachments/assets/b113931d-138e-4d02-a0af-580aac43f138) <br>
새로운 request P0가 들어왔을때 이후에 가능한지 확인한다고 생각한다. <br>
![image](https://github.com/user-attachments/assets/b13da65e-f5cc-4bf6-a56e-0204dfeb775d)

## Banker’s Algorithm (6) - Resource-Request Algorithm(의사 코드)
![image](https://github.com/user-attachments/assets/6ec84522-8a9c-4441-8ea6-f2be01fe9bc2)

## Banker’s Algorithm - Exercise problem
![image](https://github.com/user-attachments/assets/3194f895-b5d9-43e1-8b29-9fa5ad537c2e)

# Deadlock Detection
## Deadlock Detection (1)
- deadlock handling mechanisms의 한계점
  - 교착 상태 예방(Prevention of deadlock)은 비용높고, 비효율
  - 탐지(Detection) 비용 높고, 복구(recovery)는 거의 불가능
- 각 자원 유형에 단일 인스턴스(Single Instance)만 존재할 경우 -> graph그려 cycle확인한다 -> cycle의 존재가 교착 상태의 필요충분조건이다
- 자원 유형에 여러 인스턴스(Multiple Instance)가 존재할 경우 -> banker’s algorithm과 유사한 deadlock detection algorithm을 사용한다

## Deadlock Detection (2) - Single Instance
- 각 자원 유형에 단일 인스턴스만 존재할 경우
  - Wait-for graph를 유지한다
  - 주기적으로 그래프에서 사이클을 탐색하는 algorithm을 실행 -> 사이클이 있으면 deadlock가 존재
  - 이클을 탐색 알고리즘의 시간 복잡도: O(n²), 여기서 n은 정점의 수(즉, 자원이 몇개인가에 따라 복잡도를 결정한다.) <br>
  wait-for graph에서 인접 행렬을 쓰고 n*n행렬이다보니 O(n²)이 걸린다.<br>
![image](https://github.com/user-attachments/assets/d3f0ddec-722e-44b7-9009-208000362b0c)

## Deadlock Detection (3) - Multiple Instance
- Deadlock detection algorithm of multiple instances of a resource type <br>
![image](https://github.com/user-attachments/assets/d5aaadd0-1c4e-4513-9da7-5678c1f2b679)
- Example
  - 5 processes: P0, P1, P2, P3, P4
  - 3 resources: A (7 instance), B (2 instances), and C (6 instances)
  - Snapshot at time T0 <br>
  ![image](https://github.com/user-attachments/assets/a60c07b4-9f04-4168-a8f3-555789085609)
- Example 2
  - 5 processes: P0, P1, P2, P3, P4
  - 3 resources: A (7 instance), B (2 instances), and C (6 instances)
  - Snapshot at time T0 <br>
  ![image](https://github.com/user-attachments/assets/69367a7e-8f96-4ccb-a80c-611965fc0e2b)

## Deadlock Recovery 
- Process 종료(termination)
  - 모든 deadlocked processes를 강제 종료한다. -> critical하기떄문에 비추천
  - 또는 하나씩 종료하여 교착 상태의 사이클을 제거할 때까지 반복한다
    - 어떤 프로세스를 종료할지 선택하는 기준:(여러가지를 고려한다)
      - 프로세스의 우선순위(Priority)
      - 지금까지 얼마나 실행되었는지, 완료까지 얼마나 남았는지
      - 지금까지 사용한 자원의 양
      - 완료에 필요한 자원의 양
      - 종료해야 할 프로세스 수
      - 대화형(interactive) 프로세스인지, 배치(batch) 작업인지
- 자원 선점(Resource preemption)
  - 고려해야 할 사항:
    - 희생자 선택(Selecting a victim) – minimize cost
    - 롤백(Rollback): 안전한 상태(safe state)로 되돌아가야 하며, 해당 상태에서 프로세스를 다시 시작
    - Starvation: 같은 프로세스가 계속 희생자로 선택될 수 있으므로, rollback 횟수를 비용 요소에 포함시켜 고려해야 함

------------------------------------------------------------------------------------------------------------------------

# 9. Memory Management
## Background
![image](https://github.com/user-attachments/assets/a9152c32-6ee7-4146-b397-cc6ce397edea) <br>
- program은 실행되기 위해 disk에서 memory로 옮겨져 porcess 내에 위치해야 한다.
- main memory와 register는 CPU가 직접 접근할 수 있는 유일한 저장 장치이다.
- 메모리 유닛은 주소 + 읽기 요청(load) 또는 주소 + 데이터 및 쓰기 요청(store)의 형태로 된 연속적인 흐름만을 본다.
- 올바른 동작(=정확한 연산)을 보장하기 위해 메모리 보호가 필요하다.

## Base and Limit Registers
- base(기본) 레지스터와 limit(한계) 레지스터 한 쌍이 logical address space 정의.
- CPU는 user mode에서의 모든 메모리 접근이 해당 사용자의 base와 limit 사이에 있는지 확인. <br>
![image](https://github.com/user-attachments/assets/55b1ed21-9738-4606-ab0f-05e469ba6354) <br>

## Hardware Address Protection
![image](https://github.com/user-attachments/assets/c4cbb705-d163-4fce-972f-867482c2ce41)

## Address Binding
- 프로그램의 생애 주기 동안 주소(Addresses)는 여러 단계에서 서로 다른 방식으로 표현된다.
  - 소스 코드(Source code)의 주소: 기호(symbolic)로 표현된다.
  - 컴파일된 코드의 주소(Compiled code addresses): 재배치 가능한 주소(relocatable addresses)로 바인딩된다.
    - 예: 이 모듈의 시작 지점에서부터 14바이트 떨어진 위치
  - 링커(linker)나 로더(loader): 주소재배치(relocatable address) 절대 주소(absolute addresses)에 바인딩한다.
    - 예: 74014
  - 각 바인딩(binding)은 하나의 주소 공간을 다른 주소 공간으로 매핑하는 것이다. <br>
  
![image](https://github.com/user-attachments/assets/a29e701a-7026-420b-b137-956a84188eca)

## Logical vs. Physical Address Space
- 논리 주소(Logical address): CPU에 의해 생성된 주소이며, 가상 주소(Virtual address) 라고도 불린다.<br>
  program에 명시된 주소(0번 ~ limit까지)
- 물리 주소(Physical address): 메모리 유닛이 실제로 보는 주소이다. -> base + logical = 실제 주소
- 논리 주소 공간(Logical address space): 프로그램이 생성할 수 있는 모든 논리 주소의 집합이다.
- 물리 주소 공간(Physical address space): 프로그램이 실제로 접근하는 모든 물리 주소의 집합이다.

## Memory-Management Unit (MMU)
- 하드웨어 장치는 실행 시간(run time)에 논리 주소를 물리 주소로(logical to physical address) 매핑한다.
- 재배치 레지스터(relocation register)
  - 물리 주소 = 논리 주소 + 재배치 레지스터의 값(physical address = logical address + value in the relocation register)

## Contiguous Memory Allocation
모든 process가 하나 연속된 공간에 할당된다.

## Contiguous Allocation (1)
- Main memory는 OS와 user processes 모두를 지원해야 한다.
- 연속 할당(contiguous allocation)은 초기의 한 방법이다.
- 일반적으로 Main memory는 두 개의 파티션으로 나뉜다:
  - Resident OS(상주 운영체제) 는 보통 인터럽트 벡터와 함께 low memory에 위치한다. (0번 부터)
  - User processes는 high memory에 위치한다.
  - 각 프로세스는 메모리 내에 하나의 연속된 영역에 배치된다.
- relocation registers는 user processes이 서로를 침범하거나 운영체제의 코드 및 데이터를 변경하지 못하도록 보호하는 데 사용된다.
  - Base 레지스터: 가장 작은 physical address을 가진다
  - Limit 레지스터: 허용된 logical address 범위를 나타낸다.
  - MMU(메모리 관리 장치): logical address를 동적으로 물리 주소로 매핑한다. <br>
![image](https://github.com/user-attachments/assets/f3b1dc88-f222-40f8-9e77-7cb9f773717b)

##  Contiguous Allocation (2)
- 다중 파티션 할당(Multiple-partition allocation)
  - 멀티프로그래밍(multiprogramming) 정도는 파티션 수에 의해 제한된다.
  - 효율성을 위해 가변 크기 파티션(variable-partition size) 사용
  - Hole(공간): 사용 가능한 메모리 블록
  - 프로세스가 도착, 큰 hole에서 메모리를 할당
  - 프로세스가 종료, 해당 파티션이 해제, 인접한 자유 공간은 병합
  - 운영체제는 다음 정보를 관리한다: 
    - (a) 할당된 파티션 (b) 비어 있는 파티션(hole)

- 동적 저장소 할당 문제(Dynamic storage allocation problem)
  - 크기 n의 메모리 요청을 자유 공간(free hole) 목록에서 어떻게 만족시킬 것인가?
  - First-fit: 처음으로 있는 크기가 충분한 hole에 할당한다.
  - Best-fit: 크기가 충분한 hole 중에서 가장 작은 것에 할당한다.
  - Worst-fit: 크기가 충분한 hole 중에서 가장 큰 것에 할당한다.

## Contiguous Allocation (3)
- 단편화(Fragmentation)
  - 외부 단편화(External fragmentation): 남아있는 공간들이 요청을 만족할 만큼 크지만, 공간들이 연속적이지는 않다.
  - 내부 단편화(Internal fragmentation) (실제보다 더 큰 $2^n$ 으로 요청 및 할당)
    - 할당된 메모리가 요청한 메모리보다 약간 더 클 때 발생하며, 이 차이만큼의 메모리가 파티션 내부에 있지만 사용되지 않는 상태
- 50% 규칙(50-percent rule)
  - First-fit에서는 N개의 블록이 할당 -> 약 0.5N개의 블록이 단편화로 손실
    - 이 중 33%는 Fragmentation때문에 못쓴다.
- 압축(compaction)은 external fragmentation을 줄여준다. -> 메모리 내용을 이동(shuffle)시켜서 모든 자유 메모리를 한 곳에 모아 큰 블록을 만든다.
  - 압축은 재배치(relocation)가 동적(dynamic)일 때만 가능하며 실행 시간(execution time)에 수행된다.
  - I/O problem
    - 입출력 작업 중인 프로세스는 메모리에 고정(latch)되어 있어야 한다.

## Swapping
- process는 일시적으로 memory에서 백업 저장소(backing store)로 swapped될 수 있으며, 이후 실행을 계속하기 위해 다시 메모리로 불러올 수 있다.<br>
  즉, 잠깐 안쓰이는 것을 잠깐 뺸다고 생각.
- 백업 저장소(backing store): 크고 빠른 디스크, 이 메모리 이미지에 대해 직접 접근이 가능
- roll out, roll in (=sawp out, swap in)은 priority-based scheduling algorithms에 사용되는 스왑 변형으로, <br>
  lower-priority process를 스왑 아웃(swapped out)하여 높은 우선순위 프로세스를 로드하고 실행할 수 있게 한다.
- 스왑 시간의 대부분은 전송 시간(transfer time)이며, 총 전송 시간은 스왑되는 메모리 양에 정비례한다. <br>
  따라서 swap의 양이 줄면 swap시간도 줄어든다.
- 시스템은 디스크에 메모리 이미지가 저장된 실행 준비 완료 상태(ready-to-run)인 프로세스들의 ready queue를 유지한다.

## Schematic View of Swapping
![image](https://github.com/user-attachments/assets/a628efe7-aeba-4d53-a8df-8412bcf8d3f0)

## Context Switch Time Including Swapping
- CPU에 올릴 다음 프로세스가 메모리에 없으면, 기존 프로세스를 스왑 아웃하고 대상 프로세스를 스왑 인해야 한다.
- 이로 인해 context switch time이 매우 길어질 수 있다.(memory가 아닌 disk까지 가서 해야되서)
- 예를 들어, 100MB 프로세스를 하드디스크로 스왑할 때 전송 속도가 50MB/s라면,
  - swap out 시간은 약 2000ms(2초)이고, swap in도 시간은 동일하다.
  - 따라서, context switch시 swap에 걸리는 총 시간은 4000ms(4초)가 된다.
- swap하는 메모리 크기를 줄이면 시간을 단축할 수 있는데, 이는 실제로 사용 중인 메모리 양을 파악함으로써 가능하다.
  - 시스템 콜(request_memory(), release_memory())을 통해 OS에 메모리 사용 정보를 알려줄 수 있다. <br>
    사용 안하는 공간 처리하는 system call 있으면 유리하다.

## Swapping on Modern OS
- standard swapping은 현대 운영체제에서 사용되지 않는다.
  - 그러나 변형된 버전은 흔히 사용된다.(swap 개념자체는 사용)
    - 메모리가 극도로 부족할 때에만 스왑을 수행한다.
- 보통은 지원되지 않는 경우가 많다.
  - 플래시 메모리 기반 시스템의 경우(USB, SSD, 스마트폰 -> swap잘 안함)
    - 쓰기 횟수(write cycles)가 제한적이다.
    - 저장 공간이 적다.
    - 모바일 플랫폼에서 플래시 메모리와 CPU 간 데이터 처리량(throughput)이 낮다.(시간이 오래걸린다)
    - swap안하고 memory reclounation(회수)를 한다.
   
## Problem of Contiguous Allocation(연속 할당)
- 각 프로세스는 하나의 세그먼트(파티션)만 가질 수 있다.
  - 두 프로세스가 코드를 공유하면서도 각각의 데이터 영역은 독립적으로 유지하려면 어떻게 해야 할까?
- 하나의 프로그램은 여러 세그먼트(segments)로 이루어져 있다.
  - 세그먼트는 다음과 같은 logical unit을 의미한다:
    - main program
    - functions
    - object
    - local variables, global variables
    - stack
    - symbol table
    - arrays
   
## Segmentation (1)
- 사용자 관점의 메모리 구성을 지원하는 메모리 관리 기법(Memory-management scheme) <br>

![image](https://github.com/user-attachments/assets/2ccc8f74-1a0e-4cb3-a18e-ee6abf68ef53) <br>
- 프로세스를 여러 메모리 영역에 나누어 배치하는 것을 허용한다.
- 각 세그먼트마다 별도의 base와 limit 레지스터 쌍을 사용하며, <br>
  read와 write를 제어하는 protection bits 2개를 추가해 설정 가능하게 한다.
- 각 memory reference는 다음 세 가지 방식 중 하나 이상으로 세그먼트와 offset을 나타낸다:
  - 주소의 상위 비트(top bits)가 세그먼트를 선택하고, 하위 비트(low bits)가 오프셋을 나타낸다. -> 단점: segment별 크기가 정해진다. 최대 1/4
  - 명령어 자체가 암묵적으로 세그먼트를 선택한다.
    - 예: 코드 vs. 데이터, 스택 vs. 데이터
  - segment table이 프로세스의 모든 세그먼트에 대한 base와 limit 정보를 저장 <br>

![image](https://github.com/user-attachments/assets/6950a875-3b89-4007-bdbf-92cf6f0b5592)<br>
4개 쓰기위해 4등분 -> 따라서 상위 2bit만 보면 구분 가능하다. 00: code, 01: data, ..., 11: stack <br>
contigous: process마다 base와 limit 관리 VS segmentation: segment마다 base와 limit 관리

## Segmentation Architecture
- logical address는 두 개의 값으로 이루어진 튜플(two tuple)로 구성된다.
  - <segment-number, offset> <br>
  ![image](https://github.com/user-attachments/assets/29dbbb76-cc45-47dc-8de5-377e6075b768) <br>
- segment table: 2차원 물리 주소(two-dimensional physical addresses)를 매핑한다. 즉, table로 관리한다.
  - base와 limit 관리
- Segment-table base register(STBR): 세그먼트 테이블이 메모리 내 어디에 있는지를 가리킨다.
- Segment-table length register (STLR): 프로그램이 사용하는 세그먼트의 개수를 나타낸다.(table이 몇칸인지?)
  - 세그먼트 번호 s가 유효하려면 s < STLR이어야 한다. <br>

![image](https://github.com/user-attachments/assets/135064a2-e656-4a8a-9da6-ede8c5ac982c)<br>
  
![image](https://github.com/user-attachments/assets/692c7dbe-094a-4062-acfa-103023bca189)

## Segmentation Architecture (2)
![image](https://github.com/user-attachments/assets/ef84ff92-fc20-4ba1-9da2-d3e9a0c5e41d)

## Motivation of Paging 여기서 부터 다시
- 세그멘테이션(segmentation)의 문제점
  - 외부 단편화(External fragmentation) 여전히 남아있다.
- 페이징(paging)의 목표
  - 고정 크기의 메모리 블록을 사용하여 메모리 allocation과 swapping을 더 쉽게 만든다. 즉, memory공간을 똑같이 나누자.
  - 메모리 단편화(memory fragmentation)를 줄이는 것.

## Paging
- 프로세스의 물리 주소(physical memory) 공간은 연속적일 필요가 없다.
- 물리 메모리(physical memory)를 고정 크기의 블록인 프레임(frame)(= physical page)으로 나눈다.
  - 크기는 2의 거듭제곱이며, 512바이트에서 16메가바이트 사이(4KB가 표준 -> 4KB씩 쪼갠다.)
- 논리 메모리(logical memory)도 동일한 크기의 블록인 페이지(page)로 나눈다. -> 모든 memory공간을 같은 크기로 쪼갬(logical page)
- 모든 사용 가능한 프레임(free frame)을 추적하여 관리한다. (OS 할일)
- 크기가 N 페이지인 프로그램을 실행하려면, N개의 빈 프레임을 찾아 프로그램을 로드해야 한다.
- 논리 주소를 물리 주소로 변환하기 위해 페이지 테이블(page table)을 설정한다.<br>
page table에 page마다 어떤 frame이 mapping되어 있는지? -> page마다 base관리(limit은 필요X -> 크기 전부 고정)
- 하지만 여전히 내부 단편화(internal fragmentation) 문제는 존재한다. (external fragmentation은 X -> 칸 단위로 필요한 만큼만 제공)

## Address Translation Scheme (MMU가 함)
- CPU가 생성한 주소는 다음 두 부분으로 나뉜다:
- Page number (p): 페이지 테이블에서 해당 페이지가 물리 메모리의 어디에 있는지를 나타내는 base 주소를 찾기 위한 인덱스로 사용됨
- Page offset (d): base 주소와 결합되어 최종적인 물리 메모리 주소를 구성하며, 그 주소가 메모리 장치에 전달됨 <br> 
![image](https://github.com/user-attachments/assets/b4fb785e-920d-4d64-bf2e-ece44f4dbeca) <br>
- 논리 주소 공간이 $2^m$이고, 페이지 크기가 $2^𝑛$일 때 <br>
![image](https://github.com/user-attachments/assets/2ad5fb3d-fab1-42a5-9ddc-5f56f492ad38) <br>
- Translation process <br>
![image](https://github.com/user-attachments/assets/920e173b-9dc6-43c3-90e7-828718ed349b)

## Paging Hardware
![image](https://github.com/user-attachments/assets/262e1a78-cf1f-4197-8abc-d06c10f19487)

## Example of Paging
![image](https://github.com/user-attachments/assets/9a41e0da-b227-4ebd-ab00-9ebac7a44e5d)

## Calculating Internal Fragmentation
- 내부 단편화 계산 (Calculating internal fragmentation)
  - Page size = 2,048 bytes
  - Process size = 72,766 bytes (프로세스가 필요한 메모리 크기)
  - 35 pages + 1,086 bytes = 72766/2048 => 36pages(올림)
  - Internal fragmentation of 2,048 - 1,086 = 962 bytes 마지막 page에서 발생
  - Worst case fragmentation = page size - 1byte(최악의 경우 1pqge때문에 1칸이 만들어 진다.)
  - On average fragmentation = ½ page size
- Small page sizes 옳은가?
  - 작은 페이지 크기(small page size)는 페이지 테이블 항목(page table entry) 수를 증가시킨다.
    - ex) 32비트 주소 공간에서 4KB 페이지를 쓰면 -> Page table size = $2^{32−12}$ entries * 20-bit = 2.5 MB <br>
    $2^20$개 page가 있고 각 page마다 할당된 frame 번호(20bit)
  - 그래서 페이지 크기(page size)는 시간이 지남에 따라 커지고 있다.
    - 4 KB, 2 MB, 1 GB
- 프로세스가 보는 메모리 구조와 실제 물리 메모리는 이제 매우 다르다.

## Free Frames
![image](https://github.com/user-attachments/assets/f6888c42-89d0-488a-8bfe-a1008629080a)

## Implementation of Page Table (1)
- 페이지 테이블(Page table)은 주 기억장치(main memory)에 보관된다.
- Page-table base register(PTBR)는 페이지 테이블이 위치한 메모리 주소를 가리킨다. 즉, 책자위치를 가리킨다.
- Page-table length register(PTLR)는 페이지 테이블의 크기를 나타낸다.
- 이 방식에서는 모든 데이터 또는 명령어 접근 시 두 번의 메모리 접근이 필요하다.
  - 첫 번째는 페이지 테이블을 참조하기 위한 접근(page table 접근으로 실제 주소 알아냄)
  - 두 번째는 실제 데이터 또는 명령어를 읽기 위한 접근(실제 주소 접근)
- 이 두 번의 메모리 접근 문제는 Associative Memory 또는 Translation Lookaside Buffer(TLB)라는 특별하고 빠른 조회 하드웨어 캐시를 사용하여 해결할 수 있다. <br>
  TLB는 최근 page -> frame translate 정보 몇개만 저장하는 것이다. <br>

![image](https://github.com/user-attachments/assets/63637070-b06d-473f-a7cb-7e2607b91127)

## Implementation of Page Table (2)
- 일부 TLB는 각 TLB 항목에 Address-Space Identifiers(ASIDs)(어떤 process 정보인지)를 저장한다.
  - 이는 각 프로세스를 고유하게 식별하여 해당 프로세스의 주소 공간 보호를 가능하게 한다.
  - 그렇지 않으면(ASIDs 없을때) 컨텍스트 스위치 때마다 TLB를 모두 비워야(플러시) 한다. 지우는 것 가능(관련 정보가 완전히 없어지는게 아니라서)
- TLB는 보통 작고, 64개에서 1,024개 항목 정도로 구성된다.
- TLB miss가 발생하면, 해당 값이 TLB에 로드되어 다음 번 접근 시 더 빠르게 접근할 수 있다.
  - 이때 교체 정책(Replacement policy)이 중요하다.

## Paging Hardware with TLB
![image](https://github.com/user-attachments/assets/be0e0e91-760d-42a4-815e-48d5be2752ba)

## Effective Access Time(cache 고려X)
- Effective access time (EAT) <br>
![image](https://github.com/user-attachments/assets/d1a4562c-8b06-4d2f-9fef-1afa84970255)
 
## Additional Bits of Page Table Entry (1)
- 각 프레임의 보호 비트를 연결하여 메모리 보호(Memory protection)를 구현한다
  - 이는 read-only or read-write access으로 나타낸다. rw bit으로 표시한다.
  - page execute-only 등과 같이 더 많은 비트를 추가할 수도 있다
- 페이지 테이블의 각 항목에 Valid-invalid bit가 붙어 있다
  - valid (1): 해당 페이지가 프로세스의 논리적 주소 공간에 있음을 나타낸다
  - invalid (0): 해당 페이지가 프로세스의 논리적 주소 공간에 없음을 나타낸다. -> 유효한 페이지가 아니여서 접근하지 말라고 알려준다.
- 어떤 위반(violation)이 발생하면 kernel로 trap이 발생함 <br>

![image](https://github.com/user-attachments/assets/0ddb9533-675f-46e1-9c31-0c614a79a79c)
- valid bit example<br>
![image](https://github.com/user-attachments/assets/3618e865-133d-4b92-983b-ff4f354be952)

## Shared Pages (일부 page 할당 용도)
- Shared code (or data)
  - read-only(재진입 가능한) 코드의 한 복사본이 여러 프로세스에 의해 공유됨
  - 여러 스레드가 같은 프로세스 공간을 공유하는 것과 유사함
  - read-write가 허용된다면 inter-process communication(프로세스간의 통신)에도 유용함
- Private code and data -> frame번호 다르게 관리
  - 각 process는 코드와 데이터의 별도 복사본을 가짐
  - 개인 코드와 데이터에 대한 페이지는 logical address space 어디에나 위치할 수 있음
- Shared pages example <br>

![image](https://github.com/user-attachments/assets/1e3347d3-6f38-4b66-bcd8-bdf5bb4db4bd)

## Page Table Size
- 32비트 논리 주소 공간을 사용하는 현대 컴퓨터를 가정 -> 주소 32-bit -> process가 사용하는 memory 크기가 최대 4GB
- 페이지 크기가 4KB($2^{12}$)일 때 -> 4GB를 4KB단위로 쪼갠다.
- 페이지 테이블에는 1백만 개($2^{20}$)의 엔트리가 필요함 ($2^{32}/2^{12}$)
- 각 엔트리(entry)가 4바이트라면, 페이지 테이블(page table) 하나당 4MB의 물리 주소 공간(physical address space)이 필요하다.
  - 이 4MB를 주기억장치에 연속적으로 할당하는 것은 비효율적이다.
- Solution
  - Hierarchical Paging
  - Hashed Page Table <br>

![image](https://github.com/user-attachments/assets/cfde4f97-9489-4222-a759-dec82a894bfa)
![image](https://github.com/user-attachments/assets/160460d0-1c90-4f8c-b62d-1ae99ac38d6c)

## Hierarchical Page Tables (다단계로 구성)
- 논리 주소 공간(logical address space)을 여러 개의 페이지 테이블(multiple page tables)로 분할함
- 간단한 방법으로는 2단계(2-level) 페이지 테이블을 사용하는 것이 있음 <br>

![image](https://github.com/user-attachments/assets/d41f87ba-57db-42cf-a663-f4ec975f4249)
- Two-level page table <br>

![image](https://github.com/user-attachments/assets/3f6dd9e5-4fdf-4bd2-b0ed-d29b115e203b)

# Case Studies
## The Intel IA-32 Architecture (segmentation -> 선택(optional, 32-bit 주소 쓴다.) + paging 지원)
- segmentation과 segmentation + paging 모두 지원한다.
  - 각 segment 4 GB까지 가능하다.
  - 프로세스당 최대 16K(16,384)개의 segments 사용 가능
  - Divided into two partitions
    - 첫 번째 파티션(최대 8K 세그먼트)은 프로세스에만 속하며, 로컬 디스크립터 테이블(LDT)에 저장됨
    - 두 번째 파티션(최대 8K 세그먼트)은 모든 프로세스가 공유하며, 글로벌 디스크립터 테이블(GDT)에 저장됨 <br>

![image](https://github.com/user-attachments/assets/ac1b7f8d-f072-447a-bb1c-9ad132171caa)

## The Intel IA-32 Architecture
- Paging unit은 linear address를 받는다.
  - main memory에서 physical address를 생성한다. 
  - Paging units은 MMU와 동등한 역활을 한다.
  - Page size can be 4 KB or 4 MB <br>

![image](https://github.com/user-attachments/assets/2f0cbf51-9db9-4396-ab7a-40f4b0d19e46)
