---
title:  "Django와 비동기 프로그래D"
date: 2019-09-24 13:27:00

categories: devKnowledge
---

<br>

### `Django와 비동기 프로그래밍`
두 달간 진행했던, Sentiment Analysis 웹페이지를 본격적으로 배포하기 위한 준비를 시작하고 있다.<br>
Heroku에 우리의 프로젝트를 올리고, 데이터베이스를 연결하여 유저를 위한 서비스를 추가적으로 준비하고자 한다.<br>
이제 더 안정적이고 효율적인 프로그래밍을 하기 위해, 세부적인 서버와 데이터베이스 등의 대해 공부를 시작한다.<br>
<br>

### `비동기 프로그래밍`
현재 우리 프로젝트의 주요한 문제상황은 **Manager** 객체를 두고, Request가 들어왔을 시 이 객체가 run하게 한다는 점이다.<br>
<br>

### `GUNICORN`
##### 1. Django와 함께 사용되는 용도로 Heroku에서 추천되는 HTTP server
##### 2. 다이노에서 여러개의 Python 동시 프로세스를 실행할 수 있는 WSGI 어플리케이션을 위한 순수 Python으로 작성된 HTTP server
##### 3. Gunicorn은 python WSGI HTTP Server이다. 상대적으로 가볍고 빠르다.
<br>

### `WSGI 파일`
CGI - Command Gateway Interface의 일종이다.<br>
WSGI는 CGI의 일종으로, Framework의 웹서버이다. Route Web에서는 표준 인터페이스로 개발되었다.<br>
WSFI는 두 종류로 구분할 수 있는데, **Nigix나 Apache같은 Server often high profile 웹서버** 또는 **python script로 짜여진 web app** 으로 구분할 수 있다.<br>
서버는 보통 web app에 있는 정보, callback 함수 등을 실행한다. request는 앱단에서 실행되며, response는 callback 함수를 이용해 서버로 되보내진다.<br>
##### CALLBACK 함: 무엇인가 객체한테 시키고, 그 일이 끝날 때까지 기다리는 것이 아닌(동기적 프로그래밍), 일이 끝나면 다시 나를 호출하는 비동기적 방식의 함수로 사용된다.
WSGI Server는 많은 request를 다룰 수 있도록 설계되었다. framework들은 스스로 수천개의 request를 실행하고 최고의 방법으로 처리할 수 있도록 설계되어 있지 않다.<br>
Django의 경우, 단순히 manage.py runserver로 배포하면 안된다는 뜻이다.<br>
WSGI는 python web의 개발 속도를 올려준다. <br>
<br>
