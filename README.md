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
docker run [IMAGE ID or NAME] : 컨테이너 실행
docker ps : 실행중인 컨테이너 출력 
docker ps -a : 종료된 컨테이너 포함하여 출력 
docker stop [CONTAINER ID or NAME] : 컨테이너 종료
docker restart [CONTAINER ID or NAME] : 종료된 컨테이너 실행
docker attach [CONTAINER ID or NAME] : 실행중 컨테이너 표준 입력(stdin)과 표준 출력(stdout)을 연결
docker rm [CONTAINER ID or NAME] : 컨테이너 삭제 
```
## 이미지 추가 3가지 방법
1. docker pull <이미지 이름> : docker.io의 공식 저장소에서 이미지를 다운
2. 커밋 : 
3. Dockerfile : 필요한 설정이 기술된 파일 기준으로 도커 이미지 생성

## bash를 실행하지 않으면?
쉘을 실행시키지 않았을 때 이 가상 머신을 조작할 수 있는 방법이 있을까요? 원론적으로 불가능한 것은 아닙니다. 이 부분에 대해서 여기선 다루지 않습니다만, 직접 파일 시스템을 조작할 수도 있고, 리눅스 컨테이너를 조작해 특정 컨테이너에 대해 쉘을 실행시킬 수도 있습니다. 하지만 좀 더 정상적인 방법은 조작이 필요한 컨테이너에 ssh 서비스를 올려서 ssh 프로토콜로 접근하는 방법입니다. 
