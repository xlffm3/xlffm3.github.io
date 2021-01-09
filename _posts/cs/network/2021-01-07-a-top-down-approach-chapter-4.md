---
title: "[Computer Networking : A Top-Down Approach] CH 4. Network Layer"
categories:
  - Network
tags:
  - Network
toc: true
toc_sticky: true
last_modified_at: 2021-01-07T16:55:00-05:00
---

## 4.1. Introduction

Network Layer는 Host의 Transport Layer가 내려준 Segment를 Datagram으로 캡슐화하고 Receiver Host에게 전달해주는 역할을 한다. Receiver는 반대로 Datagram의 헤데를 때고 Segment로 디캡슐화하여 Transport Layer로 올려준다.

Network Layer 프로토콜은 Host와 Router 모두에게 탑재되지만, Host의 네트워크 프로토콜은 라우팅 기능이 약하다. Subnet 외부로 나가기 위한 Edge Router 혹은 Gateway 정도를 찾아주는 제한적인 기능이다. Network Core의 Router가 라우팅 루트를 계산하는 등 더 강력한 기능을 제공한다.

### 4.1.1 Forwarding and Routing

Routing은 Source Host에서 Destination Host까지 패킷이 전달되는데 거쳐야 하는 경로를 알고리즘을 통해 계산하고, Forwarding Table을 세팅하는 것이다. 모든 Router들은 Forwarding Table을 가지고 있다. 반면 Forwarding은 실제 라우터에서 패킷을 받았을 때 헤더와 Forwarding Table을 참조하여 알맞는 Output Link로 패킷을 보내주는 것이다.

비유하자면 Routing은 전체 여행 계획을 짜는 것이고 Forwarding은 여행을 다니면서 만나게 되는 여러 갈래의 교차로에서 표지판을 보고 알맞은 길을 택해 통과하는 것이다.

### 4.1.2. Network Service Models

Datagram을 전달하는 채널을 위한 서비스 모델은 크게 두 가지로 분류된다.

* 독립적인 개별 Datagram 위한 서비스.
  * Datagram을 특정 지연 시간 내에 전달하는 것을 보장한다.
* Datagram의 플로우를 위한 서비스.
  * Datagram의 전달 순서와 최소 Bandwidth 플로우를 보장한다.
  * 각 패킷 사이의 간격 등을 제한하는 서비스가 있다.

Network는 크게 Circuit Switching과 Packet Switching으로 나뉘며, Packet Switching은 Virtual Circuit과 Datagram으로 나뉜다. Network Layer에서 Virtual Circult은 Connection-Oriented Service를 제공하며, Datagram은 Connectionless Service를 제공한다. 이는 TCP와 UDP 관계와 비슷하다. 그러나 다음과 같은 차이점이 있다.

* TCP/UDP는 Transport Layer에서 Process간의 통신이지만, Virtual Circuit과 Datagram은 Host-To-Host 서비스를 의미한다.
* TCP/UDP는 Network Edge의 Process간의 상호작용이라면, Virtual Circuit과 Datagram은 Network Core에 있는 모든 것들이 협력해서 구현하는 결과이다.
* 유저는 TCP나 UDP를 선택할 수 있으나, Virtual Circuit과 Datagram 사이의 Network Service를 선택할 수 없다.
  * ISP가 제공하는 개념이기 때문이다.

<br>

## 4.2. Virtual Circuit and Datagram Networks

### 4.2.1. Virtual-Circuit Networks

Virtual Circuit은 데이터 전송 전후로 연결에 대한 Call Setup과 Tear Down이 수반된다.

* Call Setup을 위한 메시지가 목적지에게 도달하면서 Hop이 어디부터 어디까지인지 등 VC 경로가 결정된다.
* Call Setup시 VC에 해당하는 아이디가 부여되는데, 전송되는 패킷들이 해당 아이디를 헤더에 달고 있다.
* Packet Switching에 속하지만 Bandwidth 및 버퍼와 같은 Link와 Router 자원들이 VC에 할당(Predictable and Dedicated)될 수 있다.
* VC 경로에 위치한 Router들은 자신들을 통과하는 연결에 대해 상태 정보를 유지하고 있어야 한다.

VC별로 경로와 VC 번호(ID)를 할당할 때, 해당 정보들을 Forwarding Table에 저장한다. 해당 VC에 속한 패킷들은 도착지 IP가 아닌 VC 번호를 통해 Link와 Router를 이동한다. 이 때, VC 번호는 A부터 B까지 가는 모든 경로의 링크들이 동일하지 않고, 경로에서 링크가 바뀜에 따라(Router를 만나 갈아탐에 따라) 서로 다른 VC 번호들이 부여된다.

예를 들어, Call Setup시 경로 상의 N번째 Router는 이전의 N-1번째 Router에게 "내게 Datagram을 보낼 때 VC 번호 K를 붙여라"는 식으로 설정된다.

1개 VC에 속한 모든 Link에 대해 VC 번호를 통일하게 하면, Link별로 고유 VC 번호를 할당하는 것 보다 결과적으로 더 많은 VC 번호를 필요로 하기 때문이다. 각 Link에는 복수개의 VC가 지나가게 되는데, 1개의 Connection에 대해 경로의 모든 링크를 1개 VC 번호로 통일하게 되면 다른 VC 번호들과 겹치지 않는 값을 할당해야 한다. 할당하려고 하는 VC 번호를 다른 링크가 사용하는지 확인하는 등 컨트롤 오버헤드가 발생한다.

그러나 VC에 속하는 Link들이 서로 다른 VC 번호를 가지고 있다면? Router는 1번 Link에서 들어온 12번 VC와 2번 링크에서 들어온 12번 VC는 서로 다른 것으로 인지할 수 있게 되며, 이는 결과적으로 bit를 덜 사용하게 된다.

VC는 Router를 통해 추가적인 연결 상태 정보를 Forwarding Table에 저장할 수 있다. signaling protocols을 통해 연결에 대한 Setup, 유지, Tear Down 작업을 수행한다. ATM 등 전화 네트워크 PSN에서 사용하지만, 오늘날 인터넷에서는 쓰이지 않는다.

