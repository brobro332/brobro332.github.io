---
title: 🌌 React 입문 Ⅳ - Element
date: 2024-11-09 17:21:35 +0900
categories:
  - React
tags:
  - React
  - 입문
  - Javascript
---
![Pasted_image_20250522211144.png](/assets/image/Pasted_image_20250522211144.png)
> 📘 `『소플의 처음 만난 리액트』`를 읽고 정리한 글입니다.


### `Element`란?
- `Chrome` 개발자 도구를 통해 확인할 수 있는 `Elements`는 `DOM Element`를 의미한다.
- `DOM Element`란 브라우저에서 실제로 렌더링 된 `HTML` 요소이다. 
- `React Element`는 화면에 나타나는 내용을 기술한 `Javascript` 객체를 일컫는다.
- 즉 `DOM`에 직접 표시되지 않는다.

```jsx
/* JSON 형태
{
  type: 'button',
  props: {
    className: 'bg-green',
    children: {
      type: 'b',
      props: {
        children: '테스트용 텍스트 입니다!'
      } 
    }
  }
}
*/

// 리액트 엘리먼트를 생성하는 JSX 문법
<button className="bg-green">
  <b>
    테스트용 텍스트 입니다!
  </b>
</button>
```
- `JSON` 형태로 주석 처리된 부분이 `React Element`이다.
- 만들어진 `React Element`는 `DOM`에 표시되지 않은 상태이다.
- `JSX` 문법도 `React Element`를 만들어 낸다.  
- `Element`의 `type`에는 `HTML` 태그명이 문자열로 들어갈 수 있지만 `React`의 `Component`가 들어갈 수도 있다.
- 하나의 `Component`는 여러 개의 자식 `Component`를 포함할 수 있는데, 자식 `Component`를 쭉 분해하다 보면 결국 `HTML` 태그가 나올 것이다.
- `Element`의 `props`는 부모 `Component`에서 자식 `Component`로 전달되는 객체를 의미한다.  
- `Element`는 불변성을 가지고 있기 때문에 한 번 생성된 `Element`는 변하지 않는다.


### `Element` 렌더링
![Pasted_image_20250525144638.png](/assets/image/Pasted_image_20250525144638.png)
- 데이터가 갱신 되는 시점에 `Virtual DOM`은 전체 `UI`를 렌더링 한다.
- 이때 가져온 트리의 복사본과 기존 트리를 비교하여 바뀐 부분만 실제 UI에 반영하는 것이다.
- 정확히는 바뀐 노드의 자식 노드까지 `UI`에 반영된다.
- 불변성을 갖고 있다는 것은 `Element`를 수정하는 게 아니라 교체해 버린다는 것을 의미한다.
- 화면이 렌더링 되는 횟수는 `React` 애플리케이션 성능에 큰 영향을 미치는 요소이다.


### `Root DOM node`
```jsx
<div id="root"></div>
```
-  모든 `React` 애플리케이션에 포함되는 필수적인 코드이다.
- 이 `div` 태그 안에 `React Element`들이 실제로 렌더링 되는 것이다.
- 하나의 `React` 애플리케이션이 여러 개의 `Root DOM node`를 갖게 될 수도 있다.


### `React Element` 렌더링
```jsx
const element = <h1>텍스트</h1>;
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(element);
```
- `render()` 함수를 통해 `Root DOM node`에 `React Element`를 렌더링 하는 코드이다.


### 실습
```jsx
import React from "react";

function Clock() {
	return (
		<div>
			<h1>🕒 현재 시간</h1>
			<h2>✅ {new Date().toLocaleTimeString()}</h2>
		</div>
	);
}

export default Clock;
```
- 단순히 현재 시간을 보여주는 `Component`이다. 
- 이 `Component` 만으로는 화면이 단 한 번밖에 갱신 되지 않을 것이다. 

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import reportWebVitals from './reportWebVitals';
import Team from './Team';

const root = ReactDOM.createRoot(document.getElementById('root'));
setInterval(() => {
	root.render(
		<React.StrictMode>
			<Clock/>
		</React.StrictMode>
	);
}, 1000);

reportWebVitals();
```
- `setInterval()` 함수를 통해 일정 시간마다 `Clock` `Component`를 렌더링 한다.
- `new Date().toLocaleTimeString()` 값이 1초마다 갱신 되면서 
- `Clock` `Component`의 속성 값이 계속 바뀌게 되는데, 이때 `React`가 변경을 감지하고 1초마다 렌더링을 하는 것이다. 


![Pasted_image_20250525145849.png](Pasted_image_20250525145849.png)


### 회고
- `React`는 변경사항이 존재하는 `Component`만 부분적으로 렌더링 한다는 사실을 알 수 있었다.