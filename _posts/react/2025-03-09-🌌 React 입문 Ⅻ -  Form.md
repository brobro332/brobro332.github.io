---
title: 🌌 React 입문 Ⅻ -  Form
date: 2025-03-09 00:08:13 +0900
categories:
  - React
tags:
  - React
  - 입문
  - Javascript
---
![Pasted_image_20250522211144.png](/assets/image/Pasted_image_20250522211144.png)
> 📘 `『소플의 처음 만난 리액트』`를 읽고 정리한 글입니다.

### `Form`이란?
- `Form`은 사용자로부터 입력을 받기 위해 사용한다.
- `HTML`의 `Form`은 `Element` 내부에 각각의 상태가 존재한다.
- 반면 `React`의 `Form`은 `Component` 내부에서 `State`를 통해 데이터를 관리한다.

#### ✅ `HTML`의 `Form`
```html
<form>
    <label>
    	이름 :
        <input type="text" name="name"/>
    </label>
    <button type="submit">제출</button>
</form>
```
- 상기 코드는 `HTML`의 `Form`이다.
- `React`에서도 작동은 하지만 `Javascript` 코드를 통해 사용자가 입력한 값에 접근하기는 불편한 구조이다.

#### ✅ `React`의 제어 `Component`
```jsx
function NameForm(props) {
	const [value, setValue] = useState('');
	
	const handleChange = (event) => {
		setValue(event.target.value);
	}
	
	const handleSubmit = (event) => {
		alert('입력한 이름 : ' + value);
		event.preventDefault();
	}
	
	return (
		<form onSubmit={handleSubmit}>
			<label>
				이름 :
				<input type="text" value={value} onChange={handleChange}/>
			</label>
			<button type="submit">제출</button>
		</form>
	);
}
```
- 제어 `Component`란 `React`의 통제를 받으며, 사용자 입력에 대한 접근 및 제어를 처리하는 입력 `Form` `Element`이다.
- 상기 코드처럼 제어 `Component`를 사용하면 입력 값이 `React Component` `State`를 통해 관리되어 입력값을 원하는 대로 조종할 수 있게 된다.
- 예를 들어 사용자가 입력한 모든 알파벳을 대문자로 변경해 관리하고 싶다면 다음 코드와 같이 작성하면 된다.

```jsx
const handleChange = (event) => {
	setValue(event.target.value.toUpperCase());
}
```


### `textarea`
```jsx
function RequestForm(props) {
	const [value, setValue] = useState('요청사항을 입력하세요.');
	
	const handleChange = (event) => {
		setValue(event.target.value);
	}
	
	const handleSubmit = (event) => {
	alert('입력한 요청사항 : ' + value);
		event.preventDefault();
	}
	
	return (
		<form onSubmit={handleSubmit}>
			<label>
				요청사항 :
				<textarea value={value} onChange={handleChnage}/>
			</label>
			<button type="submit">제출</button>
		</form>
	);
}
```
- 상기 코드는 `textarea`에 입력할 때마다 `State`의 값이 바뀌고, 바뀔 때마다 `textarea`의 `value`의 값이 바뀐 채로 다시 렌더링된다.
-  여기에서는 `value` 선언 시 초기 값을 넣어주었기 때문에 처음 렌더링 될 때부터 `textarea`에 텍스트가 나타난다.


### `select`
```jsx
function FruitSelect(props) {
	const [value, setValue] = useState('grape');
	
	const handleChange = (event) => {
		setValue(event.target.value);
	}
	
	const handleSubmit = (even) => {
		alert('선택한 과일 : ');
		event.preventDefault();
	}
	
	return (
		<form onSubmit={handleSubmit}>
			<label>
				과일을 선택하세요 :
				<select value={value} onChange={handleChange}>
					<option value="apple">사과</option>
					<option value="banana">바나나</option>
					<option value="grape">포도</option>
					<option value="watermelon">수박</option> 
				</select>
			</label>
		</form>
	);
}
```
- `select` 태그에서 특정 값을 선택하려면 `value` 속성을 사용하면 된다.
- 다중 선택이 가능하도록 하려면 `multiple={true}`를 `select` 태그에 속성으로 추가하면 된다.

