---
layout: post
title: "알고리즘 문제해결 패턴"
author: Minsun
categories: [Development]
tags: [Algorithm]
image: "assets/images/javascript-algorithm.png"
beforetoc: ""
toc: true
---

### 1. Frequency counters(빈도를 세어야하는 문제)

해당 패턴은 객체나 세트를 사용하여 값들 / 값들의 빈도수를 수집하기 위해 사용된다.배열이나 문자열을 사용하여 이중 루프나 O(n\*\*2) 작업을 피할 수 있게 해준다.

일반적으로 객체를 사용한다. 주어진 배열이나 문자열을 분석하여 어떻게 객체로 바꿀지를 생각하고 그 객체를 서로 비교하는 것이 좋다.

```jsx
//2개의 배열을 인자로 받는 함수는 첫번째 배열의 모든 요소의 2제곱이 두번째 배열의 요소와 일치할때 true
//를 리턴한다. 값들의 빈도수는 반드시 일치해야한다.

same([1, 2, 3], [9, 1, 4]); //true
```

```jsx
//colt 풀이방법
function same(arr1, arr2){
    if(arr1.length !== arr2.length){
        return false;
    }
    let frequencyCounter1 = {}
    let frequencyCounter2 = {}
    for(let val of arr1){
        frequencyCounter1[val] = (frequencyCounter1[val] || 0) + 1
    }
    for(let val of arr2){
        frequencyCounter2[val] = (frequencyCounter2[val] || 0) + 1
    }
    console.log(frequencyCounter1);
    console.log(frequencyCounter2);
    for(let key in frequencyCounter1){
        if(!(key ** 2 in frequencyCounter2)){
            return false
        }
        if(frequencyCounter2[key ** 2] !== frequencyCounter1[key]){
            return false
        }
    }
    return true
}

same([1,2,3,2,5], [9,1,4,4,11])

-> 시간 복잡도는 O(n)이 된다.
네스팅하지 않는 여러개의 루프는 제곱화되지 않는다.
```

### 2. Multiple pointers

인덱스나 위치에 대응되는 포인터나 값들을 생성하고 조건에 의해 시작, 끝, 중간으로 옮긴다.

```jsx
//multiple pointers pattern

//1. 문제
//정렬된 정수들을 받는 함수를 작성해라. 이함수는 합이 0이 되는 첫번째 쌍을 찾는다. 두 값의 합이 0 이 되는 값들을 배열로 리턴하거나
//값이 없는 경우는 undefined를 반환한다.

//2. 접근방법
//정렬된 배열이기 때문에 같은 인덱스를 이용해서 맨앞과 끝에서 접근해서 그 두 요소의 합이 0이 되는 경우를 찾는다.

function sumZero(arr) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] + arr[arr.length - 1 - i] === 0) {
      return [arr[i], arr[arr.length - 1 - i]];
    }
  }
  return undefined;
}

sumZero([-3, -2, -1, 0, 1, 2, 3]); //[-3, 3]
sumZero([-2, 0, 1, 3]); //undefined
sumZero([1, 2, 3]); //undefined

//colt 풀이방법
//왼쪽과 오른쪽에서 접근하게 될 인덱스 값을 구해서 인덱스로 접근한 요소 값의 합을 이용하여서 조건에 맞게 인덱스의 위치를 조정하여 풀이하는 방법
function sumZero(arr) {
  let left = 0;
  let right = arr.length - 1;

  while (left < right) {
    let sum = arr[left] + arr[right];
    if (sum === 0) {
      return [arr[left], arr[right]];
    } else if (sum > 0) {
      right--;
    } else {
      left++;
    }
  }
}

//1. 문제
//정렬된 배열을 받고 배열에서 유니크한 값들의 갯수를 세는 함수를 작성해라. 배열안엔 음수값이 있을 수도 있다(정렬된)

//2. 접근방법
// 왼쪽끝과 오른쪽 끝의 인덱스를 먼저 변수에 선언한 후 두 인덱스로 접근한 값들의 뺄셈값이 0 이되면 왼쪽인덱스 = 오른쪽인덱스 +1이되고 count 변수에 +1 해준다.
// 뺄셈값이 음수면 오른쪽인덱스를 1 낮추고 양수면 왼쪽인덱스를 1 높인다. 이는 왼쪽이 오른쪽보다 작은 경우까지 계속 반복한다.

function countUniqueValues(arr) {
  let count = 0;
  let left = 0;
  let right = arr.length - 1;
  if (arr.length === 0) {
    return count;
  }

  while (left < right) {
    let sub = arr[left] - arr[right];

    if (sub === 0) {
      count += 1;
      left = right + 1;
    } else if (sub < 0) {
      right--;
      count += 1;
    } else {
      left++;
      count += 1;
    }
  }
  return count;
}

countUniqueValues([1, 1, 1, 1, 2]); //2
countUniqueValues([-2, -1, -1, 0, 1]); //4
countUniqueValues([1, 2, 3, 4, 4, 4, 7, 7, 12, 12, 13]); //7이 나와야 되는데 10이 나옴

//colt풀이방법
// 왼쪽 끝 인덱스(i)와 그 인덱스에 +1 한 인덱스(j) 의 값을 비교하여 같으면 j+1이 되고(j는 루프를 돌며 바뀌므로 이 경우는 조건을 달아줄 필요없음.) 계속 비교하여서 다른 경우 i +1 이 되고 해당 위치의 값은 j가 가리키는 값으로 대체됨.
// 다시 비교하여서 결국은 i+1이 정답으로 리턴된다(i가 지나온 자리에는 유니크한 값들이 채워지게됨)
function countUniqueValues(arr) {
  if (arr.length === 0) return 0;
  let i = 0;
  for (let j = 1; j < arr.length; j++) {
    if (arr[i] !== arr[j]) {
      i++;
      arr[i] = arr[j];
    }
  }
  return i + 1;
}
```

