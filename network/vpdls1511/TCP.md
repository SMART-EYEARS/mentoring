TCP란, 전송제어 프로토콜로서 서버와 클라이언트간에 데이터를 신뢰성 있게 전달하기 위해 만들어진 프로토콜이다.

이와 비교하여 UDP도 존재하지만, UDP의 경우 신뢰성이 없으며 TCP보다는 상대적으로 빠르다

여담으로, Google에서는 이 TCP의 안정성과 UDP의 속도를 접목시킨 프로토콜이다.
위키백과

여기서 TCP는 신뢰있는 안정적인 전달을 목적으로 만들어졌다는 사실을 알게 되었다.
그런데 어떤 방법으로 연결을 하게 되는것일까?

3-Way HandShake
네트워크에 대해서 조금이라도 들어보았다면 익숙한 단어라고 생각한다.
이는, 서버와 클라이언트간에 3번의 커넥션을 통하여 연결을 수립 후 세션을 수립하는 과정이다.

이 절차는 SYN과 ACK 이 두개의 플래그를 이용하여 나타내는데, 순서는 아래와 같다.

Client가 Server에 접속을 하기 위해 SYN 패킷을 전송하게 되는데, 이 때 Client는 SYN/ACK 상태를 받기 위한 SYN_SENT 상태로 변화된다
Server는 SYN을 받고, 연결을 수락함과 동시에 SYN/ACK 패킷을 발송한다. 이 때 Server는 SYN_RECENT 상태로 변화한다.
Client는 SYN/ACK 패킷을 받고, 연결이 수립됨을 알 수 있다. 이 후 부터는 데이터를 자유롭게 주고받을 수 있다.
4-Way HandShake
이에 대해서는 자주 들어보지는 못햇을것 같다.

3-Way HandShake는 연결 수립을 위한 과정 이라면, 4-Way HandShake는 연결 해제를 위한 과정이다.
즉, 세션 연결 or 세션 해제 의 차이이다.

Client에서 연결 해제를 위해 Server에게 FIN 패킷을 전달한다.
Server는 FIN 플래그를 받고 연결을 종료할 준비를 하는데, 우선 확인을 했다는 ACK를 보낸다. 이 때, Server는 TIME_WAIT 상태가 된다.
Server가 연결을 종료할 준비가 되었다면 FIN 패킷을 Client에게 전달한다.
Client에서 FIN패킷을 받으면 Server에게 ACK를 보내 연결을 종료한다.
흐름제어
흐름제어란 무엇일까?
위에 3-Way HandShake, 4-Way HandShake의 경우 Client 와 Server는계속해서 다양한 데이터를 주고받게 된다.

하지만, 이 때 데이터 처리의 속도 차이가 엄청나게 발생하면 어떻게 될까?

즉, Server의 경우 한번에 10개의 데이터를 처리할 수 있지만, Client에서 100개의 데이터를 보내게 되면 Server의 Queue의 크기보다 더 많은 양의 데이터가 들어와 Queue가 넘칠 수 있다. 이 때 손실의 문제가 발생할 수 있는데, 여기서 다양한 흐름제어 방법이 생기게 된다.

정지 - 대기 (Stop and Wait)
매번 전송한 패킷에 대해 응답을 받아야만 다음 패킷을 전송할 수 있도록 해주는 방법이다.

구조가 간단하지만 한 패킷 즉, 하나씩의 데이터만 받고 처리하여 넘겨주기 때문에 비 효율적 이다.

슬라이딩 윈도우 (Sliding Window)
이는 Server측에서 설정한 윈도우의 크기 만큼 Client가 Server의 응답 없이 세그먼트를 전송할 수 있게 하여 데이터의 흐름을 동적으로 조절하는 기법이다.

여기서 윈도우는 Buffer의 크기 인데, 이 Buffer의 크기 만큼 세그먼트를 받을 수 있도록 하는것이다.

이 흐름제어 방법은, 앞서 말한 정지-대기 기법의 단점을 보완한 방법이다.

윈도우의 크기 = (가장 최근 AQCK로 응답한 프레임 수) - (이전에 ACK 프레임을 보낸 프레임의 수)

