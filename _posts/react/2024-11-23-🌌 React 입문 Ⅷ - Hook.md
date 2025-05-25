---
title: 🌌 React 입문 Ⅷ - Hook
date: 2024-11-23 21:53:22 +0900
categories:
  - React
tags:
  - React
  - 입문
  - Javascript
---
![Pasted_image_20250522211144.png](/assets/image/Pasted_image_20250522211144.png)
> 📘 `『소플의 처음 만난 리액트』`를 읽고 정리한 글입니다.

### `Hook`이란?
- 기존의 `Functional Component`는`Class Component`와는 다르게 코드도 굉장히 간결하고 별도로 `State`를 정의하거나 `Component`의 생명주기 함수를 사용할 수 없었다. 
- 이런 기능을 지원하기 위해 나온 것이 바로 `Hook`이다.
- `Hook`이라는 단어는 갈고리를 뜻하는데, 갈고리를 거는 것처럼 원래 존재하는 어떤 기능에 끼어 들어가 같이 수행하는 것을 의미한다.
- `Hook`의 이름은 모두 `use`로 시작한다. 
- 개발자가 직접 `Custom Hook`을 만들어 사용할 경우 이름을 마음대로 지을 수도 있지만 `use`를 명시해 주는 것이 올바르다.


### `Hook`의 종류
#### ✅ `useState`
```jsx
function Counter(props) {
    var count = 0;
    
    return (
    	<div>
            <p>총 {count}번 클릭했습니다.</p>
            <button onClick={() => count++}>
            	클릭
            </button>
        </div>
    );
}
```
- 위 코드는 버튼을 클릭하면 `count` 값을 증가 시킬 수는 있지만 렌더링이 다시 일어나지 않아 갱신 된 `count` 값이 화면에 표시되지 않는다. 
- 따라서 아래의 코드와 같이 `State`를 사용해서 값이 바뀔 때마다 다시 렌더링 되도록 해야 할 필요성이 있다.

```jsx
import React, { useState } from "react";

function Counter(props) {
    const [count, setCount] = useState(0);
    
    return (
    	<div>
            <p>총 {count}번 클릭했습니다.</p>
            <button onClick={() => setCount(count + 1)}>
            	클릭
            </button>
        </div>
    );
}
```
- 위의 `Counter(props)` 메서드는 버튼이 눌렸을 때 `setCount()` 함수를 호출해서 `count`를 증가 시킨다.
- `count`의 값이 변경되면 `Component`가 다시 렌더링되면서 화면에 증가 된 `count` 값을 표시할 수 있다.
- `Class Component`에서는 `setState()` 함수 하나를 사용해서 모든 `State` 값을 갱신 할 수 있었지만 `Functional Component`에서는 변수 각각에 대해 `set` 함수가 따로 존재한다.

#### ✅ `useEffect`
```jsx
useEffect(이펙트 함수, 의존성 배열);
```
- `Side Effect`를 처리하기 위한 훅이다. 
- `Side Effect`라고 하면 부정적인 느낌을 가지고 있지만 `React`에서 말하는 `Side Effect`는 서버에서 데이터를 받아오거나 수동으로 `DOM`을 변경하는 등의 일반적인 이펙트를 의미한다.
- `Class Component`에서 제공하는 생명주기 함수인 `componentDidMount()`, `componentDidUpdate()`, `componentWillUnmount()`와 동일한 기능을 하나로 통합해서 제공한다.
- 의존성 배열 안에 있는 변수 중에 하나라도 값이 변경되었을 때 이펙트 함수가 실행된다. 
- 기본적으로 이펙트 함수는 처음 `Component`가 렌더링 된 이후와 업데이트로 인한 재렌더링 이후에 실행된다.
- 만약 이펙트 함수가 `Mount`, `Unmount` 시에 단 한 번만 실행되게 하고 싶으면 의존성 배열에 빈 배열을 넣으면 된다. 
- 이렇게 하면 이펙트가 `props`나 `State`에 있는 어떤 값에도 의존하지 않으므로 여러 번 실행되지 않는다.
- 의존성 배열 없이 `useEffect()`를 사용하면 `React`는 `DOM`이 변경된 이후에 해당 이펙트 함수를 실행하라는 의미로 받아들인다. 
- 그래서 `Component`가 처음 렌더링 될 때를 포함해서 매번 렌더링 될 때마다 이펙트가 실행되는데, 결과적으로 `componentDidMount()`, `componentDidUpdate()`와 동일한 역할을 하게 된다.
- `componentWillUnmount()`는 `useEffect()`에서 반환하는 함수는 `Component`가 `Unmount`될 때 호출된다.

