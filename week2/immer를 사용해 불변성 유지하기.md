# Ch12_immer를 사용해 쉽게 불변성 유지하기
### `immer`를 사용하는 이유

```
불변성을 유지하기 위해, 전개 연산자와 배열 내장 함수를 사용해 배열이나 객체를 복사하고 새로운 값을 덮어쓸 수 있다.
그러나 객체의 구조가 커지고 깊어지면, 불변성을 유지하면서 값을 업데이트하기 매우 힘들어진다.
기존의 다른 값들은 유지하면서 값 하나를 업데이트하기 위해 여러 줄의 코드를 작성해야 하는 경우가 생긴다.
→ immer 라이브러리를 이용하면 구조가 복잡한 객체를 짧은 코드를 사용해 불변성을 유지하면서 업데이트를 할 수 있다.
```
<br>

## 12.1 immer를 설치하고 사용법 알아보기
### 12.1.2 immer를 사용하지 않고 불변성 유지
**Ex)** spread 연산자와 filter 함수를 사용해 불변성을 유지하며, 폼에서 아이디/이름을 입력하면 하단의 리스트에 추가되고, <br>리스트 항목을 클릭하면 삭제되는 컴포넌트 <br>
<br>

**결과화면**
<img width="430" alt="스크린샷 2021-12-23 오후 5 23 24" src="https://user-images.githubusercontent.com/80838501/147358153-c5993232-9c2f-472c-990b-05b97b96c275.png">


**App.js**
```jsx
import React, { useRef, useCallback, useState } from 'react';

const App = () => {
   const nextId = useRef(1);
   const [form, setForm] = useState({ name: '', username: '' });
   const [data, setData] = useState({
      array: [],
      uselessValue: null,
   });

   //input 수정을 위한 함수
   const onChange = useCallback(
      (e) => {
         const { name, value } = e.target;
         setForm({
            ...form,
            [name]: [value],
         });
      },
      [form]
   );

   //form 등록을 위한 함수
   const onSubmit = useCallback(
      (e) => {
         e.preventDefault();
         const info = {
            id: nextId.current,
            name: form.name,
            username: form.username,
         };

         //array에 새 항목 등록
         setData({
            ...data,
            array: data.array.concat(info),
         });

         //form 초기화
         setForm({
            name: '',
            username: '',
         });
         nextId.current += 1;
      },
      [data, form.name, form.username]
   );

   //항목을 삭제하는 함수
   const onRemove = useCallback(
      (id) => {
         setData({
            ...data,
            array: data.array.filter((info) => info.id !== id),
         });
      },
      [data]
   );

   return (
      <div>
         <form onSubmit={onSubmit}>
            <input
               name="username"
               placeholder="아이디"
               value={form.username}
               onChange={onChange}
            />
            <input
               name="name"
               placeholder="이름"
               value={form.name}
               onChange={onChange}
            />
            <button type="submit">등록</button>
         </form>
         <div>
            <ul>
               {data.array.map((info) => (
                  <li key={info.id} onClick={() => onRemove(info.id)}>
                     {info.username} ({info.name})
                  </li>
               ))}
            </ul>
         </div>
      </div>
   );
};

export default App;
```
<br>
<br>

### 12.1.3 immer 사용법
```jsx
import produce from 'immer';
const nextState = produce(originalState, draft => {
	//바꾸고 싶은 값 바꾸기
	draft.somewhere.deep.inside = 5;
})
```
→ `produce` 함수는 두 가지 파라미터를 받는다.
   1) 수정하고 싶은 state
   2) state를 어떻게 업데이트할지 정의하는 함수 <br>
      이 함수 내부에서 원하는 값을 변경하면 produce 함수가 불변성 유지를 대신하면서 새로운 state를 생성
<br>

```jsx
import produce from 'immer';

const originalState = [
	{
		id: 1,
		todo: '전개 연산자와 배열 내장 함수로 불변성 유지하기',
		checked: true,
	},
	{
		id: 2,
		todo: 'immer로 불변성 유지하기',
		checked: false,
	}
];

const nextState = produce(originalState, draft => {
	//id가 2인 항목의 checked 값을 true로 설정
	const todo = draft.find(t => t.id === 2); //id로 항목 찾기
	todo.checked = true;

	//배열에 새로운 데이터 추가
	draft.push({
		id: 3,
		todo: '일정 관리 앱에 immer 적용하기',
		checked: false,
	});

	//id = 1인 항목 제거
	draft.splice(draft.findIndex(t => t.id === 1), 1);
});
```
→ produce의 첫번째 파라미터: originalState (수정하고 싶은 state) <br>
  두번째 파라미터: draft (state를 어떻게 업데이트할지 정의하는 함수)
<br>
<br>
<br>

