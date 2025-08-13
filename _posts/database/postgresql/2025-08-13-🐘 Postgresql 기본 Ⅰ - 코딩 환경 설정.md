---
title: 🐘 Postgresql 기본 Ⅰ - 코딩 환경 설정
date: 2025-08-13 14:01:00 +0900
categories:
  - Database
tags:
  - Database
  - Postgresql
---

![](/assets/image/Pasted%20image%2020250813142544.png)
> 📙 `『실용 SQL』`을 읽고 정리한 글입니다.

### 개요
_"시작이 좋아야 끝도 좋다."_

- 책에 있는 실습을 진행하기 위해서는 필요한 프로그램과 리소스부터 설치해야 한다.
- 생략하고 싶더라도 직접 프로그램을 설치하고 설정하는 과정은 겪어보아야 한다.

### 실습에 필요한 프로그램과 리소스
1. 텍스트 편집기
	- `VSC` / `Sublime Text` / `Notepad++` / `vim` / `GNU nano` 중 하나 선택
	- `vim`, `GNU nano`는 `macOS`와 `Linux`에 기본 설치된 텍스트 편집기다.
2. 코드 및 데이터
	- 저자: `https://github.com/authonydb/practical-sql-2`
	- 역자: `https://github.com/TeeDDub/practical-sql`
	- 저자와 역자의 깃허브 중 한 곳에 접속하여 다운로드 받으면 된다.
3. `PostgreSQL`과 `pgAdmin`
	- `Windows`의 경우 `EDB`에서 제공하는 설치 프로그램을 사용하는 것이 권장됨
	- `https://www.postgresql.org/download/windows`
	- `PostgreSQL` 패키지 번들을 다운로드하면 `pgAdmin`과 몇 가지 도구가 포함된 `Stack Builder`도 함께 설치된다.
	- 참고로 필자는 이미 `DB` 환경을 설치해본 경험이 있으므로 이번에는 `PostgreSQL`을 `Docker` 컨테이너로 띄우고, `DBeaver`를 사용할 계획이다.
4. `Python` 언어
	 - 17장에서는 `PostgreSQL`과 함께 `Python` 프로그래밍 언어를 사용하는 방법을 배운다.
	 - `Python` 다운로드 및 시스템 환경 변수 설정이 필요하다.