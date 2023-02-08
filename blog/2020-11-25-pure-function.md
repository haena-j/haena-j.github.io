---
title: 함수형 프로그래밍 기본 개념
category:
- javascript basics
tag:
- javascript
- FP
toc: true
---

함수형 프로그래밍이란 부수 효과를 제거하고 순수 함수를 만들어 모듈화 수준을 높이는 프로그래밍 패러다임이다.


# 부수효과 (Side Effect)

일반적으로 부작용(Side Effect)이란 원래의 목적과 다른 효과가 나타나는 것을 의미한다.

프로그래밍에서도 비슷한 의미로 쓰인다.

**부수 효과(=부작용)란 함수의 행위가 외부 환경에 영향을 미치는 것을 의미한다.**

함수가 결과값 이외에 다른 상태를 변경시킬 때 부수효과가 있다고 말한다. 

예를 들어, 함수가 전역변수나 정적변수를 수정하거나, 인자로 넘어온 것들 중 하나를 변경하거나 화면이나 파일에 데이터를 쓰거나, 다른 부수효과가 있는 함수에서 데이터를 읽어오는 경우가 있다. 

하여 이러한 부수효과는 프로그램의 동작을 이해하기 어렵게 한다.

부수효과를 사용하는 함수는 참조에 불투명하고 그렇지 않은 함수는 참조에 투명한 경우가 많다. 

# 순수함수 (Pure Function)
순수함수란 부수효과가 없는 함수라고 정의할 수 있다. 

외부의 상태에 어떠한 영향을 미치지 않으며, 어느 시점에서 실행되더라도 입력값이 같으면 출력값도 동일한 특징이 있다.
- 주어진 입력으로 항상 동일한 출력을 반환한다.
- 부수 효과(=부작용, Side Effect)가 없다. (함수의 실행이 외부에 영향을 끼치지 않음)
- 범위 밖의 변수에 의존하지 않는다.
- 응용 프로그램의 상태를 수정하지 않는다.

순수 함수는 예측이 가능하고, 디버그가 쉬우며 테스트하는 것은 더욱 쉽다. 



**순수한 함수**
```javascript
function add (a, b) { // 매개변수 a, b를 합산
  return a + b;
}
add(1, 2) // return 3
add(3, 4) // return 7
```
- 부수효과가 없고 주어진 입력에 항상 동일한 출력을 반환한다.

<br>

**순수하지 않은 함수**
```javascript
var c = 10;
function add (a, b) { //매개변수 a, b와 외부 변수c를 합산
  return a + b + c;
}
add(1, 2) // return 13
c = 0;
add(1, 2) // return 3
```
- 외부 변수 c를 참조, c의 변화에 영향을 받아 동일 입력 시 동일 출력을 보장하지 못한다.

```javascript
var arr = ['Hello']; // arr => ['Hello']

function addText(array) {
   array.push("Text");
}

addText(arr); // arr => ['Hello', "text"]
```
- 반환값도 없고, arr을 변경하여 부수 효과가 발생한다.


<br>

# 함수형 프로그래밍

## 특징
- 불변성 (Immutable)
- 참조 투명성 (Referential Transparency)
- 일급 함수 (First-class Function)
- 게으른 평가 (Lazy Evaluation)


### 1. 불변성 (Immutable)

**불변성은 객체가 생성된 이후 그 상태를 변경하지 않는다**는 의미이다.

상태의 변경은 부수 효과를 일으키기 때문에, 함수형 프로그래밍에서는 이를 제한한다.

쉽게 말해 함수 외부의 변수에 접근, 재할당하거나 함수의 인자를 변경하면 안 된다.

- 불변성을 이해하기 위해선 재할당과  값에 의한 호출(Call by value)과 참조에 의한 호출(Call by reference) 의 개념을 잘 알아두면 좋다.

JavaScript에서 객체(Object)를 제외한 모든 원시 타입(=기본 타입)은 변경 불가능한 값(immutable value) 이다. 
* Boolean / String / Number / null / undefined / Symbol(ES6)


ES6에서는 변수를 선언하는 let 키워드(재할당 가능)와 const 키워드(재할당 불가능)를 구분하여 제공함으로써 개발자가 실수로 메모리 공간에 저장되어 있는 값을 변경하는 행위를 방어할 수 있도록 도와준다.
- 그러나 const가 객체 내부까지 깊은 곳의 재할당까지 제어하지는 않는다.


객체의 데이터 변경이 필요한 경우, 그 데이터의 복사본을 만들어 그 일부를 변경하고, 변경한 복사본을 사용해 작업을 진행해야 한다.

의도하지 않은 객체의 상태 변화는 대부분 레퍼런스를 참조한 다른 객체에서 객체를 변경하여 발생하기 떄문이다.

**불변성을 지킨 경우**
```javascript
// immutable
const someBands = ["Metallica", "Queen"];
const bands = [...someBands, "Nirvana"];
```

