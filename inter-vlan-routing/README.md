# Inter VLAN Routing

```csharp
           [ Router ]
           Fa0/0 (Trunk)
                |
                |
           [ Switch ]
      ---------------------
      |                 |
  VLAN10             VLAN20
   PC0                 Server0
```

<br/>
<br/>

## 전체 구성
| 장비                 | 역할             | 연결 포트                 | IP / VLAN              |
| ------------------ | -------------- | --------------------- | ---------------------- |
| Router (2620XM)    | VLAN 간 라우팅     | Fa0/0 ↔ Switch Fa0/24 | Sub-IF: VLAN10/20      |
| Switch (2960-24TT) | L2 Switching   | Access + Trunk        | VLAN10, VLAN20         |
| PC0                | Browser Client | Fa0/1                 | VLAN10 / 192.168.10.10 |
| Server0            | Web Server     | Fa0/10                | VLAN20 / 192.168.20.10 |

<br/>
<br/>

## 라우터 설정 요약
```bash
interface FastEthernet0/0
 no shutdown

interface FastEthernet0/0.1
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface FastEthernet0/0.2
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
```

<br/>
<br/>

## 스위치 설정 요약
```bash
interface Fa0/1      # PC0
 switchport mode access
 switchport access vlan 10

interface Fa0/10     # Server0
 switchport mode access
 switchport access vlan 20

interface Fa0/24     # Router 연결
 switchport mode trunk

```

<br/>
<br/>

## PC / Server IP 설정
| 장비      | IP            | Subnet Mask   | Gateway      | VLAN |
| ------- | ------------- | ------------- | ------------ | ---- |
| PC0     | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 | 10   |
| Server0 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 | 20   |


<br/>
<br/>

## 테스트 결과
- PC0 → Server0 Ping 성공
- PC0 웹 브라우저 → http://192.168.20.10
- 접속 성공