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


```bash
# 기본적으로 docker는 command 실행 할 수 있다.
# But docker compose 는 yaml file 형태로 만 들 수 있다.
$ docker run --name c1 centos:7 /bin/hostname
$ docker run --name webserver -p 80:80 -v webdata:/var/www nginx:1.14
```


### compose 파일을 구성하는 명령어들
* 명령어는 기본적으로 docker와 상당히 유사하다.

|명령어|설명|
|---|---|
|service|컴포즈를 이용해서 실행 할 컨테이너 옵션을 정의<br><pre>service:<br>  webserver:<br>    image: nginx<br>  db:<br>    image: redis</pre>|
|build|컨테이너 빌드<pre>webapp:<br>  build: .</pre>|
|image|compose를 통해 실행할 이미지를 지정<pre>webapp:<br>  image: centos:7</pre>|
|command|컨테이너에서 실행될 명령어 지정<pre>app:<br>  image:python:3.10.0apline<br>    command: "python runserver 0.0.0.0:8000"""</pre>|
|port|컨테이너가 공개하는 포트를 나열<pre>webapp:<br>  image:nginx:1.14<br>    port:<br>      -80<br>      -8443:443</pre>|
|link|다른 컨테이너와 연계 할 때 연계할 컨테이너 지정.<pre>webserver:<br>  image: wordpress:latest<br>  link:<br>   db:mysql</pre>|
|volumes|컨테이너에 볼륨을 마운트<pre>webapp:<br>  image: nginx:1.14<br>  volumes:<br>    - /var/www/html</pre>|
|environment|컨테이너에 적용할 환경변수를 정의<pre>db:<br> image:postgres<br> environment:<br>   - POSTGRES_DB=postgres<br>   - POSTGRES_USER=postgres<br>   - POSTGRES_PASSWORD=postgres</pre>|
|restart|컨테이너가 종료될 때 적용할 restart 정책<br>no: 재시작 되지 않음 <br/>always: 컨테이너를 수동으로 끄기 전까지 항상 재식작.<br>on-failure: 오류가 있을 시에 재시작.<pre>db:<br>  image:mysql:5.7<br>  restart:always</pre>|
|depends_on|컨테이너간의 종속성을 정의. 정의한 컨테이너가 먼저 동작되어야 한다. <pre>services:<br/>  web:<br/>    image:wordpress:latest<br/>    depends_on:<br/>      - db</pre>|

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
django의 경우 nginx celery redis 등등이 들어가면 상당히 복잡해 질 수 있다. but 꼭 알아야 한다.

### 도커컴포즈로 컨테이너 실행 할 수 있나요 ?

### 빌드에서 운영까지 알려주세요