### 4.2.2. Datagram Networks

별도의 Call Setup이 없으며 Network 계층은 UDP처럼 Datagram을 그냥 전송하는데 최선을 다한다. Router들은 목적지 Host 주소를 보고 패킷을 포워딩한다.

Forwarding Table에는 목적지 IP 주소로 가기 위해 현재 Router에서 어느 Output Link를 사용해야 하는지 등에 대한 정보가 정의되어 있다. Router는 Datagram의 헤더에 있는 목적지 Host IP 주소를 보고 Forwarding Table을 참조하여 Datagram을 알맞은 Output Link로 보내준다.

현재 인터넷 네트워크에는 너무 많은 IP들이 존재하기 때문에, 각 IP별로 라우팅에 필요한 모든 맵핑 정보를 Forwarding에 정의할 수 없다. Forwarding Table이 너무 비대해지면 Table에서 원하는 Output Link 맵핑 정보를 Look-Up 하는데에도 많은 시간이 걸린다.

이에 대한 해결책으로 Table Entry들을 Aggregate한다. Router는 자신과 가까이 있는 Local 주소들에 대해서는 Forwarding Table에 맵핑 경로를 구체적이고 개별적으로 명시한다. 그러나 멀리 위치한 Local 주소들에 대해서는 개별적으로 맵핑 경로를 명시하지 않고, 해당 지역으로 향할 수 있는 경로로 뭉뚱그려 정의한다.

예를 들어 고속도로를 타고 서울에서 경상도 XX시를 가는 중이라고 할 때, 서울 인근의 고속도로 표지판에는 경상도 XX시, YY시, ZZ시 등 구적인 경로가 나와있지 않다. 다소 러프하게 전라도는 어느 방향, 경상도는 어느 방향 등 뭉뚱그려 설명되어 있다. 경상도 방향에 가까워질 수록 XX시, YY시, ZZ시 등 구체적인 경로 정보가 표지판에 나타난다.

### 4.2.3. Origins of VC and Datagram Networks

전화기 등이 VC를 사용하며 인터넷은 Datagram을 사용한다. 인터넷의 경우, End System가 대게 컴퓨터이기 때문에 성능이 우수하여 복잡한 작업의 처리가 가능하다. 그렇기 때문에 복잡한 작업은 Network Edge 쪽으로 몰아주고, Network Layer 등은 데이터를 전달하는 것에만 집중할 수 있도록 구조를 단순화하는데 초점을 맞추었다. 또한 인터넷을 사용하는 Application 중에는 유연한 서비스를 제공하는 것들이 많아 엄격한 Timing 준수 등 오버헤드와 관련된 제약 사항이 필요없는 경우가 많다. 아울러 Network를 연결하는 Link들의 타입이 다양해서 속도 등 특성도 천차만별이다. 그러다 보니 Datagram을 사용하는 Network의 속도가 매우 우수하다.

반면 VC의 경우 전화와 같은 End System은 복잡한 작업을 처리할 수 없다. 따라서 여러 복잡한 작업들이 Network Layer에서 진행되며 Application 자체도 대화와 관련된 것들이 많아 엄격한 Timing과 Reliablity 등 요구사항이 많다. 따라서 성능 측면에서 Datagram에 비해 우수하지 못하다.

<br>

## 4.3. What’s Inside a Router?

Management Control Plane은 프로토콜을 통해 Routing을 계산하는 등 Software의 영역에 가깝다면, 실제 데이터를 Forwarding하는 Data Control Plane은 Hardware 영역이다. Router의 구조는 크게 3가지로 분류된다.

* Input Ports.
* Switching Fabric.
* Output Ports.

### 4.3.1. Input Processing

Input Port 내부는 크게 3가지로 구분된다. 패킷을 Physical - Link - Network Layer 순으로 헤더를 떼서 Segment로 만든다.

* Input Port Function(Line Termination).
  * 전달받은 패킷을 bit 단위로 인식하며, bit를 모아서 Frame을 형성한다.
* Link Layer Protocol(Data Link Processing).
  * Ethernet 등.
  * Frame이 1 Hop을 잘 건너왔는지 등 Link Layer의 프로토콜을 수행한다.
* Decentralized Switching(Lookup, Fowarding, Queuing).
  * 제대로 된 Frame이라면 Forwarding Table을 참조하여 경로를 찾고 Queue에서 대기한다.
  * 이상적인 목표는 Link Transmission Rate(Bandwidth)로 들어오는 패킷들을 병목 없이 처리하는 것이다.

여러 Link를 통해 Router로 유입된 Datagram이 Input Port의 Queue 버퍼에서 대기하고 있으면, Switching Fabric은 Datagram을 뽑아 적합한 Output Link로 옮겨준다. Switching Fabric의 처리 속도가 느리다면 Input Port의 Queue 버퍼가 쌓이게 되면서 딜레이 및 패킷 손실이 발생한다.

특히 Switching Fabric은 2개 이상의 패킷을 동일한 Output Link로 동시에 옮기지 못한다. 이 때, HOL(Head Of Line) 블락킹이 발생하게 된다. 2개의 Input Port가 있고 각각의 Queue 버퍼에서 대기 중인 선두의 Datagram들이 동일한 Output Link로 나가기 위해 경쟁한다고 가정해보자. 경쟁에 패배한 Datagram은 다른 Datagram이 Output Link로 옮겨지는 동안 대기해야 한다. 경쟁에 패배한 Datagram은 Queue의 선두에 있기 때문에, 그 뒤에 위치한 Datagram들은 자신이 가고자 하는 Output Link가 유휴상태임에도 최선두 Datagram 때문에 처리되지 못하고 대기해야 한다.

### 4.3.2. Switching

Input으로 들어온 Segment를 Output Link로 전달해주는 Switching Rate는 N Inputs / Output Line Rate이다. Switching Fabric에서 Switching 작업이 발생하게 된다. Switching 작업은 크게 3가지로 구현될 수 있다.

