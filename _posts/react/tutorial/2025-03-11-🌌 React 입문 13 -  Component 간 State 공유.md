---
title: 🌌 React 입문 13 -  Component 간 State 공유
date: 2025-03-11 21:26:34 +0900
categories:
  - React
tags:
  - React
  - 입문
  - Javascript
---
![](/assets/image/Pasted%20image%2020250522211144.png)
> 📘 `『소플의 처음 만난 리액트』`를 읽고 정리한 글입니다.

### `Component` 간 `State` 공유란?
- 어떤 `Component`의 `State`를 여러 개의 하위 `Component`에서 공통적으로 사용하는 것을 의미한다.


### 실습
```bash
Calculator.jsx
├── BoilingVerdict.jsx
└── TemperatureInput.jsx
```
- 실습 예제 구조이다.

```jsx
// Calculator.jsx
import React, { useState } from "react";
import BoilingVerdict from "./BoilingVerdict";
import TemperatureInput from "./TemperatureInput";

function toCelsius(fahrenheit) {
	return ((fahrenheit - 32) * 5) / 9;
}

function toFahrenheit(celsius) {
	return (celsius * 9) / 5 + 32;
}

function tryConvert(temperature, convert) {
	const input = parseFloat(temperature);
	if (Number.isNaN(input)) {
		return '';
	}
	
	const output = convert(input);
	const rounded = Math.round(output * 1000) / 1000;
	return rounded.toString();
}

function Calculator(props) {
	const [temperature, setTemperature] = useState('');
	const [scale, setScale] = useState('c');
	
	const handleCelsiusChange = (temperature) => {
	setTemperature(temperature);
		setScale('c');
	};
	
	const handleFahrenheitChange = (temperature) => {
		setTemperature(temperature);
		setScale('f');
	};
	
	const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;
	const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;
	
	return (
		<div>
			<TemperatureInput
				scale="c"
				temperature={celsius}
				onTemperatureChange={handleCelsiusChange}
			/>
			<TemperatureInput
				scale="f"
				temperature={fahrenheit}
				onTemperatureChange={handleFahrenheitChange}
			/>
			<BoilingVerdict celsius={parseFloat(celsius)}/>
		</div>
	);
}

export default Calculator;

// BoilingVerdict.jsx
import React from "react";

function BoilingVerdict(props){
	if (props.celsius >=100){
		return <p>물이 끓습니다.</p>
	}
	
	return <p>물이 끓지 않습니다.</p>
}

export default BoilingVerdict;

// TemperatureInput.jsx
const scaleNames = {
	c : '섭씨',
	f : '화씨'
};

function TemperatureInput(props) {
	const handleChange = (event) => {
		props.onTemperatureChange(event.target.value);
	}
	
	return (
		<fieldset>
			<legend>
				온도를 입력해주세요.(단위:{scaleNames[props.scale]})
			</legend>
			<input value={props.temperature} onChange={handleChange}/>
		</fieldset>
	);
}

export default TemperatureInput;
```
- 부모 `Component`에게서 전달된 `props.onTemperatureChange` 속성을 통해 부모 `Component`에 영향을 주고 있다.
- 이렇게 하위 `Component`의 `State`를 공통된 부모 `Component`로 끌어올려서 공유하는 방식을 `State` 끌어올리기라고 한다.


### 회고
- 공통 `State`를 부모 `Component`에서 관리하는 것은 코드를 효율적으로 관리하는 방법 중 하나다.  
- 꼭 `React`가 아니더라도 어떤 영역이든 공통적으로 관리할 수 있다면 대부분 그렇게 하는 것이 좋은 방법인 것 같다.