---
layout: single
title: "[nest.js + next.js] 가계부 만들기 4 - swagger설치와 crud rest api개발"
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

이번엔 모든 수입/지출 record를 return해주는 api를 만들어볼 것이다. 
그리고 만들어진 api를 swagger를 설치해서 확인해보려고 한다. 
먼저 swagger부터 설치해보려고 한다.

## swagger 설치

[ nestjs 공식문서 ](https://docs.nestjs.com/openapi/introduction#setup-options/)를 참고하여 설치하였다.

>$ yarn add @nestjs/swagger swagger-ui-express

그리고 main.ts를 다음과 같이 수정한다.

```
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

을 실행한 후 http://localhost:포트번호/api로 브라우저에 접속하면 이런 화면이 뜰 것이다.

![Alt text](/assets/images/20220527_113127956.png)



## API 만들기
이제 API를 만들 차례이다. 새로 만들 파일이 총 4가지이고, 수정할 파일이 하나 있다.
아래에 분홍색으로 표시한 create-record.dto.ts, records.controller.ts, records.module.ts, records.service.ts는 새로 생성해주고, src/server 폴더에 app.module.ts에 records.module.ts를 추가해주어야 한다.

![Alt text](/assets/images/20220527_120547492.png)

### 1. src/server/records/dto 폴더 생성 후 create-record.dto.ts 생성
front단에서 받아올 정보를 담을 그릇이라고 생각하면 된다. 

```
import { RecordType } from "../record-type.enum";

export class CreateRecordDto {
   type: RecordType;
   amount: number;
   remark: string;
  }
```

no값은 자동으로 증가되고, createdDate는 생성날짜이기 때문에 위의 세가지만 작성하였다.

### 2. records.service.ts 생성

```
import { Injectable } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { Record } from './record.entity';
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
    record.createdDate = new Date();

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

create는 레코드를 생성하기 위한 메소드이고, findAll은 모든 레코드 return, remove는 기본키인 no를 사용하여 레코드를 삭제하기 위한 메소드이다.


### 3. records.controller.ts 생성
endpoint가 될 controller를 작성한다.

```
import { Body, Controller, Delete, Get, Param, Post } from '@nestjs/common';
import { CreateRecordDto } from './dto/create-record.dto';
import { Record } from './record.entity';
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

### 4. records.module.ts 생성

```
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { RecordsService } from './records.service';
import { RecordsController } from './records.controller';
import { Record } from './record.entity';

@Module({
  imports: [TypeOrmModule.forFeature([Record])],
  providers: [RecordsService],
  controllers: [RecordsController],
})
export class RecordsModule {}
```

### 5. app.module.ts 에 record 모듈 추가

```
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { TypeOrmModule } from '@nestjs/typeorm';
import { RecordsModule } from './records/records.module';

@Module({
  imports: [TypeOrmModule.forRoot(), 
    RecordsModule,
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}

```

여기까지 하면 기본적인 api는 완성되었다! 이제 swagger로 테스트를 해보면 된다!

## 실행

실행후 다시 localhost:3000/api로 들어가면 다음과 같이 뜬다.

![Alt text](/assets/images/20220527_122853886.png)

아래와 같이 데이터를 하나 저장해 두었었는데, api를 통해서 record를 하나 생성해보려고 한다.

![Alt text](/assets/images/20220527_122315233.png)

아래처럼 데이터를 작성후 execute를 누르면
![Alt text](/assets/images/20220527_123004171.png)

데이터가 새로 생성된 걸 볼 수 있다.
![Alt text](/assets/images/20220527_122516149.png)

마찬가지로 모든 records를 조회하는 api와 삭제 api도 잘 동작한다.

![Alt text](/assets/images/20220527_123711381.png)


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
