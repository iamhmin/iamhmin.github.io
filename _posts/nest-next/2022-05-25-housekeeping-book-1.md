---
layout: single
title: "[nest.js + next.js] 가계부 만들기 1 - nest.js + next.js설치"
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

## 초기세팅

[여기](https://dev.to/yakovlev_alexey/creating-a-project-with-nestjs-nextjs-3i1i)를 참고하여 세팅하고 있다. 위의 블로그에서는 이름 구별을 하기 위해 nest.js는 그대로 쓰고, next.js는 대문자로 NEXT.js라고 써서 나도 그렇게 쓰려고 한다.

### nest.js installation

아래의 명령어를 터미널에 작성한다.
>$ npx @nestjs/cli new housekeeping-book

나는 가계부를 만들어볼 것이기 때문에 housekeeping-book라고 작명했다. 여기까지 하면 알아서 폴더들이 만들어진다. 

다시 터미널을 키고

>$ yarn start:dev

를 입력한다.

나는 여기서 yarn 오류가 났었는데 
[여기](https://stackoverflow.com/questions/46013544/yarn-install-command-error-no-such-file-or-directory-install)를 보고 수정했다.

![Alt text](/assets/images/20220526_105305801.png)

오래걸려서 깜짝 놀랐다. 아무튼 위와 같이 실행되고,
브라우저에 localhost:3000을 입력하면 아래와 같이 나온다. 

![Alt text](/assets/images/20220525_185221035.png)

완성!

### NEXT.js installation

next와 react, react-dom을 설치해준다.

>$ yarn add next react react-dom
$yarn add -D @types/react @types/react-dom eslint-config-next

그 다음에 

>$yarn next dev 

로 NEXT.js를 실행한다. 실행하고 나면 next-env.d.ts라는 파일이 새로 생긴다.


![Alt text](/assets/images/20220526_113931429.png)

폴더구조를 위와 같이 살짝 변경하였다. 그리고 tsconfig.build.json을 tsconfig.server.json으로 바꿨다. nest.js만을 위한 config파일을 만들기 위함이다. 그리고 내용을 이렇게 변경해준다.

```json
// ./tsconfig.server.json
{
    "extends": "./tsconfig.json",
    "compilerOptions": {
        "noEmit": false
    },
    "include": [
        "./src/server/**/*.ts",
        "./src/shared/**/*.ts",
        "./@types/**/*.d.ts"
    ]
}
```

package.json도 변경해준다
```json
// ./package.json
"scripts": {
    "prebuild": "rimraf dist",
    "build": "yarn build:next && yarn build:nest",
    "build:next": "next build",
    "build:nest": "nest build --path ./tsconfig.server.json",
    "start": "node ./dist/server/main.js",
    "start:next": "next dev",
    "start:dev": "nest start --path ./tsconfig.server.json --watch",
    "start:debug": "nest start --path ./tsconfig.server.json --debug --watch",
    "start:prod": "node dist/main",
    // ... lint/format/test etc
},
```

이제 frontend기본 구성을 할 차례이다.
인덱스 파일과 app 컴포넌트를 src/pages에 저장해준다. 소스는 다음과 같다.

```
// ./src/pages/_app.tsx
import type { AppProps /*, AppContext */ } from 'next/app'


function App({ Component, pageProps }: AppProps) {
   return <Component {...pageProps} />;
}

export default App;


```

```
// ./src/pages/_document.tsx
import Document, { Html, Head, Main, NextScript } from "next/document";

export default class MyDocument extends Document {
    render() {
        return (
            <Html>
                <Head>
                </Head>
                <body>
                <Main />
                </body>
                <NextScript />
            </Html>
        );
    }
}
```



```
// ./src/pages/index.tsx
import { FC } from 'react';

const Home: FC = () => {
    return <h1>Home</h1>;
};

export default Home;
```

그리고 gitignore에 
```
# compiled output
/dist
/node_modules
/.next
```
이렇게 next를 추가해주는 것도 잊지 말자.


이제 
>$ yarn start:next
를 실행해주면

![Alt text](/assets/images/20220526_115145957.png)


이렇게 3000포트 위로 NEXT가 뜬다. 
next 설정까지 완성! 
일단 여기까지는 너무 재밌다ㅎㅎ~~
