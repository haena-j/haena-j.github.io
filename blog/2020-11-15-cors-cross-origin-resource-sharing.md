---
title: SOP(동일 출처 정책)와 CORS
category:
- javascript basics
tag:
- javascript
toc: true
---

웹 개발 중 CORS 정책 위반으로 인해 에러가 발생한 경험은 누구나 있을 것이다.

브라우저에서 서로 다른 도메인/포트로 리소스 요청이 갈때 CORS 에러가 발생한다.

이 문제를 이해하고 해결하기 위해서는 Same-Origin Policy와 CORS에 대해 알아야 한다.
<br>
## Same-Origin Policy (동일 출처 정책)

 Same-Origin Policy(동일 출처 정책)은 웹 브라우저 보안을 위해 동일한 서버로만 ajax 요청을 주고 받을 수 있도록 한 정책이다.

즉, 스크립트 내에서 리소스를 요청할 때 요청지가 Cross-Origin(다른 출처)이면 요청이 차단된다.

현재 리소스를 가져온 출처와 **프로토콜, 호스트, 포트** 가 같아야 Same-Origin(동일한 출처)이다.
<br>

### [Cross-Origin 요청의 경우]

| 종류 | 예시  |
| -------- | -------- | 
| 다른 도메인    | test.com 에서 example.com 으로 요청     | 
| 다른 하위 도메인    | test.com 에서 store.test.com 으로 요청     | 
| 다른 포트    | test.com 에서 test.com:81 로 요청     | 
| 다른 프로토콜    | https://test.com 에서 http://test.com 으로 요청     | 


<br>

### [Cross-Origin 를 회피하는 방법]

#### 1\. CORS를 사용해 Cross-Origin을 허용

CORS는 HTTP의 일부로, 어떤 호스트에서 자신의 콘텐츠를 불러갈 수 있는지 서버에 지정할 수 있는 방법이다.

아래에서 자세히 설명할 예정이다.

#### 2\. JSONP(JSON with Padding)로 요청

JSONP는 CORS가 활성화되기 이전에 주로 사용하던 데이터 요청 방법으로,

HTML의 script 태그로 다른 도메인을 요청하면 SOP 정책이 적용되지 않는데, 이를 이용한 우회 방법이다.

script 태그의 '호출 후 즉시 실행하는 점'을 이용하여,  JSON 데이터를 받아오는 방법이다. 
(서버에서 지원해주어야 한다.)

JSON이 아닌 데이터를 JSONP로 불러오려하면
"Uncaught SyntaxError: Unexpected token : " 에러가 발생하게 된다.

JSONP는 여러 보안상 이슈로 인하여 W3C에서는 2009년 채택된 CORS 방식의 HTTP 통신을 권장하고 있다.

#### 3\. proxy 설정
프록시 서버는 클라이언트와 서버 사이에서 정보교환을 도와주는 서버이다. 

리소스를 요청하고자 하는 서버의 Access-Control-Allow-Origin 속성을 수정할 수 없는 경우에 굉장히 유용하다. 

프록시 서버가 실제 서버에 요청을 보내서 받아온 다음 그걸 Access-Control-Allow-Origin 설정을 적절히 하여 클라이언트에게 돌려주는 방법이다.

#### 4. window.postMessage를 사용한 iFrame 과 통신

window.postMessage() 메소드는 Window 오브젝트 사이에서 안전하게 cross-origin 통신을 할 수 있게 해준다.

페이지와 생성된 팝업 간의 통신이나, 페이지와 페이지 안의 iframe 간의 통신에 사용할 수 있다.

ex) iframe을 사용하여 띄운 팝업창의 위치와 사이즈를 부모 페이지에 맞게 조절해줘야 하는 경우


<br>

## CORS (Cross Origin Resource Sharing)
한국로 직역하면 교차 출처 리소스 공유라고 하며, W3C에서 내놓은 브라우저 보안 정책이다. 

CORS는 추가 HTTP 헤더를 사용하여 브라우저가 한 출처에서 실행중인 웹 애플리케이션에 선택된 액세스 권한을 부여하도록 브라우저에 알려주는 체제이다. 

웹 응용 프로그램은 자체와 다른 출처 (도메인, 프로토콜 또는 포트)를 가진 리소스를 요청할 때 cross-origin HTTP 요청을 실행한다. (출처 MDN)

쉽게 설명하자면, **출처가 다른 도메인에서의 ajax요청이라도 서버 단에서 (HTTP헤더 설정을 통해) 데이터 접근을 허용하는 정책**이다.
<br>

### [CORS를 사용하는 요청]
CORS 표준은 다음과 같은 경우에 사이트간 HTTP 요청을 허용한다. 
<br>

| 종류 | 예시 |
| -------- | -------- |
|   XMLHttpRequest와 Fetch API 호출   |      |
|  웹 폰트(CSS @font-face에서 교차 도메인 폰트 사용)   | [참고1](https://qastack.kr/programming/5008944/how-to-add-an-access-control-allow-origin-header), [참고2](https://stackoverrun.com/ko/q/4491303)|
|  WebGL의 texture   |      |
|  drawImage()를 사용해 캔버스에 그린 이미지/비디오 프레임   |      |
| 이미지로부터 추출하는 CSS Shapes |      |




<br><br>
Reference
- [MDN : Same-origin_policy](https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy)
- [MDN : CORS](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)
- [https://fetch.spec.whatwg.org/#http-cors-protocol](https://fetch.spec.whatwg.org/#http-cors-protocol)
- [https://ko.wikipedia.org/wiki/JSONP](https://ko.wikipedia.org/wiki/JSONP)
- [https://github.com/baeharam/Must-Know-About-Frontend/blob/master/Notes/security/sop.md](https://github.com/baeharam/Must-Know-About-Frontend/blob/master/Notes/security/sop.md)
- [https://developer.mozilla.org/ko/docs/Web/API/Window/postMessage](https://developer.mozilla.org/ko/docs/Web/API/Window/postMessage)
