---
title: "script 태그의 defer와 async"
category:
- javascript basics
tag:
- javascript
---

##  \<script> 태그의 defer, async


웹 브라우저는 HTML을 파싱할 때, script tag를 만날 때 마다 파싱을 중지하고 스크립트를 로드하고 실행하게 된다. 

이 과정에서 외부 스크립트를 로드하는 시간과 실행하는 시간만큼 렌더링이 지연된다.

이 문제를 해결하기위해 defer 와 async를 사용할 수 있다.
(일부 브라우저에서는 defer와 async 속성을 지원하지 않으니 주의해야 한다.)

### defer
브라우저가  defer 속성을 가진 스크립트\<script defer>를 만나면, 외부 스크립트 다운로드를 시작한다
. 

**다운로드 중에 HTML 파싱을 막지 않으며, 다운로드 된 스크립트는 \</html>을 만났을 때  실행된다.**

(Parsing 작업이 끝난 후 DOMContentLoaded event 전에 문서에 삽입된 순서에 따라 실행) 

스크립트가 DOM을 조작하는 내용을 포함할 때 사용하면 좋다. 

### async
브라우저가 async 속성을 가진 스크립트 \<script async>를 만나면, defer와 마찬가지로 외부 스크립트 다운로드를 시작한다. 

**다운로드 중에 HTML 파싱을 막지 않지만, 다운로드가 완료되면 즉시 스크립트를 실행한다.
실행하는 동안 브라우저는 HTML 파싱을 멈춘다.**

(window의 load event 전 내려받는 즉시 바로 실행)

스크립트가 DOM을 조작하지 않고 다른 스크립트와 의존성이 없는 코드만 포함하는 것이 좋다.

<br><br>
Reference
- [https://ko.javascript.info/script-async-defer](https://ko.javascript.info/script-async-defer)