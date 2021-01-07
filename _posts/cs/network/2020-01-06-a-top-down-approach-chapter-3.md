---
title: "[Computer Networking : A Top-Down Approach] CH 3. Transport Layer"
categories:
  - Network
tags:
  - Network
toc: true
toc_sticky: true
last_modified_at: 2021-01-06T16:55:00-05:00
---

## Transport services and protocols

서로 다른 호스트에서 작동 중인 어플리케이션 프로세스들간의 논리적인 통신을 제공한다.

Transport 프로토콜들은 End System들 안에서 동작한다.
* Sender 측은 어플리케이션 메시지를 Segment로 쪼개어 Network Layer에 패스한다.
* Receiver 측은 Segment들을 조립하여 메시지로 만든 다음, Application Layer로 올려 보낸다.

Internet은 TCP와 UDP를 모두 지원하는 등, 어플리케이션은 1개 이상의 트랜스포트 프로토콜이 사용 가능하다.

## Transport vs network

* network layer : 호스트들 간의 논리적인 통신.
* transport layer : 프로세스들간의 논리적인 통신, 즉 네트워크 레이어의 서비스들을 강화하고 신뢰성있게 개선 만들어준다.
  * UDP는 단순히 전달만하는데 그친다면, TCP는 패킷이 유실되지 않고 순서대로 다 오도록 보장한다.

## Internet Transport Layer Protocols

Process-process 커뮤니케이션에 추가적으로 네트웤 서비스를 개선하기위해 추가적으로 얹어주는 프로토콜들의 서비스.

* TCP는 신뢰성있는, 순서가 보장되는 전달.
  * 커넥션 셋업, 플로우(리시버 버퍼를 오버웰밍 방지) 컨제스쳔(네트워크 오버웰빙 방지) 컨트롤.
* UDP는 순서보장x 신뢰성없음.
  * 네트워크 서비스에 더 얹어주는거 없음.  IP(네트웤서비스)에 얹어주는거없음.
  * Host 딜리버리가 발생하면 그 때 그냥 process 딜리버리만 같이 하는거임.
* 둘다 딜레이 개런티나, 미니멈 대역폭 개런티 등의 서비스는 제공하지 못함.


## Multiplexing/demultiplexing

클라이언트의 어플리케이션 레이어에는 여러 프로세스들이 있을 것이고, 각각의 프로세스들이 소켓을 통해 메시지를 transport 계층으로 내려 보낸다.
* Multiplexing : 여러 소켓의 데이터를 다루며, 메시지 패킷의 헤더에 목적지 등 주소를 찍어 Network 계층으로 1줄로 보내준다. (즉 유입구는 여러곳인데 출구는 1개)
* demultiplexing : 여러 클라이언트, 및 호스트들에게서 받은 메시지들이 Transport 레이어에 도착하면, 얘네의 목적지 프로세스가 무엇인지 헤더를 분석하고, 올바른 소켓에 집어 넣어 app 레이어로 보내줌.

## demultiplexing 작동방식

* transport의 segment format 은 header와 payload로 이루어진다.
  * header : source port, dest port 필수.
    * segment가 network로 내려갈때 host id, source port는 찍어준다.

## connectionless demultiplexing

  * Server 프로세스도 하나의 소켓만 열고있기 때문에 데이터를 보낼 때 헤더에
  DESTINATION IP와 포트 번호만 있으면 ㅇㅋ

  * demultiplexing은 리시버 측이고, 클라-서버 어느쪽이든 다 리시버가 될 수 있어.

* 소킷을 만들면 host-local port가 할당된다. (즉, app-transport 사이의 문이 socket이고 그 위치는 port 번호 )
  * 프로그래머는 모름, 운영체제가 암묵적으로 만들어 주고, segment 헤더에 자동으로 붙여줌.
* UDP는 필수적으로 목적지 아이피와 포트 써줘야함.
* 리시버는 segment의 포트번호를 확인하고, 해당 포트 번호를 가지고 있는 소켓에 segment를 보낸다.

* UDP는 서버도 1개 소킷, 클라이언트도 1개 소킷.
  * 디멀티플렉싱할 때 port번호만 있어도 되지 않을까? 포트번호는 같은데 다른 호스트(ip)거일수도 있어서.
    * 그래서 Transport layer의 작업인데도 불구하고 IP 주소를 TRANSPORT 레이어까지 올려서 사용.
    ,,, 다른 호스트의 포트번호와 겹칠수도 있기 때문에 디멀티플렉싱할땐 ip 주소도 활용해서 어디서 왔는지를 체크한다.

