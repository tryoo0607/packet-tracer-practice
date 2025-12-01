# Configuration

## 01. PC 설정
- PC 4대 준비
- IP Configuration에서 IP / Subnet mask 설정
  - Subnet Mask는 255.255.255.0으로 공통 설정
  - IP : 192.168.10.10 / 192.168.10.11 / 192.168.10.130 / 192.168.10.131

- 10 -> 130으로 ping이 가는 것을 확인

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

int fa0/2
switchport mode accesss
switchport access vlan 10
exit

int fa0/3
switchport mode accesss
switchport access vlan 20
exit

int fa0/4
switchport mode accesss
switchport access vlan 20
exit
```
- pc 들이 연결된 interface fa0/1 ~ fa 0/4를 각각 vlan에 할당
  - vlan 10 : PC0, PC1
  - vlan 20 : PC2, PC3
 
- 이렇게 하면 더이상 vlan 10과 vlan 20에 할당된 pc들이 각각 ping으로 연결 안됨

<br/>

### Trunk 설정
```
conf t

int fa0/12
switchport mode trunk
```
- 현재 switch의 fa0/12와 router가 연결된 상태
- 이 상태에서 switch의 interface 를 trunk 모드로 설정


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
ip address 192.168.10.1 255.255.255.128
exit

int fa0/0.2
encapsulation dot1q 20
ip address 192.168.10.129 255.255.255.128
exit
```
- switch와 연결된 interface fa0/0의 shutdown을 해지
  - router의 인터페이스는 기본적으로 꺼져있음
  - 이를 켜주는 작업
 
- 이후 서브 인터페이스에 switch의 vlan을 할당
   - 각 vlan의 gateway가 될 IP를 지정해주어야 함


<br/>
<br/>


## PC Gateway 수정
- 각 PC의 Gateway / Subnet Mask를 다음과 같이 변경해야 함
  - VLAN 10 (PC0, PC1)
    - IP: 192.168.10.10 / 192.168.10.11
    - Subnet Mask: 255.255.255.128
    - Gateway: 192.168.10.1

  - VLAN 20 (PC2, PC3)
    - IP: 192.168.10.130 / 192.168.10.131
    - Subnet Mask: 255.255.255.128
    - Gateway: 192.168.10.129



<br/>
<br/>



## 이슈 정리
### 01. IP 수정
- 기존 PC IP를 다음과 같이 설정했을 시 문제 발생
  - IP: 192.168.10.10 / 192.168.10.11
  - IP: 192.168.20.10 / 192.168.20.11

- Subnet Mask가 255.255.255.0이기 때문에 VLAN으로 설정하지 않아도 네트워크가 분리되어 있게 됨
  - VLAN의 분리 효과를 확인하기 위해서는 같은 네트워크 대역을 사용해야 비교 가능함

- 따라서 IP를 다음과 같이 수정했음
  - IP: 192.168.10.10 / 192.168.10.11 / P: 192.168.10.12 / 192.168.10.13

<br/>

### 02. IP 및 Subnet Mask 수정
- router에서 VLAN의 Gateway를 다음과 같이 설정했었음
  - VLAN 10 (PC0, PC1)
    - IP: 192.168.10.10 / 192.168.10.11
    - Subnet Mask: 255.255.255.0
    - Gateway: 192.168.10.1

  - VLAN 20 (PC2, PC3)
    - IP: 192.168.10.12 / 192.168.10.13
    - Subnet Mask: 255.255.255.0
    - Gateway: 192.168.20.1

- PC와 Gateway는 동일한 네트워크 대역에 있어야 하므로, 서로 다른 대역을 사용한 초기 설정은 잘못된 구성임
- 따라서 아래와 같이 변경 설정하였음

  - VLAN 10 (PC0, PC1)
    - IP: 192.168.10.10 / 192.168.10.11
    - Subnet Mask: 255.255.255.128
    - Gateway: 192.168.10.1

  - VLAN 20 (PC2, PC3)
    - IP: 192.168.10.130 / 192.168.10.131
    - Subnet Mask: 255.255.255.128
    - Gateway: 192.168.10.129


<br/>
<br/>



<br/>
<br/>


### 참고자료
[[Cisco Packet Tracer]VLAN을 설정해보자(1) - L2 Switch](https://hsm-racoon.tistory.com/42)


