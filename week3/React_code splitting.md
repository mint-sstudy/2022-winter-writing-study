> '리액트를 다루는 기술'을 읽고 작성했습니다.
# Ch 19. Code Splitting
## 코드 스플리팅이란? 
> webpack에서 별도의 설정을 하지 않으면  빌드 시 프로젝트에서 사용중인 모든 js 파일이 하나의 파일로 합쳐지고, 모든 CSS 파일도 하나의 파일로 합쳐진다.
> 애플리케이션의 규모가 커지면 필요하지 않은 컴포넌트 정보까지 모두 불러오면서 파일 크기가 매우 커지게 되고, 
> 로딩 시간이 증가한다. 코드 스플리팅을 하게되면, 지금 당장 필요하지 않은 파일을 따로 분리시켜서, 나중에 필요할 때 불러와서 사용할 수 있다. 
<br>
<br>

## Ch 19.1 자바스크립트 함수 비동기 로딩
### 1) 일반 자바스크립트 함수 스플리팅

**notify.js**

```jsx
export default function notidy() {
   alert('안녕하세요!');
}
```
<br>

- Hello React! 문구 클릭 시 notify 함수 실행되도록 App.js 수정

**App.js**

```jsx
import React from 'react';
import logo from './logo.svg';
import './App.css';
import notify from './notify';

function App() {
   const onClick = () => {
      notify();
   };
   return (
      <div className="App">
         <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <p onClick={onClick}>Hello React!</p>
         </header>
      </div>
   );
}

export default App;
```

→ 빌드하면 notify 코드가 main 파일 안에 들어간다. 하지만 위와 같이 import를 상단에서 하지 않고 메소드 안에서 import() 함수 형태로 사용할 시<br>
  파일을 따로 분리해 저장한다. (main 파일에 저장하지 않고) 그리고 실제로 함수가 필요할 때 파일을 불러와 해당 함수를 사용한다.
<br>

**App.js**

```jsx
import React from 'react';
import logo from './logo.svg';
import './App.css';

function App() {
   const onClick = () => {
      import('./notify').then((result) => result.default()); //메소드 내부로 이동
   };
   return (
      <div className="App">
         <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <p onClick={onClick}>Hello React!</p>
         </header>
      </div>
   );
}

export default App;
```
→ import를 함수로 사용하면 promise를 return한다. (dynamic import)
<br>
<br>

### 결과
<img width="1792" alt="스크린샷 2022-02-03 오후 6 26 31" src="https://user-images.githubusercontent.com/80838501/152341795-bcf2334d-d1ca-4862-8227-b149e7523be7.png">

→ Hello React!를 클릭하는 순간 새로운 js 파일을 불러온다. (src_notify_js.chunk.js) <br>
  불러온 파일의 내용을 보면, notify 관련 코드만 들어있다.
<br>
<br>
<br>
<br>

## Ch 19.2 React.lazy와 Suspense를 통한 컴포넌트 코드 스플리팅
### 2) 컴포넌트 코드 스플리팅
> 코드 스플리팅을 위해 리액트에 내장된 기능으로 유틸 함수인 **React.lazy**와 컴포넌트인 **Suspense**를 사용해보자!
<br>

### state를 사용한 코드 스플리팅
> React.lazy를 사용하지 않고 컴포넌트의 코드를 스플리팅하려면 어떻게 해야할까? <br>

**SplitMe.js** - 코드 스플리팅 대상이 되는 예제 js 파일
```jsx
import React from 'react';

const SplitMe = () => {
   return <div>SplitMe</div>;
};

export default SplitMe;
```
<br>

**App.js**

```jsx
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

class App extends Component {
   state = {
      SplitMe: null,
   };
   handleClick = async () => {
      const loadedModule = await import('./SplitMe');
      this.setState({
         SplitMe: loadedModule.default,
      });
   };
   render() {
      const { SplitMe } = this.state;
      return (
         <div className="App">
            <header className="App-header">
               <img src={logo} className="App-logo" alt="logo" />
               <p onClick={this.handleClick}>Hello React!</p>
               {SplitMe && <SplitMe />}
            </header>
         </div>
      );
   }
}

export default App;
```
→ 클래스형 컴포넌트로 변경 후, handleClick 메소드를 만들어 내부에서 SplitMe 컴포넌트를 불러와 state에 넣는다. <br>
  그리고 render()에서는 state 내부의 SplitMe가 유효할 경우 SplitMe 컴포넌트를 렌더링해준다.
<br>
<br>

### 결과
<img width="1792" alt="스크린샷 2022-02-03 오후 7 02 46" src="https://user-images.githubusercontent.com/80838501/152342518-6844ee74-f713-4bd2-bf8d-24fe428d76ab.png">

