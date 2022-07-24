---
layout: single
title: "[nest.js + next.js] 가계부 만들기 2 - ant Design 추가 및 기본 레이아웃"
categories : nest-next
tag : [nestjs, antd, nextjs]
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

## 1. antD installation

지난번에 NEXT.js를 실행했을 때 home만 나왔던 것에서 조금 업그레이드를 해보려고 합니다. 
먼저 antd를 적용해볼 건데요, ui/ux를 위한 프론트엔드 프레임워크입니다. 개발자에게 디자인과 퍼블리싱의 고통을 줄여주는 좋은 도구입니다^^ㅎㅎ

>$ yarn add antd

를 실행해서 설치해줍시다. 

## 2. App.css 생성
`pages`폴더 바로 아래에 App.css파일을 생성하고, 

>@import '~antd/dist/antd.css';

를 추가해줍니다. 간단하죠?

저는 제 취향대로(?) 색을 추가하기 위해 조금 더 추가했는데요, 귀찮으시면 아래를 통채로 복사하셔서 `App.css`에 붙여넣기 하셔도 됩니다.

``` css
@import '~antd/dist/antd.css';

.ant-menu-sub.ant-menu-inline {
    border: 0;
    background-color: rgb(243, 251, 158) !important;
  }

.ant-page-header-ghost {
    background-color: dodgerblue;
}  

.App-logo {
    height: 40vmin;
    pointer-events: none;
  }
  
  @media (prefers-reduced-motion: no-preference) {
    .App-logo {
      animation: App-logo-spin infinite 20s linear;
    }
  }
  
```


## 3. CustomLayout.tsx, HeaderComponent.tsx 생성
먼저 `pages`폴더 아래에 `common`이라는 폴더를 만들고, 그 안에 `components`라는 폴더를 만들어줍니다. 이 안에 `CustomLayout.tsx`와 `HeaderComponent.tsx`파일을 만들어줄 겁니다.


### 3-1. HeaderComponent.tsx 생성
애플리케이션의 헤더가 될 부분입니다. 나중에 로그인 기능을 구현할 것이기에, 애플리케이션 이름과 로그인 버튼을 만들었습니다.

``` javascript
import React, { Component } from 'react';
import { PageHeader, Button } from 'antd';

class HeaderComponent extends Component {
    constructor(props) {
        super(props)

        this.state = {

        }
    }
   
    render() {
        return (
            <div>
                <PageHeader
                    className="site-page-header"
                    title="AKKEO"
                    subTitle="nest-next practice by hamin"
                    extra={[
                    <>
                    <div
                        className="nav--login"
                    >
                        <Button key="1">
                        카카오로그인
                        </Button>
                    </div>
                    </>,
                ]}
                />
            </div>
        );
    }
}

export default HeaderComponent;
```

### 3-2. CustomLayout.tsx 생성
이 프로젝트는 기본적으로 header, sider, footer가 main을 감싼 구조가 될 것입니다. 여기서 레이아웃을 만들어두면, 앞으로는 메인 자리를 차지하는 components만 잘 만들면 됩니다. 


``` javascript
import HeaderComponent from './HeaderComponent';
import { Layout, Menu, Breadcrumb } from 'antd';
import { useRouter } from 'next/router';

const { Content, Sider, Footer } = Layout;

const items = [
  { label: 'save-record', key: 'save-record' }, // remember to pass the key prop
  {
    label: 'income/expense',
    key: 'sub1',

    children: [
      { label: 'daily', key: 'daily' },
      { label: 'weekly', key: 'weekly' },
      { label: 'monthly-yearly', key: 'monthly-yearly' },
    ],
  },
];

export default function CustomLayout({ children }) {
  const router = useRouter();
  const handleClick = (e) => {
    console.log(e.key);
    router.push({
      pathname: '/records/[id]',
      query: { id: e.key },
    });
  };

  return (
    <Layout style={{ minHeight: '100vh' }}>
      <Sider style={{ backgroundColor: 'rgb(255, 236, 22)' }}>
        <div className="App-logo" />
        <Menu
          defaultSelectedKeys={['1']}
          mode="inline"
          onClick={handleClick}
          items={items}
          style={{ backgroundColor: 'rgb(255, 236, 22)', color: 'black' }}
        />
      </Sider>
      <Layout>
        <HeaderComponent/>
        <Content style={{ margin: '0 16px' }}>
          <main>{children}</main>
        </Content>
        <Footer style={{ textAlign: 'center' }}>
          nest-next housekeeping book by hamin
        </Footer>
      </Layout>
    </Layout>
  );
}
```

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


## 4. _app.tsx, index.tsx 수정
이제 애플리케이션을 실행할때, 레이아웃이 자동으로 포함되도록 _app.tsx와 index.tsx를 수정할 차례입니다. 다음의 코드로 수정해주세요. 

>주의! `import './App.css';`는 반드시 `_app.tsx`에만 정의합니다!

### 4-1._app.tsx
``` javascript
import './App.css';
import CustomLayout from './common/components/CustomLayout'
import type { AppProps /*, AppContext */ } from 'next/app'


function App({ Component, pageProps }: AppProps) {
    return (
        <CustomLayout>
            <Component {...pageProps} />
        </CustomLayout>
    )
}

export default App;

```

### 4-2. index.tsx 수정
main content가 들어갈 부분입니다. 일단 어떻게 나오는지만 확인하기 위해 간단한 소개말만 적어보겠습니다.

index.tsx
``` javascript
import { FC } from 'react';
import { Typography } from 'antd';

const { Title } = Typography;

const Home: FC = () => {
  return (
    <div>
      <Title mark level={3}>
            가계부 'AKKEO'에 오신 것을 환영합니다.
      </Title>
    </div>
  );
};

export default Home;


```


이제 다시 실행해봅시다.
>$ yarn start:next


![Alt text](/assets/images/nest-next/20220723_233753.png)

이렇게 나오면 완성입니다!


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