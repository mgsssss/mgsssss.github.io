---
layout: single
title: Docker container 기초 명령어
categories: Docker
tags: [Docker]
toc: true
---


# docker container 기초 명령어

1. 이미지 검색 : docker search [옵션] <이미지이름:태그명>
* docker search nginx
2. 이미지 다운로드 : docker pull [옵션] <이미지이름:태그명>
* docker pull nginx:1.14
3. 다운 받은 이미지 목록 출력 : docker images

4. 다운 받은 이미지 상세 보기 : docker inspect [옵션] <이미지이름:태그명>
* docker inspect nginx:1.14
5. 이미지 삭제 docker rmi [옵션] <이미지이름>
* docker rmi nginx:1.14
6. 컨테이너 생성 : docker create [옵션] <이미지이름:태그명>
* docker create --name webserver nginx:1.14
7. 컨테이너 실행 : docker start [옵션] 컨테이너 이름
* docker start webserver
8. 컨테이너 생성 / 실행 : docker run [옵션] <이미지이름:태그명>
* docker run --name webserver -d nginx:1.14
* docker run --name webserver -p 80:80 nginx:1.14
9. 실행중인 컨테이너 목록 확인 : docker ps [옵션]
* docker ps
10. 동작중인 컨테이너 중지 : docker stop [옵션] 컨테이너 이름
* docker stop webserbver
11. 컨테이너 삭제 docker rm [옵션] 컨테이너 이름
* docker rm webserver
12. 포그라운드로 실행중인 컨테이너에 연결 : docker attach [옵션] 컨테이너 이름
* docker attach centos
13. 동작중인 컨테이너에 새로운 명령어 추가 실행 : docker exec [옵션] 컨테이너 이름
* docker exec -it webserber /bin/bash
14. 컨테이너에서 동작되는 프로세스 확인 : docker top [옵션] 컨테이너 이름
* docker top webserver
15. 동작중인 컨테이너가 생성한 로그 보기 : docker logs [옵션] 컨테이너 이름
* docker logs
* docker logs -f : 지속적으로 쌓이는 로그 보기

하지만 *docker run --name webserver -d -p 80:80 nginx:1.14* 이런식으로 입력하면 이미지 다운로드 부터 실행까지 한번에 된다. 그렇지만 기초가 중요한법. 이미지 다운로드부터 컨테이너 생성까지는 알아야한다고 생각한다.
이제 docker는 실무에서 정말 많이 쓰인다. 공부하자 !