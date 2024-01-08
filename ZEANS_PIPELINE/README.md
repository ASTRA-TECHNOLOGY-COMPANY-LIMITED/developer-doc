# 1. Jenkins

-   Docker 명령어 실행 가능한 환경을 위한 Custom Jenkins 이미지를 빌드합니다.

```bash
cd jenkins
docker build -t jenkins-with-docker .
```

-   Custom Jenkins 이미지를 컨테이너를 실행합니다.

```bash
docker run -u root -p 8080:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock -v /var/jenkins_home --name jenkins jenkins-with-docker
```
