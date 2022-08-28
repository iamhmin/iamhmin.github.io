---
layout: single
title: "[Typescript] 1. 변수 선언 - Variable Declaration - (var, let, const)"
categories : typescript
tag : [var, let, const, 변수]
author_profile: true
sidebar_main: true
toc: true
toc_label: Index
toc_sticky: true
---
## RELATED POSTS
[Typescript] 포스트 모음
- [1. 변수 선언 (var, let, const)](https://iamhmin.github.io/typescript/typescript-1/) 
- [2. 문자열 - string - 관련 문법](https://iamhmin.github.io/typescript/typescript-2/) 
- [3. 배열 - array - 관련 문법](https://iamhmin.github.io/typescript/typescript-3/) 

## 1. typescirpt
typescript는 javascript의 상위 집합 언어입니다. javascript의 장점이자 단점이 변수 혹은 객체의 타입을 지정하지 않아 유연하다는 것인데, 이것이 프로젝트의 규모가 커짐에 따라 오류 발생의 가능성을 높입니다. 이것을 보완하기 위한 것이 정적 타입 검사를 제공하는 typescript 입니다.

<br>

ES6은 var변수 선언의 단점을 해결하기 위해 새롭게 const와 let을 javascript에 도입했습니다. typescript는 javascript의 상위집합 언어이므로 **var, const, let**을 똑같이 사용합니다.

<br>

이 포스트에서는 typescript의 변수 선언방식을 알아보고, 왜 let과 const가 var보다 선호되는 지를 살펴보겠습니다.

<br>

### 1-1. var vs (let, const)
var가 let, const와 다른 점은, var는 let과 const는 block-scope를 가지고 있다는 것입니다. 블록 안에서 변수가 생성되고 소멸됩니다. 반면에 var는 아래와 같은 코드에서도 에러없이 컴파일 됩니다.

<br>

```typescript
var num:number = 1; 
console.log(str); //undifined

if (num = 1 ) { 
    var str: string = "일";
} 

console.log(num);  // 1
console.log(str);  // "일"
```

<br>

str이 블록 안에서 변수선언이 이루어졌음에도 불구하고, 상단에 있는 코드에서도 undifined로 출력됩니다.

<br>


```typescript
let num:number = 1; 
console.log(str); 

if (num = 1 ) { 
    let str: string = "일";
} 

console.log(num);  
console.log(str);  
```

<br>

이렇게 let으로 변수선언을 한 경우에는 아래와 같은 에러가 발생합니다. 
error: Can't find variable: str

<br>

var로 선언하는 경우 이렇게 함수의 전역범위, 함수범위에서 처음부터 참조할 수 있기 때문에, 예상치 못한 버그를 발생시킬 수 있습니다.

<br>

다른 예를 하나 더 들어보겠습니다.

<br>

```typescript
function testScope(numberArray: number[]) {
     var sum = 0;
     for (var i = 0; i < numberArray.length; i++) {
        sum += numberArray[i];
        var count = numberArray.length;
     }
     console.log(count); //3 출력
     return sum
}
var result = testScope([100,105,95]);
console.log(result);
```

<br>

count가 for반복문 안에서 선언되었음에도 불구하고, for문 밖에서 참조가 가능합니다.

<br>


```typescript
 function testScope(numberArray: number[]) {
     var sum = 0;
     for (var i = 0; i < numberArray.length; i++) {
        sum += numberArray[i];
        let count = numberArray.length;
     }
     console.log(count); //3 출력
     return sum
}
var result = testScope([100,105,95]);
console.log(result);
```

<br>

반면 let으로 변수선언을 하는 경우에는, error: Can't find variable: count 컴파일 에러가 발생합니다. 이렇게 var대신 let혹은 const로 선언함으로써 코드 작성의 실수를 방지할 수 있습니다.

<br>

### 1-2. let vs const
const는 let의 기능이 강화된 것으로 변수에 재할당을 방지합니다.
간단하게 비교해보겠습니다.

<br>

```typescript

let a = 100;
console.log(a); //100
a = 200;
console.log(a); //200

const b = 100;
console.log(b); //100
b = 200;
console.log(b); //컴파일 에러 

```

<br>

const는 변수 재할당이 불가능합니다.

다만, 아래와 같이 객체의 속성을 변경하는 것은 가능합니다.

<br>

```typescript
const fruits = { 
    apple : 20, 
    banana : 30, 
}; 
fruits.banana = 50; 
console.log(fruits)
```
<br>

### 2. 결론
오류를 방지하기 위해, 가급적이면 let과 const를 사용합니다.
변하지 않는 값을 할당할 때에는 const를 사용하여 코드를 작성하는 것이 안전합니다.



## 참고
[[typescriptlang.org]](https://www.typescriptlang.org/docs/handbook/variable-declarations.html) 




