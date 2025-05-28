---
title: 🌌 React 입문 Ⅶ - React Component의 생명주기
date: 2024-11-17 20:32:10 +0900
categories:
  - React
tags:
  - React
  - 입문
  - Javascript
---
![](/assets/image/Pasted%20image%2020250522211144.png)
> 📘 `『소플의 처음 만난 리액트』`를 읽고 정리한 글입니다.


### `React Component`의 생명주기란?
- `React Component`가 생성되고, 렌더링 되고, 화면에서 사라지는 일련의 과정을 말한다.


### `React Component`의 생명주기 종류
#### ✅ `Mount`
- `Component`의 생성자가 실행되는 과정이다. 
- 생성자에서는 `State`를 정의하고 `Component`가 렌더링 된다.
- `Class Component`에서는 `componentDidMount()` 함수가 호출된다.  

#### ✅ `Update`
- `Component`의  `props`가 변경되거나 `State`가 변경될 경우, 또는 `forceUpdate()` 함수를 통해 강제로 `Update`될 때 `Component`가 다시 렌더링 된다.
- 이때 `Class Component`에서는 `componentDidUpdate()` 함수가 호출된다.

#### ✅ `Unmount`
- 현재 `Component`를 더 이상 화면에 표시하지 않을 때 `Unmount` 된다고 할 수 있다. 
- `Unmount`하려면 현재 렌더링 하고 있는 `Component`의 상태 값을 빈 값으로 수정해야 한다.
- `Class Component`에서는 `componentWillUnmount()` 함수가 호출된다.


### 실습
```jsx
import React from "react";

const style = {
    container: {
        display: "flex",
        flexDirection: "column",
        alignItems: "center",
        justifyContent: "flex-start",
        gap: 24,
        padding: "60px 20px",
        background: "linear-gradient(135deg, #fdfbfb 0%, #ebedee 100%)",
        minHeight: "100vh",
        fontFamily: "'Segoe UI', Tahoma, Geneva, Verdana, sans-serif",
        color: "#2c3e50"
    }
};

class Alert extends React.Component {
    constructor(props) {
        super(props);

        this.state = {};

        this.style = {
            wrapper: {
                width: 800,
                height: 130,
                border: "0",
                borderRadius: 25,
                display: "flex",
                alignItems: "center",
                justifyContent: "center",
                backgroundColor: "#ffffff",
                boxShadow: "0 4px 12px rgba(0, 0, 0, 0.1)",
                fontSize: 18,
                fontWeight: "500"
            }
        };
    }

    componentDidMount() {
        console.log(`${this.props.id} componentDidMount() called!`);
    }

    componentDidUpdate() {
        console.log(`${this.props.id} componentDidUpdate() called!`);
    }

    componentWillUnmount() {
        console.log(`${this.props.id} componentWillUnmount() called!`);
    }

    render() {
        return (
            <div style={this.style.wrapper}>
                <span>{this.props.message}</span>
            </div>
        );
    }
}

const reservedAlerts = [
    {
        id: 1,
        message: "사이트에 글이 업로드 되었습니다."
    }, 
    {
        id: 2,
        message: "점심 식사 시간입니다."
    },
    {
        id: 3,
        message: "곧 회의가 시작됩니다."
    }
];

var timer;

class AlertList extends React.Component {
    constructor(props) {
        super(props);

        this.state = {
            alerts: []
        };
    }

    componentDidMount() {
        const { alerts } = this.state;
        timer = setInterval(() => {
            if (alerts.length < reservedAlerts.length) {
                const index = alerts.length;
                alerts.push(reservedAlerts[index]);
                this.setState({
                    alerts: alerts
                });
            } else {
                this.setState({
                    alerts: []
                });
                clearInterval(timer);
            }
        }, 1000);
    }

    componentWillUnmount() {
        if (timer) {
            clearInterval(timer);
        }
    }

    render() {
        return (
            <div style={style.container}>
                {this.state.alerts.map((alert) => {
                    return (
                        <Alert 
                            key={alert.id}
                            id={alert.id}
                            message={alert.message}
                        />
                    );
                })}
            </div>
        );
    }
}

export default AlertList;
```
- 알림을 순차적으로 렌더링 하고, 알림이 모두 렌더링 되었을 경우 화면을 비운다.
- 생성자에서는 `props`를 `React.Component` `Class`에 전달하고, `alerts` `State`를 선언한다.
- 해당 `Component`가 `Mount` 될 때 1초마다 갱신 된 알림 목록의 `State` 값을 변경해 준다. 
- `State` 값을 변경함으로써 `rendor()` 함수가 실행되어 다시 렌더링 되는 것이다. 
- 알림 목록이 모두 렌더링 되었을 때, `State` 값에 빈 값을 넣어서 화면을 비운다.
- 해당 `Component`가 `Unmount` 될 때 타이머의 `setInterval()` 함수가 중단된다.
- 이번 실습에서는 `index.js`의 `<React.StrictMode></React.StrictMode>` 라인을 제거해야 한다. 
- 해당 코드는 `Side Effect`를 감지하기 위해 일부 생명주기 메서드를 두 번 호출하는데, 개발 환경에서만 동작하고 실제 운영 환경에서는 동작하지 않는다고 한다. 

![](/assets/image/Pasted%20image%2020250525173138.png)
- 순차적으로 알림이 추가되고, 마지막에 화면을 비운다.

![](/assets/image/Pasted%20image%2020250525214253.png)
1. 첫 번째 요소 `Mount`
2. 첫 번째 요소 `Update`, 두 번째 요소 `Mount`
3. 첫 번째, 두 번째 요소 `Update`, 세 번째 요소 `Mount`
4. 첫 번째, 두 번째, 세 번째 요소 모두 `Unmount`


### 회고
- `Class Component`를 통해 `React Component`의 생명주기를 이해 할 수 있었다.
- `Functional Component`에서는 `Hook`을 이용해 생명주기 함수가 호출된다고 한다.