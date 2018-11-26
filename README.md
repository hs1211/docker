# eb-docker

## elastic beanstalk command line to run app

```
$ cat package.json
{
...
"start": "node app.js"
...
}

# 아래 명령으로 실행가능함
$ npm start

```

## Dockerizing app
Dockerfile


## Deploying app with elastic beanstalk cli

1. eb init 
- eb app을 생성하는 명령
```
# 먼저 eb init을 통해 배포할 region을 지정해준다.
$ eb init

Select a default region
1) us-east-1 : US East (N. Virginia)
2) us-west-1 : US West (N. California)
3) us-west-2 : US West (Oregon)
4) eu-west-1 : EU (Ireland)
5) eu-central-1 : EU (Frankfurt)
6) ap-south-1 : Asia Pacific (Mumbai)
7) ap-southeast-1 : Asia Pacific (Singapore)
8) ap-southeast-2 : Asia Pacific (Sydney)
9) ap-northeast-1 : Asia Pacific (Tokyo)
10) ap-northeast-2 : Asia Pacific (Seoul)
11) sa-east-1 : South America (Sao Paulo)
12) cn-north-1 : China (Beijing)
13) us-east-2 : US East (Ohio)
14) ca-central-1 : Canada (Central)
15) eu-west-2 : EU (London)
(default is 3): 10

# region을 선택했으면 아래와 같은 내용이 나타난다. 여기서 사용할 Application을 생성해주자
Select an application to use
1) legacyEBApplication
2) [ Create new Application ]
(default is 2): 2

# 사용할 Application name을 입력하자.
Enter Application Name
(default is "novem"): TestApp
Application TestApp has been created.

# 사용할 Platform을 선택해주자. 여기서 Docker로 배포하기 때문에 Docker를 선택한다.
Select a platform.
1) Ruby
2) Go
3) Java
4) Tomcat
5) PHP
6) Python
7) Node.js
8) IIS
9) Docker
10) GlassFish
11) Packer
(default is 1): 9

# Platform version은 Community Edition을 선택해주도록 하자.
Select a platform version.
1) Docker 17.03.1-ce
2) Docker 1.12.6
3) Docker 1.11.2
4) Docker 1.9.1
(default is 1): 1

# SSH로 접근할 수 있도록 할지 선택할 수 있다.
Cannot setup CodeCommit because there is no Source Control setup, continuing with initialization
Do you want to set up SSH for your instances?
(Y/n): Y

# SSH 접근을 허용했다면 사용할 KeyPair를 선택해주자.
Select a keypair.
1) RootKeyPair
2) OtherUserKeyPair
3) [ Create new KeyPair ]
(default is 1): 1
...

$ls -al
.elasticbeanstalk #  해당 디렉토리 생성
...
```

2. eb create
- app의 환경을 생성하는 명령
```
$ eb create
# Environment Name을 입력한다. Default로 [ApplicationName-dev] 로 잡힌다.
Enter Environment Name
(default is TestApp-dev):
# DNS Name을 입력한다. Default로 [ApplicationName-dev] 로 잡힌다.
Enter DNS CNAME prefix
(default is TestApp-dev):

# Load balancer type을 입력한다. Default로 [Classic Load Balancer] 로 잡힌다.
Select a load balancer type
1) classic
2) application
(default is 1): 2
Creating application version archive "app-170926_015406".
Uploading TestApp/app-170926_015406.zip to S3. This may take a while.
Upload Complete.
Environment details for: TestApp-dev
  Application name: TestApp
  Region: ap-northeast-2
  Deployed Version: app-170926_015406
  Environment ID: e-mdj25dyzuu
  Platform: arn:aws:elasticbeanstalk:ap-northeast-2::platform/Docker running on 64bit Amazon Linux/2.7.4
  Tier: WebServer-Standard
  CNAME: TestApp-dev.ap-northeast-2.elasticbeanstalk.com
  Updated: 2017-09-26 01:54:09.941000+00:00
Printing Status:
...
```
## eb local test
- 로컬 테스트 명령은 아래와 같다.
```
$ eb local run
```
## Link
- [dockerize](https://medium.com/@sommershurbaji/deploying-a-docker-container-to-aws-with-elastic-beanstalk-28adfd6e7e95)
- [eb-docker](http://blog.digitopia.com/elastic-beanstalk-docker-deployment/)
- [codefresh](https://codefresh.io/docs/docs/deploy-your-containers/elastic-beanstalk/)