* Switching via Memory.
  * 1세대 방식으로, 고전적인 컴퓨터의 CPU 통제 하에 Switching이 발생한다.
  * Input Port와 Output Port 사이에 메모리가 있고, 패킷이 메모리로 복사된 다음 적합한 Output Port로 이동한다.
  * 즉, 1개의 Datagram이 2번의 System Bus를 타게 된다. (Bus Crossing)
    * 메모리의 Bandwidth에 따라 Switching 속도가 제한된다.
* Switching via Bus.
  * Input Port와 Output Port를 독점적으로 연결하는 Bus 구조를 사용한다.
  * Datagram을 Input Port 메모리에서 Output Port Memory로 이동하는데 공용 버스를 사용한다.
  * Switching 속도는 Bus의 Bandwidth에 따라 결정된다.
    * 각 Connection들이 서로 Bus에 타려고 경쟁하기 때문이다.
* Switching via Interconnection Network
  * Single Bus 방식의 대역폭 제한을 극복하기 위해 고안되었다.
  * 크로스바 방식 등은 멀티 프로세서들간의 연결을 위해 개발되었다.
  * 더 빠른 성능을 위해 Datagram을 고정 길이의 Cell로 쪼개서 이용한다.

### 4.3.3. Output Processing

Input Port 구조와 동일하지만 순서만 다르다. Input Port가 Physical - Link - Network Layer 순으로 패킷의 헤더를 때고 Segment로 만들면 Switching Fabric에서 길을 찾는다. 이후 Output Port에서 Network - Link - Physical Layer 순으로 Segment에 Header를 붙여 Link를 물리적으로 1 Hop씩 이동하게 된다.

Queue에 쌓인 Datagram을 전송하는 방식에는 다양한 스케쥴링 규칙이 있다. 똑같이 Buffering으로 인한 패킷 손실이 발생할 수 있다.

### 4.3.4. Where Does Queueing Occur?

권장되는 Buffering Capacity 양은 RTT * C(Link Capacity) / N Square(파이프를 지나는 플로우의 개수)이다.

<br>

## 4.4. The Internet Protocol (IP): Forwarding and Addressing in the Internet

인터넷의 Network Layer에는 Routing 프로토콜 외에도 IP 프로토콜과 ICMP 프로토콜이 있다. Routing 프로토콜이 Management Control Plane이라면, IP 프로토콜은 Data Control Plane이다.

### 4.4.1. Datagram Format

Datagram의 너비는 32bit(4byte)이며, 헤더는 고정적인 파트가 총 5개이다. 또한 가변길이의 필드를 가질 수 있다. 헤더에는 다음과 같은 필드가 있다.

* Protocol 버전 넘버.
* 가변길이 헤더의 길이.
* Data 타입.
* TTL(Time To Live) : Hop을 건널 수 있는 최대 시간이며 고스트 데이터를 방지한다.
* Upper Layer : Payload에 실린 Segment가 UDP인지 TCP 인지 명시한다.
* Flag, Offset, Checksum 등.

옵션 필드에 경유 경로를 확인하는 등의 정보 추가 기능이 있다. IP Datagram 크기는 고정 크기(20byte의 TCP 고정 헤더와 20byte의 IP 고정 헤더) 및 그 외 Application Layer 오버헤드의 합이다.

#### IP Datagram Fragmentation

Link Layer의 Frame이 전달할 수 있는 가장 큰 데이터의 양을 MTU(Maximum Transmission Unit)이라고 한다. Link 타입마다 MTU가 다르다.

크기가 큰 IP Datagram은 Link Layer에서 하나의 Frame으로 캡슐화가 될 수 없기 때문에, Network Layer에서 MTU 정보를 가지고 Router가 하나의 Datagram을 더 작은 Datagram으로 쪼갠다. Datagram의 파편들은 각각 독립적인 Datagram으로서 운송된다. 잘라진 Datagram들은 다음 Link에서 재조립되지 않고, 최종 목적지에서 재조립된다.

Fragmentation 이슈는 Network Layer에서 불가피하게 처리할 수 밖에 없다. 그러나 복잡한 기능은 최대한 Network Edge에 위치시키는 것이 Network의 철학이기 때문에, 재조립은 Network Edge에서 관장하게 된다. Link마다 분리와 조립이 반복되면 오버헤드가 커진다.

목적지의 Network Layer는 IP Datagram의 Header를 참고하여 파편화된 Datagram들을 조립하고, Segment 메시지를 추출하여 상위 계층으로 전달한다.

### 4.4.2. IPv4 Addressing

IP 주소란 Host와 Router 인터페이스를 위한 32비트의 주소이다. 인터페이스란 Host와 Router간, 그리고 물리적인 Link 사이의 연결(문)을 의미한다. Router는 여러 인터페이스를 가지고, Host는 한 개 혹은 두 개의 인터페이스(WiFi, Ethernet)을 가진다.

예를 들어, 1개 Router는 3개의 Host를 향해 각각 다른 IP 인터페이스를 가지고 소통할 수 있다. Host는 하나의 IP 인터페이스를 가지고 다른 노드들과 소통한다. 일반적으로 8비트씩 4파트를 점으로 구분하여 나눈다.

IP 주소는 Subnet 파트와 Host 파트로 나뉜다. Subnet 파트는 IP 주소 중 앞 부분에 해당하며 Host 파트는 뒷 부분에 해당한다. Subnet이란 IP 주소들 중 동일한 Subnet 파트를 가진 인터페이스 디바이스들의 집합을 의미한다. Subnet에 속한 디바이스들은 Router를 거치지 않고 서로에게 물리적으로 도달할 수 있다.

xxx.xxx.xxx.xxx/24와 같이 마지막에 Subnet Mask라는 bit를 명시하여 IP 주소 중 어느 부분까지가 Subnet 파트인지 명시할 수 있다. 일반적으로 ISP는 CIDR(Classless Interdomain Routing)을 통해 일련의 주소 블럭을 서브넷으로 할당한다. 주소는 a.b.c.d/x의 형식이며, 각 클래스에 따라 수용할 수 있는 호스트의 개수가 다르다.