#### ✅ `useMemo`
```jsx
const memoizedValue = useMemo(() => {
	return 함수(의존성 변수1, 의존성 변수2);
}, [의존성 변수1, 의존성 변수2]);
```
- `Memoized value`를 반환하는 `Hook`이다. 
- 의존성 배열에 들어 있는 변수가 변했을 경우에만 함수를 호출하여 새로운 결과를 반환하며, 그렇지 않은 경우는 기존 함수의 결과를 그대로 반환한다. 
- `Component`가 다시 렌더링 될 때마다 연산이 많은 작업의 반복을 피할 수 있다.
- 렌더링이 일어나는 동안 실행해서는 안 될 작업을 `useMemo`에 넣으면 안된다는 점을 유의해야 한다.
- 의존성 배열을 넣지 않을 경우 렌더링이 일어날 때마다 함수가 실행되며, 의존성 배열에 빈 배열을 넣을 경우 `Component` `Mount` 시에만 함수가 실행된다.

#### ✅ `useCallback`
- `useMemo`와 유사하지만 한 가지 차이점은 값이 아닌 함수를 반환한다. 
- `useCallback`에서 파라미터로 받는 이 함수를 `Callback`이라고 부른다. 
- 의존성 배열에 따라 `Memoized value`를 반환한다는 점에서는 `useMemo`와 완전히 동일하다.
- 만약 `useCallback`을 사용하지 않고 `Component` 내에 함수를 정의한다면 매번 렌더링이 일어날 때마다 함수가 새로 정의된다. 
- 따라서 해당 `Hook`을 사용하여 특정 변수의 값이 변한 경우에만 함수를 다시 정의하여 반복을 없애주는 것이 올바르다.

#### ✅ `useRef`
```jsx
const refContainer = useRef(초깃값);
```
- `Reference`를 사용하기 위한 `Hook`으로, `Reference` 객체를 반환한다. 
- `React`에서 `Reference`란 특정 `Component`에 접근할 수 있는 객체를 의미한다. 
- `Reference` 객체에는 `current`라는 속성이 있는데 이는 현재 참조하고 있는 `Element`를 의미한다.

```jsx
function TextInputWithFocusButton(props) {
    const inputElem = useRef(null);
    
    const onButtonClick = () => {
    	inputElem.current.focus(); 	
    };
    
    return (
    	<div>
            <input ref={inputElem} type="text"/>
            <button onClick={onButtonClick}>Focus the input</button>
        </div>
    );
}
```
- 위 코드는 버튼을 클릭하면 `input` 태그를 `Focusing`하는 예제이다.
- 매번 렌더링 될 때마다 항상 같은 `Reference` 객체를 반환한다. 
- 주의해야 할 점은 `useRef` 훅은 내부의 `current` 속성이 변경되더라도 다시 렌더링 하지 않는다는 것이다.
- 따라서 `Reference`에 `DOM node`가 연결되거나 분리될 경우 어떤 코드를 실행하고 싶다면 `callbackref` 방식을 사용해야 한다. 
- 이는 `DOM node`의 `ref` 속성에 `Reference` 객체 대신 `useCallback` 반환 함수를 넣어주는 것이다. 
- 이렇게 되면 자식 `Component`가 변경되었을 때 알림을 받을 수 있고, 이를 통해 다른 정보들을 `Update` 할 수 있다. 

