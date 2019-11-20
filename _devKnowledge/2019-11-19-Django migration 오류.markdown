---
title:  "Django Migration 오류"
date: 2019-11-19 17:30:00
description: The history of Development Study
categories: [devKnowledge]
resource: true
comments: true
---
# Django Migration 오류
### `Migration`
장고로 개발을 해보다가 Model을 수정할 때, conflict가 많이 발생하여 힘듬을 겪었다.
함부로 테이블을 지웠다가 다시 생기지 않아서 문제를 겪어보기도 하고, 데이터베이스를 다시 설정해주기도 하였다.
Model이 내 맘대로 적용이 안될 경우에 대해 해결방법을 적어보려 한다.

1. 우선, init.py를 제외한 migrations 폴더 내에 다른 파일을 모두 지운다.
2. psql을 킨다.
> python3.7 manage.py dbshell
  psql
이 두가지 명령어를 이용하여 dbshell을 킬 수 있다.

3. \dt로 데이터베이스 내에 릴레이션들을 확인한다.
4. 바꾸고 싶은 테이블을 drop한다.
> drop table sentimentAnalysis_request;

위 명령어를 이용하여 drop할 수있다.

5. 아래 명령어를 이용하여 migrations의 기록을 지운다.
>DELETE FROM django_migrations WHERE app ='sentimentAnalysis';

6. python3.7 manage.py makemigrations / python3.7 manage.py migrate 를 실행한다.

> Database를 드롭하고 싶을 때, drop database sentimentdb 이런식으로 하면 되지만, 연동하는데 시간이 더 오래걸리니 이런 방법으로 하는 것이 나을 것이다.

### `참고자료`
Django 테이블을 지우고 다시 마이그레이션 하는 방법 : [forybm](https://forybm.tistory.com/2)<br>
