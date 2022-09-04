---
layout: single
title: "[Typescript] 2. 문자열 - string - 관련 문법"
categories : typescript
tag : [string, basic, 타입스크립트, 문법]
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

### 문자열의 길이 : length

```typescript
let str:string = 'test_string';
console.log(str.length) //11
```

### 문자열 대체하기 : replace

```typescript
let str:string = 'test_string';
let new_str:string = str.replace('_', ':');
console.log(new_str) //"test:string"
```

### 문자열 분리하기 (구분자로 분리하여 자르기) : split

```typescript
let str:string = 'test_string';
let parts:string[] = str.split('_');
console.log('parts[0]', parts[0]); // "parts[0]",  "test" 
console.log('parts[1]', parts[1]); // "parts[1]",  "string" 
```

### 문자열 합치기 : concat

```typescript
let concat_string:string = str.concat('_concat');
console.log(concat_string) //"test_string_concat" 
```
