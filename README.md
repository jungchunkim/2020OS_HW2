# Virtual Memory Management Simulator

Virtual Memory Systems에서 one-level, two-level Page Table 과 Inverted Page Table system을 구현하여 시뮬레이션 해 보는 것입니다.


## 1. One-level Page Table System

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

## 2. Two-level Page Table System

## 3. Inverted Page Table System

## 4. Page replacement Algorithm (LRU)
