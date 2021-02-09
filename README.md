# Virtual Memory Management Simulator

Virtual Memory Systems에서 one-level, two-level Page Table 과 Inverted Page Table system을 구현하여 시뮬레이션 해 보는 것입니다.


### 1. One-level Page Table System

one-level page Table system을 구현 합니다. 이 경우 virtual address 크기는 32bits 이고
page의 사이즈는 4Kbytes(12bits) 으로 가정 합니다. Page faults 인 경우 두 가지 page
replacement algorithm을 사용합니다. FIFO 와 LRU입니다. 시뮬레이션 수행 하면서 다음과 같은 정보를 모아 시뮬레이션이 끝나면 출력합니다. 
이들 정보는 각 process에 해당 하는 정보입니다. 
- trace 파일 이름 (procTable[i].traceName) 
- 처리한 memory trace 의 수 (procTable[i].ntraces) 
- Page Faults 의 수 (procTable[i].numPageFault) 
- Page Hits의 수 (procTable[i].numPageHit)
 
이들 정보는 다음과 같은 관계가 성립된다. 
```
procTable[i].numPageHit + procTable[i].numPageFault == procTable[i].ntraces
```

### 2. Two-level Page Table System

두 번째 인자(firstLevelBits)가 결정 되면 각 process의 first level page table이 구성 됩니다. 하지만 second level page table은 메모리에 접근 하면서 필요한 경우에만 구성하면 됩니다. process 가 memory trace에서 읽어 들인 virtual address를 접근하게 되면 차례로 first level page table을 접근하고 그 다음 second level page table에 접근 하게
됩니다. 최초의 접근에서는 second level page table이 없을 수 있고 이럴 경우에는 해당 page table을 
만들어 구성하여야 합니다. 한번 만들어진 second level page table은 없어지지 않습니다. second level page table 접근하여 page table entry를 확인하고 해당 page에 이미 physical
memory frame 할당 되어 있으면 page hit 이고 할당 되어 있지 않으면 page faults 인데 이 경우 page replacement algorithm (LRU) 에 따라 frame을 할당하게 됩니다. 
기존에 할당 되어 있던 frame을 다른 page 에 할당 할 경우 기존의 매핑 정보를 invalid하게
설정해야 합니다. frame 이 할당 되어 있거나 할당되면 physical address를 구성할 수 있습니
다. Two-level Page Table을 시뮬레이션 하면서 다음과 같은 정보를 모아 시뮬레이션이 끝나면 출
력합니다. 이들 정보는 각 process에 해당 하는 정보입니다.

- trace 파일 이름 (procTable[i].traceName) 
- 처리한 memory trace 의 수 (procTable[i].ntraces) 
- 만들어진 second level page table의 수 (procTable[i].num2ndLevelPageTable)  
- Page Faults 의 수 (procTable[i].numPageFault) 
- Page Hits의 수 (procTable[i].numPageHit)

이들 정보는 다음과 같은 관계가 성립된다. 

```
procTable[i].numPageHit + procTable[i].numPageFault == procTable[i].ntraces
```

### 3. Inverted Page Table System

Inverted Page Table 에서는 Hash Table을 사용하여 process의 virtual page 와 frame간의
매핑 정보를 저장합니다. Hash Table의 크기는 frame의 수와 같고 hash 함수로는 다음과 같습
니다. Hash Table index 값 = (virtual page number + process id) % frame 의 수
process 가 접근하려하는 virtual address에서 virtual page number를 얻어내어 process id
를 더하고 세 번째 인자(PhysicalMemorySizeBits)에서 frame의 수를 얻어서 % 연산으로 Hash
table의 index 값을 계산합니다. 같은 Hash table의 entry에 여러 개의 매핑 정보(노드)가 있을 수 있는데 가장 최근에 만들어진
매핑 정보(노드)가 가장 먼저 접근 될 수 있도록 앞쪽에 삽입되어야 합니다 (매핑 정보가 삽입된
후에는 LRU와는 다르게 접근될 때마다 위치를 변화하지 않습니다). Hash table의 entry에 연결
된 매핑 정보를 다 찾아도 접근하고자하는 virtual page number 와 process id를 발견할 수
없으면 Page faults이고 이와 달리 발견하면 해당 매핑 정보에 있는 frame number를 사용하여
physical address를 만들어 내면 됩니다. Page faults 가 발생하면 page replacement algorithm (LRU) 에 따라 frame을 할당하게 됩
니다. 기존에 할당 되어 있던 frame을 다른 page 에 할당 할 경우 기존의 매핑 정보를 없애야
합니다. Inverted Page Table을 시뮬레이션 하면서 다음과 같은 정보를 모아 시뮬레이션이 끝나면 출력
합니다. 이들 정보는 각 process에 해당 하는 정보입니다.
- trace 파일 이름 (procTable[i].traceName) 
- 처리한 memory trace 의 수 (procTable[i].ntraces) 
- Inverted Hash Table 접근의 수 (procTable[i].numIHTConflictAccess) : Hash table에서 매핑 정보(노드)에 접근한 수를 나타낸다.
- Empty Inverted Hash Table Access (procTable[i].numIHTNULLAccess) : Hash table에 접근했을 때 해당 엔드리가 연결된 매핑 정보를 하나도 갖지 않은 경우의 수를
나타낸다. 
- Non-Empty Inverted Hash Table Access (procTable[i].numIHTNonNULLAcess)
Hash table에 접근했을 때 해당 엔드리가 연결된 매핑 정보를 하나라도 갖는 경우의 수를
나타낸다. 
- Page Faults 의 수 (procTable[i].numPageFault) - Page Hits의 수 (procTable[i].numPageHit)

이들 정보는 다음과 같은 관계가 성립된다. 

```
procTable[i].numPageHit + procTable[i].numPageFault == procTable[i].ntraces
procTable[i].numIHTNULLAccess + procTable[i].numIHTNonNULLAcess == procTable[i].ntraces
```

### 4. Page replacement Algorithm (LRU)

virutal memory management simulation에서는 명시되어 있지 않으면 Page
Replacement Algorithm으로 LRU 방법을 사용한다. 따라서 매번의 Memory 참조를 수행할 때
마다 (매 한 개의 memory trace 레코드 처리) LRU에 따라 다음에 replace될 frame의 순서를
정하여야 한다. Global replacement를 적용하여 process에 상관없이 모든 frame에 적용된다. LRU의 구현으로는 수업에서 설명하였듯이 linked list 방법이나 counter를 이용한 방법을 사용
할 수 있다. Hint로 보여준 프로그램에서는 linked list방식을 사용하고 있다. 단 초기 메모리 상태에서 Frame의 할당은 낮은 번호(주소)의 Frame부터 할당 합니다.
