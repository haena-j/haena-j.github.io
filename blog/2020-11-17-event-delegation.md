---
title: 이벤트 위임(Event Delegation)
category:
- javascript basics
tag:
- javascript
toc: true
---

# 이벤트 위임(Event Delegation)

이벤트 위임은 다수의 자식 요소에 각각 이벤트 핸들러를 바인딩하는 대신, 하나의 부모 요소에 이벤트 핸들러를 바인딩하는 방법이다. 

예를 들어, 6개의 자식 요소 li 에 각각 이벤트 핸들러를 바인딩하는 것 대신 부모 요소 ul에 이벤트 핸들러를 바인딩하는 것이다.

DOM 트리에 새로운 li 요소를 추가하더라도 이벤트 처리는 부모 요소인 ul 요소에 위임되었기 때문에 새로운 요소에 이벤트를 핸들러를 다시 바인딩할 필요가 없다.

이처럼 동적으로 노드를 생성하고 추가할 때, 상위노드에서 하위 노드들의 이벤트를 제어하는 방식으로 사용한다.


## 동작 방법
아래와 같은 메뉴가 있다고 생각하자.
```html
<ul id="menu">
  <li><button id="save">저장</button></li>
  <li><button id="edit">편집</button></li>
  <li><button id="delete">삭제</button></li>
</ul>
```

각각의 기능을 클릭 시, 특정 동작을 하게 하려면 아래와 같이 이벤트를 등록할 수 있다.
```javascript
document.getElementById("save").addEventListener("click", function(e) {
  // 저장 이벤트 동작
});
document.getElementById("edit").addEventListener("click", function(e) {
  // 편집 이벤트 동작
});
document.getElementById("delete").addEventListener("click", function(e) {
  // 삭제 이벤트 동작
});
```
이때, 메뉴가 추가될 때마다 이벤트 핸들러는 하나씩 늘어날 것이다.

하지만 이벤트 위임을 사용하면 상위 엘리먼트인 \<div id='menu'>에만 이벤트 리스너를 추가하면 된다.
```javascript
document.getElementById("menu").addEventListener("click", function(e) {
  var target = e.target;
  if (target.id === "save") {
    // 저장 이벤트 동작
  } else if (target.id === "edit") {
    // 편집 이벤트 동작
  } else if (target.id === "delete") {
    // 삭제 이벤트 동작
  }
});
```
target은 이벤트가 발생한 엘리먼트를 반환하기 때문에 엘리먼트의 특징에 따라 분기 처리만 하면 된다. 

메뉴가 추가될 때마다 핸들러를 추가할 필요도 없다.

### data 속성 활용
여기서 **data-action을 사용**하면 아래와 같은 방법으로 작성할 수도 있다.
```html
<ul id="menu">
  <li><button data-action="save">저장</button></li>
  <li><button data-action="edit">편집</button></li>
  <li><button data-action="delete">삭제</button></li>
</ul>
```

```javascript
class Menu {
    constructor(elem) {
      this._elem = elem;
      elem.onclick = this.onClick.bind(this); // 중요
    }

    save() {
      // 저장 이벤트 동작
    }

    edit() {
      // 편집 이벤트 동작
    }

    delete() {
      // 삭제 이벤트 동작
    }

    onClick(event) {
      let action = event.target.dataset.action;
      if (action) {
        this[action]();
      }
    };
  }

  new Menu(menu);
```
this.onClick은 this에 바인딩했다는 점에 주의해야 한다.

이렇게 하지 않으면 this는 Menu 객체가 아닌 DOM 요소(elem)를 참조하게 되고, this[action]에서 원하는 것을 얻지 못한다.

.action-save, .action-load 같은 클래스를 사용할 수도 있지만, data-action 속성이 좀 더 의미론적으로 낫다.

CSS 규칙을 적용할 수도 있게 된다.

위와 같은 방식으로 작성하면 버튼마다 핸들러를 할당해주는 코드를 작성할 필요가 없어지고, 언제든지 버튼을 추가하고 제거할 수 있어 HTML 구조가 유연해지는 이점이 있다.

이벤트 위임은 행동을 추가할 때도(data-counter, data-toggle-id 등..) 사용할 수도 있다.

이번엔 [참고]( https://ko.javascript.info/event-delegation) 자료를 꼭 함게 보자.

## 이벤트 위임의 장점
- 동적인 엘리먼트에 대한 이벤트 처리가 수월하다.
- 이벤트 핸들러 관리가 쉽다.
- DOM 수정이 쉬워진다.
- 메모리 사용량이 줄어든다. 
- 메모리 누수 가능성도 줄어든다.
- 코드가 짧아진다.

상위 엘리먼트에서만 이벤트 리스너를 관리하기 때문에 하위 엘리먼트는 자유롭게 추가 삭제할 수 있고,

동일한 이벤트에 대해 한 곳에서 관리하기 때문에 각각의 엘리먼트를 여러 곳에 등록하여 관리하는 것보다 관리가 수월하다.

## 이벤트 위임의 단점
- 이벤트 위임을 사용하려면 이벤트가 반드시 버블링 되어야 한다. 하지만 몇몇 이벤트는 버블링 되지 않는다. 
- 그리고 낮은 레벨에 할당한 핸들러엔 event.stopPropagation()를 쓸 수 없다.
- 컨테이너 수준에 할당된 핸들러가 응답할 필요가 있는 이벤트이든 아니든 상관없이 모든 하위 컨테이너에서 발생하는 이벤트에 응답해야 하므로 CPU 작업 부하가 늘어날 수 있다. 그런데 이런 부하는 무시할만한 수준이므로 실제로는 잘 고려하지 않는다.

<br><br>
Reference
- https://ko.javascript.info/event-delegation
- https://poiemaweb.com/js-event
