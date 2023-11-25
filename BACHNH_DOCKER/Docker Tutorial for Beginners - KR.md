# Docker Tutorial for Beginners - KR

## 1. What is Docker?

### 1.1. 개념

> Docker는 애플리케이션을 빌드(building), 실행(running), 운송(shipping)하기 위한 플랫폼입니다. 만약 어떤 애플리케이션이 우리의 컴퓨터에서 정상적으로 실행된다면, 그것은 다른 컴퓨터에서도 정상적으로 실행될 것입니다.

현실에서 어떤 애플리케이션은 우리의 컴퓨터에서 잘 동작하지만 다른 사람의 컴퓨터에서는 그렇지 않을 수 있습니다. 그 이유는 몇 가지가 있습니다:

- 몇몇 파일이 누락되었을 수 있음
- 소프트웨어 버전의 차이
- 설정의 차이 (환경 변수, ...)

Docker를 사용하면 우리의 애플리케이션을 다른 어떤 애플리케이션과 함께 하나의 패키지로 묶고 어디에서든 실행할 수 있습니다.

### 1.2. 이점

- 동일한 컴퓨터에서 여러 애플리케이션이 서로 다른 소프트웨어 버전을 동시에 사용할 수 있습니다.
  예를 들어 프로젝트에 새로 참여하는 직원이 있다면, 그들은 시간을 들여 컴퓨터를 설정할 필요가 없습니다. 그들은 단순히 Docker를 호출하여 애플리케이션을 실행할 수 있습니다.
  Docker는 **컨테이너**라고 불리는 독립된 환경에서 의존성을 자동으로 로드하고 실행합니다.
  ![Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled.png](Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled.png)
  두 개의 다른 Node 버전에서 실행 중인 두 애플리케이션
- Docker의 두 번째 이점은 애플리케이션과 해당 의존성을 쉽게 제거할 수 있게 해줍니다.
  Docker가 없다면 여러 프로젝트에서 작업할 때 컴퓨터에는 여러 응용 프로그램이 사용하는 여러 라이브러리와 도구가 존재합니다. 그런 다음 응용 프로그램이 여전히 사용 중인 경우 해당 종속성을 제거할 수 없습니다.
  Docker를 사용하면 각 응용 프로그램은 **컨테이너** 내의 종속성에 의해 실행됩니다. 따라서 응용 프로그램과 해당 종속성을 안전하게 제거할 수 있습니다.

## 2. 가상 머신 대 컨테이너

> 컨테이너는 응용 프로그램을 실행하는 독립적인 환경입니다.

> 가상 머신은 물리적 컴퓨터의 추상화로 실제 컴퓨터처럼 운영 체제, 응용 프로그램이 있는 가상 환경을 생성합니다.

> 그러나 VM은 물리적 하드웨어가 아닙니다.

### 2.1. 가상 머신

- Macbook에서 Window 및 Linux 가상 머신을 실행해야 하는 경우 가상 머신을 생성하고 실행하기 위해 **하이퍼바이저**라는 도구를 사용해야 합니다.
  > 하이퍼바이저는 가상 머신을 생성하고 관리하는 데 사용되는 소프트웨어입니다.
  몇 가지 흔한 하이퍼바이저는 Virtual Box (크로스 플랫폼), VMware (크로스 플랫폼), Hyper-v (Windows 전용) 등이 있습니다.

![Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled%201.png](Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled%201.png)

- 가상 머신 사용의 이점은 독립적인 환경에서 각각의 애플리케이션을 실행할 수 있게 해줍니다.
  ![Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled%202.png](Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled%202.png)
  예를 들어 두 개의 가상 머신이 있고 각각 다른 앱을 실행한다면 각 앱은 서로 다른 종속성을 사용할 수 있습니다.
  이 모든 것들은 동일한 컴퓨터에서 다른 환경에서 실행됩니다.
