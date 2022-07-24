---
layout: single
title: "[nest.js + next.js] 가계부 만들기 4 - swagger설치와 crud rest api개발"
categories : nest-next
tag : [nestjs, nextjs, swagger, rest api]
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


## 1. swagger 설치
### 1-1. swagger란?
이번엔 몇가지 간단한 api를 만들어보려고 합니다. 이때 유용한 tool이 swagger인데요, swagger를 사용하면 controller에 만들어진 api의 기능을 확인하고, 쉽게 문서화할 수 있습니다. 

### 1-2. 설치
[ nestjs 공식문서 ](https://docs.nestjs.com/openapi/introduction#setup-options/)를 참고하여 설치하였습니다. 

>$ yarn add @nestjs/swagger swagger-ui-express

그리고 main.ts를 다음과 같이 수정해줍니다.

```typescript
import { NestFactory } from '@nestjs/core';
import { SwaggerModule, DocumentBuilder } from '@nestjs/swagger';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  const config = new DocumentBuilder()
    .setTitle('housekeeping-book')
    .setDescription('nestjs + nextjs project by hamin')
    .setVersion('1.0')
    .build();
  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup('api', app, document);

  await app.listen(3000);
}
bootstrap();

```

>$ yarn start:dev

을 실행한 후 http://localhost:포트번호/api로 브라우저에 접속하면 이런 화면이 뜨게됩니다.

![Alt text](/assets/images/20220527_113127956.png)

자, 이제 swagger설치가 끝났습니다. 우리는 api를 만들어서 제대로 동작하는지 확인만 하면 됩니다.



## 2. API 만들기
### 2-1. nest.js와 module
이제 API를 만들 차례입니다. 그전에, nest.js가 어떻게 돌아가는지 살짝 짚고 넘어가는 것이 좋을 것 같습니다. nest는 **모듈** 기반의 프레임워크입니다.

![Alt text](/assets/images/nest-next/20220724_025402.png)

위의 이미지처럼, 각 모듈은 관련있는 모듈로 묶여지고, 마지막에 app.module에 등록을 마쳐야 합니다. 

예를 들어서, 이 프로젝트에서 저는 user와 record를 다룹니다.
user와 관련된 api를 호출할 users.controller가 필요할 것이고, 비즈니스 로직을 구현할 users.service가 필요할 것입니다. 이 두가지를 일차적으로 users.module에 등록합니다. 그리고 이 users.module을 app.module에 등록해야 정상적으로 동작하게 됩니다.

[ nest 공식문서(클릭) ](https://docs.nestjs.com/modules)에 잘 설명이 되어있으니 참고하시면 좋을 것 같습니다. 

참고로, 아래와 같은 구조가 가독성이 좋은 것 같습니다. (이미지 출처 - nest공식문서)

![Alt text](/assets/images/nest-next/20220724_030948.png)



### 2-2. create-record.dto.ts 생성
`src>records>dto`폴더 생성 후 `create-record.dto.ts`파일을 만들어줍니다. front단에서 받아올 정보를 담을 그릇이라고 생각하면 됩니다. 

```typescript
import { RecordType } from "../../enum/record-type.enum";

export class CreateRecordDto {
   type: RecordType;
   amount: number;
   remark: string;
   createdDate: Date;
   uid: number;
  }
```

### 2-3. records.service.ts 생성
`src>records`안에 `records.service.ts`파일을 만들고, 아래의 코드를 작성해주세요.

```typescript
import { Injectable } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { Record } from '../entities/record.entity';
import { CreateRecordDto } from './dto/create-record.dto';

@Injectable()
export class RecordsService {
  constructor(
    @InjectRepository(Record)
    private recordsRepository: Repository<Record>,
  ) {}

  create(createRecordDto: CreateRecordDto): Promise<Record> {
    const record = new Record();
    record.remark = createRecordDto.remark;
    record.amount = createRecordDto.amount;
    record.type = createRecordDto.type;
    record.createdDate = createRecordDto.createdDate;
    record.uid = createRecordDto.uid;

    return this.recordsRepository.save(record);
  }
  async findAll(): Promise<Record[]> {
    return this.recordsRepository.find();
  }

  async remove(no: string): Promise<void> {
    await this.recordsRepository.delete(no);
  }
}
```

<br>

1. create는 레코드를 생성하기 위한 메소드이고, 
2. findAll은 모든 레코드를 return하는 메소드, 
3. remove는 기본키인 no를 사용하여 레코드를 삭제하기 위한 메소드입니다.
<br>

### 2-4. records.controller.ts 생성
`src>records`안에 `records.controller.ts`파일을 만들고, 아래의 코드를 작성해주세요. 

```typescript
import { Body, Controller, Delete, Get, Param, Post } from '@nestjs/common';
import { CreateRecordDto } from './dto/create-record.dto';
import { Record } from '../entities/record.entity';
import { RecordsService } from './records.service';

@Controller('records')
export class RecordsController {
  constructor(private readonly recordsService: RecordsService) {}

  @Post()
  create(@Body() createRecordDto: CreateRecordDto): Promise<Record> {
    return this.recordsService.create(createRecordDto);
  }

  @Get()
  findAll(): Promise<Record[]> {
    return this.recordsService.findAll();
  }

  @Delete(':no')
  remove(@Param('no') no: string): Promise<void> {
    return this.recordsService.remove(no);
  }
}
```

### 2-5. records.module.ts 생성

```typescript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { RecordsService } from './records.service';
import { RecordsController } from './records.controller';
import { Record } from '../entities/record.entity';

@Module({
  imports: [TypeOrmModule.forFeature([Record])],
  providers: [RecordsService],
  controllers: [RecordsController],
})
export class RecordsModule {}
```

### 2-6. app.module.ts 에 record 모듈 추가

```typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { TypeOrmModule } from '@nestjs/typeorm';
import { User } from './entities/user.entity';
import { Record } from './entities/record.entity';
import { RecordsModule } from './records/records.module';

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
    RecordsModule    //추가되었음
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

여기까지 하면 기본적인 api는 완성되었습니다! 이제 swagger로 테스트만 해보면 됩니다.

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

## 3. 실행

>$ yarn start:dev

실행후 다시 localhost:3000/api로 들어가면 다음과 같은 화면이 나옵니다. 

![Alt text](/assets/images/20220527_122853886.png)

<br>
이제 api를 통해서 record를 하나 생성해보려고 합니다. 실행 전에, 유저 테이블에 임의의 값 하나를 넣어줍니다. uid로 인한 외래키 오류를 방지하기 위함입니다.
전 그냥 아래와 같이 넣었습니다.

![Alt text](/assets/images/nest-next/20220724_035822.png)

<br>

아래처럼 데이터를 작성후 execute를 누르면

![Alt text](/assets/images/nest-next/20220724_035919.png)

<br>
record 테이블에 데이터가 새로 생성된 걸 볼 수 있습니다.

![Alt text](/assets/images/nest-next/20220724_040019.png)

마찬가지로 모든 records를 조회하는 api와 삭제 api도 잘 동작합니다. 

수고하셨습니다^^


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