## connection-oriented demux.

* TCP는 서버는 도어소킷이 있고 각 요청마다 소킷을 새로 생성.
  * 디멀티플렉스할 때 소스와 데스트의 IP 및 포트 번호 전부 다 필요.
* 하나의 서버가 각 클라이언트를 위한 소킷들이 다 존재하기 때문에, destination만 있어서는 불충분.
  * 서버가 가지고 있는 여러 소킷들중 어떤 클라이언트 호스트의 어떤 프로세스가 요청한 커넥션인지 확인해야하기때문.

C는 소켓을 만들때 소켓으로 데이터를 주고받을 자식프로세스를 pork함. 데이터 송수신만 담당.
한 애플리케이션 프로세스 밑에 여러 프로세스가 생김. 멀티쓰레딩 기법.

## 상세한 설명 : UDP

커넥션리스 : 핸드쉐이킹없음.
각 세그멘트들은 다 각각 독립적임. 순서가 맞는지 확인 모름.
네트워크 계층에서 호스트 딜리버리를 해주는 서비스에 다른 추가적인 서비스를 해주지않음. (tcp는 네트워크 계층에서도 여러 추가적인 서비스를 해주기 때문에 각 세그멘트들의 순서등을 보장.)

best effor를 함 : 어떤 세그먼트 배달할때 정말 배달만함. 중간에 손실, 순서 이상한난거 udp는 뭐 알려주지않음. 그냥 배달에만 최선다할뿐.

UDP 사용처
  * 스티리밍 멀티 미디어.  (reliable하지 않아도됨. 오디오나 비디오 프레임 살짝 잃어버려도 용인할수준이기때문에... loss ㅇㅋ), 지속적인 플레이가 필요하기때문에 RATE SENSITIVE함 최소 처리량이 중요. TCP는 컨제스쳔 컨트롤 플로우 컨트롤로 최소 처리량보다 더 낮게 속도 조절할수 있기 때문에 부적절.
  * DNS
    * 쿼리를 DNS 서버들 루트, TLD 각각 1번씩만 왔다 갔다 하는 1회성 메시지만 주고받기 때문에 커넥션 안맞ㄴ들어도됨.
  * SNMP (네트워크 관리 프로토콜)
    * 네트워크 그룹에 속한 애들을 관리하는 녀석. 네트워크들이 관리자한테 자신의 상태 보고, 주기적으로, 정보 업데이트.. 그렇기 때문에 TCP는 노필요. 손실되도 좀있다 최신 데이터로 또 보고 오기때문ㅇ.
  * 데이터 무결성이 중요한경우도 udp를 사용.
    * 어차피 앱 계층에서도 데이터 무결성 체크하기때문.
    * TCP쓰면 RELIABLE한 통신때문에 APP과 기능중복, 오버헤드를 줄이기 위해 UDP를.

## udp 헤더

* 커넥션이 없어서 딜레이가 짧음. 간단함. 센더와 리시버 모두 커넥션 상태를 알 필요가 없음.
* 헤더 사이즈가 작음. 혼잡 관리를 안하기 때문에 최대한 빠른 속도로 데이터 전달.
  * 헤더에는 그냥 소스포트 데스트 포트정도면 끝. ip랑.
  * 그외 헤더에 length, checksum이 있음.

## udp checksum

보내는쪽 세그먼트 컨텐츠를 헤더 필드까지 포함해서 16비트의 정수로 표현하고 checksum(1, 0 치환)
받는쪽은 받은 세그멘트의 체크섬을 똑같이 계산하고, sender가 작성한 체크섬과 동일한지 확인.
  * 동일하면 에러 없,
  * 동일하지 않으면 전송 중 비트가 변경된것.
  * 체크섬만으로는 모든 에러를 잡을 수 없음. (값이 변경되었는데도 체크섬은 동일한 경우)
  * UDP는 그냥 내가 받은데이터가 보낸 데이터와 동일한지 확인하는 수준에 그침. 추후 액션은 프로토콜이 정하지않음

## reliable data transfer의 원칙

