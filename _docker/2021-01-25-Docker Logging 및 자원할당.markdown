---
title:  "Docker Logging 및 자원할당"
date: 2021-01-25 19:22:00
description: Inflearn
categories: [docker]
resource: true
comments: true
---

# Docker Logging 및 자원 할당
# Docker Logging
- 컨테이너의 표준 출력(StdOut)과 에러(StdErr) 로그를 별도의 메타데이터 파일로 저장하며 이를 확인하는 명령어를 제공함
- `$ docker logs [컨테이너명]`
  - 컨테이너 내부에서 출력을 보여주는 명령어
- `$ docker logs --tail n [컨테이너명]` : 최근 n개의 줄의 로그만 보여줌
- `$ docker logs -f [컨테이너명]` : **로그를 스트림으로 확인할 수 있음**

## 로그 저장방식
- 컨테이너 로그는 JSON 형태로 도커 내부에 저장됨
- `--log-opt` 옵션으로 컨테이너 Json 로그 파일의 최대 크기를 지정할 수 있음
  > docker run -it \
  --log-opt max-size=10k --log-opt max-file=3 \
  --name log-test ubuntu:14.04
- 각종 로깅 드라이버를 사용하여 수집할 수도 있음
- 도커 데몬에 적용함으로 모든 컨테이너에서 일괄적으로 동일한 조건으로 사용할 수도 있음
  - `DOCKER_OPTS="--log-driver=syslog"`

### syslog 로그
- 컨테이너 로그는 JSON 뿐만 아니라 syslog로 보내 저장하도록 설정할 수 있음
#### syslog란?
> 유닉스 계열 운영체제에서 로그를 수집하는 오래된 표준 중 하나
> 커널, 보안 등 시스템과 관련된 로그, 어플리케이션의 로그 등 다양한 로그를 수집해 저장함
- 기본적으로 로컬호스트의 syslog에 저장
- 운영체제 및 배포판에 따라 syslog 파일의 위치를 알아야 확인이 가능
- 우분투
  - /var/log/syslog
- CentOS || RHEL
  - /var/logmessages
- 우분투 16.04 || CoreOS
  - journalctl -u docker.service
- syslog를 원격 서버에 설치하면, 로그 정보를 원격 서버에 보낼 수 있음
  - rsyslog를 이용해 중앙 컨테이너로 로그를 저장할 수 있음

### fluentd 로깅
- 각종 로그를 수집 및 저장할 수 있는 오픈소스 도구
- JSON을 사용하기 때문에, 다양한 저장소에 저장할 수 있음
  
## Docker 컨테이너 자원할당 
### Docker 자원 확인 및 업데이트
- `$ docker inspect (컨테이너 이름)`
  > docker update (변경할 자원 제한) (컨테이너 이름)
  docker update --cpuset-cpus=1 centos ubuntu

1. 메모리 제한
> docker run -d \ <br>
> -- memory="1g" \ <br>
> --name memory_1g \ <br>
> nginx 
- m(메가바이트), g(기가바이트)로 제한할 수 있는 최소 메모리는 4MB
- 컨테이너에 할당된 메모리를 초과하면 컨테이너는 자동으로 종료되므로 어플리케이션에 따라 적절한 메모리 할당이 필요

2. 컨테이너 CPU 제한
- `--cpu-shares`
  - 컨테이너에  가중치를 설정해 해당 컨테이너가 CPU를 상대적으로 얼마나 사용가능한지 설정
  - 어느 비중만큼 나눠 쓸 것인지 명시하는 옵션
  - 상대적인 값으로 아무 설정도 하지 않았을 시, default는 1024로 1의 비중을 뜻함
  - 다른 컨테이너가 512를 할당하면 상대적으로 2:1의 비율로 CPU를 사용하여 총 66%:33%를 쓰게 됨
- `--cpuset-cpu`
  - 컨테이너가 특정 CPU만 사용하도록 설정
  - index는 0부터 시작
  - 여러개 지정을 원하면 --cpuset-cpu="0,3" 또는 "0-2"로 설정 가능함
- `--cpu-period, --cpu-quota`
  - 컨테이너의 CFS(Completely Fair Scheduler) 주기는 기본적으로 100ms
  - 위 옵션으로 이 주기를 변경 가능함
  - `--cpu-quota=100000`이 100ms을 의미함
  - `--cpu-period=100000 \--cpu-quota=25000`을 하면 CPU 주기가 1/4로 감소
  - [cpu-quota]/[-cpu-period] 만큼의 CPU 시간을 할당받음

#### [CFS(Completely Fair Scheduler)](https://www.kdata.or.kr/info/info_04_view.html?field=&keyword=&type=techreport&page=120&dbnum=145770&mode=detail&type=techreport)란
- 리눅스 커널의 2.6.23 릴리스에 병합된 프로세스 스케줄러이며 현재의 기본 스케줄러
- 프로세스 실행을 위해 CPU 자원 할당을 관리하며 상호작용 성능을 극대화하면서도 전반적인 CPU 이용을 극대화
- 프로세서 시간을 제공할 때 밸런스(공평성)를 유지하는 것
- 프로세스에 공평한 양의 프로세서가 제공되어야 함
- 작업 시간의 밸런스가 무너진 경우에는(다른 작업에 비해 하나 이상의 작업에 공평한 양의 시간이 주어지지 않은 경우) 작업 시간이 적게 지정된 작업에 실행 시간이 주어져야 함

3. Block I/O 제한
- 컨테이너를 생성할 때, 옵션 미설정 시 컨테이너 내부에서 파일을 읽고 쓰는 대역폭 제한이 설정되지 않음
- 블록 입출력을 과도하게 사용하지 않도록 하려면, 블록 입출력을 제한해야 함
- `--device-write-bps` / `--device-read-bps` / `--device-write-iops` / `--device-read-iops` 
  - 초당 설정
- Direct I/O의 경우만 블록 입출력에 제한이 되며, Buffered I/O는 제한되지 않음

