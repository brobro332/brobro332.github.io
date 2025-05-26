---
title: 🌌 React 입문 XVI - Styled-Component
date: 2025-04-06 22:32:12 +0900
categories:
  - React
tags:
  - React
  - 입문
  - Javascript
---
![Pasted_image_20250522211144.png](/assets/image/Pasted_image_20250522211144.png)
> 📘 `『소플의 처음 만난 리액트』`를 읽고 정리한 글입니다.

### `Styled-Component`
```bash
npm install --save styled-components
```
- `CSS` 문법을 그대로 사용하면서 결과물을 스타일 적으로 다듬어진 `Component` 형태로 만들어 주는 오픈소스 라이브러리다.
- `Component` 개념을 사용하므로 `React`와 궁합이 잘 맞다. 


### 기본적인 사용법
```jsx
const name = '이름';
const region = '경기도';

function myTagFunction(strings, nameExp, regionExp) {
    let str0 = strings[0];  
    let str1 = strings[1];  
    let str2 = strings[2];  
		
    return `${str0}${nameExp}${str1}${regionExp}${str2}`;
}

const output = myTagFunction`제 이름은 ${name}이고, 사는 곳은 ${region}입니다.`

// 출력 결과 : 제 이름은 이름이고, 사는 곳은 경기도입니다.
console.log(output)
```
- `Styled-Components`는 `Tagged-Template-Literals`을 사용하여 구성 요소의 스타일을 지정한다.
- `Template Literal`은 `Javascript`에서 제공하는 문법 중 하나고, `Literal`은 소스 코드의 고정된 값을 의미한다.
- `Template Literal`은 다음과 같이 백틱을 사용하여 문자열을 작성하고 그 안에 대체 가능한 `Expression`을 넣는 방법이다.

```jsx
import React from "react";
import styled from 'styled-components'

const Wrapper = styled.div`
    padding: 1em;
    background: grey;
`;
```


### `props` 사용
```jsx
const Button = styled.button`
    color: ${props => props.dark ? "white" : "dark"};
    background: ${props => props.dark ? "black" : "white"};
    border: 1px solid black;
`;

function Sample(props) {
    return (
        <div>
            <Button>Normal</Button>
            <Button dark>Dark</Button>
        </div>
    )
}

export default Sample;
```
- `<Button dark>Dark</Button>`에서 `props`로 `dark`를 넣어 주고 있다.
- 이렇게 들어간 `props`는 그대로 `Styled-Components`로 전달된다.


### `Styled-Components` 스타일 확장
```jsx
const Button = styled.button`
    color: ${props => props.dark ? "white" : "dark"};
    background: ${props => props.dark ? "black" : "white"};
    border: 1px solid black;
`;

const RoundButton = styled(Button)`
    border-radius: 16px;
`;

function Sample(props) {
    return (
        <div>
            <Button>Normal</Button>
            <Button dark>Dark</Button>
            <RoundButton>Rounded</RoundButton>
        </div>
    )
}

export default Sample;
```
- `Styled-Components`를 사용하면 `React Component`가 생성되는데, 이렇게 생성된 `Component`를 기반으로 상기 코드와 같이 추가적인 스타일을 적용할 수 있다.

```jsx
const RoundButton = styled(Button)`
    border-radius: 16px;
`;
```
- 상기 코드가 스타일을 확장하는 핵심 코드다.


### 실습
```jsx
// Blocks.jsx
const Wrapper = styled.div`
    padding : 1rem;
    display : flex;
    flex-direction : row;
    align-items : flex-start;
    justify-content : flex-start;
    background-color : lightgrey;
`;

const Block = styled.div`
    padding : ${(props) => props.padding};
    border : 1px solid black;
    border-radius : 1rem;
    background-color : ${(props) => props.backgroundColor};
    color : white;
    font-size : 2rem;
    font-weight : bold;
    text-align : center;
`;

const blockItems = [
    {
        label: "1",
        padding: "1rem",
        backgroundColor: "red",
    },
    {
        label: "2",
        padding: "3rem",
        backgroundColor: "green",
    },
    {
        label: "3",
        padding: "2rem",
        backgroundColor: "blue",
    },
];

function Blocks(props) {
    return (
        <Wrapper>
            {blockItems.map((blockItem) => {
                return (
                    <Block
                        padding={blockItem.padding}
                        backgroundColor={blockItem.backgroundColor}
                    >
                        {blockItem.label}
                    </Block>
                );
            })}
        </Wrapper>
    );
}

export default Blocks;
```


### 회고
- 일단 책 읽은 부분을 모두 정리하였지만, 아직 많이 부족하다.
- 토이 프로젝트를 통해 배운 부분들을 적용하고 이해하는 과정이 필요하다.