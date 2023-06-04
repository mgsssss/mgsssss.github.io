---
layout: single
title: Docker compose 가 뭘까 ?
categories: Docker
tags: [Docker]
toc: true
---

# Docker compose

### 도커 컴포즈는 뭐에요?

* 여러 컨테이러를 일괄적으로 정의하고 실행할 수 있는 툴
    * 하나의 서비스를 운영하기 위해서는 여러 개의 application 이 동작해야 함
    * 컨테이너화 된 application 들을 통합 관리 할 수 있음

간단하게 말해서 한번에 연관성 있는 여러개의 서비스를 관리 및 실행 할 수 있다는 것이다.
docker 를 편하게 사용 하기 위해서 나온 것이다.

기본적으로 django service 를 구성할 때 docker를 사용한다면

django ,postgres, nginx 3개의 컨테이너가 필요한데 

서로의 의존성 및 환경 변수를 하나의 yml 파일로 정의 할 수 있어 상당히 유용하다.


```bash
# 기본적으로 docker는 command 실행 할 수 있다.
# But docker compose 는 yaml file 형태로 만 들 수 있다.
$ docker run --name c1 centos:7 /bin/hostname
$ docker run --name webserver -p 80:80 -v webdata:/var/www nginx:1.14
```


### compose 파일을 구성하는 명령어들
* 명령어는 기본적으로 docker와 상당히 유사하다.


#### service 
컴포즈를 이용해서 실행 할 컨테이너 옵션을 정의
```
service:
  webserver:
    image: nginx
  db:
    image: redis
```
#### build
컨테이너 빌드
```
webapp:build: .
```
#### image 
compose를 통해 실행할 이미지를 지정
```
webapp:
  image: centos:7
```
#### command 
컨테이너에서 실행될 명령어 지정
```
app:
  image:python:3.10.0apline
    command: "python runserver 0.0.0.0:8000"""
```
#### port
컨테이너가 공개하는 포트를 나열
```
webapp:
  image:nginx:1.14
  port:
    -80
    -8443:443
```
#### link
다른 컨테이너와 연계 할 때 연계할 컨테이너 지정.
```
webserver:
  image: wordpress:latest
    link:
      db:mysql
```
#### volumes
컨테이너에 볼륨을 마운트
```
webapp:
  image: nginx:1.14
    volumes:
      - /var/www/html
```
#### environment
컨테이너에 적용할 환경변수를 정의
```
db:
  image:postgres
  environment:
    - POSTGRES_DB=postgres
    - POSTGRES_USER=postgres
    - POSTGRES_PASSWORD=postgres
```
#### restart
* 컨테이너가 종료될 때 적용할 restart 정책
* no: 재시작 되지 않음
* always: 컨테이너를 수동으로 끄기 전까지 항상 재식작.
* on-failure: 오류가 있을 시에 재시작.
```
db:
  image:mysql:5.7
  restart:always
```
#### depends_on
컨테이너간의 종속성을 정의. 정의한 컨테이너가 먼저 동작되어야 한다. 
```
services:
  web:
    image:wordpress:latest
      depends_on:
        - dbß
```

### docker compose feat: django 예시


```bash
# dockerFile
# docker-compose 에서 사용될 django image
FROM python:3
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
WORKDIR /code
COPY requirements.txt /code/
RUN pip install -r requirements.txt
COPY . /code/
```

```yaml
# docker-compose yaml
services:
  db:
    image: postgres
    volumes:
      - db_data:/data/db:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - web:/code
    ports:
      - "8000:8000"
    environment: # 환경 변수
      - POSTGRES_NAME=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    depends_on: # db가 먼저 실행 되어야 한다.
      - db
volumes:
    db_data: {}
    web: {}
```

docker compose 의 django 기본 sample 이다.



### build 및 운영
```bash
$ docker-compose up -d 
# build & run 여기서 -d 는 background mode

$ dcoker-compose ps 
# 동작 중인 컨테이너들의 현재 상태 확인

$ docker-compose sacale db=2 
# docker compose 의 정의된 service의 동작중인 서버를 늘릴 수 있다.

$ dcoker-compose down 
# 동작중인 컨테이너 들의 일괄 중지 및 삭제
```


|명령어|설명|예시|
|------|---|---|
|up|컨테이너 생성/시작|$docker-compose up|
|ps|컨테이너 목록 표시|$docker-compose ps|
|logs|컨테이너 로그 출력|$docker-compose logs|
|run|컨테이너 실행|$docker-compose run|
|start|컨테이너 시작|$docker-compose start|
|stop|컨테이너 정지|$docker-compose stop|
|restart|컨테이너 재시작|$docker-compose restart|
|pause|컨테이너 일시 정지|$docker-compose pause|
|unpause|컨테이너 재개|$docker-compose unpause|
|port|공개 포트 번호 표시|$docker-compose port|
|config|구성 확인|$docker-compose config|
|kill|실행 중인 컨테이너 강제 정지| $docker-compose kill|
|rm|컨테이너 삭제|$docker-compose rm|
|down|리소스 삭제|$docker-compose down|

이런 식으로 관리 할 수 있다.
일반적인 docker 를 사용하다 docker compose 사용하면 신세계가 열린 기분이다.
너무 편해서 ...
요즘은 github에서 dockeFile, docker-compose.yml 파일을 많이 볼 수 있다.

알아야 한다.