---
layout: single
title: "[nest.js + next.js] 가계부 만들기 2 - ant Design 추가 및 기본 레이아웃"
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


지난번에 NEXT.js를 실행했을 때 home만 나왔던 것에서 조금 업그레이드를 해보려고 한다.
먼저 antd를 적용해볼 건데, ui/ux를 위한 프론트엔드 프레임워크이다.

>$ yarn add antd

를 실행하면 설치된다.

그리고 폴더정리를 좀 할것이다.

## 1. App.css 생성
client폴더에 utils폴더와 components폴더를 만든다. 그리고 utils폴더에 App.css파일을 추가한다. 

>@import '~antd/dist/antd.css';
를 추가해준다.

## 2. CustomLayout.tsx, HeaderComponent.tsx 생성
src/client/components에 만들면 된다. 기본적인 Layout을 커스터마이징 할것이다.

### 헤더 생성

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
                    title="가계부"
                    subTitle="nest-next practice by hamin"
                    extra={[
                        <Button key="2">Operation</Button>,
                        <Button key="1" type="primary">
                          Primary
                        </Button>,
                      ]}
                />
            </div>
        );
    }
}

export default HeaderComponent;
```

### 레이아웃을 만든다.
``` javascript
import HeaderComponent from './HeaderComponent';
import { Layout, Menu, Breadcrumb } from 'antd';

const { Content, Sider, Footer } = Layout;

const items = [
    { label: 'index', key: '1' }, // remember to pass the key prop
    {
      label: 'income/expense',
      key: 'sub1',
     children: [{ label: 'daily', key: '2' },
                 { label: 'weekly', key: '3' },
                 { label: 'monthly', key: '4' },
                 { label: 'yearly', key: '5' }],
    },
  ];

export default function CustomLayout({ children }) {
    return (
        <Layout style={{ minHeight: '100vh' }}>
        <Sider>
          <div className="App-logo" />
          <Menu theme="dark" defaultSelectedKeys={['1']} mode="inline" items={items}/>
        </Sider>
        <Layout>
          <HeaderComponent />
          <Content style={{ margin: '0 16px' }}>
            <Breadcrumb style={{ margin: '16px 0' }}>
              <Breadcrumb.Item>income/expense</Breadcrumb.Item>
              <Breadcrumb.Item>weekly</Breadcrumb.Item>
            </Breadcrumb>
            <main>{children}</main>
          </Content>
          <Footer style={{ textAlign: 'center' }}>
            nest-next housekeeping book by hamin
          </Footer>
        </Layout>
      </Layout>
    )
}
```

## 3. _app.tsx 수정
다음의 코드로 수정해준다.

``` javascript
import '../client/utils/App.css';
import CustomLayout from '../client/components/CustomLayout'
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


## 4. index.tsx 수정

main content가 들어가는 곳이다. 일단 어떻게 나오는지만 확인하기 위해 데이터를 하드코딩해보았다.

``` javascript
mport { FC } from 'react';
import { Table } from 'antd'

const Home: FC = () => {
    const dataSource = [{
        key: '1',
        type: '+',
        amount: 50000,
        description: '용돈 받았음'
    }, {
        key: '2',
        type: '-',
        amount: 30000,
        description: '도서 <죄와 벌> 구매'
    }, {
        key: '2',
        type: '-',
        amount: 10000,
        description: '햄버거'
    }, {
        key: '2',
        type: '-',
        amount: 30000,
        description: '가방 '
    }];
    const columns = [{
        title: 'Type',
        dataIndex: 'type',
        key: 'type',
    }, {
        title: 'Amount',
        dataIndex: 'amount',
        key: 'amount',
    }, {
        title: 'Description',
        dataIndex: 'description',
        key: 'description',
    }];
    return (
        <div>
            <Table dataSource={dataSource} columns={columns} />
        </div>
    )
};

export default Home;

```


이제 다시 실행해보자
>$ yarn start:next


![Alt text](/assets/images/20220526_152147289.png)

완성!
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