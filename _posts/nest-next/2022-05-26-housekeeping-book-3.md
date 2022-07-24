---
layout: single
title: "[nest.js + next.js] 가계부 만들기 3 - typeorm 설치와 MariaDB 연동"
categories : nest-next
tag : [nestjs, nextjs, typeorm, mariaDB]
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

## typeORM과 MariaDB
**ORM(Object Relational Mapping)**은 객체와 관계형 데이터베이스를 매핑해주는 도구입니다. ORM을 사용하면, 우리는 테이블을 하나하나 만들지 않아도 되고, 쿼리 작성시에 보안상의 이점도 생깁니다. 
<br>

**typeORM**은 node환경에서 실행되고 TypeScript로 작성된 객체 관계형 매퍼 라이브러리입니다.
MySQL, PostgreSQL, MariaDB, SQLite 등 여러 데이터베이스를 지원합니다.

이 프로젝트에서 우리는 typeORM을 다뤄볼 것이고, 연동할 데이터베이스는 원하신 것을 고르시면 됩니다. 저는 **MariaDB**를 사용하기로 결정하였습니다.

## 1. MariaDB에 데이터베이스 만들고, vscode에 세팅하기.
일단 database를 만들어줍니다. MySQL Client (MariaDB 10.7)을 실행하여, 아래의 명령어를 작성해주세요. 

>MariaDB [(none)]> create database housekeeping_book;

database가 이제 만들어졌습니다. vscode에서 DB를 쉽게 확인하기 위해, 아래처럼 connect to server에 연결해줍니다. 데이터베이스를 확인하는 다른 툴(ex.heidiSQL)을 이미 사용하고 계시다면 건너뛰셔도 됩니다. 
<br>

![Alt text](/assets/images/20220526_175332299.png)


## 2. Typeorm설치하기
이제 typeorm을 설치할 차례입니다. 프로젝트 터미널로 가서 아래의 명령어를 실행해봅시다. 

>$yarn add mysql typeorm @nestjs/typeorm


## 3. entity생성
먼저 `src>entities` 폴더를 만들어줍니다. 앞으로 entity들은 여기서 관리해줄 생각입니다. entity.ts에 작성된 코드를 토대로 테이블이 만들어질 것입니다.

### 3-1. user 테이블
현재는 카카오로그인만 구현할 계획입니다. 따라서 칼럼은 
1. uid (유저 아이디, primary key)
2. kakaoUid (카카오에서 제공하는 아이디)
3. email (이메일)
4. nickname (닉네임)

정도로 정했습니다. 

<br>

`src>entities`에 `user.entity.ts`파일을 만들고 아래의 코드를 넣어주세요. 에러가 생길텐데요, 아직 record entity를 만들지 않아서 그렇습니다. 아래에서 바로 만들어 줄 거에요.

```typescript
import {
  BaseEntity,
  Column,
  Entity,
  OneToMany,
  PrimaryGeneratedColumn,
} from 'typeorm';
import { Record } from './record.entity';

@Entity()
export class User extends BaseEntity {
  @PrimaryGeneratedColumn()
  uid: number;

  @Column({ unique: true })
  kakaoId: string;

  @Column({ unique: true })
  email: string;

  @Column()
  nickname: string;

  @Column()
  password: string;

  @OneToMany(() => Record, (record) => record.uid)
  records: Record[];                         // 한 유저당 여러개의 record 생성
}

```

### 3-2. record 테이블
수입/지출 내역을 기록할 테이블은 record입니다. 

칼럼은 
1. no 
2. createdDate
3. type(income/expense/social_income/social_expense)
- INCOME 수입
- EXPENSE 지출
- SOCIALINCOME 경조사 수입
- SOCIALEXPENSE 경조사 지출
4. amount
5. remark(비고)
6. uid(유저아이디)

입니다. 경조사는 나중에 따로 조회해보고 싶은 일이 생기지 않을까 해서, 따로 분류를 해봤습니다ㅎㅎ

entity파일을 만들기 전에, type칼럼의 enum을 따로 만들기로 했습니다. 


먼저 `src>enum`폴더를 만들고, `record-type.enum.ts`를 만들어 줍시다.

record-type.enum.ts
```typescript
export enum RecordType {
    INCOME = 'INCOME',
    EXPENSE = 'EXPENSE',
    SOCIALINCOME = 'SOCIALINCOME',
    SOCIALEXPENSE = 'SOCIALEXPENSE'
  }
```
<br>
<br>

그리고 `src>entities`에 `record.entity.ts`파일을 만들고 아래의 코드를 넣어주세요.

record.entity.ts
```typescript
import {
  BaseEntity,
  Column,
  Entity,
  PrimaryGeneratedColumn,
} from 'typeorm';
import { RecordType } from '../enum/record-type.enum';

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

  @Column()
  uid: number;
}

```
<br>

* * *
광고
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-6596953683217931"
     crossorigin="anonymous"></script>
<ins class="adsbygoogle"
     style="display:block"
     data-ad-format="fluid"
     data-ad-layout-key="-i5+5+1+2-3"
     data-ad-client="ca-pub-6596953683217931"
     data-ad-slot="2948544388"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>
* * *

<br>
## 4. app.module.ts 수정
`src > app.module.ts` 파일을 아래와 같이 수정해줍니다.

```typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { TypeOrmModule } from '@nestjs/typeorm';
import { User } from './entities/user.entity';
import { Record } from './entities/record.entity';

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'mysql',
      port: 3306,
      username: 'root',
      password: 'qwer',
      database: 'housekeeping_book',
      entities: [User, Record],
      synchronize: true,
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}

```
<br>

DB를 연동하는 방법에는 여러가지가 있습니다. 더 자세한 내용은 [nestjs 공식 문서 참조(클릭)](https://docs.nestjs.com/techniques/database/) 에서 보실수 있습니다.

<br>
>$ yarn start:dev

실행하면 테이블이 만들어집니다. 고생하셨습니다!

<br>
![Alt text](/assets/images/nest-next/20220724_022905.png)

<br>
끝!

<br>
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-6596953683217931"
     crossorigin="anonymous"></script>
<ins class="adsbygoogle"
     style="display:block"
     data-ad-format="fluid"
     data-ad-layout-key="-i5+5+1+2-3"
     data-ad-client="ca-pub-6596953683217931"
     data-ad-slot="2948544388"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>
