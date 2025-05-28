---
title: 🌌 React 입문 XIV - Composition · Specialization
date: 2025-04-06 19:47:53 +0900
categories:
  - React
tags:
  - React
  - 입문
  - Javascript
---
![](/assets/image/Pasted%20image%2020250522211144.png)
> 📘 `『소플의 처음 만난 리액트』`를 읽고 정리한 글입니다.

### `Composition`
- 여러 개의 `Component`를 합쳐서 새로운 `Component`를 만드는 것을 말한다.
- 가령 `Component A`와 `Component B`를 사용해 하나의 페이지를 만들었다고 하면 `Composition`을 사용했다고 볼 수 있다.
- 여러 개의 `Component`를 조합하는 방법에 대한 `Composition` 기법이 존재한다.


### `Containment`
```jsx
// 1. Parent
function FancyBorder(props) {
	return (
		<div className={'FancyBorder FancyBorder-' + props.color}>
			{props.children}
		</div>
	);
}

// 2. Children
function WelcomeDialog(props) {
	return (
		<FancyBorder color="blue">
			<h1 className="Dialog-title">
				어서오세요
			</h1>
			<p className="Dialog-message">
				우리 사이트에 방문하신 것을 환영합니다!
			</p>
		</FancyBorder>
	);
}
```
- 하위 `Component`를 포함하는 형태의 합성 방법이다.
- `Sidebar`나 `Dialog`와 같은 박스 형태의 `Component`는 자신의 하위 `Component`를 미리 알 수 없다.
- 이럴 땐 `props`에 기본 내장된 `children` 속성을 사용하는 `Containment` 기법을 사용하면 된다. 


### `Specialization`
```jsx
// 1. Parent
function Dialog(props) {
	return (
		<FancyBorder color="blue">
			<h1 className="Dialog-title">
				{props.title}
			</h1>
			<p className="Dialog-message">
				{props.message}
			</p>
		</FancyBorder>
	);
}

// 2. Children
function WelcomeDialog(props) {
	return (
		<Dialog
			title="어서오세요"
			message="우리 사이트에 방문하신 것을 환영합니다!"
		/>
	);
}
```
- 범용적인 개념을 구별이 되도록 구체화하는 것을 의미한다.
- 일반적인 객체 지향 언어에서는 상속을 이용하여 `Specialization`을 구현하지만 `React`에서는 `Composition`을 이용하여 구현한다.
- 상기 코드처럼 `Specialization`은 범용적으로 쓸 수 있는 `Component`를 만들어 놓고 이를 특수화 시켜서 `Component`를 사용하는 `Composition` 방식이다.


### `Containment` + `Spectialization`
```jsx
// 1. Parent
function Dialog(props) {
	return (
		<FancyBorder color="blue">
			<h1 className="Dialog-title">
				{props.title}
			</h1>
			<p className="Dialog-message">
				{props.message}
			</p>
			{props.children}
		</FancyBorder>
	);
}

// 2. Children
function SignUpDialog(props) {
	const [nickname, setNickname] = useState('');
	
	const handleChange = (event) => {
		setNickname(event.target.value);
	}
	
	const handleSignUp = () => {
		alert(`어서오세요, ${nickname}님!`);
	}
	
	return (
		<Dialog 
			title="화성 탐사 프로그램"
			message="닉네임을 입력해 주세요">
			<input 
				value={nickname}
				onChange={handleChange} />
			<button onClick={handleSignUp}>
				가입하기 
			</button>
		</Dialog>
	);
}
```
- `props.children`은 `Containment`에 해당하고, `props.title`, `props.message`는 `Spacialization`에 해당한다.


### 상속
- `React`를 개발한 `Meta`는 상속 기반의 `Component` 생성 방법을 찾지 못했다.
- 결국 복잡한 `Component`를 쪼개 여러 개의 `Component`로 만들고, 만든 `Component`들을 조합하여 새로운 `Component`를 만들자는 결론에 수렴한다.


### 실습
```jsx
// Card.jsx
function Card(props) {
	const { title, backgroundColor, children } = props;
	
	return (
		<div
			style={{
				margin : 8,
				padding : 8,
				borderRadius : 8,
				boxShadow : '0px 0px 4px grey',
				backgroundColor : backgroundColor || 'white'
			}}>	
			{title && <h1>{title}</h1>}
			{children}
		</div>
	);
}

export default Card;

// ProfileCard.jsx
function ProfileCard(props) {
	return (
		<Card title="Inje Lee" backgroundColor="#4ea04e">
			<p>안녕하세요,</p>
			<p>저는 리액트를 사용해서 개발하고 있습니다.</p>
		</Card>
	);
}

export default ProfileCard;
```
- `Card`는 레이아웃만 담당하고, 실제 내용은 외부에서 전달 받는다.


### 회고
- `React`에서 합성이 어떻게 활용되는지 체감 할 수 있었다.