### 3. Sliding Window pattern

배열이나 문자열 데이터의 일부분을 추적할때 유용하게 쓰인다.

```jsx
//sliding window pattern

//1. 문제
// 정수로 구성된 배열과 숫자(n)를 인자로 받는 함수는 배열에서 n 갯수로 요소들을 묶었을때 가장 큰 합을 리턴한다.

//2. 접근방법
//루프를 돌면서 i번째 인덱스에 i+n 까지 이중 루프를 돌면서 합을 arr에 푸시한 뒤 그 중 최댓값을 리턴한다. -> 빅오 O(n**2) -> 좋지 않은 방법..

function maxSubarraySum(arr, n) {
  if (arr.length === 0) return null;
  let sumArr = [];

  //n으로 묶었을때 묶이는 곳까지만 루프가 돌아야함
  for (let i = 0; i < arr.length - n + 1; i++) {
    let sum = 0;
    for (let j = 0; j < n; j++) {
      sum += arr[i + j];
    }
    sumArr.push(sum);
  }
  return Math.max(...sumArr);
}

maxSubarraySum([1, 2, 5, 2, 8, 1, 5], 2); //10
maxSubarraySum([], 4); //null
maxSubarraySum([1, 2, 5, 2, 8, 1, 5], 4); //17

//colt 방법 -> 빅오 O(n)

//접근방법
// 주어진 n의 갯수대로 요소들을 묶어서 합산을 구하는 것이 아니라 첫번째에 구하고 그다음 번째에서 구해놓은 합에 맨앞의 요소를 빼고 다음 요소를 더한다.

function maxSubarraySum(arr, n) {
  let maxSum = 0;
  let tempSum = 0;

  if (arr.length === 0) return null;

  //첫번째 n개의 요소들의 합을 구한다
  for (let i = 0; i < n; i++) {
    maxSum += arr[i];
  }

  //합을 구할 변수에 맨처음의 합을 할당
  tempSum = maxSum;

  for (let i = n; i < arr.length; i++) {
    tempSum = tempSum - arr[i - num] + arr[i];
    maxSum = Math.max(tempSum, maxSum);
  }

  return maxSum;
}
```

### 4. Divide and conquer(분할정복)

작은 부분으로 분할된 데이터를 가지고 해당 부분으로 과정을 반복하는 패턴이다.

```jsx
//divide and conquer

//1, 문제
//정수로 이루어진 배열과 숫자를 인자로 받는 함수는 숫자가 배열의 몇번째 인덱스에 위치했는지 구해서 리턴한다. 없는 경우 -1 리턴

//2. 접근방법
// indexOf 메소드를 사용해서 답구하기

function search(arr, n) {
  return arr.indexOf(n);
}

search([1, 2, 3, 4, 5, 6], 4); //3
search([1, 2, 3, 4, 5, 6], 6); //5
search([1, 2, 3, 4, 5, 6], 11); //-1

//colt 풀이방법
// 주어진 데이터가 정렬된 배열이므로 가운데의 인덱스를 구해서 해당 요소보다 구하고자 하는 값이 큰지 아닌지를 판단하여
// 작은 부분만 탐색할 수 있다. -> 빅오 O(logn)

function search(arr, n) {
  let min = 0;
  let max = arr.length - 1;

  while (min < max) {
    let mid = Math.floor((min + max) / 2);
    let currentEl = arr[mid];

    if (arr[mid] < n) {
      min = mid + 1;
    } else if (arr[mid] > n) {
      max = mid - 1;
    } else {
      return mid;
    }
  }

  return -1;
}
```
