# Configuration

## 01. PC 설정
- PC 1대 / 서버 1대 준비

  | 장비      | IP            | Subnet Mask   | Gateway      | VLAN |
  | ------- | ------------- | ------------- | ------------ | ---- |
  | PC0     | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 | 10   |
  | Server0 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 | 20   |


<br/>
<br/>

## 02. Switch 설정
###  VLAN 설정
```
en
conf t

vlan 10
name 10
exit

vlan 20
name 20
exit
```
- enable
- configuration terminal 진입
- 이후 vlan 10, vlan 20의 이름을 각각 10, 20으로 설정

<br/>

```
int fa0/1
switchport mode access
switchport access vlan 10
exit

int fa0/10
switchport mode accesss
switchport access vlan 20
exit

```


<br/>

### Trunk 설정
```
conf t

int fa0/24
switchport mode trunk
```


<br/>
<br/>


## Router 설정
```
en
conf t

int fa0/0
no shutdown
exit

int fa0/0.1
encapsulation dot1q 10
ip address 192.168.10.1 255.255.255.0
exit

int fa0/0.2
encapsulation dot1q 20
ip address 192.168.20.1 255.255.255.0
exit
```


<br/>
<br/>


## 이슈 정리
### 01. trunk 모드
- 여러 VLAN의 트래픽을 한 개의 물리 링크로 전달할 수 있게 하는 스위치 포트 동작 방식.
- VLAN 태그(802.1Q)를 붙여 VLAN 정보를 유지한 채 스위치 간 또는 스위치 ↔ 라우터 간 연결에 사용됨.
- trunk mode가 필요한 이유
  - 스위치와 라우터를 단 하나의 케이블로 연결해서 여러 VLAN(10, 20) 트래픽을 모두 전달해야 하기 때문
  - **Access 모드는 단 하나의 VLAN만 통과 가능**
  - **Trunk 모드는 여러 VLAN을 태그(802.1Q)로 구분해서 전달 가능**

<br/>

### 02. Router 설정 / IP 대역
- PC와 Server를 동일한 IP 대역(Subnet) 내에서 VLAN만 분리하는 것도 가능함
- 다만, VLAN은 Layer2에서 네트워크를 분리하므로, 서로 통신하기 위해서는 라우터가 VLAN 간 라우팅을 수행해야 함
- 이번 구성에서는 Subnet을 나누어 VLAN 별로 다른 네트워크 대역을 사용하여 Inter-VLAN Routing 동작을 더 명확하게 확인함

<br/>
<br/>