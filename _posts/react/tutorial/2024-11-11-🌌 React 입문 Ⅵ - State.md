---
title: 🌌 React 입문 Ⅵ - State
date: 2024-11-11 14:16:34 +0900
categories:
  - React
tags:
  - React
  - 입문
  - Javascript
---
![](/assets/image/Pasted%20image%2020250522211144.png)
> 📘 `『소플의 처음 만난 리액트』`를 읽고 정리한 글입니다.


### `State`란?
- `React`의 생애주기를 이해하기 위해서는 `State`에 대해서 알아야 한다.
- `State`는 `React Component`의 변경 가능한 데이터이며, `State`가 변경되면 `Component`가 다시 렌더링 된다.
- 그렇기 때문에 `State`를 정의할 때는 꼭 렌더링이나 데이터 흐름에 사용되는 값만 포함시켜야 한다.  
- `State`를 무분별하게 정의한다면 애플리케이션의 성능을 저하 시킬 수 있기 때문이다.
- `State`는 따로 복잡한 형태가 있는 게 아니라 하나의 `Javascript` 객체이다.

```jsx
class LikeButton extends React.Component {
	constructor(props) {
		super(props);
		
		this.state = {
			liked : false
		};
	}
}
```
- `Class Component`의 경우 상태는 생성자 코드 내에 정의되어 있다.

```jsx
// 직접 수정
this.state = {
	name : "kim"
}

// setState 함수를 통해 수정
this.setState({
	name : "kim"
});
```
- `State`는 일반적인 `Javascript` 변수를 다루듯 수정하면 안 된다.
- `State`는 `Component`의 렌더링과 관련 있기 때문에 마음대로 수정하게 되면 애플리케이션이 개발자가 의도한 대로 동작하지 않을 수 있다.
- `State`를 변경하고자 할 때에는 꼭 `setState()` 함수를 사용해야 한다.


### 회고
- 내용을 정리하며 `React`에서 데이터 흐름과 `UI` 렌더링 방식에 대한 이해를 할 수 있었다.
- 지금까지는 필요한 값을 전부 `state`에 넣는 습관이 있었는데, 꼭 렌더링에 영향을 주는 값만 포함해야 한다는 걸 배울 수 있었다.