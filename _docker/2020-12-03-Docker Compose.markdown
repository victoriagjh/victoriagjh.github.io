---
title:  "Docker Compose"
date: 2020-12-03 19:22:00
description: Inflearn
categories: [docker]
resource: true
comments: true
---

# Docker Compose
## Docker Compose란?
- docker compose란 다중 컨테이너 도커 어플리케이션을 정의하고 실행하기 위한 도구
- `$ docker-compose up`

## Redis
### Redis란?
- Redis(REmote Dictionary Server)는 메모리 기반의 키-값 구조 데이터 관리 시스템이며, 모든 데이터를 메모리에 저장하고 빠르게 조회할 수 있는 비관계형 데이터베이스(NoSql)이다.

### 레디스를 쓰는 이유
- 메모리에 데이터를 저장
  - Mysql과 가튼 데이터베이스에 저장하는 것보다 빠른 처리 속도
- 메모리에 저장하지만 영속적으로도 보관이 가능
- 서버를 재부팅해도 데이터를 유지할 수 있는 장점이 있음

### Node.js 환경에서 Redis 사용방법
- 먼저 redis-server를 작동시켜야한다.
- redis 모듈을 다운로드 받는다.
  - package.json의 Dependency에 레디스 추가해줬음
- 레디스 모듈을 받은 후 레디스 클라이언트를 생성하기 위해서 Redis에서 제공하는 createClinet() 함수를 이용해 redis.createClient로 레디스 클레이언트를 생성한다.
- 하지만, 여기서 redis server가 작동하는 곳과 Node.js 앱이 작동하는곳이 다르다면, 네트워크 연결이 필요하다. (port, host) 인자를 명시해줘야한다.
- 레디스 서버가 작동하는 곳이 `[redis-server.com](http://redis-server.com)` 이라면 아래와 같이 명시해야 한다.
  - redis server 컨테이너명으로 지정

>const express = require("express"); <br>
const redis = require("redis") <br>
const client = redis.createClient({ <br>
    host:"redis-server", // docker가 아닌 로컬 레디스 환경의 경우, https://redis-server.com <br>
    port:6379 // redis의 기본포트 <br>
}); <br>
const app = express(); <br>
app.listen(8080); <br>
console.log("server is running"); <br>

## Docker Containers 간 통신할 때 나타나는 에러
- 컨테이너 1 : 노드 JS앱 + 레디스 클라이언트
- 컨테이너 2 : 레디스 서버
- 컨테이너간 통신을 할 때, 따로 설정을 해주어야 함
- **멀티 컨테이너 간 네트워크 연결을 시켜주기 위해서 Docker Compose를 이용!**

## Docker Compose 파일 작성하기
### yml이 무엇일까
- YAML ain't markup language의 약자
- 일반적으로 구성 파일 및 데이터가 저장되거나 전송되는 응용프로그램에서 사용됨
- XML이나 json 포맷으로 많이 쓰였으나, 좀더 가독성있는 포맷으로 나타난게 yaml임

### docker-compose.yml
- `$ docker-compose up`
- ![Image]({{ site.baseurl }}/images/docker_compose.png)<br>
- 컨테이너는 services로 감싸져있음
- 도커 컴포즈 파일은 버전을 명시해주어야 함
  
> version: "3" # 도커 컴포즈의 버전 <br>
  services: # 이곳에 실행하려는 컨테이너들 <br>
    redis-server: # 컨테이너 이름 <br>
      image: "redis" # 해당 컨테이너에서 사용하려는 이미지 <br>
    node-app: # 컨테이너 이름 <br>
      build: . # 현 디렉토리에 있는 Dockerfile <br>
      ports: # 포트맵핑 로컬포트: 컨테이너 포트 <br>
        - "5000:8080" <br>

## Docker Compose 명령어
- `$ docker-compose up` : 이미지가 없을 때 이미지를 빌드하고 컨테이너 시작
- `$ docker-compose --build` : 이미지가 있든 없든 무조건 빌드하고 컨테이너 시작 
- `$ docker-compose -d --build` : 이미지 생성하고 컨테이너 시작 후 빠져나옴
  - docker ps 하면 컨테이너 실행중임을 확인할 수 있음 

### Docker Compose로 컨테이너를 멈추기
- `$ docker compose down`

