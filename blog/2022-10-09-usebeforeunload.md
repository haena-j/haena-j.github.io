---
title: "[react-use의 useBeforeUnload] React 에서 페이지를 떠날 때 알림창(confirm) 띄어주기"
tag:
- react
- useBeforeUnload
---

# react-use의 useBeforeUnload

useBeforeUnload는 react-use 라이브러리에서 제공하는 훅이다. 

이 훅은 사용자가 페이지를 떠나려고 할 때 (ex, 브라우저 탭을 닫거나 다른 웹 사이트로 이동할 때) beforeunload 이벤트를 추가할 수 있도록 window 객체에 리스너를 추가한다.

특징은 아래와 같다.

- 이벤트는 사용자가 페이지를 떠나려고 할 때 트리거된다. 
- 트리거되면 훅은 제공한 콜백 함수를 호출하며,  confirm과 같은 알림창 표시하거나 저장되지 않은 데이터를 저장하는 등의 일부 작업을 수행할 수 있다.
- 콜백 함수에서 문자열을 반환할 수 있는데, 이 문자열은 알림창에 표시되어 사용자에게 페이지를 떠날 것인지 확인하는 메시지로 사용할 수 있다.


## 사용법
기본적인 사용방법은 아래와 같다.
```javascript
import {useBeforeUnload} from 'react-use';

const Demo = () => {
  const [dirty, toggleDirty] = useToggle(false);
  const dirtyFn = useCallback(() => {
    return dirty;
  }, [dirty]);
  useBeforeUnload(dirtyFn, 'You have unsaved changes, are you sure?');

  return (
    <div>
      {dirty && <p>Try to reload or close tab</p>}
      <button onClick={() => toggleDirty()}>{dirty ? 'Disable' : 'Enable'}</button>
    </div>
  );
};
```

## 예제
이를 활용해서 내용의 변화가 있는 상태로 페이지를 떠날 때 알림창을 띄우는 간단한 예제를 만들어 보자.
```javascript
import { useBeforeUnload } from 'react-use';

function MyComponent() {
    const [hasUnsavedChanges, setHasUnsavedChanges] = useState(false);
    
    useBeforeUnload(() => {
        if (hasUnsavedChanges) {
            return 'You have unsaved changes, are you sure you want to leave?';
        }
    });
    
    return (
        <div>
            <textarea onChange={() => setHasUnsavedChanges(true)} />
            <button onClick={() => setHasUnsavedChanges(false)}>Save</button>
        </div>
    );
}
```
위의 예제에서 useState hook을 사용하여 컴포넌트에 저장되지 않은 변경 사항이 있는지 추적한다.
사용자가 textarea 를 변경하면 hasUnsavedChanges를 true로 설정한다. 
그리고 사용자가 "Save" 버튼을 클릭하면 hasUnsavedChanges를 false로 설정한다.

useBeforeUnload hook는 hasUnsavedChanges 상태를 확인하는 콜백 함수와 함께 호출된다. 
true인 경우 콜백은 알림창(confirm)에서 사용자에게 페이지를 떠나는 것을 확인하도록 요청하는 문자열을 반환한다.
상태가 false이면 콜백은 아무 것도 반환하지 않으며 사용자는 알림창을 보지 않고 페이지를 나갈 수 있다.


비슷한 예제로 양식이 제출중(데이터를 전송중)일 때 페이지를 떠나려하면 아래와 같이 알림창을 띄울 수 있다.
```javascript
import { useBeforeUnload } from 'react-use';

function MyComponent() {
    const [isSubmitting, setIsSubmitting] = useState(false);
    
    useBeforeUnload(() => {
        if (isSubmitting) {
            return 'You have an ongoing submission, are you sure you want to leave?';
        }
    });
    
    const handleSubmit = async () => {
        setIsSubmitting(true);
        // perform the submission here
        setIsSubmitting(false);
    }
    
    return (
        <div>
            <form onSubmit={handleSubmit}>
                <input type="text" />
                <button type="submit">Submit</button>
            </form>
        </div>
    );
}
```
이 예제에서는 useState hook을 사용하여 현재 submit 상태를 저장한다.
유저가 양식을 제출하면 isSubmitting 을 true로 설정한다.
그리고 양식 제출이 완료되면 isSubmitting을 false로 설정한다.

마찬가지로 useBeforeUnload hook은 isSubmitting 상태를 확인하는 콜백 함수와 함께 호출된다.
true인 경우 콜백은 알림창을 반환하고, false이면 콜백은 아무 것도 반환하지 않는다.


useBeforeUnload hook은 컴포넌트에서 한 번만 호출해야 하며, 
다른 훅이나 조건부 내부가 아니라 컴포넌트의 최상위 수준에서 호출해야 한다는 점에 유의해야 한다.


참고문서
- https://github.com/streamich/react-use/blob/master/docs/useBeforeUnload.md
- https://developer.mozilla.org/en-US/docs/Web/API/Window/beforeunload_event