#### Obtaining a Host Address: the Dynamic Host Configuration Protocol

> Hierarchy : DHCP - UDP - IP - Ethernet - Physical

IP 주소는 크게 시스템 관리자가 시스템 파일에 하드코딩하거나, DHCP(Dynamic Host Configuration Protocol)를 통해 할당되어진다.

DHCP는 Application Layer의 프로토콜이며, 서버로부터 호스트가 접속할 때마다 IP를 동적으로 할당받아 사용하고, 접속을 종료하면 다시 회수한다. 이를 통해 다른 Host에게도 해당 IP를 할당이 가능하다. 특히 모바일 유저는 항상 특정 Subnet에 속해있지 않기 때문에 고정 IP보다 동적인 IP가 자원을 덜 낭비한다. 주소 재사용을 통해, 궁극적으로 주어진 IP 주소 개수보다 더 많은 수의 호스트들에게 서비스를 제공할 수 있다.

End System이 Subnet 영역에서 시스템일 키게 되면, DHCP 클라이언트 프로그램이 자동으로 실행되면서 해당 Subnet 지역을  관리하는 DHCP 서버와 통신하게 된다. 일련의 요청 - 응답 과정을 통해 인터넷 접속에 필요한 IP 주소를 할당받게 된다. DHCP는 IP 할당 이외에도, 클라이언트의 First-Hop Router과 DNS 서버의 주소 및 IP 주소의 Subnet 파트를 알려준다.

IP 주소 체계 또한 계층적이다. Forwarding Table은 Router와 목적지 IP까지의 모든 맵핑 정보를 개별적으로 정의하지 않는다. 멀리 위치한 IP들에 대한 경로는 뭉뜽그려서 해당 IP들이 속해있는 특정 기관이나 Subnet으로 향하는 맵핑 정보만을 Aggregate한다. 이를 통해 Forwarding Table에서 Routing 엔트리 정보의 양을 줄일 수 있다.

Router는 자신과 가까운 목적지일 수록 더 긴 Prefix의 Forwarding Entry를 가지며, 멀 수록 더 짧은 Preifx Forwarding Entry를 가진다. "A.A.A로 시작하는 주소는 내 쪽으로 포워딩하라''는 Forwarding 조건은 그 뒤에 ``or``로 "B.B.B.B 주소도 내 쪽으로 포워딩해라" 등의 추가 조건을 추가할 수 있다. 인터넷 라우팅은 여러 Forwarding 조건들이 중복되는 경우 가능한 가장 상세하고 긴 Prefix에 해당하는 조건을 선택해 Forwarding한다.

인터넷진흥원 같은 한국 TLD 서버는 .kr 도메인 관리와 더불어 기관들에게 Subnet 주소 할당 등의 역할도 한다.

#### Network Address Translation (NAT)

CIDR은 클래스 기반의 IP 할당 전략이지만, 어떤 클래스는 가용할 Host의 개수가 부족하고 어떤 클래스는 남아도는 등 자원을 효율적으로 사용하지 못했다. IP 사용자가 폭발적으로 늘어나면서 효율적인 사용의 중요성이 증대되었다.

NAT는 Subnet의 각 End System들에게 IP를 할당하는 것이 아니라, Subnet 자체에게 하나의 IP를 주는 것이다. Subnet에 속한 End System들은 Private IP를 사용하지만, 외부 Subnet과 Datagram을 주고 받을 때에는 모두 하나의 NAT IP 주소를 통해 소통한다. 다만 End System별로 포트 번호가 다르다.

* 원리.
  * 다른 Subnet과 소통할 수 있는 Subnet의 First Edge Router가 있다.
  * Private IP 및 포트 번호를 가진 Subnet 내부 Host의 Datagram이 외부로 나갈 때, First Edge Router는 Datagram의 Source 주소를 NAT IP 주소와 새로운 포트 번호로 변경한다.
  * 이후 외부에서 NAT IP를 Destination 주소로 하는 Datagram이 First Edge Router로 들어온다.
  * First Edge Router는 포트 번호와 Translation Table을 참고하여 NAT IP로 이루어진 Destination 주소를 실제 Host의 Private IP 주소로 변경하고 Datagram을 전달한다.

NAT IP를 사용하면 Subnet에 속한 Local Host들은 일련의 IP 블록을 받을 필요 없이, 단 하나의 IP만 받으면 된다. 특히 Private IP들은 외부에 Subnet 외부로 노출되지 않기 때문에 보안 측면에서 우수하며, 자유롭게 변경이 가능하다. 또한 기기들의 주소 변경 없이 ISP를 변경할 수 있다.

효율적으로 IP 자원을 사용할 수 있지만, Network Layer인 Router가 포트 번호같은 Transport Layer까지 건들이기 때문에 논란이 다소 많다. 또한 NAT Traversal 문제가 있다. 서버가 NAT IP를 사용하는 Subnet에 속해 있으면 Private이며, 외부에 있는 클라이언트는 해당 서버를 향한 NAT IP를 먼저 알 수 없다. Subnet 내부의 서버가 먼저 외부의 클라이언트에 접촉을 시도해야 Private IP - NAT IP간의 Translation Table 정보가 라우터에 저장되기 때문이다.

이에 대한 솔루션으로 테이블을 수동 생성하는 것이다. 정적으로 특정 포트 번호로 온 연결 요청을 서버로 Forwarding하도록 설정하는 것이며, UPNP를 통해 이를 자동화할 수 있다.

  * 서버가 나트 라우터에게 public IP 주소를 배움.
  * p2p 방식은 nat 뒤 숨겨진 서버 문제를 관리하기 어려움. 클라-서버는 가능한데..

### 4.4.3. Internet Control Message Protocol (ICMP)

호스트와 라우터들이 사용하며, 네트워크 레벨 정보를 통신하기 위해. (소스 호스트에게 어떤 에러가 있는지를.)
  * 에러 리포팅, 리퀘스트나 리플라이 에코 (핑, 서로 살아있는지 확인) 등.
  * ICMP는 IP(네트워크 레이어)위에 있다.
    * ICMP 메시지는 IP 데이타그램 안에 인캡슐화된다 (tcp 세그먼트처럼.)
