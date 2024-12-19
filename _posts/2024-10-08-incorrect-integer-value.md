---
title: MySQL - Incorrect integer value
categories: [Study, DB]
tags: [study, DB, mysql, error, value, database, string, integer]
---

## 문제 상황

|  이름   | 데이터 유형 | 길이  | null 허용 | 기본값 |
| :-----: | :---------: | :---: | :-------: | :----: |
|  name   |   varchar   |  50   |  미허용   |  NULL  |
|   age   |     int     |   3   |  미허용   |  NULL  |
|  local  |   varchar   |  200  |   허용    |  NULL  |
| likeNum |     int     |   3   |   허용    |  NULL  |

위와 같은 테이블이 있다고 가정하자. (해당 테이블의 이름은 user다.)<br/>
웹 사이트에서 사용자를 등록하면 내부적으로 `insert into user(name, age, local, likeNum) values ('A', 10, '', '');` 가 실행되게 되는데, 이때 SQLException이 발생한다.


## 오류 파악

### 1. local에 빈 값 넣기

데이터 유형이 varchar인 local만 빈 값이 삽입되도록 데이터를 입력했을 때는 오류가 발생하지 않았다.

### 2. likeNum에 빈 값 넣기

데이터 유형이 int인 likeNum에만 빈 값이 삽입되도록 데이터를 입력했을 때 오류가 발생했다.

### 3. local과 likeNum에 빈 값 넣기

데이터 유형이 varchar인 local과 데이터 유형이 int인 likeNum에 빈 값이 삽입되도록 데이터를 입력했을 때 오류가 발생했다.

### 3. log 확인

`java.sql.SQLException: Incorrect integer value: '' for column 'likeNum' at row 1`

### 결론

```java
public class userDTO {
    String name;
    String age;
    String local;
    String likeNum;
}
```
위 코드는 내부에서 사용하는 DTO다. 웹 사이트에서 데이터를 입력하면 각 값은 String 타입으로 저장되게 되는데, 이때 local과 likeNum에 빈 값이 반환될 경우 ''이 저장된다. 이후 userDTO의 데이터를 DB에 insert하게 되면 varchar 타입인 local은 문제 없이 데이터가 저장되지만 likeNum은 int 타입이기 때문에 데이터 타입 오류가 발생하게 된다.



## 오류 해결

### 1. STRICT_TRANS_TABLES 제거하기

해당 옵션이 적용되어 있을 경우, MySQL은 컬럼 타입과 데이터가 호환되는지 확인한다. 따라서 해당 옵션을 제거하여 MySQL이 호환되는 값으로 최대한 변경할 수 있도록 한다.

<br/>

```SQL
select @@global.sql_mode;
```

위 명령어를 통해 MySQL에 옵션이 적용되어 있는지 확인하고 

```SQL
set @@global.sql_mode = 'ONLY_FULL_GROUP_BY,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';
```

위 명령어를 사용하여 STRICT_TRANS_TABLES을 제외한 옵션을 재적용한다.


### 2. 타입 변경하기

테이블의 likeNum의 데이터 유형을 varchar / char로 변경해면 오류가 발생하지 않는다. 혹은 DTO의 타입을 변경하는 것도 방법이 될 수 있다. 필자는 실서버가 아닌 테스트용 사이트를 열기 위한 작업 중 위의 오류를 발견하기도 했고, 동일한 DB를 다른 곳에서도 사용하고 있었기 때문에 DTO의 타입을 변경했다.


## 참고

[Incorrect integer value](https://mariadb.com/kb/en/incorrect-integer-value/){:target="\_blank"} <br/>
[MySQL Incorrect integer value 에러 발생 시 에러 나오지 않게 하려면?](https://jaesong.tistory.com/17147559){:target="\_blank"}