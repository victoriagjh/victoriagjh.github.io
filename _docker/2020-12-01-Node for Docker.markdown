---
title:  "Node For Docker"
date: 2020-12-01 19:22:00
description: Inflearn
categories: [docker]
resource: true
comments: true
---

# Node For Docker
## Node.js 앱 만들기
- package.json
  - 프로젝트의 정보와 프로젝트에서 사용중인 패키지의 의존성을 관리하는 곳
- server.js
  - 시작점(Entry-Point)으로서 가장 먼저 시작되는 파일

### package.json 만들기
> {  <br>
  "name": "node", <br>
  "version": "1.0.0", <br>
  "description": "", <br>
  "main": "index.js", <br>
  "scripts": { <br>
    "test": "echo \"Error: no test specified\" && exit 1", <br>
    "start": "node server.js" <br>
  }, <br>
  "author": "", <br>
  "license": "ISC", <br>
  "dependencies": { <br>
    "express": "^4.17.1" <br>
  } <br>
}

### server.js 만들기
> // server.js <br>
// express 모듈을 불러온다! <br>
const express = require('express'); <br>
<br>
// express PORT 설정값 <br>
const PORT = 8080; <br>
// express 호스트 주소 <br>
const HOST = '0.0.0.0'; <br>
<br>
// express class 인스턴스를 생성한다. <br>
const app = express(); <br>
<br>
// get method 설정! <br>
app.get('/', (req, res) => { <br>
  res.send('hello World'); <br>
}); <br>
<br>
// 해당 서버의 포트 및 호스트를 설정하는 것. <br>
app.listen(PORT, HOST); <br>
<br>
// 단순 로그를 남겨보자! <br>
console.log(`Running on http:://${HOST}:${PORT}`); <br>

## Docker 파일 작성하기
> FROM node:10  <br>
RUN npm install  <br>
CMD ["node", "server.js"]  <br>

### alpine 대신 node를 베이스 이미지로 쓰는 이유
- alpine 이미지는 가장 최소한의 경량화된 파일들이 들어있음
- npm을 위한 파일이 들어있지 않아서 RUN 부분에 npm install을 할 수가 없음
- npm이 있는 베이스 이미즈 중 하나가 node 이미지임

### npm install이란?
- npm은 node.js로 만들어진 모듈을 웹에서 받아서 설치하고 관리해주는 프로그램임
- npm install은 package.json에 적혀있는 종속성들을 웹에서 자동으로 다운받아 설치해주는 명령어임
 
### Node server란?
- node + [entry file] 명을 기반으로 앱을 실행시킬 수 있음

### `saveError ENOENT: no such file or directory, open '/package.json'`
### 에러 발생 원인은?
- 베이스 이미지의 파일 스냅샷을 임시 컨테이너의 하드디스크에 넣어줌
- 그 이후 npm install을 진행
  - For 어플리케이션에 필요한 종속성을 다운받아 줌
  - 다운받을 때, package.json을 보고, 그 곳에 명시된 종속성들을 다운받아서 설치
- 그때 package.json이 해당 임시 컨테이너 내부에 있지 않음
  
### package.json을 임시컨테이너 내부로 이동시켜주려면?
- `COPY package*.json ./`
  - package.json 👉 로컬에 있는 특정 파일 == 복사할 파일 경로
  - ./ 👉 도커 컨테이너의 지정 장소에 복사해주기 == 컨테이너내에서 파일이 복사될 경로

### 동일하게, server.js도 복사해주기
> FROM node:10 <br>
COPY package*.json ./ <br> || ./ ./
RUN npm install <br>
CMD ["node", "server.js"] <br>

## 생성한 이미지로 어플리케이션 실행 시 접근이 안되는 이유
### 컨테이너 실행 명령어 바꾸기
- docker run 이미지 이름
- `$ docker run -p 49160:8080 이미지 이름`
  - 로컬 파일(package.json, index.js)을 컨테이너에 복사해줌
  - 네트워크도 마찬가지로 로컬 네트워크에 있던 것을 컨테이너 내부에 있는 네트워크에 연결시켜주어야함
- ![Image]({{ site.baseurl }}/images/docker_network.png)<br>
  - 컨테이너 내부의 8080이라는 포트에 바로 접근하려면 매핑을 해주어야 함
  - 49160은 로컬 브라우저의 포트로 가면, 컨테이너의 8080으로 매핑됨
