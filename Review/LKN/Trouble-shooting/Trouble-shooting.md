# Trouble Shooting (LKN)

<br>

## **_npx create-react-app 명령어 404 Error 발생_**

- `npx create-react-app 생성할_프로젝트_폴더명` 명령어 실행 시 404 Error 발생
- **Error message** <br> `npm ERR! 404 Not Found - GET https://registry.npmjs.org/creat-react-app - Not found`

<br>

#### **해결 방법 1.**

- `npm config set registry http://registry.npmjs.org` 를 실행

#### **해결 방법 2.**

- npm 으로 create-react-app 실행
  - `npm install -g create-react-app` 으로 cra 설치
  - `create-react-app 생성할_프로젝트_폴더명` 으로 생성할 프로젝트 폴더에서 cra 실행

<br>

---

<br>

## **_npm install package.json 이슈_**

- 팀원과 개발환경을 공유하기 위해 package.json을 pull 받아서 node_modules 생성
- `npm install package.json` 실행 이후 package.json과 package-lock.json 파일 변경됨
- 변경된 파일 commit 이전에는 다른 branch 넘어갈 수 없음

<br>

- package.json에 버전이 'xxx':^1.0.0 으로 기재된 경우 --> 1.x.x 에 해당하는 최신 버전 설치
- npm 버전이 상이하고 시점이 다를 경우 정확하게 동일한 개발환경이 구축되지 않을 수 있음
- dependencies 버전을 'xxx':1.0.0 으로 고정해도 xxx와 의존관계가 있는 yyy가 ^1.0.0 으로 작성 시 다른 버전 설치 가능성 존재
- `npm install` 사용 시 package.json, package-lock.json 덮어씀

<br>

#### **해결 방법 0.**

- `git stash`로 package 변경사항을 임시저장하여 이전 단계로 돌려놓기
- `git stash drop`으로 저장된 변경사항 제거
- 혹은 파일의 stage, commit, pull 여부에 따라 `git reset ~~` 사용
- 이후 해결 방법 1 (2,3은 비현실적) 사용

#### **해결 방법 1.**

- `npm ci package-lock.json` 으로 node_modules 디렉토리 구축
  - `npm ci` 는 `npm install` 처럼 package-lock.json을 갱신하지 않음
  - 같은 node_modules 구성 가능
  - package.json, package-lock.json이 변경되지 않음

#### **해결 방법 2.**

- package.json 의 dependencies version을 ^ 없이 고정
- 번거롭고 효율성 떨어짐

#### **해결 방법 3.**

- node_modules를 git으로 공유
- 디렉토리가 너무 무겁기 때문에 효율성 떨어짐

<br>

---

<br>

## **_Prop 'className' did not match Error_**

- `Next js`, `Material UI`로 작성한 페이지 새로고침 시 style 적용 안되는 오류 발생
- **Error Meassage**(브라우저 개발자 도구)
  <br>
  `Warning: Prop `className` did not match. Server: "sc-xxxxxx xxxxx" Client: "sc-yyyyyy yyyyy"`

<br>

- 문제의 원인은 **babel-plugin-styled-components**가 없기 때문
- Babel 플러그인은 환경과 상관없이 일관된 className을 생성<br>(consistently hashed component classNames between environments)
- styled-components는 styled 함수로 만든 컴포넌트마다 generateId 함수를 이용해 유일한 식별자를 생성
- **컴포넌트가 생성되는 순서에 따라 같은 컴포넌트이더라도 다른 식별자가 붙을 수 있음**
  - SSR과 CSR을 같이 활용 시 서버와 클라이언트가 컴포넌트를 생성하는 순서에 따라 식별자가 달라질 수 있음
- **babel-plugin-styled-components**는 이런 식별자 생성 과정을 **정규화**

<br>

#### **해결 방법**

- 플러그인 설치<br> `npm i --save-dev babel-plugin-styled-components`
- 프로젝트 루트의 **.babelrc**에 코드 추가 <br>`"plugins": ["babel-plugin-styled-components"]`
- **page/\_document.js** 파일 생성 후 다음 코드 삽입<br>

```js
import React from 'react';
import Document, {
  DocumentContext,
  Head,
  Html,
  Main,
  NextScript,
} from 'next/document';
import { ServerStyleSheets } from '@material-ui/styles';

class AppDocument extends Document {
  static async getInitialProps(ctx: DocumentContext) {
    const sheet = new ServerStyleSheets();
    const originalRenderPage = ctx.renderPage;

    ctx.renderPage = () =>
      originalRenderPage({
        enhanceApp: (App) => (props) => {
          return sheet.collect(<App {...props} />);
        },
      });

    const initialProps = await Document.getInitialProps(ctx);
    return {
      ...initialProps,
      styles: (
        <>
          {initialProps.styles}
          {sheet.getStyleElement()}
          {/*{sheetStyled.getStyleElement()}*/}
        </>
      ),
    };
  }

  render() {
    return (
      <Html>
        <Head />
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    );
  }
}

export default AppDocument;
```
