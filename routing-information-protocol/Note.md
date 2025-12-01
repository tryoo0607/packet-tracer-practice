# 개념 정리

## 홉
- 패킷이 A → B 로 갈 때, 중간에 거쳐가는 라우터 1개 = 1 hop(홉)

<br/>

### 중요한 이유
- RIP 같은 라우팅 프로토콜은 홉 수 기준으로 최단 경로 선택
    - hop 1인 경로 → 가장 짧다 
    - hop 5인 경로 → 더 멀다고 판단

- 너무 홉이 많으면 딜레이 증가
  - 라우터는 매번 패킷을 검사하고 라우팅 테이블 보고 포워딩하기 때문에 많이 거치면 지연 시간 증가. 

- 최대 홉 수 제한 존재 
  - RIP: 15 hops 까지만 가능 (16은 불가)
  - 너무 많은 라우터를 거치는 경로는 안 쓰게 됨

<br/>
<br/>

---

## 라우팅(Routing)이란?
- **라우터가 목적지까지 가는 “최적의 경로”를 선택하는 과정.**  
- 네비게이션이 도로 상황을 보고 경로를 정하는 것과 동일한 개념.

<br/>
<br/>

---

## 라우팅 종류


### 1) 정적 라우팅 (Static Routing)
- 관리자가 직접 경로를 수동 설정
- 경로가 고정적이고 변화가 적을 때 사용
- 설정 단순하지만 장애 시 대처 어려움 (자동 우회 경로 없음)

<br/>

### 2) 동적 라우팅 (Dynamic Routing)
- 라우터끼리 정보를 교환하여 **자동으로 최적 경로 선택**
- 네트워크가 크거나 변화가 많을 때 필수
- 예: RIP, OSPF, EIGRP 등

<br/>
<br/>

---

## IGP vs EGP

### IGP (Interior Gateway Protocol)
**같은 AS 내부에서 사용하는 라우팅**  
예:
- RIP
- OSPF
- EIGRP
- IS-IS

<br/>

### EGP (External Gateway Protocol)
**다른 AS 간 통신**을 위한 라우팅  
예:
- **BGP (사실상 유일하게 쓰임)**

<br/>
<br/>

---

## AS(Autonomous System)
- 하나의 네트워크 관리자가 관리하는 라우터 그룹
- ISP 또는 대형 기업 단위
- 번호 범위: **1 ~ 65535**
- 사설 AS: **64512 ~ 65534**
- 인터넷에서 네트워크 구역을 구분하는 역할 (국가 코드처럼)

<br/>
<br/>

---

## 동적 라우팅 프로토콜 요약

### 1) RIP (Routing Information Protocol)
- Distance Vector 기반 (IGP 중 가장 단순)
- Metric: **Hop Count**
- 최대 **15 hops** (16 = unreachable)
- 30초마다 전체 경로 broadcast → 비효율적
- Convergence 느림

**장점:** 설정 쉬움  
**단점:** 느림, 대규모 네트워크 부적합

<br/>

### 2) IGRP (Cisco Proprietary, 거의 사용 안 함)
- Cisco Distance Vector 프로토콜
- Metric: BW + Delay + Load + MTU
- 최대 홉 255
- Classful (서브넷 마스크 없음)
- 90초마다 업데이트  
  → **EIGRP 등장 후 사실상 폐지**

<br/>

### 3) EIGRP (Enhanced IGRP)
- Cisco 대표 Hybrid 프로토콜 (DV + Link State)
- Metric: Bandwidth + Delay (옵션: Load/Rel)
- Classless (VLSM 지원)
- DUAL 알고리즘: 루프 방지 + 빠른 convergence
- Neighbor 유지(Hello 패킷)
- Partial Update로 효율적

**장점:** 빠르고 안정적  
**단점:** Cisco 장비 필요

<br/>

### 4) OSPF (Open Shortest Path First)
- 대표적인 Link-State 라우팅 프로토콜
- 알고리즘: **Dijkstra SPF**
- Area 구조 지원 (백본 Area = Area 0 필수)
- Metric: **Cost = Bandwidth 기반**
- 멀티캐스트 (224.0.0.5, 224.0.0.6)
- 변화 발생 시 즉시 업데이트  
  → **대규모 네트워크에서 최적**

<br/>

### 5) BGP (Border Gateway Protocol)
- 인터넷 전체를 움직이는 유일한 EGP 프로토콜
- AS 간 라우팅
- Path Vector 방식
- iBGP / eBGP 존재
- Looping 방지 규칙 (Split Horizon, Full-mesh 등)

**쓰임:**  
ISP, 대기업, 국가 간 경로 설정의 표준

<br/>
<br/>

---

##  전체 비교표 (핵심만)

| 프로토콜 | 타입 | Metric | 규모 | 특징 |
|----------|--------|---------|---------|---------------------------|
| RIP | Distance Vector | Hop Count | 소규모 | 단순하지만 느림 |
| IGRP | Distance Vector | BW/Delay 등 | 중간 | Cisco 구형 |
| EIGRP | Hybrid | BW/Delay | 중·대규모 | 빠르고 효율적, Cisco |
| OSPF | Link-State | Cost(BW) | 중·대규모 | 실무에서 가장 많이 사용 |
| BGP | Path Vector | 다양한 정책 | 인터넷 전체 | AS 간 라우팅 전용 |


<br/>
<br/>

---

## 요약
- **Static:** 사람이 직접 경로 지정
- **Dynamic:** 프로토콜이 자동으로 경로 학습
- **IGP:** 내부 라우팅 (RIP, OSPF, EIGRP)
- **EGP:** 외부/AS 간 라우팅 (BGP)
- **AS:** 네트워크 관리 단위
- 각 프로토콜은 Metric과 규모에 맞게 선택됨


<br/>
<br/>

---


### 참고자료
[라우팅(Routeing)의 개념과 종류(RIP, IGP, EIGRP, OSPF, BGP)](https://networkinfracloud.tistory.com/13)