* checksum : 세그먼트의 오류가 있는가? 패킷의 비트 에러가 있는지 확인.
* (negative) acknowlegement : receiver가 sender에게 '에러없이 잘 받았다', '어떤 패킷이 잘못됬으니 다시 보내달라' 등의 내용을 알려줘야함
  * 네트워크가 중간에 drop해버린 패킷이 있다면? 리시버는 못받았으니 메시지를 안날릴거고, 센더는 자기가 보낸 패킷에 응답을 계속 기다리게 될것임.
  * 따라서 Timer를 이용. 특정 시간동안 응답없으면 retransfer.
    * 문제는 실제 패킷이 잘 받고 응답을 보냈는데 응답이 렉 등으로 인해 센더에게 타이머 내에 도달못하는 경우, 센더가 또 패킷을 보낼 수 있음.
      * 따라서 SEQUENCE NUMBER를 통해 이것이 segemnt의 몇번째 바이트인지 아닌지 리시버가 구분할수있음.
* Window, Pipelining
  * Sender와 Reciever 사이 파이프가 있는데 1개 패킷보내고 ack 응답이 와야 다음 패킷보내고... 이러면 낭비이다. 파이프는 길고 넓을 수 있으니깐.
    * 따라서 파이프가 꽉찰때까지 계속 데이터를 넣어주고, 반대쪽에서 빼는 파이프라이닝이 우수;.
    * window란 ack이 오지 않아도 파이프에 넣을 수 있는 최대 패킷 데이터라고 생각.
    * 해당 윈도우 양만큼 파이프에 자료 꽂아줌.

## tcp

reliable, in-order byte stream이다.

byte stream?
  * MSS(maximum segemnt size)라는 개념이 있는데, UDP는 앱 레이어에서 내려준 메시지를 해당 사이즈에 맞춰 잘라 전송한다.
  * 근데 TCP는 이런게 없음. 일단 센더는 버퍼에 메시지를 담아두고 리시버의 상태를 봐가면서 메시지들을 바이트 단위로만 보고 바이트 단위로 쪼개서 보냄. 메시지 바운더리 개념이 없다.

* POINT-TO-POINT
  * 센더 하나, 리시버 하나

, PIPLELINED, COnnection-oriented

full duplex data
  * MSS, 클라->서버가 아니라 같은 커넥션으로 서버->클라도 bi 방향으로 보내기 가능.

congestion, flow control.. 등

TCP도 헤더와 페이로드로 나뉨.
헤더가 복잡.
* 헤더 고정 파트와 가변 파트로 나뉨.
  * 가변적인 길이이기 때문에, 헤더의 길이에 대한 필드가 존재함.
    * udp는 그냥 세그먼트 전체의 렝쓰필드가 있음. 헤더 고정이라.
    * 기본 고정 헤더 라인이 5파트고, 너비는 32bit(4byte)라서 20byte가  기본 깔고드감.

헤더에 sequence number, ack number가 다 있음. 몇번째 바이트인지, 그리고 이걸 잘 받았고 다음 몇 번째 바이트를 기대하는지 등.
receive window(내가 지금 버퍼 상태가 이정도이니 오버플로우 되지 않을 정도로 윈도우 사이즈 적어서 파이프라이닝 (flwo control) 정보 제공) checksum 등.
flag -> connection을 생성하거나 없애거나 리셋하거나 등의 내용.
  * A는 acknowlegement와 관련된..

## TCP seq. numbers. ACKS
    * sequence 넘버
      * 세그먼트에 실은 데이트의 첫번째 바이트의 번호.
    * Ack number
      * 받기를 기대하는 다음 바이트 번호. 즉 그 이전까지는 순서대로 잘 받았다는 것을 의미. (cumulative.)

## TCP RTT, timer

* RTT만큼 기달렸는데도 ack이 안온다면 다시 보내도록 설정.
  * RTT를 정확하게 예측하기 어려움,, 너무 작게 설정하면 불필요한 재전송.
  * 너무길면 세그멘트 로스에 대해 너무 반응이 느림.
* 그래서 ack이 올 때 마다 샘플 RTT를 뽑아서 다음 RTT를 예측함. 근데 1개로는 제대로 예측하기 어려움,,, average를 구하게됨.
  * retransmission에 대한 ack은 샘플 평균에 포함하지 않는다.
    * tcp에서는 ack이 original에 대한 ack인지 retransmit에 대한 ack인지 알 수 없어서 estimate할때 값ㅇ ㅣ상해짐.

