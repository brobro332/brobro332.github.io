---
title: 🌌 React 입문 Ⅺ - List와 Key
date: 2025-03-08 13:31:42 +0900
categories:
  - React
tags:
  - React
  - 입문
  - Javascript
---
![](/assets/image/Pasted%20image%2020250522211144.png)
> 📘 `『소플의 처음 만난 리액트』`를 읽고 정리한 글입니다.

### `List`와 `Key`
- `List`는 같은 유형의 아이템을 순서대로 모아 놓은 것이다.
- `List`를 구현하기 위해 `Javascript`의 변수나 객체를 하나의 변수로 묶어 놓은 것을 배열이라고 한다.
- `Key`는 각 객체나 아이템을 구분할 수 있는 고유한 값을 의미한다.

### `map()`
```jsx
function NumberList(props) {
    const { numbers } = props;
    
    const listItems = numbers.map((number) => 
        <li>{number}</li>
    );
    
    return (
    	<ul>{listItems}</ul>
    );
}
```
- 상기 코드를 실행하면 `List` 아이템에는 무조건 `Key`가 있어야 한다는 경고 문구가 나온다.

```jsx
// id 사용
const listItems = numbers.map((number) => 
    <li key={number.id}>{number}</li>
);
    
// map()의 index 사용    
const listItems = numbers.map((number, index) => 
    <li key={index}>{number}</li>
);
```
- `React`에서는 `Key`를 넣어주지 않으면 기본적으로 `index` 값을 `Key`로 사용한다. 
- `index`를 `Key`로 사용하면 아이템의 순서가 바뀔 경우 성능에 영향을 미치고 `Component`의 상태 관련 문제를 일으킬 수도 있으므로 각 요소의 식별 가능한 `ID`를 `Key`로 사용하는 것이 올바르다.


### 실습
```jsx
return (
    <TableBody>
        {currentPageData.map((member) => (
            <TableRow key={member.email} sx={{ height: "30px" }}>
                <TableCell sx={{ paddingBottom: "5px", paddingTop: "5px" }}>
                    {member.leader === member.email ? "👑 " : ""}
                    {member.name}
                </TableCell>
                <TableCell sx={{ paddingBottom: "5px", paddingTop: "5px" }}>
                    {member.email}
                </TableCell>
                <TableCell sx={{ paddingBottom: "5px", paddingTop: "5px" }}>
                    {member.description}
                </TableCell>
                <TableCell sx={{ paddingBottom: "5px", paddingTop: "5px" }}>
                    <IconButton
                        color="inherit"
                        onClick={(event) => handleMenuOpen(event, member)}
                    >
                        <MoreVertIcon />
                    </IconButton>
                    {/* 드롭다운 메뉴 */}
                    <Menu
                        anchorEl={anchorEl}
                        open={selectedRow === member}
                        onClose={handleMenuClose}
                        anchorOrigin={{
                            vertical: "top",
                            horizontal: "right",
                        }}
                        transformOrigin={{
                            vertical: "top",
                            horizontal: "right",
                        }}
                    >
                        <MenuItem onClick={() => updateWorkspace(member)}>
                            리더 지정
                        </MenuItem>
                        <MenuItem onClick={() => removeMemberFromWorkspace(member)}>
                            추방
                        </MenuItem>
                    </Menu>
                </TableCell>
            </TableRow>
        ))}
    </TableBody>
);
```
- 책에서 제공하는 실습 코드가 아니라 필자의 토이 프로젝트에서 가져온 코드 일부분이다.
- 성능 문제를 방지하기 위해 키를 `index`가 아닌 각 멤버의 이메일로 두었다.


### 회고
- `map()` 함수를 사용할 땐 `Element`에 `Key` 값을 넣어주어야 한다는 점을 알게 되었다.