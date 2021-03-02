---
title:  "Docker Compose"
date: 2021-03-02 19:22:00
description: Start with Docker/Kubernetes
categories: [docker]
resource: true
comments: true
---


# Docker Compose
## Docker Compose란?
- docker compose란 다중 컨테이너 도커 어플리케이션을 정의하고 실행하기 위한 도구
- 컨테이너를 이용한 서비스의 개발과 CI를 위해 여러 개의 컨테이너를 하나의 프로젝트로서 다룰 수 있는 작업 환경을 제공
- 여러 컨테이너의 옵션과 환경을 정의한 파일을 읽어 순차적으로 컨테이너를 생성함
- `$ docker-compose up`

## Docker Compose 파일 작성하기
#### yml이 무엇일까
- YAML ain't markup language의 약자
- 일반적으로 구성 파일 및 데이터가 저장되거나 전송되는 응용프로그램에서 사용됨
- XML이나 json 포맷으로 많이 쓰였으나, 좀더 가독성있는 포맷으로 나타난게 yaml임

### docker-compose.yml
- `$ docker-compose up`
- ![Image]({{ site.baseurl }}/images/docker_compose.png)<br>
- 컨테이너는 services로 감싸져있음
- 도커 컴포즈 파일은 버전을 명시해주어야 함
- 프로젝트 명을 명시하지 않으면, 컨테이너들은 현재 디렉토리 이름으로 시작하는 이름을 가지게 됨
- 버전 정의, 서비스 정의, 볼륨정의, 네트워크 정의 등 4가지 항목으로 구성
  - 서비스 정의
    - image
      - 컨테이너 생성 시, 쓰일 이미지 이름 설정
    - links
      - `$ docker run --link` 와 같음
      - 다른 서비스에 서비스명만으로 접근할 수 있도록 설정
      - [SERVICE:ALIAS]로 접근 가능
    - environment
      - `$ docker run --env` 와 같음
    - command
    - depends_on
      - 명시된 컨테이너가 먼저 생성되고 실행됨
      - **links**도 컨테이너의 생성 순서와 실행 순서를 정의하나, depends_on은 서비스 이름만으로도 접근 가능함
      - `--no-deps` 옵션으로 의존성이 없는 컨테이너를 생성 가능
    - ports
    - build
      - 도커 파일에서 이미지를 빌드해 서비스의 컨테이너를 생성하도록 설정
    - extends
      - 다른 YAML이나 현재 YAML에서 서비스 속성을 상속받도록 설정
- `$ docker-compose config` 명령어를 사용하여 yaml파일의 오타나 파일 포맷을 검사할 수 있음

```
version: "3" # 도커 컴포즈의 버전 <br>
services: # 이곳에 실행하려는 컨테이너들 <br>
  redis-server: # 컨테이너 이름 <br>
    image: "redis" # 해당 컨테이너에서 사용하려는 이미지 <br>
  node-app: # 컨테이너 이름 <br>
    build: . # 현 디렉토리에 있는 Dockerfile <br>
    ports: # 포트맵핑 로컬포트: 컨테이너 포트 <br>
      - "5000:8080" <br>
```


## Docker Compose 명령어
- `$ docker-compose up` : 이미지가 없을 때 이미지를 빌드하고 컨테이너 시작
- `$ docker-compose --build` : 이미지가 있든 없든 무조건 빌드하고 컨테이너 시작 
- `$ docker-compose -d --build` : 이미지 생성하고 컨테이너 시작 후 빠져나옴
  - docker ps 하면 컨테이너 실행중임을 확인할 수 있음 
- `$ docker-compose scale {컨테이너명}=2`
  - 스케일링 가능
- `$ docker-compose -p {프로젝트 명} up -d`
  - 프로젝트 명을 설정함으로 제어할 프로젝트의 이름을 명시할 수 있음
  - `$ docker-compose -p {프로젝트 명} ps`
- `$ docker compose down`
  - 컨테이너 멈추기


## 스웜모드와 함께 사용하기 : STACK
- ***스택***
  - YAML 파일에서 생성된 컨테이너의 묶음
  - 스택을 생성하면, YAML 파일의 정의된 서비스가 스웜 모드의 클러스터에서 일괄적으로 생성됨
  - 서비스가 스웜모드의 서비스로 변환된 것
- `docker-compose`가 아니라 `docker stack`으로 제어해야 함
- `$ docker stack deploy -c docker-compose.yaml {stack명}`

### 스택 명령어
- `$ docker stack ls`
- `$ docker stack ps`
- `$ docker stack rm`

