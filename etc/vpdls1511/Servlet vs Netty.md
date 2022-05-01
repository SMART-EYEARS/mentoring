# Servlet

<aside>
💡 자바를 이용해 웹 서비스를 만들기 위한 기술을 서비스 라고 할 수 있다.

</aside>

일반적으로 WebServer는 정적인 페이지만을 제공해주는데에 반해 요새는 고정적인 화면보다 동적으로 다이나믹하게 움직이는 웹 사이트가 상용화가 많이 되었다.

이 때 동적인 웹 서버가 동적인 페이질르 제공할 수 잇도록 도와주는 어플리케이션이 Servlet이며, 이를 CGI라고 한다.

사실, CGI는 다양한 언어가 존재히지만, 이 중에서 Java의 Servlet은 CGI언어들 중 부하가 가장 적다.

## 왜 CGI언어 중 Servlet이 좋을까?

바로, Servlet의 객체는 싱글톤으로 관리가 되기 때문이다.

Servlet은 객체가 Request마다 생성이 되지 않고, 최초에 로딩이 된 바로 그 시점에 객체가 한번 생성이 된다. 그리고 이 객체가 재활용 되기 때문에 서버에 부하가 적어진다.

추가로, 동시 처리를 위해 멀티 스레드를 하게 되는데 그렇다고 멀티스레드를 채택하면서도 문제가 있었다.

이 스레드의 생성 비용과 Context  Switching의 문제, 스레드의 제한이 없다면 서버는 금새 다운이 되어버린다.

햐지만 이러한 문제를 스레드 풀 이라는 개념을 이용하여 단점을 상쇄시켰다.

## 스레드 풀

스레드가 필요하면 생성되어있는 스레드를 꺼내서 사용하고, 전부 사용한다면 풀에 반납하는 과정을 담은 개념이다. 만약 스레드풀에 있던 스레드가 모두 사용중이라면 요청을 거절하거나 남은 요청들을 줄지어 대기하도록 한다.

# Netty

Netty는 NIO(Non-Blocking Input Output)방식을 채택해 비동기 네트워크 통신을 할 수 있으며, TCP / UDP 소켓 서버 개발과 같은 네트워크 프로그래밍을 크게 단순화 하기위해 생긴 프레임워크이다.

순수 NIO방식으로 네트워크 프로그래밍을 하기에는 큰 어려움이 있었고, 예기치 못한 에러의 발생도 잦았다.

그래서 나오게 된게  Netty이다.

이는 단순히 네트워크 관련 통신만을 하고 끝내는것이 아닌, 일반적으로 네트워크 애플리케이션에서 사용하는 다양한 기능들을 제공하고 있었다. 덕분에 네트워크 프로그래밍 혹은 멀티 스레드를 처리하는 과정보다 비즈니스 로직을 처리하는데 더  집중할 수 있게 되었고, 네티를 이용한 애플리케이션은 10만개 이상의 클라이언트 커넥션을 처리할 수 있도록 안정화 되었다.

## Async IO(비동기 입출력)

Netty는 NIO 즉, 비동기 방식을 사용한다. 요청을 보낸 즉시 리턴이 되고, 작업을 하다가 요청한 작업의 처리가 완료도있는지 확인 후 나중에 응답을 받는 형식이다.

## Event Modal

네티는 입출력을 위해 잘 정의된 이벤트 모델을 가지고 있다.

사용자가 코어 로직을 손대지 않고 직접 이벤트 타입을 구현할 수 있도록 지원하고 있으며, 각 이벤트 타입은 엄격하게 계층화 되어있어 서로 잘 구분된다. 다른 NIO는 이벤트 추가 시 기존 코드에 영향을 주거나 아예 이벤트의 커스터마이징을 막는 경우도 종종 있다.

이 외에도 편하고 빠른 개발을 위한 여러 컴포넌트들이 존재하고 있으며, NIO는 TSL을 지원하지 않았다. 하지만, Netty가 생기며 TSL이 적용이 되었고, 사용자는 그냥 가져다가 쓰면 된다.
또, Http 통신도 구현했으며 Protocol Buffer까지도 구현이 되었다.