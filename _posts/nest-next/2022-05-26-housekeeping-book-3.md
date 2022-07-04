---
layout: single
title: "[nest.js + next.js] 가계부 만들기 3 - typeorm 설치와 MariaDB 연동"
categories : nest-next
tag : [nestjs, node.js, nextjs]
author_profile: true
sidebar_main: true
toc: true
toc_label: Index
toc_sticky: true
---

## 순서대로 보기
nest.js + next.js 가계부 만들기                                            
- [1. nest.js + next.js installation](https://iamhmin.github.io/nest-next/housekeeping-book-1/) 
- [2. antd 설치 및 레이아웃 커스터마이징 ](https://iamhmin.github.io/nest-next/housekeeping-book-2/)       
- [3. typeorm 설치와 mariaDB(mysql) 연동 ](https://iamhmin.github.io/nest-next/housekeeping-book-3/) 
- [4. swagger 설치와 간단한 crud api 개발 ](https://iamhmin.github.io/nest-next/housekeeping-book-4/)       


이번엔 typeORM설치를 통한 MariaDB연동을 하고, 테이블을 하나 만들어볼 것이다.

## 1. MariaDB에 데이터베이스 만들고, vscode에 세팅하기.

DB는 마리아디비를 쓰기로 했다. 컴퓨터에 설치되어있었기 때문에^^; 

>MariaDB [(none)]> create database housekeeping_book;

database를 만들었다. vscode에서 쉽게 확인하기 위해, 아래처럼 connect to server에 연결했다.

![Alt text](/assets/images/20220526_175332299.png)


## 2. Typeorm설치하기
이제 typeorm을 설치할 차례이다. 프로젝트 터미널로 가서 아래를 실행하여 typeorm을 설치하였다.

>$yarn add mysql typeorm @nestjs/typeorm


### typeORM이란?
node.js에서 실행되고 TypeScript로 작성된 객체 관계형매퍼 라이브러리이다.
MySQL, PostgreSQL, MariaDB, SQLite 등 여러 데이터베이스를 지원한다.

java에서 jpa를 사용했고, python에서 sqlalchemy를 사용해본 적이 있는데, 비슷하다고 생각하면 될 것 같다. 테이블을 자동으로 생성해준다는 장점이 있다.


## 3. ormconfig.json생성
프로젝트 root폴더에 ormconfig.json을 만들고 다음과 같이 작성한다.


```json
{
  "type": "mysql",
  "host": "localhost",
  "port": 3306,
  "username": "root",
  "password": "qwer",
  "database": "housekeeping_book",
  "entities": ["dist/**/*.entity{.ts,.js}"],
  "synchronize": true
}
```
그리고 src/server에 있는 app.module.ts파일을 아래와 같이 수정해준다.

```
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [TypeOrmModule.forRoot()],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}

```

참고로 이전엔 config.ts파일을 따로 만들어서 했었는데, nest공식 문서에 위의 방법을 추천해서 고쳤다.
[nestjs 공식 문서 참조](https://docs.nestjs.com/techniques/database/) 


## 4. entity를 이용하여 테이블 만들기.
간단하게 테이블 이름은 record로 하려고 한다.
칼럼은 no, 날짜, type(income/expense), amount, remarks(비고) 정도로 만들 것이다.

type은 income 아니면 expense밖에 없으므로 enum을 따로 만들기로 했다.
먼저 src/server에 records폴더를 만든다. 그리고 폴더 안에 record-type.enum.ts와, record.entity.ts를 만들 것이다. 소스는 각각 다음과 같다. 


>record-type.enum.ts

```
export enum RecordType {
    INCOME = 'INCOME',
    EXPENSE = 'EXPENSE'
}
```

>record.entity.ts

```
import { BaseEntity, Column, Entity, PrimaryGeneratedColumn } from "typeorm";
import { RecordType } from "./record-type.enum";

@Entity()
export class Record extends BaseEntity {
    @PrimaryGeneratedColumn()
    no: number;

    @Column()
    createdDate: Date;

    @Column()
    type: RecordType;

    @Column()
    amount: number;

    @Column()
    remark: string;
}
```


>$ yarn start:dev
실행하면 테이블이 만들어진다.


![Alt text](/assets/images/20220526_180447015.png)

끝!

