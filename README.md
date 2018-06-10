# docker
참고 : [도커(Docker) 튜토리얼 : 깐 김에 배포까지](http://blog.nacyot.com/articles/2014-01-27-easy-deploy-with-docker/)
## 맛보기
```
$ docker pull centos
$ docker run --rm -i -t centos:6.4 /bin/bash
bash-4.1#
```
위 두줄 명령어로 centos 실행. pull 명령어를 통해서 centos 이미지를 다운 후 run 명령어로 centos bash 실행  
rm 옵션으로 컨테이너 종료시 컨테이너 삭제
## 기본 명령어
```
docker images : 이미지 출력 
docker images -a : 모든 이미지 출력
docker run [IMAGE ID or REPOSITORY:TAG] : 컨테이너 실행
docker ps : 실행중인 컨테이너 출력 
docker ps -a : 종료된 컨테이너 포함하여 출력 
docker stop [CONTAINER ID or NAME] : 컨테이너 종료
docker restart [CONTAINER ID or NAME] : 종료된 컨테이너 실행
docker attach [CONTAINER ID or NAME] : 실행중 컨테이너 표준 입력(stdin)과 표준 출력(stdout)을 연결
docker rm [CONTAINER ID or NAME] : 컨테이너 삭제
docker rmi [IMAGE ID or REPOSITORY:TAG] : 이미지 삭제
```
## 이미지 추가 3가지 방법
1. docker pull <이미지 이름> : docker.io의 공식 저장소에서 이미지를 다운
2. docker commit : 컨테이너 변경점 기준 이미지 생성
```
docker commit <컨테이너 이름> <이미지 이름>:<태그> : 컨테이너 기준 이미지 생성 
docker diff <컨테이너 이름> : 이미지와 다른 점 출력 git과 유사
```
이미지에서 (종료상태를 포함한) 파생된 컨테이너가 하나라도 있다면 이미지는 삭제할 수 없습니다.

3. Dockerfile : 필요한 설정이 기술된 파일 기준으로 도커 이미지 생성
```
FROM ubuntu:12.04
MAINTAINER Daekwon Kim <propellerheaven@gmail.com>

# Run upgrades
RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
RUN apt-get update

# Install basic packages
RUN apt-get -qq -y install git curl build-essential

# Install apache2
RUN apt-get -qq -y install apache2
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
RUN a2enmod rewrite

# Install php
RUN apt-get -qq -y install php5
RUN apt-get -qq -y install libapache2-mod-php5

# Install Moniwiki
RUN apt-get install rcs
RUN cd /tmp; curl -L -O http://dev.naver.com/frs/download.php/8193/moniwiki-1.2.1.tgz
RUN tar xf /tmp/moniwiki-1.2.1.tgz
RUN mv moniwiki /var/www/
RUN chown -R www-data:www-data /var/www/moniwiki
RUN chmod 777 /var/www/moniwiki/data/ /var/www/moniwiki/
RUN chmod +x /var/www/moniwiki/secure.sh
RUN ./var/www/moniwiki/secure.sh

EXPOSE 80
CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]
```
- FROM은 어떤 이미지로부터 새로운 이미지를 생성할 지를 지정   
- RUN은 직접 쉘 명령어를 실행하는 명령어입니다. 이 때 바로 뒤에 명령어를 입력하게 되면 쉘을 통해서 명령어가 실행
- 아파치 설치 과정에서 나오는 ENV를 통해 환경 변수를 지정할 수 있습니다.
- EXPOSE는 가상 머신에 오픈할 포트를 지정해줍니다. 마지막줄의 CMD는 컨테이너에서 실행될 명령어를 지정해줍니다. 이 글의 앞선 예에서는 docker run을 통해서 /bin/bash를 실행했습니다만, 여기서는 아파치 서버를 FOREGROUND에 실행시킵니다.
- Dockerfile 이미지 생성 build
```
docker build -t nacyot/moniwiki .
```
docker build 명령어는 -t 플래그를 사용해 이름과 태그를 지정할 수 있습니다. 그리고 마지막에 .은 빌드 대상 디렉토리를 가리킵니다. 이 때 알아두면 좋은 게 하나 있습니다. 위에서 정의한 RUN 명령 하나 하나는 명령 하나마다 이미지가 됩니다. 기본적으로 이 빌드를 통해서 생성되는 최종 이미지는 nacyot/moniwi가 됩니다만, docker images -a를 통해서 살펴보면 이름없는 도커 이미지들이 다수 생성되는 것을 알 수 있습니다.


## 컨테이너에 bash를 실행하지 않으면?
쉘을 실행시키지 않았을 때 이 가상 머신을 조작할 수 있는 방법이 있을까요? 원론적으로 불가능한 것은 아닙니다. 이 부분에 대해서 여기선 다루지 않습니다만, 직접 파일 시스템을 조작할 수도 있고, 리눅스 컨테이너를 조작해 특정 컨테이너에 대해 쉘을 실행시킬 수도 있습니다. 하지만 좀 더 정상적인 방법은 조작이 필요한 컨테이너에 ssh 서비스를 올려서 ssh 프로토콜로 접근하는 방법입니다. 

# docker compose
참고 : [Docker (Compose) 활용법 - 개발 환경 구성하기](http://raccoonyy.github.io/docker-usages-for-dev-environment-setup/)   
도커 컴포즈에서는 컨테이너 실행에 사용되는 옵션과 컨테이너 간 의존성을 모두  docker-compose.yml파일에 적어두고, 컴포즈용 명령어를 사용하여 컨테이너들을 실행, 관리합니다.
## 도커와 도커 컴포즈를 비교
- Dockerfile vs. Dockerfile-dev: 서버 구성을 문서화한 것(=클래스 선언이 들어 있는 파일)
- docker build vs. docker-compose build: 도커 이미지 만들기(=클래스 선언을 애플리케이션에 로드)
- docker run의 옵션들 vs. docker-compose.yml: 이미지에 붙이는 장식들(=인스턴스의 변수들)
- docker run vs. docker-compose up: 장식 붙은 이미지를 실제로 실행(=인스턴스 생성)   

## docker-compose.yml
```
version: '2.1'

services:
  db:
    image: postgres:9.6.1
    volumes:
      - ./docker/data:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=sampledb
      - POSTGRES_USER=sampleuser
      - POSTGRES_PASSWORD=samplesecret
      - POSTGRES_INITDB_ARGS=--encoding=UTF-8
    healthcheck:
      test: "pg_isready -h localhost -p 5432 -q -U postgres"
      interval: 3s
      timeout: 1s
      retries: 10

  django:
    build:
      context: .
      dockerfile: ./compose/django/Dockerfile-dev
    environment:
      - DJANGO_DEBUG=True
      - DJANGO_DB_HOST=db
      - DJANGO_DB_PORT=5432
      - DJANGO_DB_NAME=sampledb
      - DJANGO_DB_USERNAME=sampleuser
      - DJANGO_DB_PASSWORD=samplesecret
      - DJANGO_SECRET_KEY=dev_secret_key
    ports:
      - "8000:8000"
    depends_on:
      db:
        condition: service_healthy
    links:
      - db
    command: /start-dev.sh
    volumes:
      - ./:/app/
```
- version : 파일 규격 버전을 적습니다. 파일의 규격에 따라 지원하는 옵션이 다름
- service : 실행하려는 서비스들을 정의합니다. 서비스란, 앱 컨테이너나 db 컨테이너 각각의 묶음입니다.
- db, django : 서비스 이름
- image : 서비스에서 사용할 이미지 
- volumes : 로컬 연결 경로. 상대경로로 지정 
- environment : docker run -e 옵션 내용
- healthcheck : healthcheck
- build : 이미지를 빌드하여 사용
- context : docker build 명령을 실행할 디렉터리 경로
- dockerfile : 이미지 빌드엣 사용할 docker 파일
- ports : 커 컴포즈로 서비스를 실행하면 기본적으로 가상의 네트워크가 하나 만들어지고, 네트워크 외부의 접근이 막힙니다. 외부 접근 포트 지정
- depends_on : 실행 의존성 설정. 위 예제는 db 서비스가 실행된 후에 django 서비스를 실행하겠다는 의미
- links : docker run에서 --link 옵션. 다른 컨테이너 참조 설정
- command : 실행할 명령어 

## docker-compose 기본 명령어
```
up : docker-compose.yml 파일의 내용에 따라 서비스 실행
ps : 현재 환경에서 실행 중인 서비스를 보여줍니다.
stop, start : 서비스를 멈추거나, 멈춰 있는 서비스를 시작합니다.
down : 서비스를 지웁니다. 컨테이너와 네트워크를 삭제하며, 옵션에 따라 볼륨도 지웁니다.(--volume: 볼륨까지 삭제합니다.)
exec : 실행 중인 컨테이너에서 명령어를 실행합니다. 자동화된 마이그레이션용 파일 생성이나 유닛 테스트, lint 등을 실행할 때 사용합니다.
logs : 서비스의 로그를 확인할 수 있습니다. logs 뒤에 서비스 이름을 적지 않으면 도커 컴포즈가 관리하는 모든 서비스의 로그를 함께 보여줍니다.
```

## docker-compose up 옵션
```
-d:  (docker run에서의 -d와 같습니다.)
--force-recreate: 컨테이너를 지우고 새로 만듭니다.
--build: 서비스 시작 전 이미지를 새로 만듭니다.
```

## docker-compose.yml 수정 했다면?
- docker-compose.yml 파일을 수정하고 이를 서비스에 적용하려면 서비스를 멈추고(stop), 서비스를 지우고(rm), 서비스를 시작해야(up) 합니다. 하지만 up 명령만 실행해도, (현재 실행 중인 서비스 설정과 달라진 부분이 있다면) 알아서 컨테이너를 재생성하고 서비스를 재시작해줍니다.

## Dockerfile-dev 파일을 수정했다면?
- ockerfile-dev 파일을 수정했을 땐 build 명령을 사용하여 도커 이미지를 새로 만들어야 합니다. 이후 서비스 중지와 삭제, 재시작을 해야 하죠. 하지만 up 명령에 다음과 같이 --build 옵션을 넣으면 알아서 이미지를 새로 만들고 서비스를 재시작합니다.
