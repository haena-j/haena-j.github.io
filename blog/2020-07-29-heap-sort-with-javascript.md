---
title: "[Javascript] 힙 정렬 알고리즘"
toc: true
toc_label: 목차
category:
- Algorithm
tag:
- algorithm
- javascript
- heapsort
---

힙 정렬 알고리즘에 대해 다룬다.

예제 코드는 [leetcode의 문제(오름차순 정렬)](https://leetcode.com/problems/sort-an-array/)를 기반으로  구현했다.


# 1. 힙(heap)
힙(heap)은 최댓값 및 최솟값을 찾아내는 연산을 빠르게 하기 위해 고안된 완전이진트리를 기본으로 한 자료구조이다.

간단하게, 힙은 다음과 같은 두가지 속성을 가진다.

1. 힙은 완전이진트리이다.
2. 부모노드 키 값과 자식노드 키 값 사이에 대소 관계가 존재한다.


## 힙의 종류
- 최대 힙 : 부모노드의 키값이 자식노드의 키값보다 항상 큰 힙

![최대 힙](/assets/resource/max_heap.png)

<br>

- 최소 힙 : 부모노드의 키값이 자식노드의 키값보다 항상 작은 힙

![최소 힙](/assets/resource/min_heap.png)

<br>

# 2. 힙 정렬(Heap Sort)

힙리스트를 배열로 만들면 노드의 부모, 자식노드에 인덱스로 접근할 수 있다.

- i번째 노드의 부모 노드 인덱스 :   i/2, (단, i > 1)

- i번째 노드의 왼쪽 자식 노드 인덱스 : 2\*i

- i번째 노드의 오른쪽 자식 노드 인덱스 : (2\*i) + 1

<br>

## 힙정렬 과정 (최대힙)

1. 최대 힙을 구성한다. 단말 노드를 자식노드로 가진 부모노드부터 구성하며 아래부터 루트까지 올라오며 순차적으로 만들어 갈 수 있다.
2. 가장 큰 수(루트에 위치)를 가장 작은 수와 교환한 뒤, 힙의 사이즈를 하나 줄인다. 
3. 힙의 사이즈가 1보다 크면, 위의 과정을 반복한다.

<br>

## 예제 코드

```javascript
var sortArray = function(nums) { 
    if(nums.length < 2) return nums;
    
    // 1. 
    let size = nums.length;
    for(let i = Math.floor(size/2-1); i >= 0; i--) {
        heap_sort(nums, i, size);
    }
  
    // 2.
    for(let j = size-1; j >= 0; j--) {
        [nums[j], nums[0]] = [nums[0], nums[j]];
				
    // 3.
        heap_sort(nums, 0, j);
    }
    
    return nums;
}

const heap_sort = (nums, i, size) => {
    let p = i;
    let left = i * 2 + 1;
	let right = i * 2 + 2;
    
    if(left < size && nums[p] < nums[left]) p = left;
    
    if(right < size && nums[p] < nums[right]) p = right;
    
    if(p > i) {
        [nums[p], nums[i]] = [nums[i], nums[p]];
        heap_sort(nums, p, size);
    }
}
```
1. 최대힙 구성
2. 루트(nums[0])에 위치한 가장 큰 수를 배열의 마지막에 위치한 가장 작은 수와 교환 후, 사이즈를 하나 줄임(j--)
3. 사이즈가 1보다 크면 위의 과정을 반복(heap_sort)

<br>

## 시간복잡도
이진 트리를 최대 힙으로 만들기 위하여 최대 힙으로 재구성 하는 과정이 트리의 깊이 만큼 이루어지므로 **O(log n)** 의 수행시간이 걸린다.
