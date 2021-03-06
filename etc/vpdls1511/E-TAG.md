캐시 관련 내용에서 연장되는 내용으로 E-Tag, Entity Tag는 Http 프로토콜의 일부로 웹 캐시 유효성 검사를 위해 재공하는 여러 매커니즘 중 하나이다.

네트워크를 타고 서버에 접근하여 값을 받아온다. 이것은 생각보다 느리고 비용이 든다. 만약, 큰 용량의 요청을 보내고 그 요청에 대한 응답이 크다면 왕복 시 작업의 지연을 발생시킨다. 이 때 요청과 응답이 모두 같은값이라면 굳이 서버와 통신을 해서 값을 받아오는게 좋은 방법일까?

이러한 점 때문에 캐시를 이용한 자원의 재 사용은 성능 최적화에 있어 매우 중요한 부분이라고 할 수 있다.

추가로, 브라우저는 HTTP캐시를 구현하고 있다는 점이다.
우리가 해야 할 일은 각 서버단에 맞는 HTTP 헤더를 내려주어 브라우저에게 응답 캐시를 언제 얼마나 보유할지 가이드를 하기만 하면 된다.

<aside>
💡 서버가 응답을 내려 줄 때, HTTP 헤더들은 방출하고 그것은 Content-Type, Content길이, 캐시 전략(캐시 보유시간), 유효성 토큰(ETag)등 여러 정보들을 내려준다.

</aside>

E-Tag는 캐시를 검증하는 하나의 방법이라고 위에 언급을 했는데 이 태그는 HTTP Header 에 담겨있고, 이는 “유효토큰"으로 통신을 한다는 장점을 가지고 있다.

유호토큰은 효율적인 자원 업데이트 체크를 가능하게 하며, 리소스가 바뀌지 않는나면 아무 데이터도 전달되지 않는다.

## 가정

캐시의 저장 시간이 만약 300초라고 가정을 해보자. 이 300초 안에는 같은 정보를 얻기 위해 같은 요청을 보내더라도 브라우저에서 같은 내용을 보냇다는것을 인지해 서버에게 요청하고 응답을 기다리지 않고, 바로 브라우저에서 값을 반환해준다.

하지만, 서버에서 내려준 캐시 저장 시간인 300초를 지난 상태에서 똑같은 응답을 받는 요청을 보냈다고 가정을 해보자.

그렇다면, 이 때 브라우저는 캐시가 존재하는지 확인을 하고 이미 시간이 지난 값이므로 서버와 다시 통신을 해서 요청을 받는방법을 받아 같은 값을 또 저장할 것이다.

하지만, 이 때 ETag를 이용한다면 이러한 문제를 해결할 수 있다.
이는 서버에서 중재토큰 이라는것을 생성하여 헤더 안인 ETag에 담아서 보내게 되는데, 이 토큰은 대게 파일의 해시값이나 파일에 대한 식별자 이다.

자, 다시 브라우저가 값을 보낸다고 가정을 해보자

우선, 브라우저에서 서버측으로 요청을 보낸다. 이 때 요청에대한 응답을 받는데 300초 동안 1024크기의 데이터를 저장하고, 이 값에 대한 식별자(ETag)는 x129Sc1 라고 보냈다.
이 후 300초가 지난 뒤 같은 요청을 전달했는데 이미 캐시 저장시간이 지난 후라 다시 서버에게 요청을 해야하는 상태이다. 

### 리소스가 변경되지 않았을 때

브라우저는 서버에게 받았던 ETag인 “x129Sc1”를 `If-None-Match`에  담아 서버측에 전달을 했다. 서버는 해당 If-None-Match를 보고 변경되지 않은 값을 브라우저가 가지고 있을것이라고 브라우저에게 `304 Not Modifed` 응답과 ETag를 넘겨주게 된다

### 리소스가 변경되었을 때

첫 요청으로 ETag가 “ds1f44d” 를 받았고, 요청이 변경되어 If-None-Match에 “x129Sc1”를 담아서 전달하였다.

하지만, 서버측에서는 “ds1f44d”를 보냈지 “x129Sc1”를 보낸것이 아니라 리소스가 변경되었음을 알게 되었다. 그래서 서버는 새로운 헤더를 만들어서 클라이언트에게 전달을 하게 된다.

---

### 참고

[https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/ETag](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/ETag)
[https://pjh3749.tistory.com/264](https://pjh3749.tistory.com/264)
[https://tecoble.techcourse.co.kr/post/2020-09-30-ETag-with-Spring/](https://tecoble.techcourse.co.kr/post/2020-09-30-ETag-with-Spring/)
