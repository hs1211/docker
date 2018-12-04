# Docker

- [docker curriculum](https://docker-curriculum.com/)

##  Getting Started
해당 문서는 실제적인 도커 사용법에 대하여 정리한 것이다. 물론 이론 중에 중요하다고 판단되는 부분은 기록으로 남길것이다. 하지만 실제적인 명령 및 사용법에 초점을 맞추어 설명할 예정이다.

> version: 18.05.0-ce

### Hello world
프로그래밍을 시작할때 "Hello world"와 같이 busybox를 실행할 수 있다.
```
➜  eb-docker git:(master) ✗ docker run busybox
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
90e01955edcd: Pull complete
Digest: sha256:2a03a6059f21e150ae84b0973863609494aad70f0a80eaeb64bddd8d92465812
Status: Downloaded newer image for busybox:latest
➜  eb-docker git:(master) ✗ docker run busybox echo "hello from busybox"
hello from busybox
➜  eb-docker git:(master) ✗
```

### Container 삭제
- 수동 삭제
```
$ docker rm $(docker ps -a -q -f status=exited)
```

- 자동 삭제
```
$ docker run --rm prakhar1989/static-site
```

### Container Exposing Port
```
$ docker port static-site
80/tcp -> 0.0.0.0:32769
443/tcp -> 0.0.0.0:32768
```
## Docker on AWS(Beanstalk)

### single container using Beanstalk  

- [Dockerrun.aws.json](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/single-container-docker-configuration.html#create_deploy_docker_image_dockerrun)
```json
{
  "AWSEBDockerrunVersion": "1",
  "Image": {
    "Name": "prakhar1989/catnip",
    "Update": "true"
  },
  "Ports": [
    {
      "ContainerPort": "5000"
    }
  ],
  "Logging": "/var/log/nginx"
}
```

### Multi Container using ecs

- Prerequisuite

(1) 설정
```
$ ecs-cli configure --region us-east-1 --cluster foodtrucks
INFO[0000] Saved ECS CLI configuration for cluster (foodtrucks)
```

(2) 클러스터 생성
```
$ ecs-cli up --keypair ecs --capability-iam --size 2 --instance-type t2.micro
INFO[0000] Created cluster                               cluster=foodtrucks
INFO[0001] Waiting for your cluster resources to be created
INFO[0001] Cloudformation stack status                   stackStatus=CREATE_IN_PROGRESS
INFO[0061] Cloudformation stack status                   stackStatus=CREATE_IN_PROGRESS
INFO[0122] Cloudformation stack status                   stackStatus=CREATE_IN_PROGRESS
INFO[0182] Cloudformation stack status                   stackStatus=CREATE_IN_PROGRESS
INFO[0242] Cloudformation stack status                   stackStatus=CREATE_IN_PROGRESS
- aws-compose.yml
```

- aws esc파일 생성
```
es:
  image: elasticsearch
  cpu_shares: 100
  mem_limit: 262144000
web:
  image: prakhar1989/foodtrucks-web
  cpu_shares: 100
  mem_limit: 262144000
  ports:
    - "80:5000"
  links:
    - es
```

- run and up
```
$ ecs-cli compose --file aws-compose.yml up
```


### Additional Resources

- [Awesome Docker](https://github.com/veggiemonk/awesome-docker0)
- [Hello Docker Workshop](http://docker.atbaker.me/)
- [Why Docker](https://blog.codeship.com/why-docker/)
- Docker Weekly and [archives](https://blog.docker.com/docker-weekly-archives/)
- [Codeship Blog](https://blog.codeship.com/)