→ Hello React! 클릭 시 SplitMe 컴포넌트의 코드 스플리팅이 성공적으로 구현되었다! (src_SplitMe_js_chunk.js) <br>
→ 그런데 코드 스플리팅을 할 때마다 state를 선언해야 한다는 점이 불편하다..
<br>
<br>

### React.lazy와 Suspense 사용하기
> React.lazy와 Suspense를 사용하면 state를 따로 선언하지 않고 간편하게 컴포넌트 코드 스플리팅을 할 수 있다.
<br>

### React.lazy
React.lazy는 컴포넌트를 렌더링하는 시점에 비동기적으로 로딩할 수 있게 해주는 유틸 함수이다.
```jsx
const SplitMe = React.lazy(() => import('./SplitMe'));
```
<br>

### Suspense
Suspense는 리액트 내장 컴포넌트이며, 코드 스플리팅된 컴포넌트를 로딩하도록 할 수 있고, 로딩이 끝나지 
않았을 때 보여줄 UI를 설정할 수 있게 해준다.
```jsx
import React, { Suspense } from 'react';

(...)
<Suspense fallback={<div>loading...</div>}>
	<SplitMe />
</Suspense>
```
→ fallback props를 통해 로딩 중에 화면에 보여줄 JSX를 지정할 수 있다.
<br>
<br>

### 활용 예제
**App.js**
```jsx
import React, { useState, Suspense } from 'react';
import logo from './logo.svg';
import './App.css';
const SplitMe = React.lazy(() => import('./SplitMe'));

function App() {
   const [visible, setVisible] = useState(false);
   const onClick = () => {
      setVisible(true);
   };
   return (
      <div className="App">
         <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <p onClick={onClick}>Hello React!</p>
            <Suspense fallback={<div>loading…</div>}>
               {visible && <SplitMe />}
            </Suspense>
         </header>
      </div>
   );
}

export default App;
```
→ SplitMe 컴포넌트의 가시성을 의미하는 visible이라는 state만 업데이트해서 코드 스플리팅된 컴포넌트를 보여주도록 수정
<br>
<br>
<br>

### Loadable Components를 통한 코드 스플리팅
- **Loadable components**는 코드 스플리팅을 편히 하도록 도와주는 서드파티 라이브러리
- 장점: 서버 사이드 렌더링을 지원하고, 렌더링하기 전에 스플리팅 된 파일을 미리 불러올 수 있는 기능이 있다.
- preload 외에도 타임아웃, 로딩 UI 딜레이, 서버 사이드 렌더링 호환 등의 다양한 기능을 보유하고 있다.
<br>
<br>
<br>

**Loadable components 라이브러리 사용방법** <br>
**App.js**
```jsx
import React, { useState } from 'react';
import logo from './logo.svg';
import './App.css';
import loadable from '@loadable/component';
const SplitMe = loadable(() => import('./SplitMe'));

function App() {
   const [visible, setVisible] = useState(false);
   const onClick = () => {
      setVisible(true);
   };
   return (
      <div className="App">
         <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <p onClick={onClick}>Hello React!</p>
            {visible && <SplitMe />}
         </header>
      </div>
   );
}

export default App;
```
→ React.lazy와 사용 방법은 유사
<br>
<br>

- 만약 로딩 중에 다른 UI를 보여주고 싶으면 다음과 같이 loadable 사용하는 부분 수정
```jsx
const SplitMe = loadable(() => import('./SplitMe'), {
	fallback: <div>loading...</div>
});
```
<br>
<br>

**렌더링 전에 컴포넌트를 미리 불러오는 방법 (preload)** <br>
**App.js**
```jsx
import React, { useState } from 'react';
import logo from './logo.svg';
import './App.css';
import loadable from '@loadable/component';
const SplitMe = loadable(() => import('./SplitMe'), {
   fallback: <div>loading…</div>,
});

function App() {
   const [visible, setVisible] = useState(false);
   const onClick = () => {
      setVisible(true);
   };
   const onMouseOver = () => {
      SplitMe.preload();
   };
   return (
      <div className="App">
         <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <p onClick={onClick} onMouseOver={onMouseOver}>
               Hello React!
            </p>
            {visible && <SplitMe />}
         </header>
      </div>
   );
}

export default App;
```
→ onMouseOver를 사용해 마우스 커서만 올려도 preload()가 실행되도록 구현 <br>
  마우스 커서 올리면 로딩 시작, 클릭 시 렌더링
<br>
<br>
<br>
<br>

## Ch 19.3 정리
서버 사이드 렌더링을 할 계획이 없다면, React.lazy와 Suspense로 구현하고, 서버 사이드 렌더링을 할 예정이라면 Loadable Components 라이브러리를<br> 사용해야 한다. <br>
<br>

**React 공식 문서**
<img width="903" alt="스크린샷 2022-02-03 오후 7 25 09" src="https://user-images.githubusercontent.com/80838501/152343098-9b869991-7867-42c8-a197-a15191301e52.png">