의 식으로 윈도우의 크기를 정하게 되며, ACK프레임을 수신하지 않더라도, 여러개의 프레임을 연속적으로 전송할 수 있다.

여기서 전송측 윈도우는 n-1개의 프레임을 포함한다.



위 구조에서 데이터 0,1을 전송했다고 하면, 슬라이딩 윈도우의 구조는 아래 이미지와 같이 변화를 하며 윈도우의 크기는 전송한 데이터 프레임만큼 줄어들게된다.



이 때 만약 Server측에서 ACK라는 프레임을 받게 된다면, Client측은 0,1 데이터를 정상적으로 받았음을 알게 되고, Client측은 ACK 프레임에 따른 프레임의 수 만큼 오른쪽으로 경계가 확장된다.

혼잡제어
혼잡제어는 말 그대로 네트워크의 혼잡 상태를 파악하고, 이러한 상태를 히결하기 위해 데이터의 전송을 제어하는것을 이야기 한다.

즉, 흐름제어는 Client와 Server의 사이에서 발생을 한다면 이는 Host와 라우터간의 관점에서의 전송 문제이다.

이런 현상이 발생하는 이유는, Host는 지역망 혹은 대형 네트워크를 통해 데이터가 전송이 되는데 만약 한 라우터에 데이터가 몰리면 해당 라우터가 데이터를 전부 처리하기에는 무리가 생긴다.

즉, 네트워크 내에 패킷의 수가 많아지는 현상을 “혼잡" 이라고 하며, 이러한 현상을 방지하거나 제거하는 방법을 “혼잡제어”라고 한다.

이러한 “혼잡제어" 방식에는 총 4가지의 방법이 있다.

AIMD(Additive Increase / Multiplicative Decrease)
이는, 처음에 패킷을 하나씩 보내고 이것이 문제없이 도착하면 Window의 크기를 1씩 증가시키며 전송하는 방법이다.

즉, 송신측이 Window Size가 패킷 손실이 일어날 때까지 증가시키는 식의 접근법이다.

additive increase → 송신 측의 window size를 손실을 감지할 때까지 RTT(왕복시간) 마다 1mss 씩 증가시킨다.

multiplicative decrease → 손실을 감지했다면 송신측의 window size를 절반으로 감소시킨다.

이는, Window Size를 1mss씩 증가시키기 때문에 오래 걸린다는 단점이 존재한다.

Slow Start(느린 시작)
송신 측이 window size를 1부터 패킷 손실이 일어날 때 까지 2배씩 증가시켜 나가는 방법이다.

초기 window size는 1mss이며, 이 후 RTT 가 일어날 때 마다 2배씩 증가를 시킨다. ( 1 → 2 → 4 → 8 → 16...)

처음에는 속도가 느리지만, window size가 2배씩 커지므로 속도가 빠르게 증가한다.

하지만, Slow Start의 개념을 이용해도 window size는 무한으로 커질 수 없기 때문에, Threshold라는 개념이 있다. 이 Threshold를 만나면 더 이상 2배로 증가하지 않고, 1mss씩 증가한다.

Fast Retransmit(빠른 재전송)
“타임아웃 전, 중복된 ACK가 3번 생기면 세그먼트를 즉시 전송한다.” 이 한 마디로 정의가 가능할 것 같다.

예를 들어 설명을 해보자면 1부터 6까지의 세그먼트를 수신받아야 하는데 중간에 5가 누락이 되어 3,4,6 순서로 수신이 되었다. 이 때 ACK에는 6을 받았다 라는 답이 아닌, 4을 받았다는 ACK 3개를 보낸다. 그러면 송신측은 4가 누락이 되었다는것을 확인하고 다시 4를 전송하게 된다.

그리고 송신측은 통신시 정해진 시간이 정해져 있는데, 이 시간동안 세그먼트에 대한 ACK를 받지 못한다면 타임아웃이 발생하고, 이 통신은 혼잡상태라고 판단하여 혼잡 회피를 한다.

