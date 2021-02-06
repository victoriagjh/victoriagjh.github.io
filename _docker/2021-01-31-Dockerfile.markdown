---
title:  "DockerFile"
date: 2021-01-31 19:22:00
description: Start with Docker/Kubernetes
categories: [docker]
resource: true
comments: true
---


# Dockerfile
## 이미지 생성 방법
### 기본 플로우
> 1. 아무것도 존재하지 않는 이미지(우분투, CentOS 등)로 컨테이너를 생성
> 2. 어플리케이션을 위한 환경을 설치하고, 소스코드 등을 복사해 잘 동작하는 것을 확인
> 3. 컨테이너를 이미지로 커밋

- 위 방법은 어플리케이션 동작 환경 구성을 위해 일일히 수작업으로 패키지 설치 및 소스코드(Git) 복사가 필요함
- 도커에서는 build 명령어 기반으로 이미지 생성이 가능
  - 컨테이너에 설치해야하는 패키지, 추가해야하는 소스코드, 실행 명령어와 쉘 스크립트 등을 한 파일[Dockerfile]에 기록
  - 도커는 Dockerfile을 읽어 컨테이너에서 작업 수행 후 이미지를 생성함
  - Dockerfile은 한줄이 하나의 명령어, 명령어를 명시한 뒤 옵션을 추가하는 방식

### Dockerfile 예시
```
FROM ubuntu:14.04
MAINTAINER victoria.k
LABEL "purpose"="practice"
RUN apt-get update
RUN apt-get install apache2 -y
ADD test.html /var/www/html
WORKDIR /var/www/html
RUN ["/bin/bash", "-c", "echo hello >> test2.html"]
EXPOSE 80
CMD apachectl -DFOREGROUND
```

- **FROM**
  - 이미지 생성시, 기반이 되는 이미지 레이어
  - Dockerfile을 작성할 때, 한번 이상 입력해야함
  - <이미지 이름>:<테그> 형식으로 작성
  - 테그를 안붙이면 자동적으로 가장 최신것으로 다운받음
  - ex) ubuntu:14.04
- **MAINTAINER**
  - 이미지를 생성한 개발자의 정보를 나타냄
  - 일반적으로 해당 도커파일을 작성한 사람과 연락할 수 있는 이메일을 입력함
  - docker 1.13.0 이후로 사용하지 않으나, 라벨로 표현 가능함
    - `LABEL maintainer "victora.k victoria.k@kakaocorp.com"`
- **LABEL**
  - 이미지에 메타데이터 추가
  - 메타데이터는 키:값의 형태로 저장되며, `docker inspect`로 확인가능
- **RUN**
  - 도커이미지가 생성되기 전에 수행할 쉘 명령어
- **ADD**
  - 파일을 이미지에 추가
  - 추가하는 파일은 Dockerfile이 위치한 디렉토리인 컨텍스트(Context)에서 가져옴
- **WORKDIR**
  - 명령어를 실행할 디렉토리를 나타냄
  - WORKDIR을 여러번 실행하면, cd 명령어를 여러번 수행한 것과 같음
- **EXPOSE**
  - Dockerfile의 빌드로 생성된 이미지에서 노출할 포트를 설정
  - 이 포트가 호스트의 포트와 바로 바인딩되지는 않고, 그냥 표시하는 것일 뿐
- **CMD**
  - 컨테이너가 시작되었을 때, 실행할 실행 파일 또는 쉘 스크립트
  - 해당 명령어는 Dockerfile내 1회만 쓸 수 있음

## 이미지 생성 및 실행
- `docker build -t [이미지 이름:테그] .`
  - `-t` 옵션은 생성될 이미지의 이름
  - build 명령어 끝에는 빌드시킬 Dockerfile의 위치를 입력해야함
    - 일반적으로 로컬의 도커파일을 사용하지만 외부 URL로 부터 내용을 가져와 빌드할 수도 있음
- `docker run -d -P --name {컨테이너 이름} [가져올 이미지 이름:테그]`
  - `-P` 옵션은 이미지에 설정된 EXPOSE의 모든 포트를 호스트에 연결하도록 설정함
  - `docker ps` || `docker port {컨테이너 이름}`
    - 컨테이너와 연결된 호스트의 포트를 확인할 수 있음
  
