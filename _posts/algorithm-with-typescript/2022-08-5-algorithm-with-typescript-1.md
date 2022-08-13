---
layout: single
title: "[Algorithm with Typescript] 1. 여러집합의 교집합 구하기(set, map, array)"
categories : algorithm-with-typescript
tag : [교집합, typescript, hashmap, hashset]
author_profile: true
sidebar_main: true
toc: true
toc_label: Index
toc_sticky: true
---
## RELATED POSTS
[Algorithm with Typescript]포스트 모음
- [1. 여러집합의 교집합 구하기(set, map, array)](https://iamhmin.github.io/algorithm-with-typescript/algorithm-with-typescript-1/) 


## 문제
``` typescript
let initial_array = [
     [7,8,9,10],
     [4,5,6,7,8,9,10,11,12,13,14],
     [9,10,11,12,13,14,15,16,17,18,19,20,21],
     [10,11,12,13,14,15,16,17,18,19]
     ]
```     
와 같이, 두 개가 아닌 여러개의 집합이 주어졌을 때 가장 빠르게 교집합을 구할 수 있는 방법에 대해 생각해보게 되었습니다. 첫번째는 Set을 이용하는 방법이고, 두 번째는 array와 Map을 이용하는 방법입니다. 혹시 더 효율적인 방법을 아신다면 공유 부탁드립니다ㅎㅎ
<br>

## 1. Set
### 1-1 순서
1. 주어진 배열을 set으로 바꾼다
2. 가장 적은 원소를 가진 set을 찾는다
3. 가장 적은 원소를 가진 set의 원소 각각에 대하여, 나머지 모든 집합들이 원소를 가지고 있으면 교집합에 포함시키고, 하나의 집합이라도 해당 원소를 가지고 있지 않으면 포함시키지 않는다.
<br>

### 1-2 코드
``` typescript
let initial_array = [
     [7,8,9,10],
     [4,5,6,7,8,9,10,11,12,13,14],
     [9,10,11,12,13,14,15,16,17,18,19,20,21],
     [10,11,12,13,14,15,16,17,18,19]
     ]
let startTime = new Date().getTime();    //시작 시간 계산

let set_array:Set<number>[] = []        //처음에 배열로 주어졌으므로 set형태로 바꿈
initial_array.forEach((data)=>{
    set_array.push(new Set(data))
})

let min = set_array.reduce((previous, current) => {         //가장 크기가 작은 set을 찾는다
	return previous.size > current.size ? current:previous;
});

let common_element:number[] = []                       //교집합 원소가 들어갈 배열
min.forEach((el)=>{                                    //가장 크기가 작은 set의 원소에 대하여,
    let isCommon:boolean = true                        //교집합 여부의 초기값을 true로 세팅하고,
    for (let set_data of set_array) {                  //다른 set을 for문으로 돌면서
        if (!set_data.has(el)) {                       //원소를 가지고 있지 않으면 false로 값을 바꾸고 break;
            isCommon = false
            break;
        }
    }
    if (isCommon) common_element.push(el)              //true인 경우에만 배열에 넣음
})

console.log(common_element)                            //[10] 리턴

let endTime = new Date().getTime();                    //끝나는 시간 계산

console.log('set으로 계산', endTime - startTime);
```
<br>

### 1-3 worth studying
공부해보고 추가한 or 추가할 내용입니다.
- 혹시 주어진 모든 집합의 배열을 set으로 바꾸는 과정이 비효율적이었을까요? 
- array.length값을 구해 가장 짧은 배열을 구하는 것과 set.size값으로 가장 작은 크기의 set을 구하는 방법에 성능 차이가 있을까요? 
- 타입스크립트의 forEach에서는 break가 안돼서, for (let ~ of)를 사용했습니다. 그런데 break 쓴거나 안쓴거나 큰 차이가 없었습니다...
<br>
<br>

## 2. Array + Map
이 방법은 처음에 생각했던 방법입니다. 예전에 알고리즘 공부하다가 비슷한 로직을 스치듯 봤던 것 같기도 하고요.
<br>

### 2-1 순서
1. 일단 맵을 만든다.
2. 배열의 모든 원소에 대해서 full search를 하면서, map에 (key:원소이름)으로, 값을 추가한다. 값은 배열에 해당 원소가 있을 때마다 매번 1씩 증가된다.
3. search가 끝난 후 initial_array.length와 key의 value값이 같다면, 배열을 순환할 때마다 해당 숫자가 포함이 된 것이므로, 교집합의 원소로 추가한다.
<br>

### 2-2 코드
``` typescript
let initial_array = [
     [7,8,9,10],
     [4,5,6,7,8,9,10,11,12,13,14],
     [9,10,11,12,13,14,15,16,17,18,19,20,21],
     [10,11,12,13,14,15,16,17,18,19]
     ]
let startTime2 = new Date().getTime();   //시작시간 계산
let map = new Map<number, number>();     
let array_length = initial_array.length;   //주어진 배열의 길이를 구합니다. 여기서는 4! 
let common_element2:number[] = []            //교집합에 해당하는 원소를 넣을 배열

 for (let array_data of initial_array) {     //초기 배열안에 있는 네개의 배열을 search하는 for문을 만들고
        for (let num of array_data) {        //배열안의 원소를 search하는 for문을 다시 만듭니다.
        if (map.has(num)) {                  //map이 해당 숫자를 키로하는 값을 가지고 있다면, 
            map.set(num, Number(map.get(num)) + 1) // value값에 1을 더해서 다시 넣고
            if (map.get(num) === array_length) { //initial_array.length와 value값이 같다면 교집합에 추가
                common_element2.push(num)
            }
        } else {                                  //map이 해당 숫자를 키로하는 값을 가지고 있지 않다면,
            map.set(num, 1)                       //value로 1을 넣는다.
            if (map.get(num) === array_length) {  //전체 배열의 길이가 1인 경우 바로 교집합에 추가
                common_element2.push(num)
            }
        }
    }
    }

console.log(common_element2)                        //[10] 리턴

let endTime2 = new Date().getTime();                   //끝나는 시간 계산

console.log('array+hashmap으로 계산', endTime2 - startTime2);

```
<br>

### 2-3 worth studying
공부해보고 추가한 or 추가할 내용입니다.
- search할 때마다 has, set, get을 반복하는 과정들에서 뭔가 개선할 순 없을까요?
<br>

## 결과
"set으로 계산",  77 <br>
"array+hashmap으로 계산",  107 <br>
"set으로 계산",  78 <br>
"array+hashmap으로 계산",  97 <br>
"set으로 계산",  83 <br>
"array+hashmap으로 계산",  102 <br>
"set으로 계산",  80 <br>
"array+hashmap으로 계산",  88 <br>
"set으로 계산",  80 <br>
"array+hashmap으로 계산",  90 <br>
"set으로 계산",  85 <br>
"array+hashmap으로 계산",  96 <br>
"set으로 계산",  82 <br>
"array+hashmap으로 계산",  93 <br>
"set으로 계산",  85 <br>
"array+hashmap으로 계산",  93 <br>
"set으로 계산",  82 <br>
"array+hashmap으로 계산",  90 <br>
"set으로 계산",  84 <br>
"array+hashmap으로 계산",  92 <br>
"set으로 계산",  83 <br>
"array+hashmap으로 계산",  91 <br>
"set으로 계산",  81 <br>

그렇게 큰 차이는 아니지만, 가장 작은 배열이 작으면 작을수록 set을 이용한 방법이 효과적일 것 같습니다. 더 공부한 후 추가할 내용이 있으면 추가해겠습니다^^