Fast Recovery(빠른 회복)
빠른 회복은 혼잡 상태가 되면 윈도우의 크기를 1로 줄이지 않고, 절반으로 줄이고 선형증가 시키는 방법이다.
이 방법을 적용하여 혼잡 상황을 겪고나서 이 후부터는 AIMD 방식으로 동작을 한다.

오류제어
무슨 통신이든 통신을 하게 되면 다양한 이유로 오류가 발생할 수 있다.
이는 TCP통신을 하며 발생하는 오류들을 제어하는지 알아보자.

먼저, 오류가 발생할 시 오류를 인지하는 방법이 있는데 이는 크게 두가지로 설명이 가능하다.

수신측에서 송신측으로 명시적으로 NAK 를 전송
전송한 ACK에 대한 답이 오지 않거나, 중복된 ACK가 전송될 때
2번은 다양한 이유가 있는데 타임아웃이 생기거나 패킷이 유실되거나 중복된 ACK즉, 패킷이 3개 이상이 전달이 될 때 오류라고 파악한다.

1번 방법은 NAK를 확인하는 로직이 필요하기 때문에 대부분 2번 방법을 이용하여 오류를 인지한다.

이러한 두 가지의 오류인지 방법이 있다고 하는데, 이러한 오류들을 어떻게 제어할까?

Stop And Wait
흐름제어에 등장했던 방법인데, 이 개념이 다시 등장한 이유는 이 Stop And Wait이라는 방법도 기본적인 오류 제어가 가능하기때문이다.

송신측에서 패킷을 보내고, 보낸 패킷에 대한 응답이 오면 다음패킷을 보내는 방식이다.
이 때 수신된 프레임의 유무 판단에 따라 ACK혹은 NAK를 보내는 방식이다.

식별을 위해 ACK프레임은 각각 0,1 번호를 번갈아가며 부여를 하며, 수신측이 데이터를 받지 못하면 NAK를 보내고 이를 받은 송신측은 NAK를 받게된 데이터를 다시 송신한다.

만약, 데이터나 ACK가 분실되면 일정 간격의 시간을 두고, 타임아웃 발생 시 송신측은 데이터를 다시 전송한다.

ARQ (Automatic Repeat Request)
TCP는 기본적으로 재전송 기반의 오류제어를 하게 되는데, 실제로 상당량의 데이터를 재전송 하기에는 비효율적이기 때문에 최대한 작은 단위로 재전송을 하기 위해 두 가지 방법인 Go-Back-N과 Selective Repeat를 사용한다.

Go-Back-n
오류가 발생한 데이터부터 다시 재전송하는 방식이다.

1,2,3 이라는 데이터를 각각 보내야 할 때, 1과 3은 도착을 했지만 2는 도착하지 않았다. 이 때 3이라는 데이터는 폐기를 하게 되고, NAK 2라는 패킷을 송신측에게 보내 2와 3을 다시 받는다.

이는 이미 받은 데이터를 폐기하고 다시 전송을 해야한다는 단점이 있다.

Selective Repeat
오류가 발생한 데이터만 골라 재전송을 하는 방법이다.

위 예시처럼 1,2,3 이라는 데이터 중 2만 보내지지 않았을 때 2만 다시 수신을 받는 방식으로 Go-Back-N 방식보다는 좋지만, 1,3,2 라는 데이터 즉, 버퍼를 재정렬 해야하기 때문에 버퍼의 크기가 더 필요하다는 단점이 있다.

정리
Go-Back-N 방식과 Selective Repeat방식 두 가지의 방식이 존재하는데, 이는 어디서부터 재정렬을 하는지에 대한 차이이다.

필자의 경우 데이터를 폐기하고 다시 받는다는것보다 오류가 발생한 데이터만 다시 받는것이 좋다고 생각한다.

왜냐하면, 갑자기 1부터 100까지의 데이터를 한번에 송신했는데 수신을 받는 입장에서 40이라는 데이터만 못받았다고 41 ~ 100의 데이터를 폐기 후 이만큼의 데이터를 다시 받는것은 트래픽적으로 문제가 클 것 이라고 생각한다.

실제로 Selective Repeat방식을 자주 사용한다고 한다.

궁금증

4-way handshake에서 [2step] → [3step] 일 때 연결 종료할 준비가 완료되지 않으면?