## 빌드 과정
### 빌드 컨텍스트
- 빌드 컨텍스트란 이미지 생성에 필요한 각종 파일, 소스코드, 메타데이터 등을 담은 디렉토리를 의미함
- 이미지 빌드 시작 시 도커는 빌드 컨텍스트를 읽어들임 
- Dockerfile에서 빌드될 이미지에 파일을 추가할 때 사용됨
  - **ADD** || **COPY**
  - [**도커 컨텍스트 외부의 파일은 추가될 수 없음**](https://stackoverflow.com/questions/27068596/how-to-include-files-outside-of-dockers-build-context)
- Dockerfile이 위치한 곳에는 이미지 빌드에 필요한 파일만 있는 것이 바람직함
- 컨텍스트는 단순 파일뿐만 아니라 하위 디렉토리도 포함하므로 불필요한 파일이 포함되면 빌드속도가 느려지고, 호스트의 메모리를 지나치게 점유할 수도 있음
- .dockerignore 작성 시, 파일에 명시된 이름의 파일을 컨텍스트에서 제외함

### 도커 파일을 이용한 컨테이너 생성과 커밋
- 이미지로 만드는 과정이 하나의 컨테이너에서 일어나는 것은 아님
- **ADD**, **RUN** 등의 명령어가 실행될 때, 새로운 컨테이너가 하나씩 생성되며 이를 이미지로 커밋함
  - 명령어 한줄이 실행 될때마다, 이전 Step에서 생성된 이미지에 의해 새로운 컨테이너가 생성됨
  - Dockerfile에 적힌 명령어를 수행하고 다시 새로운 이미지 레이어로 저장됨
- 이미지 빌드가 완료되면 Dockerfile 명령어 줄만큼 레이어가 존재 & 컨테이너도 생성 후 삭제됨

### 캐시를 이용한 이미지 빌드
- 한번 이미지 빌드 후, 다시 같은 빌드 수행 시 이전 이미지 빌드에서 사용한 캐시를 사용
- `Using cache`라는 출력 
- 같은 내용의 명령이 있다면, 같은 명령어 줄까지 이전 이미지 레이어를 활용해 이미지를 생성함
  - 빌드 오류 시, 명령어가 중지되며 이미지 레이어 생성을 위해 마지막으로 생성된 임시 컨테이너가 삭제되지 않은 채 남게됨
  - `<none>:<none>` 으로 이미지 생성
- RUN git clone 시, 해당 이미지 레이어를 계속 캐시로 사용할 것이기 때문에 깃 저장소에서 리비전이 일어나도 매번 고정 소스코드로 사용되게 됨
  - `--no-cache` 옵션을 추가해서 이미지 빌드

### 멀티 스테이지를 이용한 Dockerfile 빌드
- 하나의 도커파일안에 여러 FROM 이미지를 정의함으로써 빌드 완료 시 최종적으로 생성될 이미지의 크기를 줄일 수 있음
- Docker 17.05 버전 이상부터 지원

```
FROM golang
ADD main.go /root
WORKDIR /root
RUN go build -o /root/mainApp /root/main.go

FROM alpine:latest
WORKDIR /root
COPY --from=0 /root/mainApp .
CMD ["./mainApp"]
```

- 두번째 FROM 아래에서 사용되는 COPY 명령어는 첫번째 FROM에서 사용된 이미지의 최종상태에 존재하는 파일을 복사함
  - `--from=0`은 첫번째 이미지의 최종 상태
  - `alpine` || `busybox`
    - 우분투나 CentOS에 비해 이미지 크기가 매우 작지만 기본적인 프로그램 실행에 필요한 필수 런타임 요소가 포함되어 있는 리눅스 배포판 이미지
- 반드시 필요한 실행 파일만 최종 이미지 결과물에 포함시킴으로 이미지 크기를 줄일때 유용하게 사용할 수 있음


## 기타 명령어
### ENV, VOLUME, ARG, USER
1. ENV
- Dockerfile에서 사용될 환경변수 지정
- ${ENV_NAME} 또는 $ENV_NAME 형태로 사용 가능
- 도커파일뿐 아니라 이미지에도 저장됨
- ${env_name:-value} 
  - env_name이 설정되지 않았으면 이 환경변수 값을 value로 사용
- ${env_name:+value} 
  - env_name이 설정되지 않았으면 빈 문자열을 value로 사용 

2. VOLUME
- 빌드 된 이미지로 컨테이너를 생성했을 때, 호스트와 공유할 컨테이너 내부의 디렉토리를 설정

3. ARG  
- 명령어를 실행할 때 추가로 입력을 받아 Dockerfile 내에서 사용될 변수 값을 설정
- build 명령어 실행 시, `--build-arg` 옵션을 통해 입력 가능

4. USER
- 컨테이너 내에서 사용될 사용자 계정의 이름이나 UID를 설정하면 그 하위 명령어는 해당 사용자 권한으로 실행됨
- 일반적으로 RUN 으로 사용자의 그룹과 계정을 생성한 뒤 사용
- 루트 권한이 필요하지 않다면 USER 사용을 추천
  - 기본적으로 root 사용자가 디폴트
  - 컨테이너가 루트 권한을 갖는 것은 보안 측면에서 지양해야하는 구조
  - 최종 배포시 새 사용자를 생성해 사용하는 것을 권장

### Onbuild, Stopsignal, HealthCheck, Shell
1. ONBUILD
- 빌드된 이미지를 기반으로 다른이미지가 Dockerfile로 생성될 때 실행할 명령어를 추가
- 이미지가 빌드될 때 수행되어야 하는 각종 명령어를 나중 빌드될 이미지를 위해 저장해놓을 수 있음
- 속성을 설정하는 다른 Dockerfile 명령어와 달리 ONBUILD는 부모 이미지의 자식에만 적용됨
- 이미지가 빌드하거나 활용할 소스코드를 ONBUILD_ADD 로 추가해 더 깔끔한 도커 파일을 생성할 수 있음

2. STOPSIGNAL
- 컨테이너가 정지될 때 사용될 시스템 콜의 종류
- Default : SIGTERM
- 명령어에서 `--stop-signal` 옵션으로 사용가능

3. HEALTHCHECK
- 이미지로부터 생성된 컨테이너에서 동작하는 어플리케이션의 상태를 체크
- 프로세스가 종료되지는 않았으나 어플리케이션이 동작하고 있지 않은 상태를 방지하기 위해 사용가능
```
# vi Dockerfile
FROM nginx
RUN apt-get update -y && apt-get install curl -y
HEALTHCHECK --interval=1m --timeout=3s --retries=3 CMD curl -f http://localhost || exit 1
```
- 1분마다 curl -f 를 실행하여 nginx 어플리케이션 상태를 체크하며 3초 이상 소요시 실패로 간주
- 3번 이상 Timeout이 나면 해당 컨테이너는 unhealthy 상태가 됨
- `HEALTHCHECK`를 추가하면, docker ps 시 (healthy/unhealthy) 상태가 출력됨
- `docker inspect ` 결과의 ['state']['health']['log']에서 상태를 볼 수 있음

4. SHELL
- 기본적으로 리눅스 "/bin/sh -c", 윈도우 "cmd /S /C"
- 셸을 따로 지정하는 법
  - SHELL 뒤에 지정
    - `SHELL ["/usr/local/bin/node"]`
  - 모든 명령어를 JSON 배열 형식으로 사용해 기본 셸 비활성화 후 사용하려는 셸을 명시할 수 있음

### ADD V.S. COPY
- 빌드 컨텍스트로부터 이미지에 파일을 복사하는 역할
- COPY
  - 로컬의 파일만 이미지에 추가 가능
- `ADD`
  - 외부 URL 및 tar 파일에서도 파일을 추가할 수 있음
    - `ADD https://raw.githubusercontent.com/alicek106/mydockerrepo/master/test.html /home`
    - `ADD test.tar home` // 해당 파일을 자동으로 해제해서 추가
  - COPY의 상위 개념인 셈 
- 단, ADD 사용은 권장되지 않음
  - URL/tar 파일의 경우, 정확히 어떤 파일이 추가될지 알수 없음
  - 반면, COPY는 로컬 컨텍스트로부터 직접 추가하기 때문에, 어떤 파일이 추가될지 명확함

### ENTRYPOINT V.S. CMD
- 두 명령 모두 커맨드가 동일하게 컨테이너가 시작될 때 수행할 명령을 지정하는 역할
- CMD
  - 컨테이너가 시작될 때, 실행할 명령어를 설정
  - run 명령어에서 맨 뒤에 입력했던 커맨드와 같은 역할
- CMD with ENTRYPOINT
  - cmd는 단지 entrypoint의 인자
- CMD without ENTRYPOINT
  - cmd 명령을 그대로 실행
- 둘다 없을 시 에러 발생
- ENTRYPOINT
  - entrypoint를 기반으로 쉘 스크립트 실행
  - 이미지 내에 스크립트 파일이 존재해야 함
  1. 어떤 설정 및 실행이 필요한지에 대해 스크립트로 정리
  2. ADD 또는 COPY로 스크립트를 이미지로 복사
  3. ENTRYPOINT를 스크립트로 설정
  4. 이미지를 빌드해 사용
  5. 스크립트에서 필요한 인자는 docker run 명령어에서 Cmd로 Entrypoint의 스크립트에 전달
- 배열 형태의 명령을 실행할 때 기본 실행 인자는 `/bin/sh -c`
  - 위 명령으로 실행할 수 없다면, JSON 배열 형태로 명령어를 설정해야 함

### Dockerfile 작성 시 좋은 습관과 주의할 점
- 좋은 습관
  - 하나의 명령어를 \ 로 나눠서 가독성 높이기
  - .dockerignore 파일을 작성하여 불필요한 파일을 빌드 컨텍스트에 포함하지 않기
  - 빌드 캐시를 이용해 기존 이미지 레이어를 재사용하기
- 주의할 점

```
# vi Dockerfile
FROM ubuntu:14.04
RUN mkdir /test
RUN fallocate -l 100m /test/dummy
RUN rm /test/dummy
```

- 위 도커 파일의 경우, 100MB의 파일을 할당하고 삭제하기 때문에 이미지 크기가 ubuntu와 비슷할 것 같음
- BUT 컨테이너를 이미지로 생성할 때, 컨테이너에서 변경된 사항만 새로운 이미지 레이어로 생성하기 때문에 이 파일이 이전 레이어에 저장되어 있음
  - `해결방안`
  - 레이어가 생기지 않도록 하나의 명령어로 작성
  - `RUN mkdir /test && fallocate -l 100m /test/dummy && rm /test/dummy`
- 공통 라이브러리를 사용하는 두 이미지가 있을 때, 공통 라이브러리를 이미지로 만들어 이를 공유하게 하는 것도 방법


