---
title: Scheduler 적용 오류
categories: [Study,Server]
tags: [Server, server, scheduler, 스케줄러, 서버, 스케쥴러, 적용, 오류]
---

## 시작

일정 시간마다 특정 메소드가 실행되도록 하기 위해 스케줄러가 필요했다. 하지만 spring scheduler를 처음 접하다보니 오류를 꽤 만났다. 이 글은 스케줄러를 적용하면서 발생한 오류를 작성했다.


### 첫번째 - 기본

아래는 작성한 최초의 코드다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns:p="http://www.springframework.org/schema/p" xmlns:context="http://www.springframework.org/schema/context"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
							http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">
	
	<bean id="testService" class="com.baemin.test.service.TestServiceImpl" />
	<bean id="testPostService" class="com.baemin.testpost.service.TestPostServiceImpl" />

	<bean id="testTrigger" 
		class="org.springframework.scheduling.quartz.CronTriggerFactoryBean"
		p:jobDetail-ref="testDtail"
		p:cronExpression="0 30 15 1/1 * ? *" />
	 
	<bean id="testDtail" 
		class="org.springframework.scheduling.quartz.JobDetailFactoryBean"
		p:jobClass="com.baemin.schedule.TestJob">
		<property name="jobDataAsMap">
			<map>
				<entry key="testService" value-ref="testService" />
				<entry key="testPostService" value-ref="testPostService" />
			</map>
		</property>
	</bean>
	<bean
		class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
		<property name="triggers">
			<list>
				 <ref bean="testTrigger" />
			</list>
		</property>	
	</bean>
</beans>
```

하지만 위와 같이 적용 시 `org.quartz.JobPersistenceException: 트리거에서 참조하는 작업(DEFAULT.XXJob)이 존재하지 않습니다.` 오류가 발생한다. <br/>
그래서 오류의 내용대로 작업을 추가했다.

### 두번째 - job 추가

```xml
<beans...>
    <bean id="testService" class="com.baemin.test.service.TestServiceImpl" />
    <bean id="testPostService" class="com.baemin.testpost.service.TestPostServiceImpl" />

    <bean id="testTrigger" 
        class="org.springframework.scheduling.quartz.CronTriggerFactoryBean"
        p:jobDetail-ref="testDtail"
        p:cronExpression="0 30 15 1/1 * ? *" />

    <bean id="testDtail" 
        class="org.springframework.scheduling.quartz.JobDetailFactoryBean"
        p:jobClass="com.baemin.schedule.TestJob">
        <property name="jobDataAsMap">
            <map>
                <entry key="testService" value-ref="testService" />
                <entry key="testPostService" value-ref="testPostService" />
            </map>
        </property>
    </bean>
    <bean
		class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
        <property name="jobDetails">
            <list>
                <ref bean="testDetail">
            </list>
        </property>
		<property name="triggers">
			<list>
				<ref bean="testTrigger" />
			</list>
		</property>	
	</bean>

</beans>
```

오류 없이 실행은 됐지만 스케쥴러가 두 번 도는지 testTrigger로 인한 결과값이 두 번 생성되었다. <br/>
검색해보니 details와 detail로 2번 등록이 되어 있기 때문이라고도 하고, server.xml 설정에서 context가 두 개 생겨서 그렇다고도 하는데 뚜렷한 해결법은 발견하지 못했다. 그렇게 정보의 바다를 떠돌다가 글 하나를 발견했다.

### 세번째 - id 등록

참고한 블로그는 [S水流年 님의 CSDN](https://blog.csdn.net/jianbo2233/article/details/84939742\) 다.

```xml
<beans...>
    <bean id="testService" class="com.baemin.test.service.TestServiceImpl" />
    <bean id="testPostService" class="com.baemin.testpost.service.TestPostServiceImpl" />

    <bean id="testTrigger" 
        class="org.springframework.scheduling.quartz.CronTriggerFactoryBean"
        p:jobDetail-ref="testDtail"
        p:cronExpression="0 30 15 1/1 * ? *" />

    <bean id="testDtail" 
        class="org.springframework.scheduling.quartz.JobDetailFactoryBean"
        p:jobClass="com.baemin.schedule.TestJob">
        <property name="jobDataAsMap">
            <map>
                <entry key="testService" value-ref="testService" />
                <entry key="testPostService" value-ref="testPostService" />
            </map>
        </property>
    </bean>
    <bean id="schedulerFactoryBean"
		class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
		<property name="triggers">
			<list>
				<ref bean="testTrigger" />
			</list>
		</property>	
	</bean>

</beans>
```

위 글에서 **SchdulerFactoryBean에는 ID가 지정되어야 합니다.** 를 보고 동일하게 id를 등록했더니 문제 없이 실행되었다.


## 회고

백업 파일을 만들기 위해 스케줄러 sh를 생성한 경험은 있지만 spring에서 스케줄러를 등록해서 처리하는 건 처음이라 많이 헤맨 것 같다.<br/>
해결을 하긴 했으니 만족하지만 내가 본 코드는 첫번째 코드와 유사한 형식임에도 오류가 없었다. 왜 ID가 없음에도 문제가 없는지 더 찾아봐야겠다.