- 여러 애플리케이션을 실행하는 가상 머신 사용의 문제점
  - 각 가상 머신은 완전한 운영 체제가 필요합니다 (MacOS, Windows, Linux 등).
  - 따라서 가상 머신을 시작하는 데는 운영 체제를 시작해야 하므로 시간이 소요됩니다.
  - 가상 머신은 컴퓨터의 자원을 차지합니다. 각 가상 머신은 실제 컴퓨터의 CPU, RAM, 하드 드라이브 일부를 사용합니다. 따라서 많은 가상 머신을 실행할 수 없습니다.

### 2.2. 컨테이너

- 컨테이너는 가상 머신과 유사한 독립적인 환경을 제공하여 여러 애플리케이션을 독립적으로 실행할 수 있습니다.
- 그러나 가볍기 때문에 더 가볍습니다. 왜냐하면 운영 체제가 필요하지 않기 때문입니다.
- 컴퓨터의 운영 체제를 공유합니다.
- 운영 체제를 시작할 필요가 없으므로 시작이 빠릅니다.
- 하드웨어 리소스가 필요하지 않으므로 수백 개의 컨테이너를 동시에 실행할 수 있습니다.

## 3. Docker의 아키텍처

### 3.1. Docker의 아키텍처

Docker는 Client-Server 아키텍처를 사용합니다:

- 클라이언트는 Restful API를 통해 Docker 엔진(서버)에 요청합니다.
- Docker 엔진은 백그라운드에서 실행되며 컨테이너를 빌드하고 실행합니다.
- 기술적으로 컨테이너는 단순한 프로세스일 뿐으로, 공통된 운영 체제를 사용하며 구체적으로는 모든 컨테이너가 컴퓨터와 동일한 커널을 공유합니다.

### 3.2. 커널

> 커널은 운영 체제의 핵심 부분으로, 자동차의 엔진과 같이 모든 소프트웨어와 하드웨어를 관리합니다.

![Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled%203.png](Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled%203.png)

각 운영 체제는 고유의 커널을 갖고 있으며 해당 커널은 다른 API를 가지고 있습니다. 따라서 Linux 애플리케이션을 Linux 커널과 통신해야 하므로 Linux 애플리케이션은 Windows에서 실행할 수 없습니다. 반면에 Windows 10부터는 Linux 커널이 Windows 커널과 함께 제공되어 Linux 컨테이너와 Windows 컨테이너를 모두 실행할 수 있습니다.

MacOS는 자체적인 커널을 갖고 있으며 Windows 및 Linux 커널과 완전히 다릅니다. 이 커널은 지속적인 애플리케이션에 대한 원시 지원이 없습니다. 따라서 Docker는 Mac에서 Linux 컨테이너를 실행하기 위해 가벼운 Linux 머신을 사용합니다.

## 4. Docker 설치

MacOS 및 Windows에서는 Docker Desktop이라는 도구가 있으며, 여기에는 Docker Engine 및 다양한 지원 도구가 포함되어 있습니다. 그러나 Linux는 Docker Desktop이 없으며 Docker Engine만 있습니다.

Windows의 경우 Hyper-V 및 Containers Windows 기능을 활성화해야 합니다. 그런 다음 "WSL 2 installation is incomplete" 오류가 발생할 것이며 이 오류를 해결하기 위해 Window 내부의 Linux 커널을 업그레이드해야 합니다. 오류 메시지에 제공된 링크를 클릭하여 업그레이드를 진행하십시오.

![Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled%204.png](Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled%204.png)

## 5. 개발 워크플로우 - 작업 흐름

우리는 어플리케이션을 선택하고 그것을 **도커화**(도커화는 도커를 통해 실행될 수 있도록 애플리케이션의 일부를 변경하는 것입니다)합니다. 우리는 보통 이를 위해 애플리케이션에 `Dockerfile`을 추가하여 도커화합니다.

`Dockerfile`은 도커가 애플리케이션을 이미지로 패키징하는 데 사용하는 명령어를 포함한 평문 텍스트 파일입니다.

이미지는 애플리케이션이 실행되기 위해 필요한 모든 것을 포함하고 있습니다. 보통 다음을 포함합니다:

- 간소화된 운영 체제 (cut-down OS)
- 런타임 환경 (예: Node)
- 애플리케이션 파일들
- 서드파티 라이브러리
- 환경 변수

한 번 이미지를 가지고 있으면 도커는 해당 이미지를 사용하여 컨테이너를 시작합니다. 컨테이너는 이미지에서 제공된 자체 파일 시스템을 가진 특별한 프로세스일 뿐입니다. 이렇게 함으로써 우리의 애플리케이션이 로컬로 우리 컴퓨터에서 실행되는 방식입니다.

따라서 특정 프로세스를 통해 애플리케이션을 직접 실행하는 대신 도커를 호출하고 도커에게 해당 애플리케이션을 컨테이너 내에서 실행하도록 지시할 수 있습니다(독립된 환경에서).

![Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled%205.png](Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled%205.png)

1. 개발자가 이미지를 가지고 있으면, 그들은 해당 이미지를 Docker Registry에 푸시합니다. (Docker Hub는 Docker와 마찬가지로 Git과 함께 사용되는 이미지를 저장하는 곳입니다).
2. 이미지가 Docker Hub에 있으면 다른 기기로 가져올 수 있습니다. 해당 기기는 개발자의 기기와 동일한 이미지를 가지고 있을 것입니다(동일한 애플리케이션 버전 및 의존성).

따라서 개발자의 기기에서와 유사하게 애플리케이션을 실행할 수 있으며, 구체적으로 도커에게 해당 이미지를 사용하여이 애플리케이션을 실행하도록 지시할 수 있습니다.

## 실습

### 프로그램 생성

터미널에서 `hello-docker` 폴더를 생성하고 Visual Studio Code에서 열겠습니다.

```bash
mkdir hello-docker
cd hello-docker
code .

```

새로운 `app.js` 파일을 생성합니다.

```jsx
console.log("Hello Docker!");
```

<aside>
💡 만약 도커가 없다면, 이 프로그램을 배포하려면 다음이 필요합니다:

- 운영 체제 시작
- Node 설치
- 앱 파일 복사
- `node app.js` 실행

도커를 사용하면 이러한 단계를 `Dockerfile`에 작성하고 도커에게 응용 프로그램을 패키지화하도록 지시할 수 있습니다.

</aside>

### Dockerfile 작성

```
FROM --platform=linux/amd64 node:alpine
COPY . /app
WORKDIR /app
CMD node app.js

```

- 일부 상황에서는 `-platform=linux/amd64`가 필요하지 않을 수 있습니다.

Visual Studio Code로 돌아가서 `Dockerfile`이라는 새 파일을 만들고 도커에게 응용 프로그램을 패키지화하기 위한 지침을 작성합니다.

- `FROM`: 우리는 `FROM`이라는 구문을 사용하여 베이스 이미지를 선택합니다 (`FROM` 이후에 나오는 베이스 이미지는 많은 파일을 포함하고 있으며 우리는 이 이미지에 추가 파일을 추가할 것입니다. 이는 OOP에서 상속과 유사합니다).
  - 그러면 어떤 베이스 이미지를 사용할까요? 우리는 `linux` 이미지를 사용하고 위에 Node를 설치할 수 있습니다. 또는 이미 `linux` 위에 구축된 `node` 이미지를 사용할 수 있습니다 (`node` 이미지의 이름을 Docker Hub에서 확인할 수 있습니다).
  - Docker Hub에는 Linux의 여러 배포판을 지원하는 많은 `node` 이미지가 있을 수 있으므로 Linux 배포판을 선택하려면 `:` 다음에 정보를 추가해야 합니다. 이 경우 `node:alpine`을 사용합니다.
- `COPY`: 다음으로 우리는 `COPY` 구문을 사용하여 응용 프로그램/프로그램을 복사해야 합니다. 우리는 현재 디렉토리의 모든 파일을 `/app` 디렉토리로 복사하고 이미지에 넣을 것입니다. 따라서 해당 이미지는 자체 파일 시스템을 가진 `/app` 디렉토리를 포함합니다.
- `CMD`: 마지막으로, `CMD` 구문을 사용하여 응용 프로그램을 시작하는 명령을 실행합니다. 여기서 우리는 `node /app/app.js` 명령을 사용하여 응용 프로그램을 시작합니다.
- `WORKDIR`: `CMD` 앞에 `WORKDIR /app` 구문을 추가하면 `node app.js`로 응용 프로그램을 시작할 때 `/app`을 사용할 수 있습니다.

