---
title:  "Docker Network"
date: 2021-01-24 19:22:00
description: Start with Docker/Kubernetes
categories: [docker]
resource: true
comments: true
---

# Docker Network
## 도커 네트워크 구조
### `$ ifconfig`
- 리눅스 시스템의 네트워크 관리를 위한 필수 유틸리티
- 현재 시스템에 설정되어 있는 네트워크 인터페이스를 설정 또는 확인하는 명령어
  - 서버가 통신하기 위해서는 네트워크 인터페이스(NIC, 랜카드)가 있어야 함 
  - 이를 설정, 변경, 확인하는 명령어
- 확인가능한 NIC 상황 내역
  - MAC 주소
  - eth0에 할당된 IP 주소
  - 브로드캐스트
  - 서브넷마스크
  - MTU (Maximum Transfer Unit)
  - RX packets (부팅 후 현재까지 받은 패킷수)
  - TX packets (부팅 후 현재까지 보내진 패킷 수)
  - Collisions
  - Interrupt 주소
  - 현재 up 되어 있는지.
    - 특정 네트워크 인터페이스의 UP/DOWN 가능

### 도커 네트워크
- 컨테이너 내부에 IP를 순차적으로 할당
- 재시작 시 변경될 수 있음
- 내부 IP는 내부망에서만 쓸 수 있는 IP이므로, 외부와 연결될 필요가 있음
- **컨테이너를 시작할 때마다 `veth` 라는 네트워크 인터페이스를 생성함으로써 이뤄짐**
- **컨테이너마다 가상 네트워크 인터페이스를 호스트에 생성하며, 이 네트워크의 이름은 veth로 시작함**
- veth == virtual eth
- `eth0`
  - 외부와 통신할 수 있는 호스트의 네트워크 인터페이스
  - veth 인터페이스는 컨테이너 시작 시 생성되었으며, 각 컨테이너의 eht0와 연결됨
  - 컨테이너의 Eth0 <-> 호스트의 Veth <-> docker0 bridge <-> 호스트의 eth0

### 도커 네트워크 기능
- 컨테이너 생성시 기본적으로 Docker0 브리지를 통해 외부와 통신가능
- 선택에 따라 여러 네트워크 드라이버를 쓸 수도 있음
- 도커 자체제공 대표 네트워크 드라이버
  - 브리지(bridge)
  - 호스트(host)
  - 논(none)
  - 컨테이너(container)
  - 오버레이(overlay)
- 서드파티 플러그인 솔루션
  - weave
  - flannel
  - openvswitch
- 127.17.0.x IP 대역을 컨테이너에 순차적으로 할당함
- `$ docker network inspect` : 네트워크의 자세한 정보를 살필 수 있음

## 도커 네트워크 종류
### 브리지 네트워크 
- 컨테이너를 생성할 때 자동으로 연결되는 Docker0 브리지를 활용하도록 설정되어 있음
- docker0이 아닌 사용자 정의 브리지를 새로 생성해 각 컨테이너에 연결하는 네트워크 구조임
- 컨테이너는 연결된 브리지를 통해 외부와 통신 가능
- `$ docker network create --driver bridge mybridge`
- `$ docker run` 또는 `create` 시, `--net` 옵션값을 통해 해당 네트워크를 사용할 수 있음
```
docker run -it --name mynetwork_container \
--net mybridge \
ubuntu:14.04 
```
- 컨테이너 내부에서 ifconfig를 입력하면, 새로운 IP 대역이 할당된 것을 알 수 있음
- 브리지 타입의 네트워크를 생성하면, 도커는 IP 대역을 차례대로 할당함
- `docker network disconnect mybridge mynetwork_container` 
- bridge 또는 overlay 네트워크 타입과 같이 특정 IP 대역을 갖는 네트워크 모드는 유동적으로 해당 네트워크를 붙이고 뗄 수 있음
- none/host 네트워크는 불가능

### 호스트 네트워크
- 네트워크를 호스트로 설정하면, 호스트의 네트워크 환경을 그대로 쓸 수 있음
- 별도로 네트워크를 생성할 필요없이, host 를 이용하면 됨
```
docker run -it --name mynetwork_container \
--net host \ 
ubuntu:14.04 
```

### 논 네트워크
- 말 그대로 아무런 네트워크를 쓰지 않음으로 외부와의 연결이 단절됨
```
docker run -it --name mynetwork_container \
--net none \ 
ubuntu:14.04 
```
- lo 외에는 네트워크 인터페이스가 존재하지 않음

### 컨테이너 네트워크
- `--net` 옵션으로 container를 입력하면, 다른 컨테이너의 네트워크 네임스페이스 환경을 공유할 수 있음
- 공유 속성은 내부 IP, 네트워크 인터페이스의 맥 주소임
- `--net container:[다른 컨테이너의 ID]`

### 브리지 네트워크와 --net-alias
- 브리지 타입의 네트워크와 run 명령어의 --net-alias 옵션을 함께 사용하면, 특정 호스트 이름으로 컨테이너 여러개에 접근 가능함
- 도커 내장 DNS에서 **라운드 로빈 방식**으로 각 컨테이너를 할당하여 실행함
1. 특정 컨테이너에서 ping alias명 
2. 도커 내장 DNS에 alias 명 호스트 이름 변환요청
3. 컨테이너에 라운드 로빈 방식으로 IP 목록 반환
4. 받은 IP로 ping을 요청
   - IP리스트의 첫번째 IP를 사용함으로 매번 다른 IP로 ping을 전송함
   - `$ dig alias-net`으로 IP 리스트 확인 가능
   - 라운드로빈 방식의 리스트 반환으로 계속 리스트 순서가 달라짐

#### 라운드 로빈이란?
- 프로세스들 사이에 우선순위를 두지 않고, 순서대로 시간단위(Time Quantum/Slice)로 CPU를 할당하는 방식의 CPU 스케줄링 알고리즘
- 컴퓨터 자원을 사용할 수 있는 기회를 프로그램 프로세스들에게 공정하게 부여하기 위한 한 방법
- 각 프로세스에 일정시간을 할당하고, 할당된 시간이 지나면 그 프로세스는 잠시 보류한 뒤 다른 프로세스에게 기회를 주고, 또 그 다음 프로세스에게 하는 식으로, 돌아가며 기회를 부여하는 운영방식

#### Docker DNS
- 호스트 이름으로 유동적인 컨테이너를 찾을 때 주로 사용됨
- `--link` 옵션으로 컨테이너의 IP가 변경되어도, 별명으로 컨테이너를 찾을 수 있도록 DNS에서 자동으로 관리

## MacVLAN 네트워크
- 호스트의 네트워크 인터페이스 카드를 가상화하여 물리 네트워크 환경을 컨테이너에게 동일하게 제공
- 물리 네트워크 상에서 가상의 맥 주소를 가지고, 해당 네트워크에 연결된 다른 장치와 통신이 가능함
- MacVLAN 사용하는 컨테이너들과 동일한 IP 대역을 사용하는 서버 및 컨테이너들은 서로 통신이 가능함