```jsx
function MeasureExample(props) {
	const [height, setHeight] = useState(0);
    
    const measuredRef = useCallback(node => {
    	if (node !== null) {
        	setHeight(node.getBoundingClientRect().height);
        }
    }, []);
    
    return (
    	<div>
        	<h1 ref={measureRef}>안녕, 리액트</h1>
            <h2>위 헤더의 높이는 {Math.round(height)}px입니다.</h2>
        </div>
    );
}
```
- 위의 예제에서는 `h1` 태그의 높이를 `Update` 하고 있다. 
- `useCallback`의 의존성 배열로 빈 배열이 들어가 있으므로 `Mount`, `Unmount` 시에만 `Callback` 함수가 호출된다. 

### `Hook`의 규칙
#### ✅ `Hook`은 최상위 레벨에서만 호출해야 한다.
- 반복문이나 조건문 또는 중첩된 함수 안이 아닌 `React` `Functional Component`의 최상위 레벨에서 `Hook`을 호출해야 한다.
- 이 규칙에 따라 `Hook`은 `Component`가 렌더링 될 때마다 매번 같은 순서로 호출되어야 한다.

#### ✅ `React` `Functional Component`에서 `Hook`을 호출해야 한다.
- 일반 `Javascript` 함수에서 `Hook`을 호출하면 안 된다. 
- `React` `Functional Component`에서 호출하거나 직접 만든 `Custom Hook`에서 호출할 수 있다.


### `Custom Hook`
- 여러 `Component`에서 반복적으로 사용되는 로직을 `Hook`으로 만들어 재사용하기 위해 `Custom Hook`을 만든다.
- 파라미터로 무엇을 받을지, 어떤 것을 반환할지 개발자가 직접 정할 수 있다. 
- `Component` 명은 꼭 `use`로 시작하도록 하여 해당 `Component`에서 `Hook`을 호출한다는 것을 명시해야 한다.

```jsx
function UserStatus(props) {
    const [isOnline, setIsOnline] = useState(null);
    
    useEffect(() => {
    	function handleStatusChange(status) {
            setIsOnline(status.isOnline);
        }
        
	    ServerAPI.subscribeUserStatus(props.user.id, handleStatusChange);
	    return () => {
    	    ServerAPI.unsubscribeUserStatus(props.user.id, handleStatusChange);
        };
    });

    if (isOnline === null) {
    	return '대기중...';
    }
    return isOnline ? '온라인' : '오프라인';
}
```
- 상기 코드에 `Custom Hook`을 적용하고자 한다. 
- `isOnline`이라는 `State`에 따라 사용자의 온라인 여부를 텍스트로 보여주는 `Component`이다.

```jsx
function useUserStatus(userId) {
    const [isOnline, setIsOnline] = useState(null);
    
    useEffect(() => {
    	function handleStatusChange(status) {
        	setIsOnline(status.isOnline);
        }
        
    ServerAPI.subscribeUserStatus(props.user.id, handleStatusChange);
	    return () => {
    	    ServerAPI.unsubscribeUserStatus(props.user.id, handleStatusChange);
        };
    });

    return isOnline;
}
```
- `State`와 관련된 중복 로직을 `useUserStatus`라는 `Costom Hook`으로 추출해낸 것이다.
- 이제 `UserStatus`와 `UserListItem`에서 `Custom Hook`을 적용하면 된다.

```jsx
// UserStatus.jsx
function UserStatus(props) {
    const isOnline = useUserStatus(props.user.id);
    
    if (isOnline === null) {
    	return '대기중...';
    }
    return isOnline ? '온라인' : '오프라인';
}

// UserListItem.jsx
function UserListItem(props) {
	const isOnline = useUserStatus(props.user.id);
	
	return (
		<li style={{ color : isOnline ? 'green' : 'black'}}>
			{props.user.name}
		</li>
	);
}
```
- 위처럼 같은 `Custom Hook`을 사용하는 두 개의 `Component`가 `State`를 공유하는 것은 아니다. 
- 단순히 `State` 연관 로직을 재사용이 가능하게 만든 것 뿐이다. 
- 여러 개의 `Component`에서 하나의 `Custom Hook`을 사용하더라도 `Component` 내부의 `State`나 `effects`는 전부 분리되어 있다.

