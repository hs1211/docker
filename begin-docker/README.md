# Docker


## 명령어

### Run
docker run명령어로 아래의 명령을 실행했을 경우에는 아래와 같은 순서로 진행이 이루어진다.
```
$ docker run -it entos:7
```
Run명령어
(1) docker pull
(2) docker create
(3) docker start
(4) docker attach(-i -t option이 있을 경우에)

### Create
docker create명령은 아래와 같은 순서로 진행이 이루어진다.
```
$ docker create -i -t centos:7
```

Create명령어
(1) docker pull
(2) docker create


### container id
```
$ docker ps -a -q
```
- a: 모든 컨테이너
- q: 컨테이너 id만 출력

### link option
링크 옵션을 이해하기 위해서 우선 아래와 같이 db를 실행했을 경우를 살펴보자. 
```
$ docker run -d \
--name wordpressdb
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress \
mysql:5.7
```
위와 동일한 경우에 아래와 같이 `wordpressdb`라는 컨테이너 이름을 해당 컨테이너 내부에서는 `mysql` 이라는 이름으로 처리함.
```
$ docker run -d \
--name wordpress
--link wordpressdb:mysql
-p 80
```

위와 같이 `link`가 동작하는 이유는 컨테이는 띄워지는 순서에 따라 동적으로 ip가 할당되므로 ip가 아닌 이름으로 링크를 연결한다.

## Volume