### 12.1.4 App 컴포넌트에 immer 적용하기
**App.js**
```jsx
import React, { useRef, useCallback, useState } from 'react';
import produce from 'immer';

const App = () => {
   const nextId = useRef(1);
   const [form, setForm] = useState({ name: '', username: '' });
   const [data, setData] = useState({
      array: [],
      uselessValue: null,
   });

   // input 수정을 위한 함수
   const onChange = useCallback(
      (e) => {
         const { name, value } = e.target;
         setForm(
            produce((form, draft) => { //immer
               draft[name] = value;
            })
         );
      },
      [form]
   );

   // form 등록을 위한 함수
   const onSubmit = useCallback(
      (e) => {
         e.preventDefault();
         const info = {
            id: nextId.current,
            name: form.name,
            username: form.username,
         };

         // array에 새 항목 등록
         setData(
            produce((data, draft) => { //immer
               draft.array.push(info);
            })
         );

         // form 초기화
         setForm({
            name: '',
            username: '',
         });
         nextId.current += 1;
      },
      [data, form.name, form.username]
   );

   // 항목을 삭제하는 함수
   const onRemove = useCallback(
      (id) => {
         setData(
            produce((data, draft) => { //immer
               draft.array.splice(
                  draft.array.findIndex((info) => info.id === id),
                  1
               );
            })
         );
      },
      [data]
   );

   return (
      <div>
         <form onSubmit={onSubmit}>
            <input
               name="username"
               placeholder="아이디"
               value={form.username}
               onChange={onChange}
            />
            <input
               name="name"
               placeholder="이름"
               value={form.name}
               onChange={onChange}
            />
            <button type="submit">등록</button>
         </form>
         <div>
            <ul>
               {data.array.map((info) => (
                  <li key={info.id} onClick={() => onRemove(info.id)}>
                     {info.username} ({info.name})
                  </li>
               ))}
            </ul>
         </div>
      </div>
   );
};

export default App;
```
→ immer 사용 시, 객체 안에 있는 값을 직접 수정하거나, 배열에 직접적인 변화를 일으키는 push, splice 등의 함수를 사용해도 된다. <br>
  immer를 사용했을 때, 무조건 코드가 간결해지는 것도 아니고, 오히려 성능적으로는 immer를 사용하는 것이 더 느릴 수 있다. <br>
  그러므로, 구조가 복잡해져 불변성을 유지하는 것이 어려운 경우에만 immer를 사용하는 것이 좋다.
<br>
<br>

### 12.1.5 useState의 함수형 업데이트와 immer 함께 쓰기
- produce 함수 호출 시 첫 번째 파라미터를 함수 형태로 전달하면 업데이트 함수를 반환하는 immer의 속성과, useState의 함수형 업데이트를 함께 활용해<br>
  코드를 더욱 깔끔하게 만들어 보자.

```jsx
import React, { useRef, useCallback, useState } from 'react';
import produce from 'immer';
 
const App = () => {
  const nextId = useRef(1);
  const [form, setForm] = useState({ name: '', username: '' });
  const [data, setData] = useState({
    array: [],
    uselessValue: null
  });
 
  // input 수정을 위한 함수
  const onChange = useCallback(e => {
    const { name, value } = e.target;
    setForm(
      produce(draft => { //파라미터를 함수 형태로 사용
        draft[name] = value;
      })
    );
  }, []);
 
  // form 등록을 위한 함수
  const onSubmit = useCallback(
    e => {
      e.preventDefault();
      const info = {
        id: nextId.current,
        name: form.name,
        username: form.username
      };
 
      // array에 새 항목 등록
      setData(
        produce(draft => { //파라미터를 함수 형태로 사용 
          draft.array.push(info);
        })
      );
 
      // form 초기화
      setForm({
        name: '',
        username: ''
      });
      nextId.current += 1;
    },
    [form.name, form.username]
  );
 
  // 항목을 삭제하는 함수
  const onRemove = useCallback(
    id => {
      setData(
        produce(draft => { //파라미터를 함수 형태로 사용
          draft.array.splice(draft.array.findIndex(info => info.id === id), 1);
        })
      );
    },
    []
  );
 
  return (
      <div>
         <form onSubmit={onSubmit}>
            <input
               name="username"
               placeholder="아이디"
               value={form.username}
               onChange={onChange}
            />
            <input
               name="name"
               placeholder="이름"
               value={form.name}
               onChange={onChange}
            />
            <button type="submit">등록</button>
         </form>
         <div>
            <ul>
               {data.array.map((info) => (
                  <li key={info.id} onClick={() => onRemove(info.id)}>
                     {info.username} ({info.name})
                  </li>
               ))}
            </ul>
         </div>
      </div>
   );
};
 
export default App;
```
<br>
<br>
<br>

## 12.2 정리
`immer`는 컴포넌트의 상태 업데이트가 까다로울 때 사용하는 것이 좋고, Redux 사용 시에도 immer를 함께 사용하면 더욱 편리하다.<br>
하지만 어디까지나 편하기 위해 사용하는 라이브러리에 불과하므로, 오히려 사용하는 것이 더 불편하다면, 굳이 사용할 필요는 없다.