**불변성을 지키지 않은 경우**
```javascript
// mutable
const bands = ["Metallica", "Queen"];
bands.push("Nirvana");
```
<br>

이 문제를 해결하기 위해 아래와 같은 방법을 사용하기도 한다.
- Object.assign : 객체 참조가 아닌 객체 복사(cloning)
- Object.freeze : 객체의 불변화를 통한 객체 변경 방지


<br>

### 2. 참조 투명성 (Referential Transparency)
 참조 투명성이란 함수가 함수 외부의 영향을 받지 않는 것을 의미한다. 
 즉, 함수의 결과는 입력 파라미터에만 의존하고 함수 외부에서 데이터를 읽지 않는다.
 
**참조에 투명한 함수**
```
const helloString = hello('heny');
console.log(helloString);
	
function hello(name) {
  return `My name is ${name}.`;
}
```
- 함수 hello가 외부의 영향을 받지 않고 입력 파라미터만 사용하고 있다.

**참조에 투명하지 않은 함수**
```
 const someName = 'heny';

function hello() {
  console.log(`My name is ${heny}.`);
}
 ```
 - 함수 hello가 외부 변수인 someName을 참조하고 있다.

<br>

### 3. 일급 함수 (First-class Function)

자바스크립트의 함수는 객체(Function Object)이므로, 일급 객체이자 일급 함수이다.

일급이라는 말을 이해하기 위해서 '일급의 조건'을 먼저 알아보면 다음과 같다.
#### 일급 시민 (first class citizen)
일반적으로 일급 시민의 조건을 다음과 같이 정의한다.
- 변수(variable)에 담을 수 있다.
- 인자(parameter)로 전달할 수 있다.
- 반환값(return value)으로 전달할 수 있다.

대부분의 프로그래밍 언어에서 숫자는 일급 시민의 조건을 충족한다.
숫자는 변수에 저장할 수 있고 인자나 반환값으로 전달할 수 있다.

#### 일급 객체(first class object)
일급 객체(first class object)라는 것은 특정 언어에서 객체(object)를 일급 시민으로써 취급한다는 뜻이다. 당연히 위의 조건을 모두 충족한다.

#### 일급 함수(First-class Function)
함수를 일급 시민으로 취급하는 것이다. 

즉,  **일급 함수는 함수를 다른 함수에 매개변수로 제공하거나, 함수가 함수를 반환할 수 있으며, 변수에도 할당할 수 있다.**

몇몇의 학자들은 일급 시민의 조건과 함께 다음과 같은 추가적인 조건을 요구한다.

- 런타임(runtime) 생성이 가능하다.
- 익명(anonymous)으로 생성이 가능하다.

<br>

Javascript에서 일급 함수가 중요한 이유는 고차 함수(high order function)와 클로저(closure) 때문이다.
- each, filter, map, sort 등이 고차 함수이다.

고차 함수와 클로저는 꼭 알아야 하는 부분이나 현재는 생략하고 지나가겠다.

<br>

### 4. 지연 평가(=게으른 평가, Lazy Evaluation)
일반적인 언어는 코드의 실행 즉시 값을 평가(Eager Evaluation)한다.

그러나 함수형 언어에서는 값이 필요한 시점에 평가(Lazy Evaluation)된다. 

**lazy evaluation의 예**
```javascript
a() && b()
```
JavaScript는 왼쪽의 a() 호출 결과를 평가하고 값이 false일 경우 필요없는 b는 평가하지 않는다. 

이 때, 즉시 평가하지 않고, 필요한 것만 평가하는 방법이 lazy evaluation이다.

값이 실제로 필요한 시점까지 실행하지 않기 때문에 시간이 오래 걸리는 작업도 손쉽게 동작시킬 수 있다.

Javascript에서는 배열 연산의 성능을 끌어올리기 위해 필요한 기법 중 하나로, memoization과 함께 이해하면 좋다.


<br><br>
Reference
- [https://flaria.wordpress.com/2008/12/14/부수효과side-effect/](https://flaria.wordpress.com/2008/12/14/%EB%B6%80%EC%88%98%ED%9A%A8%EA%B3%BCside-effect/)
- [https://medium.com/humanscape-tech/함수형-프로그래밍에-관해-7f6172599fc](https://medium.com/humanscape-tech/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%EC%97%90-%EA%B4%80%ED%95%B4-7f6172599fc)
- [https://jinminkim-50502.medium.com/immutability-불변성](https://jinminkim-50502.medium.com/immutability-%EB%B6%88%EB%B3%80%EC%84%B1-fdf379f6a35c)
- [https://developer.mozilla.org/ko/docs/Web/JavaScript/Data_structures](https://developer.mozilla.org/ko/docs/Web/JavaScript/Data_structures)
- [https://alexnault.dev/functional-programming-with-javascript-in-3-steps](https://alexnault.dev/functional-programming-with-javascript-in-3-steps)
- [https://developer.mozilla.org/ko/docs/Glossary/First-class_Function](https://developer.mozilla.org/ko/docs/Glossary/First-class_Function)
