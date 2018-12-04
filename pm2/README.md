# PM2 배포 설정

## Feature
- GitHub
- PM2 Deployment 


## GitHub
서버에서 우선 소스를 다운로드 받기위해서는 `개발팀 계정`이 필요하다.

### 개발 계정 등록

1. 우선 등록할 키를 생성하자
```
ssh-keygen -t rsa -C “your_email@example.com”
``` 

2. ssh 설정하자.
```
$ touch ~/.ssh/config
# me account
Host github.com-me
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_me

# work account
    Host github.com-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_work
```

3. GitHub 설정

우선 `개발 계정`으로 로그인 후, 설정으로 이동하여 SSH and GPG keys에 위에서 생성한 `public key`를 등록하자.

<img src=".images/ssh-key-reg.png" width="500" height="300" />

### 서버 설정
1. 서버에 가서 등록한 git 주소를 확인하고 필요하면 변경하자.
```
$ git remote -v

origin git@github.com:{GithubID}/{RepositoryName}.git 

$ git remote remove origin
$ git remote add origin ~
```

## PM2 Deployment
PM2를 활용할 경우 아래와 같은 설정 파일을 통하여 배포가 가능하다. 해당 항목들의 설명은 아래 파일의 주석을 참조하면 된다.

- ecosystem.config.js
```
module.exports = {
  /**
   * 앱 설정
   */
  apps: [
    {
      name: 'app_name',
      script: './server.js', // 앱 실행 스크립트
      instances: 4, // 앱 인스턴스의 수
      exec_mode: 'cluster', // 실행 모드.
      env: { // 환경변수. 모든 배포 환경에서 공통으로 사용한다.
        NODE_ENV: 'production',
      },
      env_staging: { // staging 배포 환경에서만 사용할 환경 변수
        API_ROOT: 'http://api.server.name'
      }
    },
  ],

  /**
   * 배포 설정
   */
  deploy: {
    staging: {
      user: 'root', // 접속할 계정. SSH를 사용해서 서버에 접속할 수 있어야 한다.
      host: 'appstaging.server.name', // 서버 도메인 또는 IP
      ref: 'origin/develop', // 서버에서 clone할 브랜치
      repo: 'git@github.com:user/reponame.git', // Git 저장소 URL
      ssh_options: 'StrictHostKeyChecking=no', // SSH 접속 옵션.
      path: '/home/www/project_root', // 앱을 설치할 폴더 위치
      'post-deploy': // PM2가 배포(git clone)한 후 실행할 명령어
        'npm install && npm run build && pm2 reload ecosystem.config.js'
    },
  },
}
```

### 상세설명

> 앱을 재시작 할 경우 `restart`보다는 `reload`가 좋다. 전자는 프로세스를 즉시 종료시키고 재시작하기에 접속이 불가능한 시간이 발생할 수 있지만, 후자는 그런 간격이 생기지 않도록 해준다. 여기서 `reload`는 `gracefulReload`에 해당한다. 

1. 설정
```
$ pm2 deploy staging setup
```

2. 배포 
```
$ pm2 deploy ecosystem.config.js staging
```

### 예시
```
module.exports = {
    apps: [{
      name: "sample",
      script: "./src/index.js",
      "env_production" : {
        "NODE_ENV": "production"
      },
      "env_development" : {
        "NODE_ENV": "development"
      },
      "env_staging" : {
        "NODE_ENV": "staging"
      }
    }],
    deploy: {
      devtest: {
        key: "~/.ssh/*******.pem",
        user: "ec2-user",
        host: ["***.***.***.***"],
        ssh_options: "StrictHostKeyChecking=no",
        ref: "origin/master",
        repo: "git@github.com-build:app/sample.git",
        path: "/home/ec2-user/sample",
        "pre-setup": "",
        "post-deploy": "npm install; pm2 reload ecosystem.config.js --env development"
      },
      mainnet: {
        key: "~/.ssh/*******.pem",
        user: "ubuntu",
        host: ["***.***.***.***"],
        ssh_options: "StrictHostKeyChecking=no",
        ref: "origin/master",
        repo: "git@github.com-build:app/sample.git",
        path: "/home/ubuntu/sample",
        "pre-setup": "",
        "post-deploy": "npm install; pm2 reload ecosystem.config.js --env staging"
      }
    }
   }
```