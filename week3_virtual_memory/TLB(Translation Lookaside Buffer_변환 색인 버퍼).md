## 1. TLB의 역할 및 구조
메모리를 고정 크기로 할당하여 사용하는 paging 방식에는 두 가지 문제점이 있었다.
- 잦은 메모리 접근
- 별도의 page table 을 위한 메모리 낭비

여기서 첫 번째 단점을 보완하기 위한 방식으로 TLB(Translation Lookaside Buffer, 변환 색인 버퍼)가 있다.


### 역할
- 가상 메모리 시스템에서 가상 주소를 물리 주소로 변환하는 과정의 속도를 높이기 위한 캐시 메모리
- CPU가 메모리 접근 시, 가상 주소를 물리 주소로 변환해야 한다. 
- 이 과정에서 page table을 참조하게 되는데, page table을 매번 참조하면 성능 저하가 발생할 수 있다.
- 이를 개선하기 위해 TLB에 자주 사용되는 page table 항목을 저장해 두고, 빠르게 주소 변환을 처리하는 역할을 한다.


### 구조
- TLB에 존재하는 정보들
  - Virtual Page Number (VPN)
  - Physical Frame Number (PFN)
  - Other bits : 페이지 상태를 관리하는 비트들 - 유효 비트(valid bit), 참조 비트(reference bit), 수정 비트(dirty bit) 등



## 2. TLB hit와 TLB miss가 발생했을 때 동작 과정
TLB hit와 miss는 하드웨어 or OS가 관리한다.


둘 중, OS로 TLB를 관리하는게 장점이 많다. 크게 유연성과 단순함이 있다. 


OS는 하드웨어 변경 없이 page table을 구현하는 모든 자료구조를 사용할 수 있고, OS TLB miss 핸들러가 작동하며 miss 처리를 해주기 때문에 하드웨어의 할 일이 없어진다.

![TLBhit](https://github.com/user-attachments/assets/dfbed3f8-bbb2-4189-9e96-70cbb10aa0d0)
![TLB miss](https://github.com/user-attachments/assets/04757a21-f358-460b-8fe6-980c718ce0b4)


- 페이지 : 가상 주소의 분할 영역
  - 가상 주소 VA=<P, D>
  - P (Page) = 가상 주소 / 한 페이지 크기
  - D (Distance, Offset) = 가상 주소 % 한 페이지 크기
- 프레임 : 물리 주소의 분할 영역
  - 물리 주소 PA=<F, D>
  - F (Frame) : page table을 통해 획득
  - D = 가상 주소의 D와 동일


## 3. TLB의 Context Switching 처리 방법
### 3-1. Context Switching 시, VPN이 같아질 수 있는 가능성
  - 각 프로세스가 고유한 가상 메모리 공간을 가지지만, 이 가상 메모리 공간이 동일한 구조를 가지고 있기 때문. 
  - 여러 프로세스가 동일한 범위의 가상 주소를 가질 수 있고, 그 결과로 페이지 테이블에서 동일한 인덱스(VPN)가 발생할 가능성이 있다.
  - Context Switching 시 VPN이 같고 PFN이 다른 경우, 이 정보를 TLB에 그대로 저장하면 어떤 정보가 어떤 프로세스의 것인지 구분 할 수 없다. 
  #### -> 프로세스에서 서로의 주소 공간이 아닌 곳에 접근할 수 있는 문제 상황이 발생

### 3-2. 처리 방법
(1) TLB Flush
- Context Switching 시 TLB에 저장된 모든 항목을 비워(Flush) 새 프로세스의 주소 변환을 위해 초기화하는 방법 
- 구현이 간단하다는 장점이 있지만, Context Switching 후 TLB miss가 증가하여 성능 저하 발생

(2) Address Space Identifier (ASID) 사용
- ASID는 각 프로세스에 고유한 식별자를 부여하여, TLB 항목이 어느 프로세스의 주소 변환 정보인지 구분하는 방식.
- flush 하지 않고 ASID를 이용해 현재 실행 중인 프로세스의 변환 정보만 확인하면 성능 저하를 막을 수 있다.

<img width="766" alt="TLB asid" src="https://github.com/user-attachments/assets/b614869c-29ac-4508-8b81-aeb7b246f899">


***

ref.
- [The role and operation of TLB for efficient paging](https://icksw.tistory.com/149)
- [TLB hit and miss process](https://velog.io/@yiwonjin/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-11-%EA%B0%80%EC%83%81%EB%A9%94%EB%AA%A8%EB%A6%AC)
- [Overall process of Virtual Memory System and Paging](https://velog.io/@chappi/OS%EB%8A%94-%ED%95%A0%EA%BB%80%EB%8D%B0-%ED%95%B5%EC%8B%AC%EB%A7%8C-%ED%95%A9%EB%8B%88%EB%8B%A4.-14%ED%8E%B8-%EA%B0%80%EC%83%81-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B0%9C%EC%9A%94-%ED%8E%98%EC%9D%B4%EC%A7%95)
- [TLB structures - asid](https://devfancy.github.io/OS-19-TLB/)