icmp 메시지 : 타입, 문제가 퇴는 아이피 데이터그램의 코드.


TTL(Time to Live) : 패킷이 라우터에서 폐기되기 전에 네트워크에 존재하는 시간. 또는 홉을 나타냄.

Traceroute : TTL을 1부터 차근차근올리며 계속 udp를 보냄. 소스에서 출발하다가 중간에 계속 EXPIRED되지만 특정 N이 됬을때 DEST에 도착. 근데 dest port를 이상한걸로 설정해놧기 때문에 응답으로 port unreachable 응답이옴. 이걸통해 경로확인.

## IPv6

32비트 주소들은 조만간 전부 할당될것임. 주소가 부족해!
CIDR, DHCP, NAT로 타이트하게 관리해도 부족함.
IPv6는 128비트를 씀.

추가적으로 디자인하면서 고려한 사항
  * 네트워크의 물리적 기술 발달하면서, 과거에는 link transimission이 오래걸려서 이쪽이 보틀넥났는데 계쏙 빨라지면서 이젠 Router의 스위칭부분이 보틀넥남.
  * 라우터쪽 처리, 포와딩을 최적화하기 위해서는 헤더 포맷을 고정 길이 헤더로 만드려고 함. 40바이트짜리. (가변길이 안쓰려고함, Optional로 뒤에 헤더 붙이는 방식으로 대체.)그리고 fragmentation 허용안하게.
    * ICMPv6에서 패킷이 투 빅하다에 대한 예외 코드가 생김!.
    QoS 헤더를 지원한다 (미래엔 멀티미디어가 중요하기 때문에)

  * 포맷
    * priority (플로우의 데이터그램들중 우선순위)
    * flow Label : 같은 플로우에서 데이터그램들을 식별하는것.
    * nextHeader : Optional 헤더가 있는지, 아니면 다음에 오는게 UDP인지 tcp 헤더인지 등.

  CHECKSUM 등 삭제됨.
  멀티캐스트 그룹 관리 기능을 제공.

## transition IPv4 to v6

* 모든 라우터들이 동시에 업그레이드 불가능.
* 하루아침에 v6되는게 아니고 중간 중간에 추가, Tunneling.
  ipv4와 ipv6가 서로 이해하는게 관건인데 터널링을 사용함.
    * ipv6 데이터그램이 ipv4 라우터들과 ipv4 데이터그램 속에서 페이로드로 사용되게 인캡슐해서 사용함.
    * ipv6 사이에잇는 ipv4들이 컴파일 에러가 나지 않게 하도록.
    * 즉 ipv6들끼리는 지들 헤더로 소통하게 하는데 ipv4를 만나면 ipv4가 이해하게끔 ipv4 데이터 형태로 주는거임.

## routing 알고리즘 프로토콜.

* 라우팅 알고리즘의 목적 : 패킷이 목적지에 찾아갈 수 있도록 경로 분석하고 패킷의 포워딩 테이블을 세팅해놓은것

Graph abstraction으로 추상화하는데

vortex set(set of routers), set of links (edge set)으로 Graph 객체만듬

인터넷에서는 모든 링크의 코스트를 1로 둠, 경우에 따라서 각각 다르게 줄 수 있음.
* 링크의 bandwidth가 크면 클수록 코스트가 낮고, congestion 등 관련해서..
* 링크 사용 비용 등을 넣어서 추정할수도있음.

라우팅 알고리즘의 목적 : 최소 비용의 경로를 발견해내는것.
가중치 등을 분석해서 하겠지 ㅇㅇ

## 라우팅 알고리즘 분류

* global (link state)
  * 각 라우터가 네트워크 전체 정보를 가지고 있음.
  * 각 라워의 정보는 항상 consistent하게 모든 라우터가 전체 정보를 유지해야함.
* decentralized(distance vector)
  * 각 라우터는 지엽적인 (물리적으로 가까운 이웃, 이웃과 연결 코스트)만 직접 안다.
  * 옆집들과 소통함, 나는 xx 서브넷 갈 수 있는데 코스트가 얼마야 정보를 주변 이웃과 교환함.

다른 분류 기준

  * static vs dynamic?
    * static 알고리즘 (route 변화가 시간에 따라 천천히 변화하는거)
      * 링크의 길이 등은 코스트가 거의 변함없어서 라우트 네트워크가 변함이 많이없음. 그래서 라우트 (알고리즘)업데이트 주기가 길어도됨.
    * dynamic :  route 변화 빠름 (주기적 업데이트, 링크 비용 변화에 따른 변화)
      * route를 더 효율적으로 하기 위해 dynamic 알고리즘을 쓸 수 있음. 자주 변함. 빈번하게 라우팅 인포메이션이 바뀌면서 오버헤드가 많이 발생할 수 있으나, 주기를 너무 길게 잡으면 필요한만큼 주기가 빨리 바뀌지않음.

## global r.a

Dijkstra's algorithm

* 하나의 소스 노드로부터 네트웤의 다른 모든 n개의 destination과의 짧은 경로를 계산하고 찾음.
  * 하나의 노드는 다른 노드들과의 정보를 모두 브로드캐스트함.
  * 이를 통해 전체 그래프를 만듬.

* n번의 반복이 발생.
* 이를 통해 node tree graph를 만들어서 한 노드에서 다른 노드와의 최단거리 구하기 가능.
  * 비교는 최대 n * (n+1)  / 2를 하다보니 O(n^2)
  * 가장 효율적으로 구현하면 O(nlogn)

## distance vector(decentralized)

Bellman-Ford Equation

x에서 y까지의 최소 비용경로는 x의 이웃들까지 가는 비용 + 이웃들에서 y가는 비용들 중 최소값.

DV를 위해서는 각 노드 x는 이웃들과의 비용을 알고 있고, 이를 이웃들에게 업데이트함.