EstimatedRtt = (1- 알파) * etimatedRtt + 알파 * 이번샘플rtt
알파는 대게 0.125. (current 샘플 반영률이 높지않다는 의미)
근데 이것도 반영률이 그렇게 좁지 못해서 DevRTT 등을 수정해서 만듬. 이건 통계적인 부분이니 패스 ㄱ
편차가 크면 timer를 크게 설정하고 그러겟쥬.

## TCP reliable data transfer.

* IP(Network)의 불확실한 서비스 위에 TCP가 신뢰가능한 데이터 전송 서비스를 만드는것.
  * 단순히 IP(HOST)만 보고 가는 Network 계층 위에, piple라이닝과 ack, time 등의 서비스를 추가.

acks가 중복되거나, timeout발생하면 재전송.

센더가 붙이는 프로토컬 헤더는 리시버의 파트너 레이어가 프로토컬 헤더를 분석하게 됨.

## TCP 센더 이벤트

* app으로 메시지를 받거나, 상대쪽으로부터 ack을 받거나, 타임아웃 세개가 발생함.

* 보낼 때
  * 세그먼트에 시퀀스 넘버를 붙이고, 시퀀스 넘버는 세그먼트의 첫 번째 바이트의 번호.
  * 타이머란 아직 액을 받지 못한 가장 오래된 세그먼트에 대한것. 다 받았다면 이제 보내는 것이 액을 받아야하니 타이머 킴.
* 타임아웃 발생하면 다시 재발생, 타이머 다시 시작.

* 액 받은 경우
  * ack받으면 unacked이었떤 세그먼트를 ack받았다고 업데이트함. unacked segment 있으면 타이머 시작.
  * nextSeqNum = 이제

타이머를 설정할 세그먼트가 더이상없으면 타이머 설정안하는거. 타이머는 unacked 세그먼트가 있을때.

[-----][-----][-----][]
메시지들의 세그먼트들은 다음처럼 구성
ack된 seg // sent, but unacked // not sent // 할당안된부분

seq이 1이고 8바이트 짜리 보냈다면, ack은 9가 올것(우린 이제 9번받을래)

sequence number는 cumulative임. ack 받으면 데이터 크기만큼 sequence number를 올리고 타이머를 다시 키는거임.

이전 패킷들에 대해 액을 보내고 현재 120번째 시퀀스 넘버 데이터를 받아야하는데, 타임아웃으로 인해 또 이전에 받은 데이터가 오면?

리시버는 이를 버리고, 현재 받을 액을 다시 발송한다.

만약 데이터 2개 받았는데 첫번째 데이터 ack은 유실되고 두번째 데이처 ack만 도착했다? 그래도 문제가 없다 어차피 ack은 cumulative기 때문에 보내는쪽은 이전파일도 잘 받았다고 판단하기 때문. 받고자하는 ack의 번호부터 데이터 보내기만하면됨.

### TCP ACK Generation

Receiver 측면에서 볼 때 in-order segement 도착하면?
ACK를 바로 보내지않고 500ms 최대 기다려본다, 다음 세그먼트를. 만약 다음 세그먼트가 없으면 ACK를 보낸다.
  * 왜 기다리는가? TCP 헤더 비용 비싸다. 파이프라이닝을 통해 세그먼트가 계속 들어오는데 만약 뒤에 또 들어오는 세그먼트가 있으면 걔를 통해 ack 세그먼트를 보내면 비용 절감!
  * 만약 대기 상태에서 추가 세그먼트가 들어오면 뒤에 들어온 세그먼트까지 확인하고 다음 ack을 즉시 보냄.

Receiver 잘못된 오더의 세그먼트가 도착하면?
  * ()해당 데이터는 받거나 버리는건 앱마다다를수있, 근데 받을 수 있음.) 내가 원래 받아야할 순서의 ack 1개를 다시 바로 보냄.

300이라는 잘못된 오더의 데이터를 받은 뒤 정상 세그먼트(200)이 왔을때?
* 갭을 채우는 새그먼트는 다음 세그먼트 기다리는 행위 없이 바로 ack 응답보냄.

### TCP fast retransmit

* timeout period는 상대적으로 길다.
  * 소실된 패킷 재전송하기까지 긴 시간이 소요됨.

ACK 중복등을 통해 잃어버린 세그먼트를 감지해서 이런 문제점 극복!

