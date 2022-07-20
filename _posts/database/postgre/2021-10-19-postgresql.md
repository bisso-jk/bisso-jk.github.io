---
title: PostgreSQL Mac 설치 및 실행
author: Bisso
date: 2021-10-19 18:30:00 +0900
categories: [Install, Postgres]
tags: [writing]
---

# Postgres

## 설치 및 실행

### Mac Install

- Homebrew 설치 후 brew install postgres
- 설치 완료 후, pg_ctl -D /usr/local/var/postgres start && brew services start postgresql
- postgres -V : 버전 확인
- psql postgres 가 잘 실행되는지 확인

### psql 단축 명령어

- \c : 접속한 DB Instance 변경
1. 사용법 \c [DB Name] [Connection User]
- \list(or \l), \d[t | s | f | v | u] : 목록 조회용 명령어
1. \list(or \l) : 전체 Database Instance 목록
2. \dt : 접속한 DB Instance의 Table 목록
3. \ds : Sequence 목록
4. \df : Function 목록
5. \dv : View 목록
6. \du : User 목록
- \d : 특정 테이블의 상세 정보를 출력
1. 사용법 \d [Table Name]
- \g, \s : Command 실행 History 조회 및 활용
1. \g : 방금 전에 실행했던 명령어를 실행. (history 사용 가능할 경우 사용 필요 없음)
2. \s : 이전에 실행했던 명령어 전체 List 조회
- \h, \? : 도움말 조회
1. \h : SQL command 관련 도움말
2. \? : psql command 관련 도움말
- \x, \a, \H : Query 결과 Display 설정
1. \x : Column 들을 한줄로 조회하기 힘들 때, Column을 세로로 배치해서 Display하는 기능 on/off
2. \a : Column Align on/off. Default는 Align On 상태
3. \H : Column 명과 결과 값을 HTML Table 형식으로 Display하는 기능 on/off.
- \timing : Query 실행 시간 표시
- \i : 외부 파일을 통한 Query 실행
1. \i [File Name]
- \e, \ef : 외부 Editor 사용
1. \e : \i가 이미 만들어진 File 안에 있는 Query를 수행하는데 비해, \e는 외부 편집기를 통해 Query를 작성해서 실행할 때 사용. 사용법은 \e [File Name]
2. \ef : \e와 유사하나 FUNCTION 편집할 때 사용한다는 측면에서 상이. 사용법은 \ef [Function Name]
- \! : Shell Command 실행
  사용법 \! [Shell Command]
  ex) \! clear : 화면 clear
  \! pwd : 현재 경로 확인
  \! ls : 현재 경로의 파일 확인
  \! cd [Path] : [Path]로 경로 이동
- \q : psql 종료