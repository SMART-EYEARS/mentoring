# Network
## TCP
TCP(Transmission Control Protocol)는 IP 프로토콜 위에서 연결형 서비스를 지원하는 전송 계층 프로토콜로, 인터넷 환경에서 기본으로 사용한다.
* 연결형 서비스를 제공 (UDP : 비연결형)
* 전이중(Full Duplex) 방식, 점대점(point to point) 방식
* 신뢰성 있는 데이터 전송, 순차적인 전달을 보장
>전이중 통신(Full Duplex) : 전송이 양방향으로 동시에 일어날 수 있음  
>점대점(point to point) : 각 연결이 정확히 2 개의 종단점을 가지고 있음

TCP 서비스는 송신자와 수신자 모두가 소켓이라고 부르는 종단점을 생성함으로써 이루어진다. TCP는 데이터를 세그먼트(Segment)라는 블록 단위로 분할해 전송한다. TCP는 멀티캐스팅이나 브로드캐스팅을 지원하지 않는다.  
  
신뢰성 있는 전달을 위해 오버헤드가 발생하여 UDP보다 속도가 느린 편이다. 속도보다 신뢰성이 중요할 때 사용한다.

### TCP 3 way handshake
TCP가 장치간 논리적인 연결을 성립하기 위해 3 way handshake 방식을 사용한다.  
통신을 하는 응용프로그램이 데이터를 전송하기 전에 먼저 정확한 전송을 보장하기 위해 상대방 컴퓨터와 사전에 세션을 수립하는 과정  
  
![image](https://user-images.githubusercontent.com/44667299/162578377-7b237f0f-d7ee-426f-b641-617b8d228477.png)  
사진 출처 : https://asfirstalways.tistory.com/356  
  
1. 클라이언트는 서버에 접속을 요청하는 SYN(a) 패킷을 보낸다. 클라이언트는 SYN을 보내고 SYN/ACK 응답을 기다리는 SYN_SENT 상태가 된다.
2. 서버는 클라이언트의 요청인 SYN(a)를 받고 클라이언트에게 요청을 수락한다는 ACK(a+1)와 SYN(b)이 설정된 패킷을 발송한다. 서버는 클라이언트가 ACK으로 응답하기를 기다리는 SYN_RECEIVED 상태가 된다.
3. 클라이언트는 서버의 수락 응답인 ACK(a+1), SYN(b) 패킷을 받고 ACK(b+1)를 서버로 보내면 연결이 성립(ESTABLISHED)된다.
>a : 임의의 번호(random number)  
>b : 서버가 클라이언트에게 주는 번호  
>SYN : SYNchronize sequence number - 요청에 대한 패킷  
>ACK : ACKnowledgement - 응답에 대한 패킷
  
**왜 3 way인가?**  
2번으로는 부족한가? 부족하다.  
좋은 비유가 있어서 가져옴  
클라이언트가 자신의 목소리가 들리는지 물어봄 : SYN(a)  
서버는 클라이언트의 목소리가 들린다고 응답 : ACK(a+1)  
그리고 자신의 목소리가 들리는지 물어봄 : SYN(b)  
클라이언트는 서버의 목소리가 들린다고 응답 : ACK(b+1)  
TCP는 양방향성이다. 클라이언트에서 서버에게 존재를 알리고 패킷이 전송가능함을 알리듯, 서버에서도 클라이언트에게 존재를 알리고 패킷이 전송가능함을 알려야 한다. 그래서 2 way로는 부족하다.  
  
**왜 난수를 사용하나?**  
연결을 맺을 때 사용하는 포트 번호는 시간이 지남에 따라 재사용하는데, 난수가 아니라 순차적인 번호를 사용한다면 이전의 연결에서 오는 패킷으로 인식할 수 있다.

### TCP 4 way handshake
3 way handshake가 TCP의 연결을 초기화 할 때 사용한다면, 4 way handshake는 연결(세션)을 해제하기 위해 사용  
  
![image](https://user-images.githubusercontent.com/44667299/162579953-cf62db42-2f6c-4225-b3fa-e782b17b438c.png)  
  
1. 클라이언트가 연결을 종료하겠다는 FIN 플래그를 전송한다.
2. 서버는 클라이언트의 요청(FIN)을 받고 일단 확인메시지(ACK)를 보내고 자신의 통신이 끝날 때까지(데이터를 모두 보낼 때까지) 기다린다(TIME_OUT).
3. 서버가 통신이 끝났으면 연결이 종료되었다고 클라이언트에게 FIN 플래그를 전송한다.
4. 클라이언트는 확인했다는 메시지(ACK)를 보낸다.
  
**TIME_WAIT**  
클라이언트는 FIN을 수신했어도 서버에서 FIN을 전송하기 전에 전송한 패킷이 FIN보다 늦게 올 것을 대비해 일정 시간동안 세션을 남겨놓고 잉여 패킷을 기다리는데 이 과정을 TIME_WAIT이라 함  
  
**왜 연결 설정(3 way)과 연결 종료(4 way) 과정이 차이나나?**
* 클라이언트가 데이터 전송을 마쳤다고 하더라도 서버는 아직 전송할 데이터가 남아있을 수 있음
* 일단 FIN에 대한 ACK 전송 후, 서버의 데이터 전송이 끝나고 FIN을 전송하므로 한 단계 더 거침

### 흐름제어
송신측과 수신측의 데이터 처리 속도 차이를 해결하기 위한 기법. 만약 송신측의 전송량이 수신측의 처리량보다 많은 경우, 전송된 패킷은 수신측의 큐(버퍼)를 넘어서 손실될 문제가 발생할 수 있기 때문에 송신측의 패킷 전송량을 제어하게 된다.  
  
**1) Stop and Wait(정지-대기)**  
* 매번 전송한 패킷에 대한 확인 응답을 받아야 다음 패킷을 전송
* 구조가 간단한 대신, 하나를 주고 하나를 받기 때문에 비효율적

**2) Sliding Window(슬라이딩 윈도우)
* 수신측에서 설정한 Window size만큼 송신측에서 확인 응답없이 패킷을 전송해, 데이터흐름을 동적으로 조절
* 패킷에 대한 응답이 확인되는 대로 옆으로 Window size만큼 다음 패킷의 데이터를 전송