### 응용 프로그램 패키지 및 Docker Hub에 푸시

이제 도커에게 응용 프로그램을 패키지화하라고 지시하려면 터미널에서 다음을 입력합니다.

```bash
docker build -t hello-docker .

```

- `t hello-docker`: 이미지를 식별하는 데 사용할 태그 추가
- `.`: `Dockerfile`이 있는 위치

우리의 디렉토리에는 이미지 파일이 없습니다. 왜냐하면 이미지가 파일이 아니고 디렉토리에 저장되지 않기 때문입니다. 도커가 이미지를 어떻게 저장하는지는 복잡하므로 너무 신경 쓰지 마세요.

현재 시스템에있는 이미지를 확인하려면 터미널에서 다음을 실행하십시오.

```bash
docker images

```

![Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled%206.png](Docker%20Tutorial%20for%20Beginners%20-%20KR%207cd487ffa5904eb5b262d36ea6f7a399/Untitled%206.png)

- REPOSITORY: 이미지의 이름
- TAG: lastest (Docker의 기본값), 일반적으로 태그를 사용하여 응용 프로그램의 다른 버전을 포함하는 각 이미지를 식별합니다.

이 이미지를 실행하려면 다음 명령을 실행합니다.

```bash
docker run hello-docker

```

이미지를 Docker Hub에 게시하려면 다음을 수행하십시오 (아직 없음).

1. **Docker Hub에 로그인:**
   - Docker Hub 계정으로 로그인하려면 **`docker login`** 명령을 사용합니다.
     ```bash
     docker login -u username -p password docker.io

     ```
   - 프롬프트에서 Docker Hub 사용자 이름과 암호를 입력합니다.
2. **이미지 태그:**
   - 성공적으로 로그인 한 후에는 로컬 이미지를 Docker Hub 저장소 정보와 함께 태그해야 합니다. **`docker tag`** 명령을 사용하십시오. **`yourusername`**, **`imagename`**, \**`tag`*를 빌드 이미지에서 사용한 값으로 대체하십시오.
     ```bash
     docker tag hello-docker user/hello-docker

     ```
3. **이미지 푸시:**
   - 마지막으로 **`docker push`** 명령을 사용하여 이미지를 Docker Hub에 업로드합니다.이 명령은 지정된 이미지를 Docker Hub

저장소로 푸시합니다.

````
    ```bash
    docker push user/hello-docker
    ```

````

### Docker에서 가상 머신 만들고 테스트하는 방법

여기에서 [[https://labs.play-with-docker.com/](https://labs.play-with-docker.com/)]([https://labs.play-with-docker.com/?_gl=1*ass8a*_ga*NjQwOTAwOTYzLjE3MDA2MTYxOTc.*_ga_XJWPQMJYHQ*MTcwMDY0NTYwMi40LjEuMTcwMDY0NTYyMy4zOS4wLjA.)에](https://labs.play-with-docker.com/?_gl=1*ass8a*_ga*NjQwOTAwOTYzLjE3MDA2MTYxOTc.*_ga_XJWPQMJYHQ*MTcwMDY0NTYwMi40LjEuMTcwMDY0NTYyMy4zOS4wLjA.)%EC%97%90) 액세스합니다.

로그인하고 새 인스턴스를 만듭니다. 이 가상 머신은 Linux 및 Docker 만 포함합니다. 여기에서 이미지를 끌어오겠습니다.

```bash
docker pull codewithmosh/hello-docker

```

이미지를 다시 확인하려면 다음 명령을 실행하십시오.

```bash
docker images

```

프로그램을 실행하려면 다음을 입력하십시오.

```bash
docker run codewithmosh/hello-docker

```
