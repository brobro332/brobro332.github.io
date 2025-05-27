---
title: 🌌 React 입문 XV -  Context
date: 2025-04-06 22:20:53 +0900
categories:
  - React
tags:
  - React
  - 입문
  - Javascript
---
![](/assets/image/Pasted%20image%2020250522211144.png)
> 📘 `『소플의 처음 만난 리액트』`를 읽고 정리한 글입니다.

### `Context`
- `Context`는 기존의 `props`를 통해 `React Component`들 사이에서 데이터를 전달하는 방식 대신 `Component` 트리를 통해 곧바로 `Component`에 전달하는 새로운 방식을 제공한다.
- 기존 방식의 경우 하위 `Component`로 데이터를 전달하려면 트리를 타고 몇 번을 내려가며 전달을 해야하지만, `Context`를 사용한다면 데이터를 필요로 하는 `Component`에 곧바로 데이터를 전달할 수 있다.

```jsx
function App(props) {
	return <Toolbar theme="dark"/>;
}

function Toolbar(props) {
    return (
    	<div>
        	<ThemeButton theme={props.theme}/>
        </div>
    );
}

function ThemeButton(props) {
	return <Button theme={props.theme}/>;
}
```
- `Context`를 적용하지 않은 코드이다.
- 이렇게 처리하면 반복된 코드를 계속 작성해주어야 하기 때문에 비효율적이고 직관적이지도 않다.

```jsx
const ThemeContext = React.createContext('light');

function App(props) {
    return (
    	<ThemeContext.Provider value="dark">
        	<Toolbar/>
        </ThemeContext.Provider>
    );
}

function Toolbar(props) {
    return (
    	<div>
        	<ThemeButton />
        </div>
    );
}

function ThemeButton(props) {
    return (
    	<ThemeContext.Consumer>
        	{value => <Button theme={value}/>}
        </ThemeContext.Consumer>
    );
}
```
상위 `Context`를 `Provider`로 감싸면 모든 하위 `Context`가 얼마나 깊이 위치해 있는지 관계없이 `Context`의 데이터를 읽을 수 있다.


### `Context` 사용 시 주의할 점
- 무조건 `Context`를 사용하는 것은 옳지 않다.
- `Component`와 `Context`가 연동되면 재사용성이 떨어지기 때문이다.
- 다른 레벨의 많은 `Component`가 데이터를 필요로 하는 경우가 아니라면 기존에 사용하던 방식대로 `props`를 통해 데이터를 전달하는 `Component` `Composition`방식이 더 적합하다.


### `Context` 대신 `Element` 변수 형태
```jsx
function Page(props) {
    const user = props.user;
    
    const userLink = (
    	<Link href={user.peralink}>
        	<Avatar user={user} size={props.avatarSize}/>
        </Link>
    );
    
    return <PageLayout userLink={userLink}/>
}

// NavigationBar 컴포넌트를 렌더링
<PageLayout userLink={...}/>

// props로 전달받은 userLink 엘리먼트를 반환
<NavigationBar userLink={...}/>
```
- 최상위 `Component`에서 `Avatar` `Component`를 변수에 저장하여 직접 넘겨주면 중간 `Component`들은 `user`와 `avatarSize`에 대해 몰라도 된다.
- 이는 최상위 `Component`에 좀 더 많은 권한을 부여해준다. 
- 그러나 데이터가 많아질수록 상위 `Component`에 코드가 몰리기 때문에 복잡해지고, 하위 `Component`는 너무 유연해진다는 단점이 있다.

```jsx
function Page(props) {
    const user = props.user;
	 
    const topBar = (
    	<NavigationBar>
        	<Link href={user.peralink}>
            	<Avatar user={user} size={props.avatarSize}/>
        	</Link>
    	</NavigationBar>
    );
    
    const content = <Feed user={user}>
    
    return (
    	<PageLayout
        	topBar={topBar}
            content={content}
        />
    );
}
```
이 방식은 하위 `Component`의 의존성을 상위 `Component`와 분리할 필요가 있는 대부분의 경우에 적합한 방법이다.


### `Context API` 종류
#### ✅ `React.createContext`
```jsx
const MyContext = React.createContext(기본값);
```
- `React`에서 렌더링이 일어날 때 `Context` 객체를 구독하는 하위 `Component`가 나오면 현재 `Context` 값을 가장 가까이에 있는 상위 레벨의 `Provider`로부터 받아오게 된다.
- 상위 레벨에 매칭되는 `Provider`가 없다면 이 경우에만 기본 값이 적용된다.
- 기본 값에 `undefined`를 넣으면 기본 값이 사용되지 않는다.

#### ✅ `Context.Provider`
```jsx
<MyContext.Provider value={/* 특정값 */}/>
```
- `Context`를 만들었다면 하위 `Component`들이 해당 `Context`의 데이터를 받을 수 있도록 설정해 줘야 한다.
- 모든 `Component`는 `Provider`라는 `React Component`를 갖고 있다.
- Context.Provider로 상위 `Component`를 감싸면 모든 하위 `Component`들이 해당 `Context` 데이터에 접근할 수 있게 되는데, 이러한 하위 `Component`들을 `Consumer` `Component`라고 부른다.
- 모든 Consumer `Component`은 `Provider`의 `value prop`이 바뀔 때마다 렌더링된다. 
- 값이 변경되었을 때 상위 `Component`가 `Update` 대상이 아니더라도 하위 `Component`가 `Context`를 사용한다면 하위 `Component`에서는 `Update`가 일어난다.
- 값의 변화를 판단하는 기준은 `Object.is()`라는 함수와 같은 방식으로 판단한다.