전송측 윈도우  
![image](https://user-images.githubusercontent.com/44667299/162582043-4de310ed-88e2-4188-b98f-7e94a72dbb98.png)  

수신측 윈도우  
![image](https://user-images.githubusercontent.com/44667299/162582055-87fd0b51-947c-4dc0-86a7-93885adaaaf0.png)  
  
사진 출처 : https://woovictory.github.io/2018/12/28/Network-Erro-Flow-Control/

### 혼잡제어
네트워크에 존재하는 전송 패킷의 수가 많아질수록 네트워크의 성능은 자연스럽게 감소한다. 이와 같은 성능 감소 현상이 급격하게 악화되는 현상을 혼잡(Congestion)이라 한다.  
  
흐름제어는 송신 호스트와 수신 호스트 사이의 논리적인 점대점 전송 속도를 다룬 반면, 혼잡제어는 더 넓은 관점에서 호스트와 라우터를 포함한 서브넷에서 네트워크의 전송 능력 문제를 다룬다.  
  
송신측의 데이터 전송 속도와 네트워크의 데이터 처리속도의 차이를 해결하기 위한 기법  
  
**혼잡의 원인**
* 초기 혼잡 과정에서 타임 아웃 시간이 작으면 혼잡도가 급격히 증가
* 패킷 도착 순서가 다른 상황에서 패킷을 분실 처리하면 타임아웃 증가
* 의도적으로 피기배킹을 사용하면 응답시간이 느려져 타임아웃 증가
* 패킷 생존 시간을 작게 하면 패킷이 강제로 제거되어 타임아웃 증가
* 라우팅 알고리즘 : 혼잡이 발생하지 않는 경로를 설계해야 함
* 혼잡이 발생하는 경로를 선택하면 혼잡이 주변으로 확대됨
>피기배킹(Piggybacking) : 양방향으로 동시에 정보 프레임과 응답 프레임을 교차하여 전송하는 경우를 사용하는 방식이다. 정보 프레임과 응답 프레임을 각각 보내는 것이 아니라 정보 프레임을 전송하면서 응답 기능까지 동시에 수행하도록 프레임 구조를 변형시킨 것이다. 이렇게 되면 응답 프레임의 전송 횟수를 줄이는 효과가 있어 전송 효율을 높일 수 있다.  
>피기배킹 방식을 사용하는 응답 방식은 혼잡이 발생했을 때 송신 호스트의 타임아웃 기능을 통한 재전송을 유발하여 혼잡을 오히려 가중시킬 우려도 있다.



### 오류제어 
## UDP
|프로토콜|TCP|UDP|
|:---:|:---:|:---:|
|연결 방식|연결형 서비스|비연결형 서비스|
|패킷 교환 방식|가상회선 방식|데이터그램 방식|
|전송 순서|보장|보장하지 않음|
|수신 여부 확인|확인|확인하지 않음|
|통신 방식|Unicast 지원|Unicast, Multicast, Broadcast 지원|
|신뢰성|높음|낮음|
|속도|느림|빠름|
## HTTP
### HTTP 버전 
## HTTPS 
### HTTPS가 필요 이유 
### HTTPS HandShake   
## GET VS POST   
## 쿠키와 세션 
## REST와 RESTful의 개념   
## DNS 흐름 
### DNS round robin 방식
### DNS Round Robin 방식의 문제점
### Weighted round robin (WRR)
### Least connection
## Socket(TCP/IP Socket) 
### Socket 이전 실시간 통신 기술들 
### 클라이언트 소켓과 서버 소켓      
### 소켓 API 실행흐름  
## Web Socket(HttpSocket) 
### Web Socket HandShake 및 실행 흐름 
### WebSocket 한계 
> Socket.io 및 SocketJS
> STOMP - SubSocket 
## CORS
## OSI 7Layer VS TCP/IP 4Layer VS TCP/IP 5Layer
## 웹 통신 전체 흐름 