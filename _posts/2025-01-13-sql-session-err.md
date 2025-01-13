---
title: SqlSession - synchronization is not active
categories: [Study, MyBatis]
tags: [study, server, apache, tomcat, mybatis, MyBatis, code, error]
---

## 문제점

![code_lan](/assets/img/post_img/blog_etc/sqlsessionerr.png)<br/>

화면에서 '오류' 시 반환되는 문구가 출력되서 로그를 확인했는데 위와 같은 내용만 확인할 수 있었다. 구글링 했을 때는 Transaction 적용 문제라고만 나왔지만 내 경우, 다른 메서드는 문제 없이 동작하는 것에 반해, 하나의 메소드에서만 위와 같은 오류가 반환되었다.

## 파악

우선은 곳곳에 printf문을 달아 정확히 어디에서 문제가 발생하는 지 확인했다.<br/>
확인 결과, 해당 문제는 Sql.xml 쪽에서 발생하고 있었다. (필자의 프로젝트는 Spring과 MyBatis를 사용하고 있다.)

## 가정

### 1. DB 연결 정보 문제

다른 메서드에서는 문제가 발생하지 않기 때문에 위 가정은 금방 폐기되었다.

### 2. 테이블 존재 유무의 문제

혹시나 싶어서 DBMS를 통해 DB를 확인했으나 테이블도 있고 데이터도 있었다. 따라서 위 가정 또한 폐기되었다.

### 3. mapper의 문제

혹시 mapping 과정에서 문제가 있는게 아닐까? 하는 생각이 들어서 관련 설정을 모두 확인해보았지만 다른 mapper와 동일했다. 가능성이 없진 않으나 이 가정은 아닌 것 같다는 느낌이 들었다.


## 해결

### parameterType의 문제

오류를 반환했던 sql.xml은 아래와 같았다.

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="testDao">

	<select id="selectTestList" parameterType="testVO" resultType="resultVO">
	        SELECT
    			id,
                name,
                age
		    FROM    test
		    WHERE   id = 1
			<if test='testName != null and !testName.equals("")'>
				and name = #{tname}
			</if>
	</select>
</mapper>
```

그리고 문제가 되었던 testVO는 아래와 같았다.

```java
public class testVO {
	private String tname;

	public String getTname() {
		return tname;
	}
	public void setTname(String tname) {
		this.tname = tname;
	}
}
```

즉, 트랜잭션 문제는 testSql.xml에서 parameterType인 testVO에는 없는 testName을 비교하고 있었기 때문에 발생했다. 필자는 위 코드에서 if문 내의 testName을 tname으로 수정하고 나서야 오류가 발생하지 않았다.

## 회고

처음 구글링 했을 때 나온 결과를 적용해보려고 애너테이션도 달고 설정도 바꾸고 삽질만 했는데.. 다행히 빠르게 해결할 수 있어서 다행이었다.