각 노드들이 변경되는 것은 크게 2가지이다 (본인의 로컬 링크 코스트가 변하거나, 이웃으로부터 변경 메시지를 받거나.)
그럼 다시 내 주변 이웃들간의 거리를 재계산하고, Distance Vector(다른 노드까지의 최단거리)들에 대해 이웃에게 노티.

* 좋은소식은 빨리 가면 좋은데..
링크코스트가 바꼈는데 두 노드들간 count to infinity 문제가 발생함. distance가 현실 반영을 못해서,,,  a노드 b노드가 c노드로 가는길을 a와 b가 의존을하게되서
b타면 2 a타면3 b타면4..... n넘었을대 그제서야 판단함.
  * 두 노드가 의존하게 되면서 현재 개븅신짓이란걸나중에 알게됨.
* posinoned reverse : z라우트가 x로 가는데 y를 통해야 한다면, z는 y한테 x로 가는 길이 무한이다 라고 먼저 말함.  그러면 infinity로 생각해서 해당 길은 고려안하게되고 다른길 파악해서 무한루프를 줄일수잇음.
  * 모든 경우를 완벽하게 해결하진못함.


* 단점 bad news travels slow : 어느 노드와의 길이 끊어진경우. 다른 노드들이 해당 노드와의 거리를 무한으로 산정해서 계속 반복돌게됨. 알고리즘이 안정화되기  까지 그 이전에 반복을 꽤 많이 돔. (x 노드로 가는거 무한이야 아직 계산이안되서) 그래서 계속 루플를 돌며 계산하게됨.
  * posinoned reverse? 있는데 이건 나중에..

## 비교

message complexity
* LS : N개 노드와 E개 링크 있으면 O(N^E) 메시지 센트
* DV : 근접 이웃들관의 메시지 교환

convergence speed (정보 변경으로 라우팅 업데이트)
LS : O(n^2) 앍골맂므은 O(n^E) 메시지 필요
DV : Convergence는 다양. 좋을땐 라우팅 루프만큼 돌지만, 최악은 무한대로 돌수있음.

robustness : 만약 라우터가 맛이가면?

LS : 노드는 잘못된 링크 비용을 홍보한다. 각노드는 자기의 테이블만을 계산. 문제가 발생한 부분근처의 경로는 잘못되겠지만 나머지쪽은 그래도 괜찮음.
DV : DV 노드는 잘못된 경로 비용을 홍보한다 (모든 목적지에 대한 경로 비용을 계산하다보니.). 각 노드들의 테이블은 다른 노드들이 사용하기 때문에 에러가 네트워크 전반으로 펼쳐져벼린다.

## discussion


* oscillations possible : 다이나믹 링크 비용은 트래픽의 양과 같다.
  * 어느 특정 링크에서 발생하는 트래픽이 많으면, 그만큼 병목현상 등 비용이 많이 발생할 수 있으니 다른 링크를 찾으려고 라우팅을 업데이트함.
  * 이런 트래픽 상황에 기반한 노드의 비용이 계속 바뀌기 때문에 계속 업데이트 되며, 다이나믹 라우팅 알고리즘을 채택하면 계속 테이블과 목적지에 대한 경로가 바뀌게 됨.
    * tcp는 순서대로 오는게 중요한데, 데이터 전송중에 계속 다이나믹하게 경로가 변경되면 outoforder가 올 수 있음.
    * 이는 ack 재전송 등 앱 계층 성능에 영향, 또한 데이터가 전송되는 와중에 테이블이 변경되는데 미처 모두에게 퍼지지 못해 잘못된 정보로 가는 애들은 routing loop에 빠짐.

## Hierarchical Routing

네트웤이 커지면?

LS : 노드들이 가져야하는 정보가 방대해질 수 있음
DV : 또한 커지면 커짐 ㅠ

전체 네트웤을 하나의 알고리즘으로 돌리지는 않음.  왜? 네트웤 스케일이 엄청나게 큼. routing table exchange 규모가 너무 커질 수 있음.

administrative autonomy 기관 : internet이란 네트워크의 네트워크. 네트워크 어드민은 자신의 네트워크 속에서 라우팅을 컨트롤하고싶을수있다. (외부에게 내부 테이블 정보를 일일이 알리지 않고.)

따라서 라우팅을 Autonomous system(as)로 나눈다. 제한된 지역의 한 기관에 속한 라우터들을 aggregate하는건데, 같은 AS에 속한 라우터들은 같은 라우팅 프로토콜을 돌린다.
속해있는 라우터들에 대한 라우팅 테이블을 만듦.

이를 Intra AS 라우팅 프로토콜이라고 하며, 서로 다른 AS에 속한 라우터들은 다른 라우팅 프로토콜을 돌릴 수도 있다.

AS와 AS의 엣지에 있는 라우터들끼리를 연결해주는것은 gateway router라고 한다.

Intra AS Subnet과 Interconnected AS 두가지로 나뉠수있다.
intra는 하나의 프로토콜로 다 돌릴 수 있으나, inter된 as들간의 라우팅을 위해 inter AS 라우팅 프로토콜이 있음. 이걸 협력해서 포워딩 테이블 만듬.

AS 내에 속한 subnet들은 하나의 라우팅 프로토콜 이용, Gateway router간을 통해,
이 때 InterAS routing 프로토콜을 사용. 다른 AS를 통해 어떤 목적지에 도달할 수 있는지 정보를 교환해야함. 또한 해당 정보를 as 내의 다른 노드들(gateway router가 아닌 것들)에게도 해당 정보를 공유함. 이 모든게 interAS routing의 일.

만약 하나의 AS에서 다른 AS의 서브넷으로 가는데 복수개의 외부 as를 거칠 수 있다면, 어떻게 라우팅을 셋팅하는가? 이 또한 inter AS routing이 협력.

이 때 hot potato routing을 자주 쓰는데, 이동해야할 때 여러개의 gateway router중 현재 나와 가장 가까운 gateway router를 통해 갈 수있는 경로를 선택한다. 현재 내가 속한 AS에서 가장 리소스를 덜 사용하고 Gateway를 통해 빠져나갈 수 있는 경로를 택하는것. 나와 거리가 먼 gateway router를 사용해 다른 as로 나가는 경로를 택하면, 현재 as에서 많은 홉을 이동하며 자원쓰기때문.

