# 엔티티 매핑(pdf. 121)

- 4.1 @Entity
- 4.2 @Table
- 4.3 다양한 매핑 사용
- 4.4 데이터 베이스 스키마 자동 생성
- 4.5 DDL 생성 가능
- 4.6 기본 키 매핑
- 4.7 필드와 컬럼 매핑: 레퍼런스
- 4.8 정리
- 실전예제 | 1. 요구사항 분석과 기본 매핑


- 객체와 테이블 매핑: @Entity, @Table
- 기본 키 매핑: @Id
- 필드와 컬럼 매핑: @Column
- 연관관계 매핑: @ManyToOne. @JoinColumn

## 4.1 @Entity

|속성|기능|기본값| 
|---|---|---|
|name|JPA에서 사용할 엔티티 이름을 지정한다<br>보통 기본값인 클래스 이름을 사용한다<br>만약 다른 패키지에 이름이 같은 엔티티 클래스가 있다면 이름을 지정해서 충돌하지 않도록 해야한다.|설정하지 않으면 클래스 이름을 그대로 사용한다.(예. Member)|

@Entity 적용 시 주의사항
- 기본 생성자는 필수다 (파라미터가 없는 public 또는 protected 생성자)
- final 클래스, enum, interface, inner 클래스에는 사용할 수 없다.
- 저장할 필드에 final을 사용하면 안된다.

JPA가 엔티티 객체를 생성할 때 기본 생성자를 사용하므로 이 생성자는 반드시 있어야 한다.<br>
자바는 생성자가 하나도 없으면 다음과 같은 기본 생성자를 자동으로 만든다.
```java
public Member () {} // 기본 생성자
```


문제는 다음과 같이 생성자를 하나 이상 만들면 자바는 기본 생성자를 자동으로 만들지 않는다.<br> 이때는 기본 생성자를 직접 만들어야 한다.
```java
// 임의의 생성자
public Member(String name) {
    this.name = name;
}
```

## 4.2 @Table

@Table은 엔티티와 매핑할 테이블을 지정한다.<br>
생략하면 매핑한 엔티티 이름을 테이블 이름으로 사용한다.

|속성|기능|기본값|
|---|---|---|
|name|매핑할 테이블 이름|엔티티 이름을 사용한다.|
|catalog|catalog 기능이 있는 데이터베이스에서 catalog를 매핑한다.||
|schema|schema 기능이 있는 데이터베이스에서 schema를 매핑한다.||
|uniqueConstraints|DDL 생성 시에 유니크 제약조건을 만든다.<br>2개 이상의 복합 유니크 제약조건도 만들 수 있다.<br>참고로 이 기능은 스키마 자동 생성 기능을 사용해서 DDL을 만들 때만 사용된다.||

## 4.3 다양한 매핑 사용
JPA 시작하기 장에서 개발하던 회원 관리 프로그램에 다음 요구사항이 추가
1. 회원은 일반회원과 관리자로 구분해야 한다.
2. 회원 가입일과 수정일이 있어야 한다.
3. 회원을 설명할 수 있는 필드가 있어야 한다. 이 필드는 길이 제한이 없다.

요구사항을 만족하도록 회원 엔티티에 기능을 추가

예제코드: ch04-jpa-start2

## 4.4 데이터 베이스 스키마 자동 생성

JPA는 데이터베이스 스키마를 자동으로 생성하는 기능을 지원한다.<br>
클래스의 매핑 정보를 보면 어떤 테이블에 어떤 컬럼을 사용하는지 알 수 있다.<br>
JPA는 이 매핑정보와 데이터베이스 방언을 사용해서 데이터베이스 스키마를 생선한다.

스키마 자동 생성 기능을 사용

`hibernate.hbm2ddl.auto생성`

|옵션|설명|
|---|---|
|create|기존 테이블을 삭제하고 새로 생성한다.<br> DROP + CREATE|
|create-drop| create속성에 추가로 애플리케이션을 종료할 때 생성한 DDL을 제거한다.<br>DROP + CREATE + DROP|
|update|데이터베이스 테이블과 엔티티 매핑정보를 비교해서 변경 사항만 수정한다.|
|validate|데이터베이스 테이블과 엔티티 매핑정보를 비교해서 차이가 있으면 경고를 남기고 애플리케이션을 실행하지 않는다.<br> 이 설정은 DDL을 수정하지 않는다.|
|none|자동 생성 기능을 사용하지 않으려면 hibernate.hbm2ddl.auto 속성 자체를 삭제하거나 유효하지 않은 옵션 값을 주면 된다.<br>(참고로 none은 유효하지 않은 옵션 값이다.)|

## 4.5 DDL 생성 가능
회원 이름은 필수로 입력되어야 하고, 10자를 초과하면 안된다는 제약조건이 추가