how? 센더가 세그멘트 5개를 보냈는데 2번째께 중간 소실됬다.
그럼 3, 4, 5번에 해당하는 세그먼트들을 받은 리시버는 out-of-order 패킷으로 간주하고 2번 ack을 달라고 요청.
보낸쪽은 2번 ack달라는 요청을 3번 쭈르륵받는데, 2번에 해당하는 ack이 올때까지의 타이머를 기다리지 않고 re-ack 3번 오면 "아 2번이 소실됬구나" 판단하고 타임아웃 완료 전에도 2번 ㅍ ㅐ킷을 다시 재전송함.

왜 3번이나 기다리는가? 불필요한 전송을 막기위해.

## flow control.

스트림을 받는 쪽의 tcp.

link 프레임 헤더 때고 net에서 데이터그램.. 전달 레이어에 세그먼트 도착.

app-transport 연결하는 소켓을 위한 버퍼가 있다. segment를 버퍼에 담는ㄷ 속도의 불균형으로 인해 버퍼에서 queueing delay나 손실발생할수있음.

소켓이 만들어질때 os가 버퍼를 할당한다. tcp 헤더에 현재 남은 리시버의 버퍼가 얼마인지 (rwnd) 등을 적어서 커뮤니케이션하며, 보내는쪽은 해당 rwnd를 감안하며 넘치지 않도록 데이터 흐름을 조절함.

rwnd는 수신촉의 버퍼 크기이며, 이를 다시 응답하는 ack 세그먼트에 붙여서 송신측에 보내는거임.

rwnd : 나에게로 들어오는 스트림과 관련된 버퍼 사이즈.

## connection management

handshake로 연결 셋업을 해야한다.

내가 내보낼 sequence 처음번호, 받는 sequence 번호, 버퍼 사이즈 등을 서로 공유하고 연결을 셋업하는거.

tcp는 3way handshaking이다

2way handshaking을 안하는 이유?

* 서버 connect 요청, 데이터 전송 요청에 대해 서버가 답장하지 않아 재전송해서 server와 연결을 하고 작업후 서버 닫았는데
이후에 처음 보낸 요청들이 server에 늦게 도착하는 경우. 이미 서버는 닫혔는데 갑자기 연결맺어지거나, 데이터가 accept되는 자원낭비 가비지 데이터생성.

3 way

* 클라 : 서버에게 연결 요청
* 서버 : 연결 요청을 accept할게. (투핸드였으면 이때 서버가 establish되버림.)
* 클라 : (서버에 응답이 왔네, alive 상태구나) establish됨. 그리고 다시 서버에게 오케이 맺었어 답장보냄.
* 서버 : (클라에게서 응답이 왔네, alvie 상태구나) establish 됨.
SYNACK을 보내고 받아서 쓰리웨이적용.


## closing

TCP Full Duplexed. 양방향 스트림 흐른다.

* 세그멘트에 FIN bit 1을 붙여 보내 스트림을 종료.
* 수신측은 ack를 보냄.
* 클라는 더이상 데이터 보내지않아. 서버가 종료되길 기다림.
* 서버측은 계속 데이터 보낼 수 있지만, 이후 FIN을 보냄.
* 이제 서버 쪽은 스트림이 닫혀 못보냄.
* 클라는 FIN을 받으면 마지막 ACK를 보냄. 이걸 받으면 서버는 종료.
  * 클라쪽은 세그먼트 라이프타임 맥시멈의 2배정도를 기다림. 혹시라도 서버가 클라의 마지막 ACK을 못받아서 꺼지지 못할것을 대비

* 양 스트림은 각각 클로징 되어야하며 동시에 closing되도 가능.
  * 대게 클라이언트가 먼저 closing 요청을 보냄.


## Congestion Control

* 네트워크에서 너무 많은 소스들이 몰려서 데이터가 유실되거나 그런것을 방지하기 위함.

congestion cost : loss, 재전송.... goodput(처리 데이터들 중에서 좋은 것들)이 줄어들게됨.

* end-to-end 접근
  * network 레이어가 congestion에 대한 피드백을 transport한테 주지 않음. end system에서 로스나 딜레이가 발생하는 것이 관측되면 TCP가 작용함
* network-assited 접근
  * 라우터가 엔드시스템들에게 피드백을 제공함. 혼잡이 있는지 싱글비트로 알려주거나, 센더에게 보낼 속도를 명시하거나 등의 방식.

## TCP congestion control

