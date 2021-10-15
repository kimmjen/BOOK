# JPA의 시작(pdf. 63)
- 2.1 이클립스 설치와 프로젝트 불러오기
- 2.2 H2 데이터 베이스 설치
- 2.3 라이브러리와 프로젝트 구조
- 2.4 객체 매핑 시작
- 2.5 persistence.xml 설정
- 2.6 애플리케이션 개발
- 2.7 정리

## 2.1 이클립스 설치와 프로젝트 불러오기
## 2.2 H2 데이터 베이스 설치
- 드라이버 클래스: org.h2.Driver
- JDBC URL: jdbc:h2:tcp://localhost/~/test
- 사용자명: sa
- 비밀번호:

`회원테이블`
```sql
CREATE TABLE MEMBER (
    ID VARCHAR(255) NOT NULL, -- 아이디(기본 키)
    NAME VARCHAR(255),        -- 이름
    AGE INTEGER NOT NULL,     -- 나이
    PRIMARY KEY (ID)
)
```
## 2.3 라이브러리와 프로젝트 구조
## 2.4 객체 매핑 시작
## 2.5 persistence.xml 설정
## 2.6 애플리케이션 개발
## 2.7 정리