@Column 매핑정보의 nullable 속성 값을 false로 지정하면 자동 생성되는 DDL에 not null 제약 조건을 추가할 수 있다.<br>
그리고 length 속성 값을 사용하면 자동 생성되는 DDL에 문자의 크기를 지정할 수 있따.<br>
nullable = false, length = 10 으로 ㅣㅈ정

이번에는 유니크 제약조건을 만들어 주는 @Table의 uniqueConstraints 속성

> 생성된 DDL
> ALTER TABLE MEMBER
>   ADD CONSTRAINT NAME_AGE_UNIQUE UNIQUE (NAME, AGE)

DDL을 보면 유니크 제약조건이 추가<br>

## 4.6 기본 키 매핑
- 직접할당: 기본 키를 애플리케이션에서 직접 할당
- 자동 생성: 대리 키 사용 방식
  - IDENTITY: 기본 키 생성을 데이터베이스에 위임
  - SEQUENCE: 데이터베이스 시퀸스를 사용해서 기본 키를 할당
  - TABLE: 키 생성 테이블을 사용

### 4.6.1 기본 키 직접 할당 전략

```java
@Id
@Column(name = "id")
private String id;
```
@Id 적용 기능 자바 타입
- 자바 기본형
- 자바 래퍼(wrapper)형
- String
- java.util.Date
- java.sql.Date
- java.math.BigDecimal
- java.math.BigInteger

기본 키 직접 할당은 em.persist()로 엔티티를 저장하기 전에 애플리케이션에서 기본 키를 직접 할당하는 방법
```java
Board board = new Board();
board.setId("id") // 기본키 직접 할당
em.persist(board);
```

### 4.6.2 IDENTITY 전략
기본 키 생성을 데이터베이스에 위임하는 전략
- mysql: auto_increment
- oracle: 

개발자가 엔티티에 직접 식별자를 할당하면 @Id 어노테이션만 있으면 되지만,<br>
식별자가 생성되는 경우에는 @GenerationValue 어노테이션을 사용하고 식별자 생성전력을 선택<br>
IDENTITY 전략을 사용하려면 @GeneratedValue의 strategy 속성 값을 GenerationType.IDENTITY로 지정<br>

```java
@GeneratedValue(strategy = GenerationType.IDENTITY)

private static void logic(EntityManager em) {
  Board board = new Board();
  em.persist(board);
  System.out.println("board.id = " + board.getId());
}

// 출력: board.id = 1
```

### 4.6.3 SEQUENCE 전략
유일한 값을 순서대로 생성하는 특별한 데이터 베이스 오브젝트<br>
SEQUENCE 전략은 이 시퀸스를 사용해서 기본키를 생성
```java
CREATE TABLE BOARD (
  ID BIGINT NOT NULL PRIMARY KEY,
  DATA VATCHAR(255)
)

// 시퀸스 생성
CREATE SEQUENCE BOARD_SEQ START WITH 1 INCREMENT BY 1;

@Entity
@SequenceGenerator(
  name = "BOARD_SEQ_GENERATOR",
  sequenceName = "BOARD_SEQ", // 매핑할 데이터 베이스 시퀸스 이름
  initialValue = 1, allocationSize = 1
)
public class Board {

  @Id
  @GeneratedValue(strategy = GenerationType.SEQUENCE,
                  generator = "BOARD_SEQ_GENERATOR")
  private Long id;
}
```

@SequenceGenerator 를 사용해서 BOARD_SEQ_GENERATOR라는 시퀸스 생성기를 등록<br>
그리고 sequenceName 속성의 이름으로 BOARD_SEQ를 지정했는데 JPA는 이 시퀸스 생성기를 실제 데이터 베이스의 BOARD_SEQ 시퀸스와 매핑<br>

키 생성 전략을 GenerationType.SEQUENCE로 설정하고 generator = "BOARD_SEQ_GENERATOR"로 방금 등록한 시퀸스 생성기를 선택

`시퀸스 사용 코드`
```java
private static void logic(EntityManager em) {
  Board board = new Board();
  em.persist(board);
  System.out.println("board.id = " + board.getId());
}
// 출력 : board.id = 1
```

### @SequenceGenerator

|속성|기능|기본값|
|---|---|---|
|name|식별자 생성기 이름|필수|
|sequenceName|데이터베이스에 등록되어 있는 시퀸스 이름|hibernate_sequence|
|initialValue|DDL 생성 시에만 사용됨<br>시퀸스 DDL을 생성할 때 처음 시작하는 수를 지정|1|
|allocationSize|시퀸스 한 번 호출에 증가하는 수(성능 최적화에 사용됨)|50|
|catalog, schema|데이터 베이스 catalog, schema 이름||

매핑할 DDL
```
create sequence [sequenceName]
start with [initialValue] increment by [allocationSize]
```

### 4.5.4 TABLE 전략
키 생성 전용 테이블을 하나 만들고 여기에 이름과 값으로 사용할 컬럼을 만들어 데이터베이스 시쿠니스를 흉내내는 전략<br>
테이블을 사용하므로 모든 데이텁이스에 적용

