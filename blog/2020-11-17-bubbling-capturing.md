---
title: 이벤트 버블링(Bubbling)과 캡처링(Capturing)
category:
- javascript basics
tag:
- javascript
toc: true
---

# 이벤트의 전파(Event Propagation)
이벤트의 전파란 이벤트가 발생했을 때, 브라우저가 이벤트 핸들러를 실행시킬 대상 요소를 결정하는 과정이다.

중첩된 요소에서 이벤트가 발생할 때, HTML DOM API 의 **이벤트 전파(Event Propagation)는 버블링과 캡처링**  두 가지 방식으로 구분된다.

# 버블링 (Bubbling)
이벤트가 발생한 요소에서부터 가장 최상단의 조상 요소까지 이벤트가 전파된다.

아래의 예제를 보자.

```html
<form onclick="alert('form')">FORM
  <div onclick="alert('div')">DIV
    <p onclick="alert('p')">P</p>
  </div>
</form>
```
가장 안쪽의 \<p> 요소를 클릭하면 p → div → form 순서로 3개의 얼럿 창이 뜬다.

이런 흐름을 '이벤트 버블링’이라고 한다.
	
한 요소에 이벤트가 발생하면, 이 요소에 할당된 핸들러가 동작하고, 이어서 부모 요소의 핸들러가 동작한다. 

가장 최상단의 조상 요소를 만날 때까지 이 과정이 반복되면서 요소 각각에 할당된 핸들러가 동작한다.

거의 모든 이벤트는 버블링 된다.

## event.target
부모 요소의 핸들러는 이벤트가 정확히 어디서 발생했는지 등에 대한 자세한 정보를 얻을 수 있다.

이벤트가 발생한 가장 안쪽의 요소는 타깃(target) 요소라고 불리고, event.target을 사용해 접근할 수 있다.

**event.target**과 **this(=event.currentTarget)**는 다음과 같은 차이점이 있다.

- event.target은 실제 이벤트가 시작된 ‘타깃’ 요소로, 버블링이 진행되어도 변하지 않는다.
- this는 ‘현재’ 요소로, 현재 실행 중인 핸들러가 할당된 요소를 참조한다.


## 버블링 중단
### event.stopPropagation()
이벤트 객체의 메서드인 event.stopPropagation()를 사용하면, 핸들러에게 이벤트를 완전히 처리하고 난 후 버블링을 중단하도록 명령할 수 있다.
```html
<body onclick="alert(`버블링은 여기까지 도달하지 못합니다.`)">
  <button onclick="event.stopPropagation()">클릭해 주세요.</button>
</body>
```

### event.stopImmediatePropagation()
한 요소의 특정 이벤트를 처리하는 핸들러가 여러개인 상황에서, 핸들러 중 하나가 버블링을 멈추더라도 나머지 핸들러는 여전히 동작한다.

event.stopPropagation()은 위쪽으로 일어나는 버블링은 막아주지만, 다른 핸들러들이 동작하는 건 막지 못한다.

버블링을 멈추고, 요소에 할당된 다른 핸들러의 동작도 막으려면 event.stopImmediatePropagation()을 사용해야 한다.

이 메서드를 사용하면 요소에 할당된 특정 이벤트를 처리하는 핸들러 모두가 동작하지 않는다.

### 버블링을 막지 말아야 하는 이유

버블링을 꼭 멈춰야 하는 명백한 상황이 아니라면 버블링을 막지 말아야 한다.

stopPropagation을 사용한 영역은 '죽은 영역(dead zone)'이 되어버린다.

예를 들어,  페이지에서 어디를 클릭했는지 행동 패턴을 분석하기 위해, 모든 이벤트 클릭을 감지하는 코드를 추가한다고 하자.

document.addEventListener('click'…)의 코드를 추가했을 때, stopPropagation로 버블링을 막아놓은 영역에선 해당 코드가 동작하지 않게 되어 제대로 된 분석이 불가능해진다.

버블링을 막아야 해결되는 문제라면 커스텀 이벤트 등을 사용해 문제를 해결할 수 있다.

# 캡처링 (capturing)

이벤트가 최상위 조상에서 시작해 아래로 전파된다.

캡처링 단계를 이용해야 하는 경우는 흔치 않기 때문에, 간략하게 설명하고 넘어가겠다.

캡처링 단계에서 이벤트를 잡아내려면 addEventListener의 capture 옵션을 true로 설정해야 한다.
```javascript
elem.addEventListener(..., {capture: true})
elem.addEventListener(..., true)
```
capture 옵션은 false(default값)과 true 두가지를 갖는다.





<br><br>

버블링과 캡처링은 **'이벤트 위임(event delegation)'**의 토대가 된다.

이벤트 위임을 알기위해 선행하는 학습과 같다고 볼 수 있으니, **이벤트 위임**에 대해 꼭 알고 넘어가자.


<br><br>
Reference
- https://ko.javascript.info/bubbling-and-capturing