<br>

## Intra-AS Routing 프로토콜들

IGP(Interior Gateway Protocol)이라고 함.

RIP, OSPF, IGRP 등이 유명.

rip : 가장 초창기에 사용, distance vector 사용. routing information protocol.
  * 링크 코스트들을 매우 단순하게 측정하여, (대부분 링크가 비슷한 그런 네트워크를 대상으로함)결국 링크 코스트의 합은 hop counts가 됨. distance metric. 맥스 hop 카운트는 15.
  * 30초마다 이웃들의 응답메시지에 반응하여 DV 정보를 교환함.
  * advertisement 메시지에 Distance vector를 실어 보냄. 실릴 수 있는 목적지 서브넷의 개수는 최대 25개.
  * link failrue가 발생하면? 두 라우터가 더이상 정보를 공유할수없음 ad. 180초동안 이웃들과 통신안되면 link가 fail 했다고 판단하며 해당 이웃을 통해 갈 수 있는 경로들을 전부 무효화시킨 뒤, 다시 경로 측정해서 이웃들에게 전파.
  * poision reverse : DV 방식이라 3개 노드가 관여된 경로에서 한개 경로의 링크코스트 변경되었을 때 계속 무한루프돌수있으나 최대 경로가 16이기 때문에 어느정도 방지.
  * rip는 라우팅 프로토콜로 어플리케이션 레벨 프로세스 레이어임. route-d 데몬.
  * advertisement 메시지를 만들고 udp 패킷에 해당 정보를 실음. network 계층에 ip datagram만들어서 주변과 통신. 그래서 네트워크 계층의 포워딩 테이블이 업데이트됨.

### OSPF(Open Shortest Path First)

RIP는 제한이 있어서 대규모 라우팅에 적용하기 어려움.
* public availabe한 프로토콜임.
* Link State 알고리즘 사용.
  * 각 노드들은 전체 맵 그래프를 그리게 되고, 자신의 소스로 하는 Dijkstra 알고리즘을 통해 라우팅 계산.
  * OSPF advertisement는 하나의 엔트리를 이웃들에게 전달, 그리고 이건 전체 AS에게 흘러들어감.
  * OSPF 프로토콜은IP 바로 위에서 구현되어, UDP나 TCP에 캡슐화되지 않음.

### ospf 장점
  * rip와 다르게 더 큰 규모의 네트워크에서 계층적인 OSPF 사용.
  * 보안. (모든 ospf 메시지는 인가됨.)
  * rip는 링크코스트를 1로 뭉뚱, ospf는 링크코스트를 다양하게 적용. (delay, bandwith, money 등). 각 링크코스트를 adv하며 각 링크코스트에 대한 다양한 table을 가짐.
    * multiple cost metirics가 가능함.
  * 멀티캐스트 라우팅을 제공함. (MOSPF). 하나의 메시지가 전체에 퍼지게하도록.
  * 또한 특정 목적지까지 가는 경로가 같은 비용인게 여러개이면 rip는 1개만 테이블에 남겼는데, ospf는 여러개를 모두 남김.


## hierarchical ospf

LS는 패킷 정보가 전체로 퍼저야하기때문에 크기가 커질수록 (AS 등) 패킷 전송량이 많아져서 부하큼.

AS를 여러개로 나누어 inter as 적용하듯. h.ospf는 여러 지역을 나누고 지역 내부에서는 LS Broadcasting함. 각 지역을 각 크기를 일정하게 유지하기 때문에.

각 area마다 border router는 (gateway router와 유사) 우리 area안에 속한 라우터들과의 거리 및 정보를 알고 summary를 알고 외부 as들과 교환함. (니네 as에는 그게잇니? 우린 이게있단다.)

area border routers들은 backbone이라는 지역 내에서 연결되는데
border routers를 연결해주는 것은 backbone 안의 backbone router들임.
backbone 내부에서는 또한 하나의 OSPF 프로토콜이 동작. (LS broadcasting이 일어남)

backbone 또한 boundary router가 있고 다른 외부 backbone의 boundary router들과 내부 backbone 노드들에 대한 정보들을 inter 교환하고 ... 또 위에 계층ㅇ적.

**two level hierachy** local area,  backbone
  * link state는 area 내부에서만 발생함.
  * 각 노드들은 area 내부 안의 그래프만을 알고.
  * area border router는 다른 area border router와 통신하여 서로간의 정보를 공유함.
  * backbone router들은 backbone 지역 내부에 위치한 area border router들을 연결.
  * boundary router는 backbone 지역의 가장자리에 위치해서 다른 지역의 backbone과 연결...

  계층 ㄱ

## Inter AS Routing : BGP

Border Gateway Protocol.
딱 1개의 inter domain 라우팅 프로토콜임.

eBGP : 지역 AS로부터 서브넷 도달가능성 정보를 가져옴.
iBGP : 받아온 이웃 서브넷 도달 정보를 AS 내부 라우터들에게 전파.

* 도달 정보 및 정책 등에 기반하여 다른 네트워크에 대한 "좋은 라우터"를 구별함.
  * 정책 등..
서브넷이 다른 인터넷에게 자신의 존재를 홍보하는 방법인셈.

BGP basic
  * BGP Session : 두 인근 BGP router들 (gateway)이 BGP 메시지를 교환함.
    * semi-permanent TCP 커넥션을 다른 as에게 우리 as 내부에 어떤 prefix의 라우터 도착지들이 있는지 경로 등을 홍보 도착지로 갈 수 있는 내가 아는 prefix 경로들을 홍보함.
    * 정보 교환은 향후 해당 정보로 요청오면 forward 해주겠다는 의미.

BGP 라우터들이 주고받는 핵심 정보
   * 목적지 서브넷의 prefix + attribute(경로정보) = "route"
