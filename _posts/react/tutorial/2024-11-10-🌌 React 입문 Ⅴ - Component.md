---
title: 🌌 React 입문 Ⅴ - Component
date: 2024-11-10 18:32:31 +0900
categories:
  - React
tags:
  - React
  - 입문
  - Javascript
---
![](/assets/image/Pasted%20image%2020250522211144.png)
> 📘 `『소플의 처음 만난 리액트』`를 읽고 정리한 글입니다.


### `Component`란?
- `Component`는 `props`를 통해 속성을 입력하여 `React Element`를 출력하는 `Javascript` 함수로, 애플리케이션을 구성하는 재사용 가능한 부품이라고 할 수 있다.
- 하나의 `Component`는 여러 개의 `Component`로 구성할 수 있다.  
- `Component`로 분리함으로써 코드의 가독성을 확보할 수 있고, 코드 양을 줄임으로써 개발 및 유지 보수 시간을 절감할 수 있다.

### `props`란?
- `Component`는 하나의 객체이며, `props`는 객체의 속성에 해당한다.
- 가령 속성만 다른 `React Element`를 추가하고 싶다면 동일 `Component`를 재사용하여 `props`만 다르게 주입하면 된다.  
- `props`는 읽기 전용이어서 값을 수정할 수 없다.
- `React Element`를 생성하는 도중에 `props`가 바뀌어 버리면 `React`가 제대로 동작하지 못하기 때문에 읽기 전용이라는 특징을 갖는다.  
- 동일 `props`에 대해서는 항상 같은 결과를 보여주어야 한다. 
- 다른 `props`를 가진 `React Element`를 렌더링 하고 싶으면 새로운 값을 `Component`에 전달하면 되는데, 이 과정에서 `React Element`가 렌더링 된다.

```jsx
function App(props) {
	return (
		<Profile
			name="brobro332"
			introduction="안녕하세요!"
			viewCount={20}
		/>
	);
}
```
- `Profile` `Component`에 `name`, `introduction`, `viewCount` 속성을 전달하여 `React Element`를 생성하는 예제이다.
- 문자열 이외의 정수, 변수, `Component` 등을 사용할 경우 중괄호로 감싸야 한다.
- 문자열도 중괄호로 감싸도 상관없다. 
- 중괄호를 사용하면 `Javascript` 코드를 사용한다고 선언하는 것과 같다.


### `Component` 종류
#### ✅ `Class Component`
```jsx
class Welcome extends React.Component {
	render() {
		return <h1>안녕, {this.name}</h1>;
	}
}
```
- `Class Component`는 `Javascript` `ES6`의 `Class`를 사용해서 만들어진 형태이다.
- `React`의 모든 `Class Component`는 `React.Component`를 상속 받아서 만든다.

#### ✅ `Functional Component`
- 초기 버전에는 `Class Component`를 주로 사용하였으나 불편하다는 의견이 다수였다.
- 개선하는 과정에서 `Hook`이라는 것이 개발되었는데 `React`의 주력 기능이기 때문에 앞으로는 주로 `Functional Component`를 사용하게 될 것이다.  


### `Component` 특징
#### ✅ `Component` 이름은 항상 대문자로 시작해야 한다.
- `React`는 소문자로 시작하는 `Component`를 `DOM` 태그로 인식하기 때문이다.
- 예를 들어 `<div>`는 내장 `Component`이며 `"div"` 문자열로 `React.createElement()`에 전달된다.
- `<Foo />`와 같이 대문자로 시작하는 경우는 `"Foo"` 문자열로 `React.createElement()`에 전달되는 건 동일하지만 내장 `Component`가 아닌 `Javascript` 파일 내에서 사용자가 정의했거나 `import`한 `Component`를 가리킨다.

#### ✅ `Component` 렌더링
- `Element` 렌더링과 동일하게 `root.render()`에 인자로 `Component`를 전달하면 된다.

#### ✅ `Component` 합성
- 여러 개의 `Component`를 합쳐서 하나의 `Component`를 만드는 것을 의미한다.

#### ✅ `Component` 추출
- 큰 `Component`에서 일부를 추출해서 새로운 `Component`를 만드는 것이다.
- 기능 단위로 구분하고 나중에 곧바로 재사용할 수 있는 형태로 추출하는 것이 좋다.
- `Component` 내의 반복되거나 기능으로 구분되는 코드를 함수로 추상화하는 것이다.


### 실습
```jsx
const style = {
    wrapper: {
        display: 'flex',
        flexDirection: 'column',
        alignItems: 'center',
        gap: 20,
        padding: 20,
        backgroundColor: '#f4f4f9',
        minHeight: '100vh'
    },
    card: {
        width: 600,
        padding: '20px 30px',
        borderRadius: 20,
        boxShadow: '0 10px 20px rgba(0, 0, 0, 0.1)',
        backgroundColor: '#ffffff',
        transition: 'all 0.3s ease',
        cursor: 'default'
    },
    title: {
        margin: 0,
        fontSize: 20,
        fontWeight: 'bold',
        color: '#2c3e50'
    },
    description: {
        marginTop: 8,
        fontSize: 16,
        color: '#555'
    }
};

function Project(props) {
    return (
        <div style={style.card}>
            <h3 style={style.title}>✅ {props.name}</h3>
            <h4 style={style.description}>{props.description}</h4>
        </div>
    );
}

const projects = [
    {
        name: "java-co-working project",
        description: "협업 자바 프로젝트"
    },
    {
        name: "java-schedule-project",
        description: "스케쥴 자바 프로젝트"
    },
    {
        name: "python-ai-project",
        description: "AI 파이썬 프로젝트"
    }
];

function ProjectList() {
    return (
        <div style={style.wrapper}>
            {
	            projects.map((project) => (
	                <Project
	                    key={project.name}
	                    name={project.name}
	                    description={project.description}
	                />
	            ))
            }
        </div>
    );
}

export default ProjectList;
```
- `ProjectList` `Component` 는 `Project` `Component` 를 자식 `Component` 로 포함하고 있다.

![](/assets/image/Pasted%20image%2020250525164111.png)


### 회고
- `React Component`는 동일한 `props`를 받으면 항상 동일한 결과를 반환해야 하며, 이러한 특성을 `pure`하다고 표현한다.  
- 이는 `React`의 `Element`가 불변 객체이기 때문에 가능한 것이며, 따라서 `Component`의 `props`도 읽기 전용으로 취급된다는 사실을 이해 할 수 있다.