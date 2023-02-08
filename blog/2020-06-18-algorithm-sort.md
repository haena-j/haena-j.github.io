---
title: "[Javascript] 기본 정렬 알고리즘"
toc: true
toc_label: 목차
category:
- Algorithm
tag:
- algorithm
- javascript
---

정렬 알고리즘에 대한 기본 개념이 있는 상태에서 자바스크립트로 구현해보기 위해 정리한 문서로,
거품·선택·삽입정렬과 퀵·합병정렬을 다룬다.

예제 코드는 [leetcode의 문제(오름차순 정렬)](https://leetcode.com/problems/sort-an-array)를 기반으로 직접 구현했으며, 가독성이나 효율성이 떨어질 수 있다.


<br>

# 1. Bubble Sort (거품 정렬) 
서로 인접한 두 원소의 대소를 비교하고, 조건에 맞지 않다면 자리를 교환하며 정렬하는 알고리즘

![버블정렬](/assets/resource/bubble_sort.gif)

Bubble Sort는 구현이 매우 간단하고 소스 코드가 직관적이지만,
시간복잡도가 좋은 퍼포먼스를 내지 못해서 실제로는 잘 사용되지 않는다.

## 예제 코드
```javascript
var sortArray = function(nums) {
    for (let i = 0; i < nums.length; i++) { // 1
        for (let j = 1; j < nums.length-i; j++) {
            if (nums[j-1] > nums[j]) { // 2
                [nums[j], nums[j-1]] = [nums[j-1], nums[j]]; // 3
            }
        }
    }

    return nums;
}
```
1. 정렬이 되어 제외될 원소의 갯수를 의미. 1회전이 끝난 후, 배열의 마지막 위치에는 가장 큰 원소가 위치하기 때문에 하나씩 증가시켜줌
2. 현재 원소(j)와 바로 이전의 원소 값(j-1)을 비교
3. 이전 원소의 값이 클 경우 두 원소의 값을 교환



## 시간복잡도
Bubble Sort의 시간복잡도는 (n-1) + (n-2) + (n-3) + .... + 2 + 1 => n(n-1)/2이므로,  **O(n^2)** 이다. 

Bubble Sort는 정렬이 돼있던 안돼있던, 2개의 원소를 비교하기 때문에 최선, 평균, 최악의 경우 모두 시간복잡도가 O(n^2) 으로 동일하다. 

 <br><br>

# 2. Selection Sort (선택 정렬) 
해당 순서에 원소를 넣을 위치는 이미 정해져 있고, 어떤 원소를 넣을지 선택하는 알고리즘

![선택정렬](/assets/resource/selection_sort.gif)


Selection Sort는 정렬을 위한 비교 횟수는 많지만, Bubble Sort에 비해 실제로 교환하는 횟수는 적기 때문에 많은 교환이 일어나야 하는 자료상태에서 비교적 효율적이다.

## 예제 코드
```javascript
var sortArray = function(nums) {
    let idxMin;
    for (let i = 0; i < nums.length-1; i++) { // 1
        idxMin = i;
        for (let j = i+1; j < nums.length; j++) { // 2
            if (nums[j] < nums[idxMin]) {
                idxMin = j;
            }
        }
        [nums[i], nums[idxMin]] = [nums[idxMin], nums[i]]; // 3
    }
    
    return nums;
}
```
1. 원소를 넣을 위치(i)를 선택 -> 배열의 첫 번째부터 차례로 진행
2. i+1 원소부터 차례대로 값을 비교하여 자리에 오는 값을 찾음
3. 원소를 넣을 위치(i)와 찾은 위치의 값을 교환



## 시간복잡도
Selection Sort의 시간복잡도는 (n-1) + (n-2) + (n-3) + .... + 2 + 1 => n(n-1)/2이므로,  **O(n^2)** 이다. 

최선, 평균, 최악의 경우 모두 시간복잡도가 O(n^2) 으로 동일하다. 

<br><br>

# 3. Insertion Sort (삽입 정렬)
2번째 원소부터 시작하여 그 앞(왼쪽)의 원소들과 비교하여 삽입할 위치를 지정한 후, 원소를 뒤로 옮기고 지정된 자리에 자료를 삽입 하여 정렬하는 알고리즘

![삽입정렬](/assets/resource/insertion_sort.gif)


알고리즘이 단순하며, 대부분의 원소가 이미 정렬되어 있는 경우 매우 효율적일 수 있다.

최선의 경우 O(N)로, 다른 정렬 알고리즘의 일부로 사용될 만큼 좋은 정렬 알고리즘이다. 

## 예제 코드
```javascript
var sortArray = function(nums) {
    for(let i = 1; i < nums.length; ++i) { // 1
        for(let j = i; j > 0; --j) {
            if(nums[j] < nums[j-1]) { // 2
                [nums[j], nums[j-1]] = [nums[j-1], nums[j]]; 
            } else break;
        }
    }
    return nums;
}
```
1. 두 번째 원소부터 탐색
2. 이전 위치(j-1)가 현재 위치(j) 보다 값이 크면 서로 값을 교환



## 시간복잡도
최악의 경우(역으로 정렬되어 있을 경우), (n-1) + (n-2) + .... + 2 + 1 => n(n-1)/2 즉, **O(n^2)** 이다.

하지만, 최선의 경우(모두 정렬이 되어있는 경우), 한번씩 밖에 비교를 안하므로 **O(n)** 의 시간복잡도를 가지게 된다. 

또한, 이미 정렬되어 있는 배열에 자료를 하나씩 삽입/제거하는 경우에는, 현실적으로 최고의 정렬 알고리즘이 되는데, 탐색을 제외한 오버헤드가 매우 적기 때문이다.

<br><br>

# 4. Quick Sort (퀵정렬)
2번째 원소부터 시작하여 그 앞(왼쪽)의 원소들과 비교하여 삽입할 위치를 지정한 후, 원소를 뒤로 옮기고 지정된 자리에 자료를 삽입 하여 정렬하는 알고리즘

![퀵정렬](/assets/resource/quick_sort_01.gif)


분할 정복 알고리즘의 하나로, 평균적으로 매우 빠른 수행 속도를 보여주며, Merge Sort와 달리 리스트를 비균등하게 분할한다.

피벗 선택 방식 : 첫번째, 중간, 마지막, 랜덤
(선택 방식에 따라 속도가 달라지므로 중요함)

Quick Sort는 피벗 값이 최소나 최대값으로 지정되어 파티션이 나누어지지 않았을 때 O(n^2)에 대한 시간복잡도를 가진다.

이런 상황에서는 피벗을 중간 값으로 선택하여 해결할 수 있다.

## 예제 코드
```javascript
var sortArray = function(nums) {
    quickSort(nums);
    return nums;
}

const quickSort = (nums, start = 0, end = nums.length-1) => {
    if(start >= end) return;
    
    let left = start, right = end;
    let pivot = nums[Math.floor((left+right) / 2)]; // 1
    
    while(left <= right) { // 2
        while(left <= right && nums[left] < pivot) left++; // 3
        while(left <= right && pivot < nums[right]) right--; // 4
        if(left <= right) { // 5
            [nums[left], nums[right]] = [nums[right], nums[left]];
            left++;
            right--;
        }
    }
    // 6
    quickSort(nums, start, right); // 7
    quickSort(nums, left, end); // 7
}
```
1. 피벗을 중간값으로 선택
2. 탐색하지 않은 값이 있을 때까지 탐색&교환(3,4,5)을 진행
3. pivot보다 큰 값이 나올 때까지 left에서 오른쪽으로 하나씩 이동
4. pivot보다 작은 값이 나올 때까지 right에서 왼쪽으로 하나씩 이동
5. 탐색하여 찾은 두 요소를 교환한 뒤 left, right 하나씩 이동
6. 이제 피벗 기준 왼쪽에는 작은 값, 오른쪽에는 큰 값이 위치하게 됨
7. 왼쪽 값과 오른쪽 값으로 나누어 정렬을 진행


## 시간복잡도
평균과 최선의 경우 **O(nlogn)**의 시간복잡도를 가진다.

Quick Sort는 서로 먼 거리에 있는 요소를 교환하면서 한번 선택된 기준은 제외하기 때문에,

동일한 O(nlogn) 시간복잡도를 가지는 알고리즘 중 가장 빠르다.

최악의 경우(ex 이미 정렬된 리스트), **O(n^2)** 의 시간복잡도를 가지게 된다. 

<br><br>

# 5. Merge Sort (병합 정렬)
하나의 리스트를 두 개의 균등한 크기로 분할하고, 다시 합병시키면서 정렬해나가는 알고리즘

![병합정렬](/assets/resource/merge_sort.gif)

합병 정렬이라고도 하며, 데이터 분포에 영향을 덜 받아 안정적이고 빠른 정렬 방법 중 하나이다.

병합정렬은 순차적인 비교로 정렬을 진행하므로, LinkedList의 정렬이 필요할 때 사용하면 효율적이다.

## 예제 코드
```javascript
var sortArray = function(nums) { 
   if(nums.length < 2) return nums; // 1
   
    const mid = Math.floor(nums.length/2);
    const left = sortArray(nums.slice(0, mid)); // 2
    const right = sortArray(nums.slice(mid)); // 2
    return merge(left, right); // 3
}

const merge = (left, right) => { // 3
    let sorted = [];
    let iLeft = 0, iRight = 0;
    while(iLeft < left.length && iRight < right.length) { // 4
        (left[iLeft] < right[iRight]) ? 
        sorted.push(left[iLeft++]) : sorted.push(right[iRight++]);
    }
    
    return [...sorted,...left.slice(iLeft),...right.slice(iRight)];
}
```
1. 배열의 길이가 1이하면 이미 정렬된 것으로 봄
2. 정렬되지 않은 배열을 절반으로 잘라 두 배열로 나눔 (두 배열을 다시 재귀적으로 분할)
3. 두 부분 배열을 다시 하나의 정렬된 배열로 합병
4. shift함수를 사용할때보다 Runtime이 줄어듬 (252ms -> 100ms)
```javascript
// shift 사용 예제
while(left.length && right.length) {
        sorted.push((left[0] < right[0]) ? left.shift() : right.shift());
    }
    return [...sorted,...left,...right];
```

## 시간복잡도
시간복잡도는 최소 **O(nlogn)**을 보장한다.

<br><br>
참고 자료
- https://github.com/GimunLee/tech-refrigerator/tree/master/Algorithm
- https://ko.wikipedia.org/wiki/%ED%80%B5_%EC%A0%95%EB%A0%AC
- https://ko.wikipedia.org/wiki/%ED%95%A9%EB%B3%91_%EC%A0%95%EB%A0%AC
- https://commons.wikimedia.org/wiki/File:Quicksort-example.gif