Attribute의 핵심 두가지
  * AS-PATH : 해당 프레픽스까지 가는데 거쳐야 하는 AS들 (as68, as17)
  * NEXT-HOP : 어느 프레픽스를 가기 위해 내부 as router(gateway)가 다음 홉 as 어디로 가야하는지 정보를 제공함. (두 as간에 multiple 링크가 맺어져있을 수 있기 때문에, 다음 as의 어떤 gateway를 타야하는지.)

advertisement를 받은 게이트웨이 라우터들은 승인 혹은 거절할 때 중요한 정책을 사용함.
  * 정책 기반의 라우팅.

BGP route selection.

다른 as에 갈 수 있는 gateway가 여러개라면? hot potato를 통해 선택하는데

BGP는 1) 정책 (보안 등)의 이슈, 2) 짧은 AS 경로, 3) hot potato routing, 4) 그 외 기준을 통해 선택한다.

BGP 메시지 : as들간 tcp 통신을 통해 연결만드는 메시지. OPEN, UPDATE, KEEPALIVE, NOTIFICATION 등 내용.

## shortest path bgp route 방식

nexthop이란 as path를 시작하는 라우터 인터페이스(게이트웨이)의 ip 주소이다.
라우터는 ospf를 통해 현재 위치에서 가장 가까운 as게이트웨이로 가는 경로를 택한다.

## hot potato routing.

2개 이상의 inter routes가 있다고 해보자. 가장 짧은 next hop을 가진 루트를 선택한다.

<br>

summary : prefix를 위한 라우터 아웃풋을 결정.!

## BGP routing policy

import policy : 남한테 들은 advertisement를 내가 take해서 내부에 전파할거냐 정책.

beast as path 선정할때 policy도 사용함. 그리고 외부에 알릴지도 .

customer network가 2개의 provider network에 연결되어 있을 때, customer는 a provider에게 b를 b에게 a 라우트 정보를 알려주지 않는다.

( dual homed) : 굳이 내가 알려줄 필요가 없는 정보들은 bgp routing policy에 의해 정보 공유안함. (나없어도 잘 갈수있는 path인데?)

<br>

## intra inter as routing 차이?

policy
  * inter : 어드민이 어떻게 트래픽이 라우트 되는지, 누가 네트워크를 통해 라우터하는지 등 컨트롤을 원함.
  * intra : 싱글 어드민이 컨트롤함, 정책 결정 필요 x

scale
  * 계층적인 라우팅은 포워딩 테이블 크기를 줄임, 업데이트 트래픽 또한 줄어듬.

performance
  * intra : 성능에 초점을 둘 수 있음.
  * inter : 성능보다 policy가 중요할수도.

BGP는 라우팅 루프 문제가 생기지 않는다.


요기까진 unicast여씀.


## 4.7. broadcast and multicast routing

브로드캐스트 : 하나의 소스에서 모든 소스로 패킷을 다 전달해야함.

멀티,브로드 라우팅없이 유니캐스트 라우팅을 이용해서 구현할수있음. 그러나 이렇게 하면 r1 -> r2 -> r3
                    -> r4일때

r1이 r2 r3 r4 모두에게 주는데,, source와 가까운 링크에서 복사가 많이 일어나서 부하가 심해짐. 비효율적임. 목적지 개수만큼 dup이 발생하다보니.. 또한 source가 네트워크에 있는 모든 목적지에 대해 다 알아야하는 문제..

어케 효율적으로 캐스트해야하나?

## in-network duplication

* flooding : 노드가 브로드ㅐ스트 패킷을 받으면, 자기와 연결된 이웃들에게 모두 복사해서 보냄.
  * 문제는 브로드캐스트 스톰. 내가 예전에 브로드캐스트 했던 패킷이 또 오면 또 이웃들에게 다시 다 뿌리게됨
* controlled flooding : 노드는 패킷을 온ㄹ리 브로드캐스트함, 한번도 자기가 브로드캐스트하지 않은것에 대해서만
  * 자기가 브로드캐스트한 패킷 아이디를 일정기간동안 외워둠.
  * RPF(Reverse path forwarding) : 소스와 자기 노드간 거리가 짧은 것에 대해서만 브로드캐스트 포워딩해주고 아닌건 자기가 걍 먹음. spanning tree를 대략적으로 그리는 방식이기도 함.
* spanning tree
  * 그래프에 모든 노드를 다 지나는 트리. tree에 속하는 노드들만 브로드캐스트를 낭비없이 보냄.  노드들이 불필요한 패킷을 수신받지않음. 그러나 그래프를 그려야함.
  * 브로드캐스트를 위한 spanning tree를 만들고 브로드캐스는 걔네만 하면된다.


## multicast 라우팅 :

군데군데 멀티캐스트에 해당하는 호스트가 있음.
유니캐스트는 그냥 그래프만 알면 됬었지만, 멀티캐스트는 그래프 + 어디어디에 멀티캐스트를 필요로하는 호스트가 있는지를 알아야함.

멀티캐스트를 위한 별도의 테이블이 있어야한다.

멀티캐스트의 호스트에 따른 그래프를 또 만들면 n개의 트리를 만듬.
그럼 그냥 멀티캐스트들을 위한 shared-tree를 그릴 수 있음.
  * 가장 optimal한 모든 호스트들이 사용할 수 있는. 그러나 시간이 오래 걸릴수있음.
  * 적당히 그리는 center based tree
    * 멀티캐스트 소스들의 정보를 센터 라우터에게 보내고, 멀티캐스트는 센터를 소스로하는 경로만을 사용하면됨. 문제는 경로가 최적은아님. 다만 네트워크 규모가 크면 사용할수있음.


group member라는 개념으로 멀티캐스트 브랜치 멤버인지 아닌지 확인.

shared-tree (각 소스별로 shortest path를 그리고) 하는등..

approachs
  * source-based tree : 한 소스별로 트리 하나를 그림. 이후 n개를 그려야함.
  * group-shared-tree  
  * center-based tree : 하나의 라우터가 트리의 중심이 되고, 거기서 모든 정보를 딜리버해준다.


<br>

---

## Reference

* Computer Networking: A Top-Down Approach
* [KOCW 컴퓨터 네트워크](http://www.kocw.net/home/cview.do?mty=p&kemId=1046412)