`TABLE 전략 키 생성 DDL`
```
create table MY_SEQUENCES (
  sequence_name varchar(255) not null,
  next_val bigint,
  primary key (sequence_name)
)
```

sequence_name 컬럼을 시퀸스 이름으로 사용하고 next_val 컬럼을 시퀸스 값으로 사용<br>

`TABLE 전략 매핑 코드`
```
@Entity
@TableGenerator(
  name = "BOARD_SEQ_GENERATOR",
  table = "MY_SEQUENCE",
  pkCoumnValue = "BOARD_SEQ", allocationSize = 1
)
public class Board {

  @Id
  @GeneratedValue(strategy = GenerationType.TABLE,
  generator = "BOARD_SEQ_GENERATOR");
  private Long id;
  ...
}


위는 아래처럼
`TALBE 전략 매핑 사용 코드`
private static void logic(EntityManager em) {
  Board board = new Board();
  em.persist(board);
  System.out.println("board.id = " + board.getId());
}
// 출력: board.id = 1
```

TABLE 전략은 시퀸스 대신에 테이블을 사용한다는 것만 제외하면 SEQUENCE 전략과 내부 동작방식이 같다.

@TableGenerator.pkColumnValue에서 지정한 "BOARD_SEQ"가 컬럼명으로 추가된 것을 확인<br>

### @TableGenerator
|속성|기능|기본값|
|---|---|---|
|name|식별자 생성기 이름|필수
|table|키생성 테이블명|hibernate_sequence|
|pkColumnName|시퀸스 컬럼명|sequence_name|
|valueColumnName|시퀸스 값 컬렴명|next_val|
|pkColumnValue|키로 사용할 값 이름|엔티티 이름|
|initialValue|초기 값, 마지막으로 생성된 값이 기준이다.|0|
|allocationSize|시퀸스 한 번 호출에 증가하는 수(선능 최적화에 사용됨)|50|
|catalog, schema|데이터 베이스 catalog, schema 이름||
|uniqueConstrainits|유니크 제약 조건을 지정||

JPA 표준 명세서에서는 table, pkColumnName, valueColumnName의 기본값을 JPA 구현체가 정의<br>

|||
|---|---|
|{pkColumnName}|{valueColumnName}|
|{pkColumnValue}|{initialValue}|

### 4.6.5 AUTO 전략
GenerationType.AUTO는 선택한 데이터베이스 방언에 따라 IDENTITY, SEQUENCE, TABLE 전략 중 하나를 자동으로 선택

`AUTO 전략 매핑 코드`
```java
@Entity
public class Board {
  @Id
  @GeneratedValue(strategy = GenerationType.AUTO)
  private Long id;
  ...
}
```

@GenerationType.strategy의 기본값은 AUTO<br>

```java

@Id @GeneratedValue
private Long id;
```

AUTO 전략의 장점은 데이터베이스를 변경해도 코드를 수정할 필요가 없다는 것<br>
AUTO를 사용할 때 SEQUENCE나 TABLE 전략이 선택되면 시퀸스나 키 생성용 테이블을 미리 만들어 두어야 한다.<br>
만약 스키마 자동 생성 기능을 사용한다면 하이버네이트가 기본값을 사용해서 적절한 시퀸스나 키 생성용테이블을 만들어 줄 것<br>


### 4.6.6 기본 키 매핑 정리

영속성 컨텍스트는 엔티티를 식별자 값으로 구분하므로 엔티티를 영속 상태로 만들려면 식별자 값이 반드시 있어야 한다.<br>
em.persist()를 호출한 직후에 발생하는 일을 식별자 할당 전략별로 정리
- 직접 할당: em.persist()를 호출하기 전에 애플리케이션에서 직접 식별자 값을 할당해야한다. 만약 식별 자 값이 없으면 예외가 발생한다.
- SEQUENCE: 데이터베이스 시퀸스에서 식별자 값을 취득한 후 영속성 컨텍스트에 저장
- TABLE: 데이터베이스 시퀸스 생성용 테이블에서 식별자 값을 획득한 후 영속성 컨텍스트에 저장
- IDENTITY: 데이터베이스에 엔티티를 저장해서 식별자 값을 획득한 후 영속성 컨텍스트에 저장한다.(IDENTITY 전략은 테이블에 데이터를 저장해야 식별자 값을 획득할 수 있다.)


## 4.7 필드와 컬럼 매핑: 레퍼런스(pdf. 145)

### 4.7.1 @Column
### 4.7.2 @Enumerated
### 4.7.3 @Temporal
### 4.7.4 @Lob
### 4.7.5 @Transient
### 4.7.6 @Access

## 4.8 정리

## 실전예제 | 1. 요구사항 분석과 기본 매핑
`ch04-model1`