### `input file`
```jsx
<input type="file"/>
```
- 입력 값이 읽기 전용이므로 `React`에서는 비제어 `Component`가 된다.

#### ✅  여러 개의 입력 다루기
	- 하나의 `Component`에서 여러 개의 입력을 다루려면 여러 개의 `State`를 선언하여 각각의 입력에 대해 사용하면 된다.

#### ✅ `input null value`
- 제어 `Component`에 `value` 속성을 정해진 값으로 넣으면 코드를 수정하지 않는 한 입력 값을 바꿀 수 없다.
- 만약 `value` 값은 넣되 자유롭게 입력할 수 있게 만들고 싶다면 값에 `undefined` 또는 `null`을 넣어주면 된다.


### 실습
```jsx
const [email, setEmail] = useState('');
const [name, setName] = useState('');
const [memberList, setMemberList] = useState([]); 
const [page, setPage] = useState(1);

const pageSize = 10;
const startIndex = (page - 1) * pageSize;
const currentPageData = memberList.slice(startIndex, startIndex + pageSize);

return (
    <Box>
      <Box display="flex" gap={2} sx={{ marginBottom:"15px" }}>
        <TextField 
          label="이메일" 
          variant="outlined" 
          size="small" 
          sx={{ flex: 1 }}
          value={email}
          onChange={(e) => setEmail(e.target.value)} 
        />
        <TextField 
          label="이름" 
          variant="outlined" 
          size="small" 
          sx={{ flex: 1 }} 
          value={name}
          onChange={(e) => setName(e.target.value)}
        />
      </Box>
      <Box>
        <TableContainer component={Paper}>
          <Table sx={{ tableLayout: "fixed", width: "100%" }}>
            <TableHead>
              <TableRow>
                <TableCell sx={{ width: "20%" }}>이메일</TableCell>
                <TableCell sx={{ width: "20%" }}>이름</TableCell>
                <TableCell sx={{ width: "30%" }}>소개</TableCell>
                <TableCell sx={{ width: "10%" }}>처리</TableCell>
              </TableRow>
            </TableHead>
            <TableBody>
            {currentPageData.map((member) => (
              <TableRow key={member.id} sx={{ height: "30px" }}>
                <TableCell sx={{ paddingBottom: "5px", paddingTop: "5px" }}>
                  {member.email}
                </TableCell>
                <TableCell sx={{ paddingBottom: "5px", paddingTop: "5px" }}>
                  {member.name}
                </TableCell>
                <TableCell sx={{ paddingBottom: "5px", paddingTop: "5px" }}>
                  {member.description}
                </TableCell>
                <TableCell sx={{ paddingBottom: "5px", paddingTop: "5px" }}>
                  {member.status === null ? (
                    <Button
                      variant="contained"
                      color="primary"
                      onClick={() => createInvitation(member)}
                    >
                      초대
                    </Button>
                  ) : (
                    member.status === 'PENDING' ? (
                      <Button
                        variant="contained"
                        color="error"
                        onClick={() => deleteInvitation(member)}
                      >
                        취소
                      </Button>
                    ) : (
                      member.status === 'ACCEPTED' ? '가입완료' : '거절' 
                    ) 
                  )}
                </TableCell>
              </TableRow>
              ))}
            </TableBody>
          </Table>
          {memberList && (
            <Pagination
              count={Math.ceil(memberList.length / pageSize)}
              page={page}
              color="primary"
              onChange={handlePageChange}
              sx={{ height: "50px", display: "flex", justifyContent: "center" }}
            />
          )}
        </TableContainer>
      </Box>
    </Box>
  );
```
- 책에서 제공하는 실습 코드가 아니라 필자의 토이 프로젝트에서 가져온 코드 일부분이다.
- 입력 `Form`에 문자를 입력할 때마다 `State`를 갱신한다.

### 회고
- `Mui`의 `TextField` `Component`를 사용한 실습 코드인데, 다음에는 직접 `Component`를 만들어 봐야겠다.