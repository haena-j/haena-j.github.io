---
title: React 사용 중 eslint 에러 Component definition is missing display name(react/display-name)
category: React
tag:
- javascript
- eslint
- errors
---

forwardRef, memo를 사용하는 컴포넌트에서 Component definition is missing display name(react/display-name) 가 발생했다.

## React의 forwardRef
React의 forwardRef는 부모 컴포넌트가 자식 컴포넌트에 ref를 전달할 수 있도록 하는 기능이다. 
DOM에 직접 접근해야 하는 사용자 정의 컴포넌트를 만들 때 유용하다.

React는 이 함수를 두 개 인자 props와 ref를 사용하여 호출하고, 이 함수는 React 노드를 반환한다.

### 해결책
AS-IS
```javascript
const ComponentName = forwardRef((props, ref) => {
  return (
    <div ref={ref}></div>
  );
});
export default ComponentName;
```

위의 코드는 Component definition is missing display name 에러가 발생한다.
아래와 같이 수정해서 에러를 해결했다.

TO-BE
```javascript
const ComponentName = (props, ref) => {
  return (
    <div ref={ref}></div>
  );
};
export default forwardRef(ComponentName);
```

## React의 memo
React의 memo는 UI 컴포넌트의 렌더링을 최적화하는 데 사용된다. 
컴포넌트가 다시 렌더링 되는 것을 막으려면 memo로 감싸준다.
React.memo 에서 두번째 파라미터에 propsAreEqual 이라는 함수를 사용하여 특정 값들만 비교를 하는 것도 가능하다.
### 해결책
기본형은 forwardRef의 예제와 같으니 두번째 파라미터가 있는 경우의 예제를 살펴보자.

AS-IS
```javascript
const ComponentName = React.memo((props) => {
	 return (
    <div>{props.name}</div>
  );
},
  (prevProps, nextProps) => prevProps.users === nextProps.users
);
```

TO-BE
```javascript
const ComponentName = (props) => {
	  return (
    <div>{props.name}</div>
  );
}
export default React.memo(
  ComponentName,
  (prevProps, nextProps) => prevProps.users === nextProps.users
);
```


참고 문서
- https://react.vlpt.us/basic/19-React.memo.html
- https://github.com/jsx-eslint/eslint-plugin-react/issues/2105
- https://stackoverflow.com/questions/67992894/component-definition-is-missing-display-name-for-forwardref
