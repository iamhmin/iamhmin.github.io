---
layout: single
title: "[nest.js + next.js] 가계부 만들기 5 - nest-next 패키지로 서버 통합하기"
categories : nest-next
tag : [nestjs, node.js, nextjs]
author_profile: true
sidebar_main: true
toc: true
toc_label: Index
toc_sticky: true
---
## RELATED POSTS  
nest.js + next.js 가계부 만들기                                            
- [1. nest.js + next.js installation](https://iamhmin.github.io/nest-next/housekeeping-book-1/) 
- [2. antd 설치 및 레이아웃 커스터마이징 ](https://iamhmin.github.io/nest-next/housekeeping-book-2/)       
- [3. typeorm 설치와 mariaDB(mysql) 연동 ](https://iamhmin.github.io/nest-next/housekeeping-book-3/) 
- [4. swagger 설치와 간단한 crud api 개발 ](https://iamhmin.github.io/nest-next/housekeeping-book-4/)  
- [5. nest-next 패키지로 서버 통합하기 ](https://iamhmin.github.io/nest-next/housekeeping-book-5/)  

처음에 참고했던 [여기](https://dev.to/yakovlev_alexey/creating-a-project-with-nestjs-nextjs-3i1i)에서 건너뛴 부분이 있었는데, nest-next 패키지 설치였다. 현재는 nest와 NEXT의 서버를 따로 사용하고 있는데, 하나의 서버만을 사용하기 위해 패키지 설치를 결정하였다. 생각보다 무척 간단하다.

## 1. nest-next package 설치
먼저 설치를 위해 아래를 터미널에서 실행해보자.

>$ yarn add nest-next

## 2. app.module.ts 수정
nest-next의 렌더모듈을 추가해주었다.


```
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { RenderModule } from 'nest-next';
import Next from 'next';
import { AppService } from './app.service';
import { TypeOrmModule } from '@nestjs/typeorm';
import { RecordsModule } from './records/records.module';

@Module({
  imports: [TypeOrmModule.forRoot(), 
    RecordsModule,
    RenderModule.forRootAsync(Next({})),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}

```


## 3. app.controller.ts 수정



```
// ./src/server/app.controller.ts
import { Controller, Get, Render } from '@nestjs/common';
import { AppService } from './app.service';

@Controller()
export class AppController {
    constructor(private readonly appService: AppService) {}

    @Get()
    @Render('index')
    home() {
        return {};
    }
}
```

여기까지 하고 실행해보자

>$ yarn start:dev

이제 http://localhost:3000/ 서버가 통합되었다.


끝!



