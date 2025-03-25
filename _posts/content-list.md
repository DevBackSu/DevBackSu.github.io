1. 통신 관련

ping - ICMP 통신 (ICMP : network 계층인 3계층 프로토콜) -> port 불가능

telnet -  ssh 하위호환으로 원격 터미널 접속 기능이 있음. 7계층 프로토콜이라서 port 번호 사용 가능

ssh - 

curl - HTTP 외의 프로토콜도 사용 가능. 서버와 통신 가능한 커맨드 명령어 도구. 웹 브라우저처럼 터미널에서 요청을 보내고 응답을 받기 위해 사용됨. 구글로 쏴보니까 html 반환됨

이외의 방법

dev/tcp/[IP]/[PORT] - [설명](http://devmes.com/dev-tcp-seolmyeong-mic-sayongbangbeob/) / [설명2](https://medium.com/@stefanos.kalandaridis/bash-ing-your-network-f7069ab7c5f4)


각 예시 추가하기

----------------
2. httpd.service

회사 서비스 중에 하나의 아파치 위에서 여러 개의 톰캣을 돌리는 경우가 있음.
갑자기 하나의 톰캣이 503 뜨면서 connection time out이 발생함.

그래서 톰캣을 재시작 했는데 여전함!

결국 아파치 재시작 함.

근데 아파치 재시작하는 명령어가 httpd.service인거임. 톰캣은 ./shutdown 이랑 ./startup으로 종료/시작을 하는데 왜 아파치는 httpd.service일까? 가 궁금해짐.

httpd : 아파치 하이퍼텍스트 전송 프로토콜 (HTTP) 서버 프로그램

아파치 -> 웹 서버 (html 등 정적 소스 처리) -> httpd 제공
톰캣 -> 웹 애플리케이션 서버 (java 등 동적 소스 처리)


참고
https://httpd.apache.org/docs/2.4/programs/httpd.html



-------------------

3. kworker

3번 처리 전, top 확인 -> kworker가 톰캣마다 도니까 여러 개 돌면서 cpu를 많이 잡아먹은 것으로 추정



4 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/0:0H
5 root      20   0       0      0      0 S   0.0  0.0   1:35.17 kworker/u80:0
16 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/1:0H
17 root      20   0       0      0      0 S   0.0  0.0   0:19.58 kworker/u81:0



그래서 kworker가 뭔지 궁금해졌다.

참고
https://askubuntu.com/questions/33640/kworker-what-is-it-and-why-is-it-hogging-so-much-cpu
https://austindhkim.tistory.com/43
https://community.frame.work/t/tracking-kworker-stuck-at-near-100-cpu-usage-with-ubuntu-22-04/23053


--------
5. 스케쥴러

1. 최초 코드
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns:p="http://www.springframework.org/schema/p" xmlns:context="http://www.springframework.org/schema/context"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
							http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">
	
	<bean id="crsService" class="com.baemin.crs.service.impl.CrsServiceImpl" />
	<bean id="smsSndgService" class="com.baemin.smssndg.service.impl.SmsSndgServiceImpl" />
	<bean id="mngrService" class="com.baemin.mngr.service.impl.MngrServiceImpl" />

	<bean id="usrCrsLrgNtfTrigger" 
		class="org.springframework.scheduling.quartz.CronTriggerFactoryBean"
		p:jobDetail-ref="usrCrsLrgNtfDetail"
		p:cronExpression="0 42 15 1/1 * ? *" /> <!-- 매일 오전 17시   -->
	 
	<bean id="usrCrsLrgNtfDetail" 
		class="org.springframework.scheduling.quartz.JobDetailFactoryBean"
		p:jobClass="com.baemin.schedule.UsrCrsLrgNtfJob">
		<property name="jobDataAsMap">
			<map>
				<entry key="crsService" value-ref="crsService" />
				<entry key="smsSndgService" value-ref="smsSndgService" />
			</map>
		</property>
		<property name="durability" value="true">
		</property>
	</bean>
	
	<bean id="mngrBatchTrigger" 
		class="org.springframework.scheduling.quartz.CronTriggerFactoryBean"
		p:jobDetail-ref="mngrBatchDetail"
		p:cronExpression="0 0 1 1/1 * ? *" /> <!-- 매일 오전 1시   -->
	 
	<bean id="mngrBatchDetail" 
		class="org.springframework.scheduling.quartz.JobDetailFactoryBean"
		p:jobClass="com.baemin.schedule.MngrBatchJob">
		<property name="jobDataAsMap">
			<map>
				<entry key="mngrService" value-ref="mngrService" />
			</map>
		</property>
		<property name="durability" value="true">
		</property>
	</bean>
	
	<bean
		class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
		<property name="triggers">
			<list>
				 <ref bean="usrCrsLrgNtfTrigger" />
				<!--<ref bean="mngrBatchTrigger" />-->
			</list>
		</property>	
	</bean>
	
</beans>

-> 이거 하면 org.quartz.JobPersistenceException: 트리거에서 참조하는 작업(DEFAULT.XXJob)이 존재하지 않습니다. 오류 뜸

2. 수정 ver
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns:p="http://www.springframework.org/schema/p" xmlns:context="http://www.springframework.org/schema/context"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
							http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">
	
	<bean id="crsService" class="com.baemin.crs.service.impl.CrsServiceImpl" />
	<bean id="smsSndgService" class="com.baemin.smssndg.service.impl.SmsSndgServiceImpl" />
	<bean id="mngrService" class="com.baemin.mngr.service.impl.MngrServiceImpl" />

	<bean id="usrCrsLrgNtfTrigger" 
		class="org.springframework.scheduling.quartz.CronTriggerFactoryBean"
		p:jobDetail-ref="usrCrsLrgNtfDetail"
		p:cronExpression="0 42 15 1/1 * ? *" /> <!-- 매일 오전 17시   -->
	 
	<bean id="usrCrsLrgNtfDetail" 
		class="org.springframework.scheduling.quartz.JobDetailFactoryBean"
		p:jobClass="com.baemin.schedule.UsrCrsLrgNtfJob">
		<property name="jobDataAsMap">
			<map>
				<entry key="crsService" value-ref="crsService" />
				<entry key="smsSndgService" value-ref="smsSndgService" />
			</map>
		</property>
		<property name="durability" value="true">
		</property>
	</bean>
	
	<bean id="mngrBatchTrigger" 
		class="org.springframework.scheduling.quartz.CronTriggerFactoryBean"
		p:jobDetail-ref="mngrBatchDetail"
		p:cronExpression="0 0 1 1/1 * ? *" /> <!-- 매일 오전 1시   -->
	 
	<bean id="mngrBatchDetail" 
		class="org.springframework.scheduling.quartz.JobDetailFactoryBean"
		p:jobClass="com.baemin.schedule.MngrBatchJob">
		<property name="jobDataAsMap">
			<map>
				<entry key="mngrService" value-ref="mngrService" />
			</map>
		</property>
		<property name="durability" value="true">
		</property>
	</bean>
	
	<bean
		class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
        <property name="jobDetails">
            <list>
                <ref bean="usrCrsLrgNtfDetail">
            </list>
        </property>
		<property name="triggers">
			<list>
				 <ref bean="usrCrsLrgNtfTrigger" />
				<!--<ref bean="mngrBatchTrigger" />-->
			</list>
		</property>	
	</bean>
	
</beans>

-> 단순히 없다니까 추가함.
-> 실행은 되는데 스케쥴러가 두 번 돎
-> details랑 detail로 2번 등록이 되어 있어서 그렇다고도 하고 server.xml 설정에서 context가 두 개 생겨서 그렇다고도 하는데 이것저것 수정해도 실행이 안 되거나 두 번 돌기만 함


3. 최종
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns:p="http://www.springframework.org/schema/p" xmlns:context="http://www.springframework.org/schema/context"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
							http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">
	
	<bean id="crsService" class="com.baemin.crs.service.impl.CrsServiceImpl" />
	<bean id="smsSndgService" class="com.baemin.smssndg.service.impl.SmsSndgServiceImpl" />
	<bean id="mngrService" class="com.baemin.mngr.service.impl.MngrServiceImpl" />

	<bean id="usrCrsLrgNtfTrigger" 
		class="org.springframework.scheduling.quartz.CronTriggerFactoryBean"
		p:jobDetail-ref="usrCrsLrgNtfDetail"
		p:cronExpression="0 42 15 1/1 * ? *" /> <!-- 매일 오전 17시   -->
	 
	<bean id="usrCrsLrgNtfDetail" 
		class="org.springframework.scheduling.quartz.JobDetailFactoryBean"
		p:jobClass="com.baemin.schedule.UsrCrsLrgNtfJob">
		<property name="jobDataAsMap">
			<map>
				<entry key="crsService" value-ref="crsService" />
				<entry key="smsSndgService" value-ref="smsSndgService" />
			</map>
		</property>
		<property name="durability" value="true">
		</property>
	</bean>
	
	<bean id="mngrBatchTrigger" 
		class="org.springframework.scheduling.quartz.CronTriggerFactoryBean"
		p:jobDetail-ref="mngrBatchDetail"
		p:cronExpression="0 0 1 1/1 * ? *" /> <!-- 매일 오전 1시   -->
	 
	<bean id="mngrBatchDetail" 
		class="org.springframework.scheduling.quartz.JobDetailFactoryBean"
		p:jobClass="com.baemin.schedule.MngrBatchJob">
		<property name="jobDataAsMap">
			<map>
				<entry key="mngrService" value-ref="mngrService" />
			</map>
		</property>
		<property name="durability" value="true">
		</property>
	</bean>
	
	<bean id="schedulerFactoryBean"
		class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
		<property name="triggers">
			<list>
				 <ref bean="usrCrsLrgNtfTrigger" />
				<!--<ref bean="mngrBatchTrigger" />-->
			</list>
		</property>	
	</bean>
</beans>

-> 돌아감!
-> 단지 id를 등록해줬을 뿐인데 이게 왜 되지 (참고 사이트에서도 id를 등록하면 됩니다. 정도만 있지 자세한 설명은 없음)
-> 참고 : https://blog.csdn.net/jianbo2233/article/details/84939742\


----------------------------------------------------------------------------------------------------------------------------------------------------------