```jsx
const userList = [
    { id : 1, name : 'Inje' },
    { id : 2, name : 'Mike' },
    { id : 3, name : 'Steve' }
];

function ChatUserSelector(props) {
    const [userId, setUserId] = userState(1);
    const isUserOnline = useUserStatus(userId);
	    
    return (
	    <div>
		    <Circle color={isUserOnline ? 'green' : 'red'}/>
		    <select
			    value={userId}
			    onChange={event => setUserId(Number(event.target.value))}>
	            {userList.map(user => (
	                <option key={user.id} value={user.id}>
	                    {user.name}
	                </option>
	            ))}
	        </select>
	    </div>
    );
}
```
- `Hook` 간의 데이터를 공유하는 예제 코드이다.
- `userId`가 변경될 때마다 `setUserId()` 함수가 호출되고, `useUserStatus` `Hook`은 이전에 선택된 사용자를 구독 취소하고 새로 선택된 사용자의 온라인 여부를 구독한다.


### 실습
```jsx
import React, { useEffect, useState } from "react";

const MAX_CAPACITY = 10;
const buttonStyle = {
    padding: "8px 16px",
    marginRight: "8px",
    backgroundColor: "#4CAF50",
    color: "white",
    border: "none",
    borderRadius: "4px",
    cursor: "pointer",
    fontSize: "14px",
};

  

const disabledButtonStyle = {
    ...buttonStyle,
    backgroundColor: "#ccc",
    cursor: "not-allowed",
};

  

function UseCounter(initialValue) {
    const [count, setCount] = useState(initialValue);
    const increaseCount = () => setCount((count) => count + 1);
    const decreaseCount = () => setCount((count) => Math.max(count - 1, 0));
    
    return [count, increaseCount, decreaseCount];
}

  

function ManageTeamMembers(props) {
    const [isFull, setIsFull] = useState(false);
    const [count , increaseCount, decreaseCount] = UseCounter(0);

    useEffect(() => {
        console.log("useEffect() 메소드가 호출되었습니다.");
        console.log(`정원 도달여부 ${isFull}`);
    });

    useEffect(() => {
        setIsFull(count >= MAX_CAPACITY);
        console.log(`최근 개수: ${count}`);
    }, [count]);

    return (
        <div style={{ padding : 16 }}>
            <p>{`팀에 가입한 멤버는 총 ${count}명 입니다.`}</p>
            <button onClick={increaseCount} style={isFull ? disabledButtonStyle : buttonStyle} disabled={isFull}>가입</button>
            <button onClick={decreaseCount} style={buttonStyle}>탈퇴</button>

            {isFull && <p style={{ color : "red" }}>정원이 가득찼습니다.</p>}
        </div>
    );
}

export default ManageTeamMembers;
```
- `useEffect()`를 선언하여 `Team` `Component`가 렌더링 될 때마다 `useEffect()` 메서드의 호출 여부와 팀의 멤버 정원 도달 여부를 콘솔 로그에 찍는다. 
- `count` 상태가 변할 때마다 최근 개수를 콘솔 로그에 찍는다.
- 가입 버튼을 클릭하면 멤버의 `count` 수가 증가하며, 정원이 꽉 찼을 경우 비활성화된다.
- 탈퇴 버튼을 클릭하면 멤버의 `count` 수가 감소한다.
- 또한 정원이 꽉 찰 경우 빨간 글씨로 "정원이 가득 찼습니다." 문구가 노출된다.

![Pasted_image_20250525230218.png](/assets/image/Pasted_image_20250525230218.png)
![Pasted_image_20250525230438.png](/assets/image/Pasted_image_20250525230438.png)


### 회고
- `Hook`은 굉장히 중요하고도 복잡한 기능이어서 사실 완벽히 숙지하지는 못했다.
- 반복해가며 부족한 부분은 공부 해야겠다는 생각이 들었다.