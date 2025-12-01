# Router with VLan
```csharp
              [Router]
                 |
          Fa0/0 (Trunk)
                 |
            [Switch]
      -----------------------
      |      |       |      |
    PC0     PC1     PC2    PC3
   VLAN10  VLAN10  VLAN20 VLAN20

```

<br/>
<br/>


## 전체 구성
- L2 Switch 1대
- Router 1대 (1개의 물리 포트에 서브인터페이스 구성)
- PC 4대
  - PC0, PC1 → VLAN 10
  - PC2, PC3 → VLAN 20
- Switch ↔ Router 연결 포트는 Trunk(802.1Q)


<br/>
<br/>


## 라우터 설정 요약 (Router on a Stick)
<img width="1439" height="764" alt="image" src="https://github.com/user-attachments/assets/4cf5f5bf-4921-48b9-b150-b1760951b067" />

- 라우터의 FastEthernet0/0 인터페이스에 VLAN 별 서브인터페이스를 만들고 IP를 부여해 VLAN 간 라우팅을 수행함

```bash
interface FastEthernet0/0
 no ip address

interface FastEthernet0/0.1
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.128

interface FastEthernet0/0.2
 encapsulation dot1Q 20
 ip address 192.168.10.129 255.255.255.128

```

- VLAN 10 게이트웨이: 192.168.10.1/25
- VLAN 20 게이트웨이: 192.168.10.129/25


<br/>
<br/>


## 스위치 설정 요약
<img width="1679" height="841" alt="image" src="https://github.com/user-attachments/assets/f7e3bbd0-82d6-44e5-9fbb-e38d778ed38e" />

- PC가 연결된 포트는 Access 모드
- 라우터로 연결되는 포트(Fa0/1)는 Trunk 모드로 설정합니다.

```bash
# VLAN 생성
vlan 10
vlan 20

# Access 포트 설정
interface Fa0/2
 switchport mode access
 switchport access vlan 10

interface Fa0/3
 switchport mode access
 switchport access vlan 20

# Router 연결 포트 트렁크
interface Fa0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
```

<br/>
<br/>


## PC IP 설정
### VLAN 10 (pc0, pc1)
```
IP: 192.168.10.10~30
Gateway: 192.168.10.1
```

<br/>

### VLAN 20 (PC2, PC3)
```
IP: 192.168.10.130~150
Gateway: 192.168.10.129
```

<br/>
<br/>

## 테스트 결과
<img width="1872" height="856" alt="image" src="https://github.com/user-attachments/assets/570e24a2-c97f-407f-a7f8-f36b8071aa80" />

- 같은 VLAN 간 Ping 성공
- 서로 다른 VLAN 간 Ping 성공 (Inter-VLAN Routing 정상 작동)

<br/>

- 예: PC0 → PC3
```
Reply from 192.168.10.130: bytes=32 time<1ms TTL=127
```

<br/>
<br/>


## 요약
| 구성 요소  | 역할                                      |
| ------ | --------------------------------------- |
| Router | VLAN 10/20 간 라우팅 수행 (Router-on-a-Stick) |
| Switch | Access VLAN + Trunk 구성                  |
| PC     | VLAN 별 IP 및 게이트웨이 설정                    |
| 목적     | Inter-VLAN Routing 동작 검증                |



<br/>
<br/>


### 참고
[[Cisco Packet Tracer]VLAN을 설정해보자(1) - L2 Switch](https://hsm-racoon.tistory.com/42)
