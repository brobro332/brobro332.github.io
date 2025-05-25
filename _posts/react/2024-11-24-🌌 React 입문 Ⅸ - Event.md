---
title: 🌌 React 입문 Ⅸ - Even
date: 2024-11-24 20:13:44 +0900
categories:
  - React
tags:
  - React
  - 입문
  - Javascript
---
![Pasted_image_20250522211144.png](/assets/image/Pasted_image_20250522211144.png)
> 📘 `『소플의 처음 만난 리액트』`를 읽고 정리한 글입니다.

### `Event` 처리
```jsx
// DOM
<button onclick="activate()">Activate</button>

// 리액트
<button onClick={activate}>Activate</button>
```
- `DOM`에도 `React`에도 `Event`가 있지만 사용하는 방법이 조금 다르다.
- `Event`가 발생했을 때 해당 `Event`를 처리하는 함수가 있는데 이것을 `Event Handler` 또는 `Event Listener`라고 한다.

#### ✅ `Class Component`의 `Event` 처리
```jsx
class Toggle extends React.Component {
	constructor(props) {
    	super(props);
        
        this.state = { isToggleOn : true };
        this.handleClick = this.handleClick.bind(this);
    }
    
    handleClick() {
    	this.setState(prevState => ({
        	isToggleOn : !prevState.isToggleOn
        }));
    }
    
    render() {
    	return (
            <button onClick={this.handleClick}>
            	{this.state.isToggleOn ? '켜짐' : '꺼짐'}
            </button>
        );
    }
}
```
- `Class Component`는 기본적으로 `Binding` 되지 않는다. 
- 그렇기 때문에 직접 `Bind` 하지 않으면 `this.handleClick`은 `Global Scope`에서 호출되지만, `Global Scope`에서 해당 값은 `undefined`이므로 사용할 수 없다.
- 따라서 함수의 이름 뒤에 괄호 없이 사용하려면 해당 함수를 `Bind` 해주어야 한다.
- 아니라면 다음처럼 화살표 함수를 사용해야 한다.

```jsx
class MyButton extends React.Component {
    handleClick () {
    	console.log('this is:', this);
    }
    
    rendor() {
    	return (
            <button onClick={() => this.handleClick()}>
            	클릭
            </button>
    }
}
```
- 화살표 함수는 자신이 종속된 `Instance`를 가리킨다. 

#### ✅ `Functional Component`의 `Event` 처리
```jsx
function Toggle(props) {
    const [isToggleOn, setIsToggleOn] = useState(true);
    
    // 함수 안의 함수
    function handleClick() {
    	setIsToggleOn((isToggleOn) => !isToggleOn);
    }
    
    // 화살표 함수
    /*
    const handleClick = () => {
    	setIsToggleOn((isToggleOn) => !isToggleOn);
    }
    */
    
    return (
    	<button onClick={handleClick}>
            {isToggleOn ? '켜짐' : '꺼짐'}
        </button>
    );
}
```
- 상기 코드는 위의 `Toggle` `Class Component`를 `Functional Component`로 변환한 것이며, 1번 방식과 2번 방식 모두 사용 가능하다. 


### `Arguments`
```jsx
function myButton(props) {
    const handleDelete = (id, event) => {
    	console.log(id, event.target);	
    }
    
    return (
    	<button onClick={(event) => handleDelete(1, event)}>삭제하기</button>
    );
}
```
- 화살표 함수 방식으로 `handleDelete()` 메서드를 호출하며 `id`와 `event`를 전달하고 있다.


### 실습
```jsx
import { useState } from "react";

function ConfirmButton(props) {
    const [isConfirmed, setIsConfirmed] = useState(false);
    const handleConfirm = () => {
        setIsConfirmed((prevIsConfirmed) => !prevIsConfirmed);
    };
    const buttonStyle = {
        padding: "10px 20px",
        backgroundColor: isConfirmed ? "#aaa" : "#007BFF",
        color: "white",
        border: "none",
        borderRadius: "4px",
        fontSize: "14px",
        cursor: isConfirmed ? "not-allowed" : "pointer",
        transition: "background-color 0.3s ease",
        marginTop: "20px",  
        marginLeft: "20px",
    };

  

    return (
        <button onClick={handleConfirm} disabled={isConfirmed} style={buttonStyle}>
            {isConfirmed ? '인증완료' : '인증'}
        </button>
    );
}

export default ConfirmButton;
```
![Pasted_image_20250525232604.png](Pasted_image_20250525232604.png)
![Pasted_image_20250525232715.png](Pasted_image_20250525232715.png)


### 회고
- 실습 예제를 실제로 실행해보며 이메일 인증 버튼 등 유용하게 쓰이고 충분히 재사용도 가능한 `Component`라는 생각이 들었다.