- 호스트의 특정 IP를 사용하려면 192.168.0.100:8000:8080 과 같이 바인딩할 IP와 포트를 명시
  - 또한 여러개의 포트를 외부에 개방하려면 -p 옵션을 여러번 써서 설정할 수 있음
  - `$ docker run -it -p 5000:8080 -p 192.168.0.100:8000:8080 ubuntu:14.04`

### 옵션 --link
- A 컨테이너에서 B 컨테이너로 접근하는 가장 쉬운 방법 : NAT로 할당받은 내부 IP를 사용하는 것
  - 하지만, 도커엔진은 내부 IP를 순차적으로 할당함
  - 컨테이너 시작마다 재할당함으로 계속 IP가 변경됨
- 컨테이너에 alias를 설정함
  - `--link container_name:alias`
  - 이용하려는 컨테이너명을 alias로 매핑하여 컨테이너 내부에서는 alias로 접근가능함
- 이때 이용하려는 컨테이너가 정지된 상태라면, 해당 컨테이너에서 컨테이너를 실행시켰을 때 에러가 발생함
  - 컨테이너 실행 순서의 의존성을 정의해줌


## Working Directory 명시하기
### Working Directory란?
- 이미지안에서 어플리케이션 소스 코드를 가지고 있을 디렉토리를 생성하는 것
- 이 디렉토리가 어플리케이션의 working directory가 됨

### Working Directory를 설정하지 않고 COPY를 했을 때, 문제점
1. COPY할 파일의 이름이 기존 파일시스템의 이름과 동일하다면?
   - copy하는 파일이 기존 파일을 덮어씌움
2. 정리가 되지 않음

### Working Directory 설정 
> FROM node:10  <br>
WORKDIR usr/src/app <br>
RUN npm install  <br>
CMD ["node", "server.js"]  <br>

- 기존 컨테이너의 루트 디렉토리에 있는 것을 보는 것이 아니라
- `$ docker run -it <이미지 이름> bash/sh/zsh`시 working directory 내부로 바로 이동함

<!-- ## 어플리케이션 소스 변경으로 다시 빌드하는 것에 대한 문제점 -->
## 도커를 이용해서 실시간으로 소스가 반영시키는 법
### 현재까지 어플리케이션 실행 순서
1. 도커 파일 작성
2. `$ docker build` : 도커 파일로 도커 이미지 생성
3. `$ docker run` : 도커 이미지로 컨테이너 생성 후 앱 실행
- 변경된 파트를 어플리케이션에 적용시키려면 2,3 구간 전체를 다시 실행시켜야 함


- `$docker run -d -p 5000:8080 <이미지 이름>`
- d : detach 의 줄임말
- 컨테이너 실행 후 바로 터미널에서 빠져나오기
- 백그라운드 전용으로 이미지를 실행시켰기 때문에 -a(포그라운드)로 접근이 불가능함
- 대신 exec 옵션을 통해 해당 컨테이너에 접속할 수 있음

### 어플리케이션 소스 변경으로 재빌드 효율적으로 하는법
- `COPY ./ ./` -> `COPY package.json ./`
- 종속성을 따로 카피함으로 캐시를 이용해서 빠르게 이미지를 빌드함

## Docker Volumes
### Docker Volume이란?
- ![Image]({{ site.baseurl }}/images/docker_volume.png)<br>
- COPY 를 적용하면, 로컬의 파일을 컨테이너로!
- 볼륨을 이용하면, 도커 컨테이너에서 계속 로컬의 파일을 바라보고(매핑) 있음
- **도커 이미지를 빌드하지 않고, RUN만으로 변경된 코드를 적용시킬 수 있음**

### Volume을 사용해서 어플리케이션 실행
- `$ docker run -p 5000:8080 -v /usr/src/app/node_modules -v $(pwd):/usr/src/app <이미지 아이디>` 
- `-v /usr/src/app/node_modules`
  - 호스트 디렉토리에 node_modules는 없기에 컨테이너에 맵핑을 하지 말라고 하는 것
- `-v $(pwd):/usr/src/app`
  - pwd 경로에 있는 디렉토리 혹은 파일을 /usr/src/app 경로에서 참조
  - pwd :: print working directory
    - 현재 작업중인 디렉토리의 이름을 출력하는데 쓰임
  
### Stateless한 Docker 설계
- **Stateless** : 컨테이너가 아닌 외부에 데이터를 저장하고, 컨테이너는 그 데이터로 동작하도록 설계하는 것
- **Stateful** : 컨테이너가 데이터를 저장하고 있어 상태가 있는 경우


