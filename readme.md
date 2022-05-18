# Docker

## CH1
- 전통적인 서버관리 방식
  - Linux -> Add user -> System Env -> Firewall -> Network -> Dependencies -> Python -> Git clone -> Package -> Configuration -> Migration -> Proxy -> Run

- 도커
  - 컨테이너 기반
  - 오픈소스
  - 가상머신보다 빠르고, 효율적


## CH2. 
### 1. 설치

Homebrew로 설치
```
brew install --cask docker
```
설치확인
```
docker version
```
도커는 Client-Server 구조이다.

### 2. 명령어

#### 1) run 명령어
- 컨테이너 실행
```
docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
```

|옵션|설명|
|------|---|
|-d|detached mode (백그라운드 모드)|
|-p|호스트와 컨테이너의 포트를 연결|
|-v|호스트와 컨테이너의 디렉토리를 연결|
|-e|컨테이너 내에서 사용할 환경변수 설정|
|--name|컨테이너 이름 설정|
|--rm|프로세스 종료시 컨테이너 자동 제거|
|-it|-i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션|
|--network|네트워크 연결|


ubuntu 20.04 컨테이너 /bin/sh 실행
```
docker run --rm -it ubuntu:20.04 /bin/sh
```

CentOS 컨테이너 /bin/sh 실행
```
docker run --rm -it centos:8 /bin/sh
```

웹 어플리케이션 실행
```
docker run --rm -p 5678:5678 jxlwqq/http-echo -text="hello world"
```

Redis 실행
```
docker run --rm -p 1234:6379 redis
```

MySQL 실행
```
docker run -d -p 3306:3306 \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
  --platform linux/x86_64 \
  --name mysql \
  mysql:5.7
```
```
docker exec -it mysql mysql
```
```mysql
create database wp CHARACTER SET utf8;
grant all privileges on wp.* to wp@'%' identified by 'wp';
flush privileges;
quit
```



워드프레스 블로그
```
docker run -d -p 8080:80 \
-e WORDPRESS_DB_HOST=host.docker.internal \
-e WORDPRESS_DB_NAME=wp \
-e WORDPRESS_DB_USER=wp \
-e WORDPRESS_DB_PASSWORD=wp \
wordpress
```

#### 2) exec 명령어
- exec 명령어는 run 명령어와 달리 실행중인 도커 컨테이너에 접속할 때 사용
```
docker exec -it mysql mysql
```

#### 3) ps 명령어
- 실행중인 컨테이너 목록
- 중지된 컨테이너 목록 -a 옵션 사용

```
docker ps
```
```
docker ps -a
```

#### 4) stop 명령어
- 실행중인 컨테이너 정지
```
docker stop [OPTIONS] CONTAINER [CONTAINER...]
```

#### 5) rm 명령어
- 종료된 컨테이너 제거
```
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```

#### 6) logs 명령어
- 컨테이너의 로그 확인
- -f 옵션으로 지속적으로 로그 확인
```
docker logs [OPTIONS] CONTAINER
```

#### 7) images 명령어
- 다운로드한 이미지 목록 조회
```
docker images
```

#### 8) pull 명령어
- 이미지 다운로드
```
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```
```
docker pull ubuntu:18.04
```

#### 9) rmi 명령어
- 이미지 삭제
```
docker rmi [OPTIONS] IMAGE [IMAGE...]
```

#### 10) network create 명령어
- 도커 컨테이너끼리 이름으로 통신할 수 있는 가상 네트워크 생성
```
docker network create [OPTIONS] NETWORK
```
``` 
docker network create app-network
```

#### 11) network connect 명령어
- 기존에 생성된 컨테이너에 네트워크 추가
```
docker network connect [OPTIONS] NETWORK CONTAINER
```
- mysql 이라는 이름의 컨테이너에 app-network 네트워크 추가
```
docker network connect app-network mysql
```

#### 13) network option을 사용한 명령어
- wordpress 컨테이너를 app-network 네트워크에 속하게 하고 mysql을 이름으로 접근
```
docker run -d -p 8080:80 \
  --network=app-network \
  -e WORDPRESS_DB_HOST=mysql \
  -e WORDPRESS_DB_NAME=wp \
  -e WORDPRESS_DB_USER=wp \
  -e WORDPRESS_DB_PASSWORD=wp \
  wordpress
```

#### 14) 

```
docker run -d -p 3306:3306 \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
  --network=app-network \
  --platform linux/x86_64 \
  --name mysql \
  mysql:5.7
```


#### 15) volume mount (-v) 명령어

```
docker stop myysql
docker rm mysql
docker run -d -p 3306:3306 \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
  --network=app-network \
  --platform linux/x86_64 \
  --name mysql \
  -v /Users/junhwan/Desktop/Web/Docker/mysql:/var/lib/mysql \
  mysql:5.7
```