#### ✅ `Class.contextType`
```jsx
MyClass.contextType = MyContext;
```
- `Provider` 하위에 있는 `Class Component`에서 `Context`의 데이터에 접근하기 위해 사용하는 것이다.
- `Class Component`는 현재 거의 사용하지 않기 때문에 참고만 하면 된다.
- 상기 코드와 같이 작성하면 `MyClass` `Component`는 `MyContext`의 데이터에 접근할 수 있게 된다.
- 이 `API`는 단 하나의 `Context`만을 구독할 수 있다.

#### ✅ `Context.Consumer`
```jsx
<MyContext.Consumer>
    {value => /* 컨텍스트 값에 따라서 컴포넌트 렌더링 */}
</MyContext.Consumer>
```
- `Context`의 데이터를 구독하는 `Component`이다.
- `Component`의 자식으로 함수가 올 수 있는데 이것을 `function as a child`라고 한다. 
- `Context.Consumer`로 감싸면 자식으로 들어간 함수가 현재 `Context`의 `value`를 받아서 `React` 노드로 반환한다.

#### ✅ `Context.displayName`
```jsx
const MyContext = React.createContext(/* some value*/);

// 개발자 도구에 MyDisplayName.Provider로 표시됨
<MyContext.Provider/>

// 개발자 도구에 MyDisplayName.Consumer로 표시됨
<MyContext.Consumer/>
```
- `Context` 객체는 `displayName`이라는 문자열 속성을 가진다.
- `Chrome`의 개발자 도구에서는 이 `displayName`를 같이 표시해준다.

#### ✅ 여러 `Context` 사용
```jsx
const ThemeContext = React.createContext('light');
const UserContext = React.createContext({
    name: 'Guest',
});

class App extends React.Component {
    render() {
        const { signedInUser, theme } = this.props;
			
        return (
            <ThemeContext.Provider value={theme}>
                <UserContext.Provider value={signedInUser}>
                    <Layout />
                </UserContext.Provider>
            </ThemeContext.Provider>
        );
    }
}

function Layout() {
    return (
        <div>
            <Sidebar />
            <Content />
        </div>
    )
}

function Content() {
    return (
        <ThemeContext.Consumer>
            {theme => (
                <UserContext.Consumer>
                    {user => (
                        <ProfilePage user={user} theme={theme} />
                    )}
                </UserContext.Consumer>
            )}
        </ThemeContext.Consumer>
    );
}
```
- 여러 개의 `Context`를 동시에 사용하려면 `Context.Provider`를 중첩 해서 사용하여 구현할 수 있다.
- 하지만 두 개 또는 그 이상의 `Context` 값이 함께 사용될 경우 모든 값을 한 번에 제공해주는 별도의 `render prop` `Component`를 직접 만드는 것을 고려하는 것이 좋다.

#### ✅ `useContext`
```jsx
function MyComponent(props) {
    const value = useContext(MyContext);
	    
    return (
    	// ...
    );
}

// 올바른 사용법
useContext(MyContext);

// 잘못된 사용법
useContext(MyContext.Consumer);
useContext(MyContext.Provider);
```
- `Functional Component`에서는 `Context`를 사용하기 위해 `Component`를 매번 `Consumer` `Component`로 감싸주는 것보다 더 좋은 방법이 있다. 
- 바로 `useContext()` `Hook`이다.
- 해당 `Hook`은 `React.createContext()` 함수 호출로 생성된 `Context` 객체를 인자로 받아서 현재 `Context`의 값을 반환한다.
- `useContext()` `Hook`은 다른 방식과 동일하게 `Component` 트리 상에서 가장 가까운 상위 `Provider`로부터 `Context`의 값을 받아오게 된다. 
- 만약 `Context`의 값이 변경되면 변경된 값과 함께 `useContext()` `Hook`을 사용하는 `Component`가 렌더링 된다. 
- 그러므로 해당 `Hook`을 사용하는 `Component`의 렌더링이 무거운 작업일 경우에는 별도로 최적화 작업을 해주어야 한다.
- `useContext()` `Hook`을 사용할 때에는 파라미터로 `Context` 객체를 넣어줘야 한다.


### 실습
```jsx
// ThemeContext.jsx
const ThemeContext = React.createContext();

ThemeContext.displayName = "ThemeContext";

export default ThemeContext;

// MainContent.jsx
function MainContent(props) {
	const { theme, toggleTheme } = useContext(ThemeContext);
		
	return (
		<div
			style={{
				width: "100vw",
				height: "100vh",
				padding: "1.5rem",
				backgroundColor: theme == "light" ? "white" : "black",
				color: theme == "light" ? "black" : "white",
			}}>	
			<p>안녕하세요, 테마 변경이 가능한 웹사이트 입니다.</p>
			<button onClick={toggleTheme}>테마 변경</button>
		</div>
	);
}

export default MainContent;

// DarkOrLight.jsx
function DarkOrLight(props){
	const [theme, setTheme] = useState("light")
	
	const toggleTheme = useCallback(() => {
		if (theme == "light") {
			setTheme("dark");
		} else if (theme == "dark") {
			setTheme("light");
		}
	},[theme]);
			
	return (
		<ThemeContext.Provider value={/*{theme, toggleTheme}*/}>
			<MainContent />
		</ThemeContext.Provider>
	);
}

export default DarkOrLight;
```
- `Context API`를 이용한 전역 상태 관리를 수행한다.


### 회고
- 여러 `Component`에서 공통으로 사용하는 데이터는 `Context`로 관리하면 `props drilling` 없이 깔끔하게 해결 가능하다는 점을 알게 되었다.