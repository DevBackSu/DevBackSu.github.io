---
title: MyBatis Annotation
categories: [Study,MyBatis]
tags: [mybatis, MyBatis, Annotation, 주석]
description: >-
    주석을 단 후 오류가 발생할 경우, 동적 쿼리에 --을 사용했는지 확인해보자.
---

## 오류

특정 버튼을 눌렀을 때, 데이터 수정이 일어나야 하는 부분을 수정하기 위해 insert문을 수정한 후부터 해당 기능이 정상적으로 동작하지 않았다.

```log
15:38:05.914 [http-nio-80-exec-9] DEBUG org.mybatis.spring.SqlSessionUtils - Releasing transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@4c6a4faa]
15:38:05.914 [http-nio-80-exec-9] DEBUG org.mybatis.spring.SqlSessionUtils - Transaction synchronization deregistering SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@4c6a4faa]
15:38:05.914 [http-nio-80-exec-9] DEBUG org.mybatis.spring.SqlSessionUtils - Transaction synchronization closing SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@4c6a4faa]
15:38:05.934 [http-nio-80-exec-9] DEBUG org.springframework.jdbc.datasource.DataSourceUtils - Returning JDBC Connection to DataSource
15:38:05.934 [http-nio-80-exec-9] INFO  jdbc.connection - 10. Connection closed  
```

위와 같이 sqlSessionUtils가 트랜잭션 sql session을 release하고 transaction 동기화를 등록 해제하고 catch의 반환값이 반환됐다.


### 기존 insert문과 쿼리문 실행 log

```sql
insert into test (
    user_name,
    user_phone,
    user_email
    -- , user_test
    )
values (
    #{userName},
    #{userPhone},
    #{userEmail}
    -- , #{userTest}
)
```
<br/>

```log

test (user_name, user_phone, user_email --, userTest) VALUES ('a', '010-1234-5678', 'test@gmail.com', ?)

```

<br/>

-> 주석으로 처리한 userTest가 주석 처리 되지 않고 insert 문에 포함되어 있다. 또한, 전달되지 않은 userTest를 입력한다. 하지만 값이 존재하지 않기 때문에 ?가 입력된다.


## 수정한 insert문과 쿼리문 실행 log

```sql
insert into test (
    user_name,
    user_phone,
    user_email
    -- , user_test
    )
values (
    #{userName},
    #{userPhone},
    #{userEmail}
    <!-- , #{userTest} -->
)
```

<br/>

```log

test (user_name, user_phone, user_email) VALUES ('a', '010-1234-5678', 'test@gmail.com')

```

<br/>

-> 전과 달리 주석 처리한 user_test가 사라지고 userTest 또한 입력하지 않는다.

## 결과

iBatis (MyBatis)를 사용해 xml에 쿼리를 작성할 때, insert into 부분은 -- 을 사용하여 주석 처리를 해도 문제가 발생하지 않지만 동적쿼리 #{ } 부분을 -- 으로 주석 처리하면 오류가 발생한다.<br/>
따라서 `<!-- -->`을 사용하여 주석 처리 해야 의도하지 않은 문제가 발생하지 않을 수 있다.

---


## 참고
[뚜루리 님 - Mybatis SQL 주석 사용 시 주의할점 (/**/, -- 등)](https://ddururiiiiiii.tistory.com/382)<br/>
[kyle 님 - 주석으로 인한 파라](https://velog.io/@jonghne/Mybatis-%EC%A3%BC%EC%84%9D%EC%9C%BC%EB%A1%9C-%EC%9D%B8%ED%95%9C-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0-%EC%84%B8%ED%8C%85-%EC%98%A4%EB%A5%98)


