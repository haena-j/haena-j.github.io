---
title: 순열 (Permutation)
category:
- Algorithm
tag:
- javascript
- algorithm
- permutation
toc: true
---

순열 : 중복을 허용하지 않고 순서가 의미 있다.

조합 : 중복을 허용하지 않고 순서가 의미 없다.

순서가 의미 있다 = 구성이 같아도 위치가 다르면 다른 값으로 본다.

# 순열 (Permutation)
순열이란 중복없이 순서대로 나열하는 것이다.

ex) 6가지 종류의 과일이 있을 때 3가지 과일을 선택하여 3명의 학생에게 나누어 줄 수 있는 경우의 수
이때, [사과, 배, 귤]과 [배, 귤, 사과]는 받는 학생이 다르므로 다른 값이다.

이제 공식으로 확인하자.

첫번째 과일을 선택할 때는 경우의 수가 6가지가 된다.<br>두번째 과일을 선택할 땐, 첫번째 과일이 빠진 5가지가 된다.<br>세번째 과일의 경우의 수는 하나가 더 빠진 4가지가 된다.

즉, **6x5x4= 120**가지이고 **6(6-1)(6-2)** 이다.

**n개 중 r개를 선택하여 나열하는 경우의 수**는 아래와 같이 공식이 성립하게 된다.
```
n(n-1)(n-2) ... (n-(r-1))
= n(n-1)(n-2) ... (n-r+1)
= n! / (n-r)!
```

## 순열 Javascript 코드
이를 Javascript로 구현해보자.<br>
탐색할 배열 arr과 선택할 개수 r을 입력받아 순열을 진행하는 함수 getPermutations 이다.<br>배열에 담긴 아이템들(arr.length 개) 중 r개를 선택하여 나열하는 경우의 수를 구해준다.

### n가지 중 r개 선택하여 나열
```js
 // 순열
 // n(=arr.legnth)개 중  r개를 선택하여 나열함 
 // r을 입력하지 않을 경우 기본값 = arr.length
  const getPermutations = function (arr, r=arr.length) {
    if (r === 1) return arr;
    const results = [];
    for (let i = 0; i < arr.length; i++) {
        const rest = [...arr.slice(0, i), ...arr.slice(i+1)]; // arr[i]를 제외한 나머지 배열
      for(let permutation of getPermutations(rest, r - 1)) { // 나머지 배열에 대해 순열 구하기
          results.push([arr[i]].concat(permutation)); // 구한값을 arr[i]에 이어붙여줌
      }
    }
    return results;
  };
```

위의 함수를 호출하면 대략 아래와 같은 결과를 얻을 수 있다.

```javascript
const results = getPermutations(['사과','배','딸기','포도','수박', '파인애플'],3);
console.log(results);
/* 
[["사과", "배", "딸기"],["사과", "배", "포도"],["사과", "배", "수박"],
["사과", "배", "파인애플"],["사과", "딸기", "배"],["사과", "딸기", "포도"],
["사과", "딸기", "수박"],["사과", "딸기", "파인애플"],["사과", "포도", "배"],
["사과", "포도", "딸기"],["사과", "포도", "수박"],
 ... 중략 ... 
 ["파인애플", "수박", "포도"]] 
 */
 // length = 120

```
<br>

#### 코드 상세 풀이
1) r === 1(1가지 선택 남음)이라면,  arr 리턴해준다. n부터 (n-1) .... n-(r-1)까지 탐색이 끝났다.
```javascript
if (r === 1) return arr;
```
- arr = ['사과', '배', '귤', '포도', '딸기', '수박']에서 1가지를 선택한다는 건 -> 사과를 선택한 경우, 배를 선택한 경우 ... 수박을 선택한 경우를 담은 배열 arr을 리턴해주는 것이다.
<br>

2) for문을 통해 첫번째 선택할 과일을 고른다. (arr[i]번째에 있는 과일)

```javascript
for (let i = 0; i < arr.length; i++) {
```
<br>

3) 선택한 과일을 제외한 나머지를 구한다. 
```javascript
const rest = [...arr.slice(0, i), ...arr.slice(i+1)]; // arr[i]를 제외한 나머지 배열
```
<br>

4) 이미 선택한 과일을 제외한 나머지 과일 rest를 기준으로 과일을 선택한다.<br>
```javascript
getPermutations(rest, r - 1) // r-1 중요
```
<br>

5) 나머지 과일에 대한 순열을 구한 값을 차례로 for문 돌아가며
```javascript
for(let permutation of getPermutations(rest, r - 1)) {
```
<br>

6) 처음 선택했던 과일 arr[i]에 나머지 값들을 붙여준다.
```javascript
 results.push([arr[i]].concat(permutation));
```
<br>

n개 중 r개를 선택하여 나열하는 방법을 터득했다.

이제 n개가 있을 때, 1개부터 n개까지 모두 선택하여 나열하는 경우의 수를 구해보자.

위의 코드를 그대로 활용한다면, 반복문을 통해 1부터 n까지 getPermutations 함수를 호출하면 된다.

그렇다면 리턴값을 각각 담아줄 변수도 필요할텐데, 이 작업을 그냥 함수안에서 진행하자.


### n가지 중 1~n개 선택하여 나열
```javascript
const getAllPermutations = (arr, permutedList, m=[]) => {
    if(m.length > 0) permutedList.push(m);
    
    for(let i = 0; i < arr.length; i++){
        const rest = [...arr.slice(0,i), ...arr.slice(i+1)];
        getAllPermutations(rest, permutedList, [...m, arr[i]]);
    }
}
```
<br>

위의 함수를 호출하면 대략 아래와 같은 결과를 얻을 수 있다.

```javascript
const list = [];
getAllPermutations([1,2,3], list);

console.log(list);
/*
[[1],[1,2],[1,2,3],[1,3],[1,3,2],[2],[2,1]...중략...[3,2,1]]

length = 15
*/

```

<br><br>
Reference 
- [https://ko.wikipedia.org/wiki/순열](https://ko.wikipedia.org/wiki/%EC%88%9C%EC%97%B4)
