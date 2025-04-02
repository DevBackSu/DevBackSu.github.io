---
title: DB 백업
categories: [Study,DB]
tags: [데이터베이스, DB, database, DATABASE, DataBase, backup, Backup, 백업]
description: >-
    파일 형태로 데이터를 백업하는 방법
---


## BackUp

원본과 별도로 저장되는 데이터의 사본으로, 시스템 장애나 자연재해 등으로 인한 예상치 못한 사건이 발생했을 때 전체, 혹은 일부 데이터 손실 위험을 줄이는 데이터 보호 기능을 한다.<br/>
백업을 주기적으로 수행할 경우, 데이터 손실을 원하는 시점으로의 복구가 가능하다.

## Mysqldump

MySQL의 데이터 상태를 백업할 수 있는 MySLQ Tool이다. mysqldump로 특정 시점의 데이터 상태인 스냅샷을 생성할 수 있다.

## 주기적으로 DB 자동 백업하기

### 백업을 위한 Bash Shell Script 작성

우선 DB가 세팅된 서버 내에 .sh 파일을 만든다. 필자는 **backup_(DB명).sh**로 만들었다. 그리고 해당 파일에 아래와 같은 script를 작성한다.

```bash
mysqldump -u[사용자 계정] -p[비밀번호] [DB명] > [백업할 파일 경로와 파일명]

find [백업한 파일 경로] -ctime +10 -exec rm -rf {} \;
```

예시는 아래와 같다.

```bash
mysqldump -utest -ptest test_db > /backup/$(date '+%Y%m%d')_test.sql

find /backup/ -ctime +10 -exec rm -rf {} \;
```

#### 간략한 설명

위 스크립트의 간략한 설명은 아래와 같다.

1. mysqldump를 사용해 test_db의 구조 및 데이터를 /backup/ 아래에 날짜_test.sql 형식으로 저장한다.
2. find 명령어로 /backup/ 아래에서 10일이 지난 파일을 찾아 강제 삭제한다.


<br/>

#### 자세한 설명

1. `mysqldump -utest -ptest test_db > /backup/$(date '+%Y%m%d')_test.sql`
   1. **mysqldump** : MySQL DB의 구조 및 데이터를 파일로 저장 (dump) 한다.
   2. **-u** : 사용자 계정을 지정한다. MySQL DB에 접근할 사용자 이름을 띄어쓰기 없이 입력해야 한다.
   3. **-p** : 사용자의 비밀번호를 지정한다. -u와 마찬가지로 공백이 없어야 한다.
   4. **test_db** : 백업할 DB의 이름이다.
   5. **>** : 출력 리디렉션 연산자다. mysqldump의 결과를 오른쪽으로 출력 (저장)한다.
   6. **/backup/$(date '+%Y%m%d')_test.sql** : /backup/ 디렉터리 안에 YYYYMMDD_test.sql 형식으로 파일을 생성한다.
      - **$(date '+%Y%m%d')** : 현재 날짜를 YYYYMMDD 형식으로 출력하는 bash 명령어이다.
      - 예를 들어, 2024년 4월 2일에 위 명령어를 실행할 경우, 20250402_test.sql이 생성된다.

2. `find /backup/ -ctime +10 -exec rm -rf {} \;`
   1. **find /backup/** : /backup/ 디렉터리 내에서 파일을 검색한다.
   2. **-ctime +10** : 10일 이전에 생성된 파일을 찾는다. 만약 오늘이 2025년 04월 02일이라면 2025년 03월 26일에 생성된 파일을 찾는다.
   3. **-exec** : 검색된 파일에 대해 다음의 명령을 실행한다.
   4. **rm -rf {}** : find 명령어가 찾은 파일의 경로에서 해당 파일을 강제 삭제 (-f) 한다. 디렉터리라면 재귀적으로 (-r) 삭제 한다.
   5. **\;** : -exec 옵션을 끝낸다.


<br/>


### 파일에 권한 부여

```bash
chmod -R 755 /(경로)/(파일명).sh
```

예시는 아래와 같다.

```bash
chmod -R 755 /backup/backup_test.sh
```

위 명령어를 통해 .sh 파일에 권한을 부여할 수 있다. -R 옵션은 경로와 그 하위 파일을 모두 재귀적으로 변경하겠다는 의미여서 특정 파일만 지정할 거라면 없어도 된다.
- user : 읽기 / 쓰기 / 실행 권한 부여
- group : 읽기 / 실행 권한 부여
- other : 읽기 / 실행 권한 부여


<br/>


### cron

일정 시간마다 backup_test.sh가 실행되려면 cron을 설정해야 한다. 이를 위해서는 crontab을 설정해야 한다.<br/>
필자는 매일 자정에 백업 스크립트가 실행되도록 설정했다.

```bash
# 1. 터미널에서 cron 작업 추가를 위해 아래와 같이 입력
crontab -e

# 2. 편집기가 열리면 아래와 같이 새 Cron 작업을 추가하고 ESC -> :wq로 종료
0 0 * * * /backup/backup_test.sh

# 3. cron 데몬을 재시작해서 추가한 설정을 반영한다.
service crond stop

service crond start

# 혹은 service crond restart || systemctl restart crond로 재시작할 수 있다.

# 이후 적용된 내용을 확인한다.
crontab -l
```

## 회고

데이터를 한 번 날리고 백업의 중요성을 알게 되었기에 글을 작성했다. 다음에는 잊지 말고 처음 DB를 구성하고 나서부터 적용하자.<br/>
백업에 관해 알아보다보니 전체 백업 말고도 차등 백업과 증분 백업이 있다는 것을 알았다. 지금은 간단히 처리하기 위해 전체 백업을 진행하고 있는데 추후 데이터가 많아지면 다른 백업 방법도 적용해보고 싶다. 그러려면 공부해야지.