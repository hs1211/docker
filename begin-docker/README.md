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

## Network
도커 컨테이너 내부에 `ifconfig`명령을 입력해서 IP를 확인할 수 있다. 도커 내부에는 순차적으로 IP가 할당되며, 이 내부 IP는 도커가 설치된 호스트, 즉 내부 망에서만 쓸 수 있는 IP이므로, 외부완 연결될 필요가 있다. 이 과정에서 컨테이너가 시작할때마다 호스트에 `veth...`라는 네트워크 인터페이스 생성됨. veth(virtual eth)을 의미함.  veth <-> eth0이 연결됨.

- Bridge
- Host
- None
- 컨테이너 연결


### Bridge
컨테이너를 생성할때 자동으로 연결되는 docker0 브리지를 활용하도록 설정돼 있습니다. 이 네트워크는 172.17.0.x IP대역을 사용하여 순차적으로 IP를 할당함.
```
$ docker network create --driver bridge mybridge

$ docker run -i -t --name mynetwork_container \
  --net mybridge ubuntu:14.04
```

### Host
호스트 네트워크는 호스트 환경을 그대로 사용할 수 있다. 
```
$ docker run -i -t --name mynetwork_container \
  --net host ubuntu:14.04
```

### None
외부와 연결 차단
```
$ docker run -i -t --name network_none \
  --net none ubuntu:14.04
```

### Container연결
`--net` 옵션으로 컨테이너를 주게되면 컨테이너 연결을 할 수 있게된다. 만약 아래와 같이 구성하게 될 경우, 
`network_container_2`는 새로운 네트워크를 생성하지 않고, 모든 설정이 `network_container_1`과 동일하게 된다.

```
$ docker run -i -t -d --name network_container_1 ubuntu:14.04

$ docker run -i -t --name network_container_2 --net container:network_container_1 ubuntu:14.04
```

### net-alias
`--net-alias`라는 명칭으로 여러개의 컨테이너에 접근할 수 있다.

```
$ docker run -i -t -d --name network_container_1 --net mybridge --net-alias alice ubuntu:14.04

$ docker run -i -t -d --name network_container_2 --net mybridge --net-alias alice ubuntu:14.04

$ docker run -i -t -d --name network_container_3 --net mybridge --net-alias alice ubuntu:14.04
```

```
$ docker run -i -t --name network_ping_container --net mybridge ubuntu:14.04

# ping -c 1 alice
# 개별 컨테이너의 ip로 접근됨
```

## Cotainer Log

- json-file
- syslog