* approach : 센더는 window size(파이프라이닝을 통해 연속적으로 보내는 데이터 전송양)(전송량)을 계속 늘려나간다. 가용한 대역폭내에서.
  * additive increase : cwnd (rwnd와 반대되는 개념, 보내는 윈도우 전송량)을 매 RTT마다 1MSS씩 늘려나간다. (즉 한번에 파이프에 꼽아버리는 양을 늘려나간다는것. cwnd.)
  * multiplicative decrease : 만약 파일 손실이 발생하면 cwnd를 절반으로 줄임.
이걸 반복하는 행태.

cwnd는 매우 동적인다... 네트워크 혼잡 상황에 영향받으며.

TCP sending rate : cwnd / RTT.

처음 연결시작하면 cwnd는 1MSS로 세그먼트 1개씩만 파이프로 보냄.
매 RTT마다 cwnd를 2배로 늘려나가서 파이프라이닝을 함.
  * ack을 받을 때 마다 cwnd를 1개씩 증가하는것. ㅇㅇ
그러니 1,2,4,8,, 지수처럼증가.

## tcp congestion avoidance(<-> slow start)

cwnd가 충분히커진경우 늘리는거 조심해야함.
  * threshold 밸류 (ssthresh)가 있으며, cwnd가 그 값보다 낮을땐 지수처럼 증가, 그 이후에는 조심해서 증가함.
    * os가 정하는 default value임.
  * loss가 발생하면 ssthresh 또한 cwnd /2로 조정됨.

avoidance가 시작하면, RTT 한 번 마다 cwnd를 1씩 증가시킴.
(이전엔 exponential하게 증가햇죠?)

## TCP 로스 감지, 반응
2가지 방법이있음

* Ssthresh는 로스 발생하면 절반으로 줄어듬!

* TCP가 타임아웃을 통해 loss 감지
  * cwnd를 1 MSS로 조정되기 때문에 윈도우 사이즈는 슬로우 스타트처럼 초기처럼 2배씩늘어나는 exponentiall 방향. threshold까지.
* TCP가 3 DUPLICATE ACK을 통해 로스 감지 : TCP RENO
  * cwnd가 절반으로 깎이기 때문에 윈도우는 선형적으로 증가.

(TCP Tahoe 예전 방식)은 로스 감지하면 무조건 CWND를 mss 1로 .


## TCP Throughput

cwnd가 K이고 RTT가 있다면, 쓰루풋은 k/RTT.
(패킷 여러개를 거의 동시에 보내기 때문에 여러개의 패킷이 거의동시에 들어오고 이 시간을 1rtt)

윈도우 크기는 W와 W/2사이를 왓다갔다함

W : 로스 발생했을 때의 Window 크기.
  * average window size = 3/4 * W
  * average throughtput : 3/4 * W / RTT.

TCP 특징 : long fat pipe (기술 발전으로 파이프가 길어지고 밴드위드가 넓어짐.).. 그럼 윈도우 사이즈도 커져야함.
  * 문제는 이런 congestion control 같은 것 때문에 윈도우 사이즈를 확 줄여버리거나 그러면 파이프 잉여가 심해짐.. loss rate.. 계쏙 연구하는 문제.

TCP Fairness
  * tcp가 공평해야 하는데, bottleneck 링크 capacity가 R이고, K 커넥션이 접근중이라면 각각은 R/K 만큼의 데이터 전송 속도를 써야함.
  * 그럼 경쟁중인 두가지 세션이 공평하게 써야하는데, 실상은 공정하게 쓰지않음.
  * 그래프로 그리면 이론상 두 세션이 공유하고있는 자원의 기울기가 1이 되어야하는데, 그렇지 앟음.
  * multiplicative decrease가 이런 쓰루픗을 비율적으로 감소시켜버림. 둘다 cwnd 늘리려고 경쟁하다보니, 로스가 터지고 줄어들고...
  * 두 연결이 접근하는데 한 쪽이 다른쪽보다 더 가깝게 있는경우 먼저 cwnd를 엄청 증가시켜서 독식.

이런 페어니스의 적.
  * UDP (걍 던저버리기만함. )
  * 멀티플 TCP 연결. (한 애플리케이션이 여러 TCP 연결맺으면 그보다 적게 연결하는 애플리케이션은 경쟁에 뒤쳐짐.)
<br>


---

## Reference

* Computer Networking: A Top-Down Approach
* [KOCW 컴퓨터 네트워크](http://www.kocw.net/home/cview.do?mty=p&kemId=1046412)
