# 설정

## .prettierrc설정

```json
{
  "singleQuote": true,
  "useTabs": false,
  "tabWidth": 2,
  "semi": true,
  "trailingComma": "all"
}
```

## 의존성

- 의존성 설치 -> 필요 라이브러리
  - react-router-dom: 라우터
  - sass, styled-components, classnames: 스타일링 목적
  - immer: 불변성 관리
  - react-icons: 리액트에서 제공하는 아이콘 라이브러리
  - @loadable/component: 지연로딩
  - react-helmet-async: head 태그 내의 특정 태그의 내용을 변경할 때 사용
   > SPA에서 React를 사용할때 페이지간 동적으로 `<head>` 요소를 변경시 각 페이지마다 다른 제목, 스타일 시트 등등 설정하고 제어 할 수 있도록 사용한다.

## react-helmet-async 설정
- src/index.js
``` js
import { HelmetProvider } from 'react-helmet-async';
...

root.render(
  <React.StrictMode>
    <HelmetProvider>
      <App />
    </HelmetProvider>
  </React.StrictMode>,
);

...
```
- 사용법
```js
import { Helmet } from 'react-helmet-async';

const App = () => {
  return (
    <>
      <Helmet>
        <title>사이트 제목 변경 테스트</title>
      </Helmet>
    </>
  );
};

export default App;
```

# 메시지 다국어 처리
- 의존성: i18next, react-i18next
- 의존성 설치

```js
yarn add i18next react-i18next
```

- 언어 파일 생성 (해당 언어에 맞는 json 파일 만들기)
  - `src/language/ko`, `src/language/en` 폴더 생성
  - 각 폴더별로 공통 문구: `commons.js`, 검증 문구: `validations.js`, 에러문구: `errors.js`

- 언어 파일 통합: 예) `src/languages/ko/index.js`

```js
import commons from './commons';
import validations from './validations';
import errors from './errors';

const ko = { ...commons, ...validations, ...errors };

export default ko;

```

- 설정 파일 구성: `src/i18n.js`

```js
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';
import ko from './languages/ko'; // ko/index.js
import en from './languages/en';

const resources = {
  en: {
    translation: en,
  },
  ko: {
    translation: ko,
  },
};

i18n.use(initReactI18next).init({
  resources, //설정명과 객체명 동일하게 만들어주면 resources:resources라고 해줄 필요없이 하나로 가능
  lng: 'ko',
});

```
- 설정 반영: `src/index.js`

```js
...
import './i18n';
...

```

- 적용하기: useTranslation 훅을 통해서 사용가능/ react-i18next
  - t: 메시지 조회 함수
  - i18n: 편의 기능 객체(changeLanguage(..): 언어 변경함수 - 제일 많이 씀)

```js
import { Helmet } from 'react-helmet-async';
import { useTranslation } from 'react-i18next';

const App = () => {
  const { t, i18n } = useTranslation();

  return (
    <>
      <Helmet>
        <title>사이트 제목 변경 테스트</title>
      </Helmet>
      <div>{t('아이디')}</div>
      <div>{t('약관에_동의')}</div>
      <div>{t('없는_문구')}</div>
      <button type="button" onClick={() => i18n.changeLanguage('ko')}>
        한국어
      </button>
      <button type="button" onClick={() => i18n.changeLanguage('en')}>
        English
      </button>
    </>
  );
};

export default App;
```
  > i18n.changeLanguage(): 언어를 변환해주는 함수
    t function `t('resource key)` 형태로 언어 리소스 key를 입력 하면 해당 언어값을 출력해준다.
    useTranslation(): 함수형 컴포넌트에서 tfunction과 i18n 인스턴스를 사용할 수 있게 해주는 훅이다.

# 레이아웃 구성
- `src/layouts/MainLayout.js`
- `src/outlines/Header.js`
- `src/outlines/Footer.js`

# 라우팅 구성

## 설정
- `src/index.js`: BrowserRouter 컴포넌트로 감싸기

```js
...
import { BrowserRouter } from 'react-router-dom';
...
root.render(
  <React.StrictMode>
    <HelmetProvider>
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </HelmetProvider>
  </React.StrictMode>,
);

```

## 메인 페이지
- /: 메인 페이지 경로

## 회원
- /member/join: 회원가입 페이지 경로
- /member/login: 로그인 페이지 경로

## 없는 페이지
- /-: 없는 페이지(`commons/pages/NotFound.js`)

## 에러 페이지
>class형 컴포넌트: componentDidCatch 사용

- `commons/pages/Error.js`
- `commons/components/ErrorDisplay.js`